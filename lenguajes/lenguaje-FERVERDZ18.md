# Propuesta de Lenguaje Backend: Python con FastAPI

**Estudiante:** Fernando García Verduzco (\FERVERDZ18\)  
**Proyecto:** Sistema de Gestión Clínico Veterinario  
**Rol Asignado:** Arquitectura de API REST y Persistencia Relacional

---

## 1. Selección Tecnológica
* **Lenguaje:** Python 3.12+
* **Framework:** FastAPI
* **Servidor ASGI:** Uvicorn / Gunicorn
* **ORM / Driver de Base de Datos:** SQLAlchemy 2.0 (modo AsyncIO) con driver \syncpg\
* **Gestor de Base de Datos:** PostgreSQL 17

---

## 2. Justificación Técnica y Beneficios

### A. Rendimiento Asíncrono Nativo (I/O Concurrency)
FastAPI está construido sobre el estándar ASGI (*Asynchronous Server Gateway Interface*) mediante Starlette y Pydantic. Al gestionar operaciones clínicas intensivas en entrada/salida (como registrar consultas médicas, subir historiales clínicos en PDF y consultar cartillas de vacunación), el uso de \sync/await\ combinado con el driver \syncpg\ permite atender miles de solicitudes concurrentes por segundo sin bloquear el hilo principal de ejecución, compitiendo directamente en latencia con entornos como Node.js y Go.

### B. Validación Automática de Datos y Tipado Estricto
Mediante esquemas de Pydantic V2, los datos entrantes en el cuerpo de peticiones JSON (\POST\, \PUT\, \PATCH\) son serializados y validados en tiempo de compilación/ejecución con tipos nativos de Python. Si un cliente envía una fecha de vacunación o un identificador numérico con formato erróneo, el framework intercepta la petición y emite respuestas de error estandarizadas (\422 Unprocessable Entity\) antes de llegar a la lógica de negocio, blindando la integridad de la base de datos PostgreSQL.

### C. Generación Automatizada de Documentación OpenAPI / Swagger
FastAPI genera de forma dinámica interfaces interactivas de especificación de API en los endpoints \/docs\ (Swagger UI) y \/redoc\ (ReDoc). Esto agiliza la colaboración con el frontend, permitiendo probar rutas, consultar esquemas de datos esperados y autenticarse con tokens Bearer JWT sin requerir herramientas externas adicionales.

---

## 3. Ejemplo de Integración y Conexión Asíncrona a PostgreSQL

\\\python
from fastapi import FastAPI, Depends, HTTPException, status
from sqlalchemy.ext.asyncio import create_async_engine, async_sessionmaker, AsyncSession
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column
from sqlalchemy import select, String, Integer
import os

# Cadena de conexión asíncrona hacia PostgreSQL
DATABASE_URL = os.getenv(
    "DATABASE_URL", 
    "postgresql+asyncpg://admin_vet:SecurePass2026@localhost:5432/veterinaria_db"
)

engine = create_async_engine(DATABASE_URL, echo=False, pool_size=10, max_overflow=20)
AsyncSessionLocal = async_sessionmaker(engine, expire_on_commit=False)

class Base(DeclarativeBase):
    pass

class PacienteMascota(Base):
    __tablename__ = "pacientes"
    id: Mapped[int] = mapped_column(Integer, primary_key=True, index=True)
    nombre: Mapped[str] = mapped_column(String(50), nullable=False)
    especie: Mapped[str] = mapped_column(String(30), nullable=False)
    propietario: Mapped[str] = mapped_column(String(100), nullable=False)

app = FastAPI(title="API Veterinaria", version="1.0.0")

async def get_db():
    async with AsyncSessionLocal() as session:
        yield session

@app.get("/api/v1/pacientes", response_model=list)
async def listar_pacientes(db: AsyncSession = Depends(get_db)):
    result = await db.execute(select(PacienteMascota))
    pacientes = result.scalars().all()
    return [{"id": p.id, "nombre": p.nombre, "especie": p.especie} for p in pacientes]
\\\

---

## 4. Comparativa con Alternativas Tradicionales

| Criterio | Python / FastAPI | Node.js / Express | PHP / Laravel |
| :--- | :--- | :--- | :--- |
| **Paradigma Asíncrono** | Nativo (\syncio\, ASGI) | Nativo (Event Loop, Callbacks/Promises) | Tradicional sincrónico por hilo |
| **Documentación API** | Automática (OpenAPI / Swagger) | Manual (Swagger-JSDoc o Postman) | Manual o vía paquetes externos |
| **Manejo de Tipos** | Type Hints nativos + Pydantic | Requiere TypeScript compilar | Tipado gradual en PHP 8+ |
| **Conexión a PostgreSQL** | Driver asíncrono (\syncpg\) de alto rendimiento | Driver \pg\ nativo con Connection Pool | PDO / Eloquent sincrónico |
