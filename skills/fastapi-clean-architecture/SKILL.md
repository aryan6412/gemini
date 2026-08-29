---
name: fastapi-clean-architecture
description: |
  Enterprise-grade FastAPI architecture following Clean Architecture / Hexagonal patterns.
  Covers Domain-Driven Design (DDD), Pydantic v2 schemas, SQLAlchemy 2.0 async sessions,
  dependency injection (Depends), repository & service layer separation, JWT security, and pytest-asyncio suites.

  Use when developing, structuring, or refactoring production FastAPI services in Python.
license: MIT
metadata:
  version: v1
---

# FastAPI Clean Architecture & DDD Pattern

Build scalable, maintainable Python microservices and RESTful APIs using FastAPI, Pydantic v2, and Async SQLAlchemy 2.0.

---

## 1. Project Directory Layout

```
app/
├── api/                  # Interface adapters (HTTP / Routers)
│   ├── v1/
│   │   ├── endpoints/    # Route controllers
│   │   └── router.py     # Main API router aggregator
│   └── deps.py           # Dependency injection providers (DB session, Current user)
├── core/                 # Config, security, logging
│   ├── config.py         # pydantic-settings BaseSettings
│   ├── security.py       # Password hashing, JWT tokens
│   └── database.py       # AsyncEngine, async_sessionmaker
├── domain/               # Core business logic & models
│   ├── models/           # SQLAlchemy Declarative models
│   └── schemas/          # Pydantic request/response DTOs
├── repositories/         # Database access layer (CRUD)
│   ├── base.py           # Generic BaseRepository[ModelType]
│   └── user_repo.py
├── services/             # Business workflow orchestration
│   └── user_service.py
└── main.py               # FastAPI application factory
```

---

## 2. Pydantic v2 Schemas & Settings

Use `pydantic-settings` for type-safe environment configurations:

```python
# app/core/config.py
from pydantic_settings import BaseSettings, SettingsConfigDict
from pydantic import PostgresDsn, computed_field

class Settings(BaseSettings):
    model_config = SettingsConfigDict(env_file=".env", env_ignore_empty=True, extra="ignore")
    
    PROJECT_NAME: str = "FastAPI Enterprise"
    API_V1_STR: str = "/api/v1"
    SECRET_KEY: str
    ACCESS_TOKEN_EXPIRE_MINUTES: int = 60 * 24 * 7
    
    POSTGRES_SERVER: str = "localhost"
    POSTGRES_PORT: int = 5432
    POSTGRES_USER: str = "postgres"
    POSTGRES_PASSWORD: str = "postgres"
    POSTGRES_DB: str = "app"

    @computed_field
    @property
    def SQLALCHEMY_DATABASE_URI(self) -> str:
        return f"postgresql+asyncpg://{self.POSTGRES_USER}:{self.POSTGRES_PASSWORD}@{self.POSTGRES_SERVER}:{self.POSTGRES_PORT}/{self.POSTGRES_DB}"

settings = Settings()
```

---

## 3. Database Layer (SQLAlchemy 2.0 Async)

```python
# app/core/database.py
from sqlalchemy.ext.asyncio import create_async_engine, async_sessionmaker, AsyncSession
from sqlalchemy.orm import DeclarativeBase
from app.core.config import settings

class Base(DeclarativeBase):
    pass

engine = create_async_engine(
    settings.SQLALCHEMY_DATABASE_URI,
    pool_size=20,
    max_overflow=10,
    pool_pre_ping=True,
)

AsyncSessionLocal = async_sessionmaker(
    bind=engine,
    autocommit=False,
    autoflush=False,
    expire_on_commit=False,
)

async def get_db() -> AsyncSession:
    async with AsyncSessionLocal() as session:
        try:
            yield session
        except Exception:
            await session.rollback()
            raise
```

---

## 4. Repository & Service Layer Pattern

Keep database queries in the repository and business rules in the service:

```python
# app/repositories/user_repo.py
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy import select
from app.domain.models.user import User

class UserRepository:
    def __init__(self, session: AsyncSession):
        self.session = session

    async def get_by_email(self, email: str) -> User | None:
        stmt = select(User).where(User.email == email)
        result = await self.session.execute(stmt)
        return result.scalar_one_or_none()

    async def create(self, user: User) -> User:
        self.session.add(user)
        await self.session.commit()
        await self.session.refresh(user)
        return user
```

---

## 5. Clean Endpoint Controller with Dependency Injection

```python
# app/api/v1/endpoints/users.py
from fastapi import APIRouter, Depends, HTTPException, status
from sqlalchemy.ext.asyncio import AsyncSession
from app.api.deps import get_db
from app.domain.schemas.user import UserCreate, UserResponse
from app.services.user_service import UserService

router = APIRouter(prefix="/users", tags=["Users"])

@router.post("/", response_model=UserResponse, status_code=status.HTTP_201_CREATED)
async def create_user(
    user_in: UserCreate,
    db: AsyncSession = Depends(get_db),
):
    service = UserService(db)
    return await service.register_user(user_in)
```
