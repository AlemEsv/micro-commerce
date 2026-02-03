# MicroCommerce

Sistema de microservicios para gestión de comercio electrónico con arquitectura orientada a servicios.

## Qué es

MicroCommerce es una plataforma de e-commerce distribuida compuesta por tres microservicios independientes:

- **User Service**: Gestión de usuarios y autenticación
- **Order Service**: Procesamiento y gestión de pedidos
- **Inventory Service**: Control de inventario y disponibilidad de productos

Implementado con FastAPI, Docker, Kubernetes y Terraform, siguiendo prácticas de testing BDD y arquitectura hexagonal.

## Requisitos

- Docker >= 20.10
- Docker Compose >= 2.0
- Python >= 3.9 (solo para desarrollo local)
- Kubernetes CLI (kubectl) - opcional para deploy en k8s
- Terraform >= 1.0 - opcional para infraestructura

## Quickstart

### Instalar

Clonar el repositorio:

```bash
git clone <repository-url>
cd repo-test-susti-1
```

### Configurar

No requiere configuración inicial. Los servicios usan valores por defecto.

Para desarrollo local, opcionalmente crear un archivo `.env` (no incluido por defecto).

### Ejecutar

Levantar todos los servicios con Docker Compose:

```bash
docker-compose up --build
```

Los servicios estarán disponibles en:

- User Service: <http://localhost:8001>
- Order Service: <http://localhost:8002>
- Inventory Service: <http://localhost:8003>

Verificar que funcionan:

```bash
curl http://localhost:8001/ping
curl http://localhost:8002/ping
curl http://localhost:8003/ping
```

## Configuración

### Puertos

Cada servicio expone el puerto 8000 internamente, mapeado externamente:

- User: 8001 → 8000
- Order: 8002 → 8000
- Inventory: 8003 → 8000

### Variables de entorno (opcional)

Los servicios pueden configurarse mediante variables de entorno en `docker-compose.yml`:

- `LOG_LEVEL`: Nivel de logging (default: INFO)
- `DATABASE_URL`: Conexión a base de datos (si aplica)

## Uso

### Ejecutar tests

Tests unitarios de un servicio específico:

```bash
./run-unit-tests.sh
```

Tests de integración:

```bash
pytest tests/integration/
```

Tests E2E:

```bash
pytest tests/e2e/
```

Tests BDD (Behave):

```bash
cd inventory-service
behave tests/features/
```

### Deploy en Kubernetes

Con kubectl directamente:

```bash
kubectl apply -f kubernetes/
```

Con Terraform:

```bash
cd terraform
terraform init
terraform plan -var-file=dev.tfvars
terraform apply -var-file=dev.tfvars
```

### Ver logs

```bash
./logs.sh
```

## Estructura

```txt
├── inventory-service/     # Microservicio de inventario
├── order-service/         # Microservicio de pedidos
├── user-service/          # Microservicio de usuarios
├── kubernetes/            # Manifiestos K8s
├── terraform/             # IaC para despliegue
├── tests/                 # Tests de integración y E2E
├── docker-compose.yml     # Orquestación local
└── pytest.ini             # Configuración de tests
```

Cada servicio sigue arquitectura hexagonal:

- `app.py`: Punto de entrada (FastAPI)
- `service.py`: Lógica de negocio
- `repository.py`: Capa de datos
- `interfaces.py`: Contratos/abstracciones
- `tests/`: Tests unitarios y BDD

## Problemas comunes

**Los servicios no arrancan**

- Verificar que Docker esté corriendo: `docker ps`
- Limpiar contenedores anteriores: `docker-compose down -v`
- Reconstruir imágenes: `docker-compose up --build --force-recreate`

**Error de puerto en uso**

- Cambiar los puertos en `docker-compose.yml` si 8001-8003 están ocupados
- Matar procesos que usen esos puertos: `netstat -ano | findstr :8001` (Windows)
