# Backend Despacho y Ventas - Evaluación DevOps EP2

## Descripción

Este repositorio contiene la capa Backend utilizada en la Evaluación Parcial N°2 de la asignatura Introducción a Herramientas DevOps.

El backend está compuesto por dos microservicios desarrollados con Spring Boot:

- Backend Despacho
- Backend Ventas

Ambos servicios fueron preparados para ejecutarse mediante Docker y Docker Compose, permitiendo su despliegue en una instancia EC2 privada de AWS.

Dentro de la arquitectura propuesta, esta capa backend no queda expuesta directamente a Internet. El acceso a los servicios debe realizarse desde la capa frontend mediante reglas de Security Groups.

## Tecnologías utilizadas

- Java
- Spring Boot
- Maven
- MySQL
- Docker
- Docker Compose
- AWS EC2
- GitHub Actions

## Estructura principal del proyecto

```text
back-despacho-ventas/
├── Springboot-API-REST-DESPACHO/
│   ├── src/
│   ├── pom.xml
│   └── Dockerfile
├── Springboot-API-REST/
│   ├── src/
│   ├── pom.xml
│   └── Dockerfile
├── docker-compose.yml
└── README.md
```

## Servicios incluidos

| Servicio | Descripción | Puerto |
|---|---|---:|
| back-despacho | Microservicio de despacho | 8081 |
| back-ventas | Microservicio de ventas | 8082 |
| mysql | Base de datos MySQL | 3306 |

## Persistencia de datos

La persistencia se implementa mediante un volumen Docker llamado `mysql_data`.

Este volumen permite que los datos de MySQL se mantengan aunque los contenedores sean detenidos, reiniciados o recreados.

Configuración utilizada:

```yaml
volumes:
  mysql_data:
```

El volumen se monta en el contenedor MySQL:

```yaml
volumes:
  - mysql_data:/var/lib/mysql
```

## Variables de entorno

Los microservicios utilizan variables de entorno para conectarse a la base de datos.

| Variable | Descripción |
|---|---|
| SERVER_PORT | Puerto interno del microservicio |
| DB_ENDPOINT | Host o nombre del servicio MySQL |
| DB_PORT | Puerto de MySQL |
| DB_NAME | Nombre de la base de datos |
| DB_USERNAME | Usuario de la base de datos |
| DB_PASSWORD | Contraseña de la base de datos |

## Ejecución con Docker Compose

Para construir y levantar todos los servicios:

```bash
docker compose up -d --build
```

Para verificar los contenedores activos:

```bash
docker ps
```

Para revisar logs:

```bash
docker logs mysql-devops
docker logs back-despacho
docker logs back-ventas
```

Para detener los servicios:

```bash
docker compose down
```

Para detener los servicios eliminando también el volumen:

```bash
docker compose down -v
```

## Acceso a Swagger

Si los servicios están corriendo correctamente, Swagger queda disponible en:

```text
http://localhost:8081/swagger-ui.html
http://localhost:8082/swagger-ui.html
```

También se puede probar con:

```text
http://localhost:8081/swagger-ui/index.html
http://localhost:8082/swagger-ui/index.html
```

## Dockerfile de los microservicios

Cada microservicio incluye su propio Dockerfile.

El Dockerfile utiliza una construcción multi-stage:

1. Una etapa con Maven para compilar el proyecto.
2. Una etapa final con Java Runtime para ejecutar el archivo `.jar`.
3. Ejecución mediante usuario no root.
4. Separación entre construcción y ejecución para generar una imagen más liviana.

## Docker Compose

El archivo `docker-compose.yml` ubicado en la raíz del repositorio permite levantar:

- MySQL
- Backend Despacho
- Backend Ventas
- Red interna Docker
- Volumen de persistencia

Los servicios backend se conectan a MySQL usando el nombre del servicio:

```text
mysql
```

Esto permite la comunicación interna entre contenedores dentro de la red `backend-net`.

## Despliegue en AWS EC2

El backend se despliega en una instancia EC2 ubicada en una subred privada.

La instancia debe contar con:

- Docker instalado.
- Docker Compose instalado.
- Git instalado.
- Repositorio clonado desde la rama `deploy`.
- Contenedores backend ejecutándose en los puertos `8081` y `8082`.
- Contenedor MySQL ejecutándose con volumen persistente.

## Seguridad en AWS

La capa backend debe permanecer en una subred privada.

Reglas esperadas:

```text
Frontend → Backend Despacho : puerto 8081
Frontend → Backend Ventas   : puerto 8082
Backend  → MySQL            : puerto 3306
```

El backend no debe exponerse directamente a Internet. Solo debe aceptar tráfico desde el Security Group del Frontend.

## Automatización inicial con User Data

La instancia EC2 puede prepararse mediante User Data para automatizar la configuración inicial.

El User Data debe realizar las siguientes acciones:

```text
Actualizar sistema
Instalar Docker
Instalar Docker Compose
Instalar Git
Clonar el repositorio desde GitHub
Cambiar a la rama deploy
Ejecutar docker compose up -d --build
```

## Pipeline CI/CD

El repositorio está preparado para incorporar un pipeline de GitHub Actions.

El pipeline debe activarse al realizar push sobre la rama `deploy`.

Flujo esperado:

```text
Push rama deploy → Build imágenes Docker → Push imágenes a Docker Hub/ECR → Deploy automático en EC2
```

Este flujo permite automatizar la entrega continua de los microservicios backend hacia la instancia EC2 correspondiente.

## Relación con la arquitectura general

Este backend forma parte de una arquitectura separada por capas:

```text
Internet
   ↓
EC2 Frontend pública
   ↓
EC2 Backend privada
   ↓
Base de datos MySQL con volumen Docker
```

El objetivo es mantener solo el frontend expuesto a Internet, mientras que los servicios backend y la base de datos permanecen protegidos mediante subredes privadas y Security Groups.

## Integrantes

- Genesis Manque
- Miguel Trujillo