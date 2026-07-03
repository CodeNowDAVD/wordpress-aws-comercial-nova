# Implementación de WordPress en AWS - Comercial Nova

## 1. Datos del proyecto

**Curso:** Virtualización de Servicios Tecnológicos  
**Caso de estudio:** Implementación de WordPress en AWS  
**Empresa simulada:** Comercial Nova  
**Plataforma cloud:** AWS Academy / Amazon Web Services  

## 2. Integrantes y roles

| Integrante | Código | Rol |
|---|---|---|
| Dan Mayhuire Buendia | 201910784 | Arquitectura cloud / documentación |
| Pawel Armando Paricahua Adco | 202312725 | Implementación EC2 / WordPress |
| Rusbel Daniel Cari Mamani | 202310107 | Base de datos RDS / seguridad |
| Randy Emanuel Concha Valle | 202311899 | Monitoreo / costos / evidencias |

## 3. Problema planteado

Comercial Nova requiere implementar un portal web corporativo y de contenidos utilizando WordPress en la nube. La empresa busca mejorar la disponibilidad, seguridad, escalabilidad y control de costos de su plataforma web.

## 4. Alcance de la solución

La solución contempla el despliegue de una arquitectura funcional en AWS para WordPress, incluyendo cómputo, red, almacenamiento, base de datos, seguridad, monitoreo y análisis de costos.

El alcance incluye:

- Implementación de instancias EC2 para la capa web/app.
- Configuración de una base de datos administrada en Amazon RDS.
- Uso de Amazon S3 para almacenamiento de medios, respaldos o logs.
- Configuración de red mediante VPC, subredes y reglas de seguridad.
- Implementación de balanceo de carga y/o escalabilidad.
- Monitoreo mediante Amazon CloudWatch.
- Estimación y optimización de costos.
- Documentación técnica y evidencias del despliegue.

## 5. Arquitectura propuesta

La arquitectura propuesta utiliza servicios administrados y recursos de infraestructura en AWS para desplegar WordPress de forma funcional, segura y escalable.

Componentes principales:

- Amazon VPC para segmentación de red.
- Subred pública para instancias EC2 y balanceador de carga.
- Subred privada para la base de datos RDS.
- Amazon EC2 para alojar WordPress.
- Amazon RDS MySQL/MariaDB para la base de datos.
- Amazon S3 para almacenamiento persistente de archivos.
- Application Load Balancer para distribución de tráfico.
- Auto Scaling Group para escalabilidad.
- Security Groups para control de acceso.
- CloudWatch para monitoreo y alertas.

El diagrama de arquitectura se encuentra en:

```text
arquitectura/diagrama.png