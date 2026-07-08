# Backend Despacho y Ventas - Evaluación Final DevOps

Este repositorio contiene los microservicios backend de ventas y despachos del proyecto final de Introducción a Herramientas DevOps.

Los servicios fueron contenedorizados con Docker, publicados en Amazon ECR y desplegados en Amazon EKS mediante manifiestos Kubernetes y GitHub Actions.

## Servicios

| Servicio | Puerto | Descripción |
|---|---:|---|
| despachos-backend | 8081 | Microservicio de gestión de despachos |
| ventas-backend | 8082 | Microservicio de gestión de ventas |
| mysql-db | 3306 | Base de datos MySQL interna del clúster |

## Despliegue

El despliegue utiliza:

- Amazon EKS
- Amazon ECR
- GitHub Actions
- Kubernetes Deployments
- Services tipo ClusterIP
- Frontend expuesto mediante LoadBalancer
- HPA para escalabilidad básica