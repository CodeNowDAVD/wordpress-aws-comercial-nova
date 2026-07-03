# Justificación de Arquitectura

## 1. Enfoque general

La solución propuesta para Comercial Nova consiste en desplegar un sitio WordPress funcional sobre AWS, utilizando una arquitectura orientada a disponibilidad, seguridad, escalabilidad y control de costos.

La arquitectura separa la capa de aplicación, base de datos, almacenamiento y monitoreo para evitar dependencia de un único componente y facilitar futuras mejoras.

## 2. Componentes principales

| Componente | Servicio AWS | Justificación |
|---|---|---|
| Red | Amazon VPC | Permite aislar lógicamente los recursos del proyecto. |
| Capa web/app | Amazon EC2 | Aloja WordPress y permite instalar Apache/Nginx, PHP y dependencias. |
| Base de datos | Amazon RDS MySQL/MariaDB | Evita administrar manualmente la base de datos y permite backups automáticos. |
| Almacenamiento | Amazon S3 | Permite guardar medios, respaldos o logs de forma persistente. |
| Balanceo | Application Load Balancer | Distribuye tráfico entre instancias EC2. |
| Escalabilidad | Auto Scaling Group | Permite aumentar o reemplazar instancias según demanda o fallos. |
| Seguridad | IAM + Security Groups | Controla permisos y accesos mínimos necesarios. |
| Monitoreo | Amazon CloudWatch | Permite visualizar métricas y generar alertas operacionales. |
| Costos | Cost Explorer / estimación | Permite analizar costos mensuales y proponer optimizaciones. |

## 3. Diseño de red

La arquitectura considera una VPC con subredes distribuidas en diferentes zonas de disponibilidad.

Se plantea usar:

- Subred pública para recursos que requieren acceso desde internet, como el balanceador de carga y las instancias EC2 si el laboratorio lo permite.
- Subred privada para la base de datos RDS.
- Internet Gateway para permitir acceso público controlado.
- Tablas de rutas diferenciadas para separar tráfico público y privado.

Esta segmentación mejora la seguridad porque la base de datos no queda expuesta directamente a internet.

## 4. Capa de aplicación

WordPress se ejecutará sobre instancias EC2 con sistema operativo Linux, servidor web Apache o Nginx, PHP y los módulos necesarios.

Se utilizarán al menos dos instancias EC2 para cumplir el requisito de disponibilidad y permitir distribución de carga.

## 5. Base de datos

La base de datos se implementará con Amazon RDS usando MySQL o MariaDB.

RDS será configurado para permitir conexiones únicamente desde la capa de aplicación EC2 mediante Security Groups. También se habilitarán respaldos automáticos para mejorar la recuperación ante fallos.

## 6. Almacenamiento persistente

Amazon S3 se utilizará para almacenar archivos de prueba, respaldos, logs o medios de WordPress.

El bucket no será público salvo que exista una justificación técnica. Se habilitará versionado o una política de ciclo de vida para cumplir con la política básica de retención.

## 7. Seguridad

La arquitectura aplicará el principio de mínimo privilegio:

- SSH restringido por IP o uso de alternativa segura si AWS Academy lo permite.
- HTTP/HTTPS permitido para acceso al sitio.
- MySQL permitido solo desde las instancias EC2.
- Bucket S3 sin acceso público por defecto.
- Uso de roles IAM cuando sea necesario.
- No uso de credenciales root para operaciones diarias.

## 8. Alta disponibilidad y escalabilidad

La alta disponibilidad se plantea mediante:

- Dos instancias EC2.
- Distribución en más de una zona de disponibilidad si el laboratorio lo permite.
- Application Load Balancer para distribuir tráfico.
- Auto Scaling Group para reemplazo automático o crecimiento según CPU.

Si AWS Academy limita algún componente, se documentará la restricción con evidencia técnica.

## 9. Monitoreo

CloudWatch será usado para crear un dashboard con métricas de EC2 y RDS.

También se configurará al menos una alarma operacional, por ejemplo:

- CPU alta en EC2.
- CPU alta en RDS.
- Bajo almacenamiento disponible en RDS.

## 10. Costos

La arquitectura será evaluada considerando costos de:

- EC2.
- RDS.
- S3.
- Load Balancer.
- CloudWatch.
- Transferencia de datos.

Se propondrán optimizaciones como rightsizing, apagado de recursos no usados, lifecycle en S3 y ajuste de almacenamiento en RDS.

## 11. Limitaciones del entorno AWS Academy

AWS Academy puede presentar restricciones de presupuesto, cuotas, servicios disponibles o duración del laboratorio.

Toda restricción que afecte la implementación será documentada con capturas y explicación técnica.