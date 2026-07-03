# Matriz de Accesos y Seguridad

## 1. Objetivo

Documentar los accesos configurados en la arquitectura WordPress en AWS, aplicando el principio de mínimo privilegio.

## 2. Security Groups

| Security Group | Recurso asociado | Puerto | Protocolo | Origen permitido | Justificación |
|---|---|---:|---|---|---|
| SG-ALB | Application Load Balancer | 80 | TCP | 0.0.0.0/0 | Permitir acceso público HTTP al sitio WordPress |
| SG-ALB | Application Load Balancer | 443 | TCP | 0.0.0.0/0 | Permitir acceso HTTPS si se configura certificado SSL/TLS |
| SG-EC2-WP | Instancias EC2 WordPress | 80 | TCP | SG-ALB | Permitir tráfico web desde el balanceador |
| SG-EC2-WP | Instancias EC2 WordPress | 22 | TCP | IP del estudiante | Permitir administración SSH restringida |
| SG-RDS-WP | Base de datos RDS | 3306 | TCP | SG-EC2-WP | Permitir conexión MySQL/MariaDB solo desde WordPress |
| SG-S3 | Bucket S3 | N/A | N/A | Acceso mediante IAM | Evitar exposición pública directa |

## 3. IAM

| Rol / Usuario | Servicio | Permisos requeridos | Justificación |
|---|---|---|---|
| Usuario estudiante AWS Academy | Consola AWS | Permisos asignados por laboratorio | Administración de recursos durante la práctica |
| Rol EC2 WordPress | EC2 / S3 | Acceso limitado a bucket S3 si aplica | Permitir carga o lectura de archivos necesarios |
| Rol CloudWatch | EC2 / RDS / CloudWatch | Lectura de métricas y gestión de alarmas | Monitoreo operacional |
| RDS | RDS | Acceso administrado por Security Group | Base de datos protegida de acceso público |

## 4. Reglas de endurecimiento básico

- No utilizar credenciales root para operaciones diarias.
- Restringir SSH únicamente a la IP del estudiante.
- No exponer RDS a internet.
- No permitir acceso público al bucket S3 salvo justificación documentada.
- Usar Security Groups separados para ALB, EC2 y RDS.
- Abrir solo los puertos necesarios.
- Documentar cualquier excepción por limitaciones de AWS Academy.

## 5. Observaciones durante la implementación

Pendiente de completar con evidencias reales de AWS Academy.