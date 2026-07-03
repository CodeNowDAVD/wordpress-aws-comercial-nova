# Implementación de WordPress en AWS — Comercial Nova

## 1. Datos del proyecto

| Campo | Valor |
|---|---|
| **Curso** | Virtualización de Servicios Tecnológicos |
| **Caso de estudio** | Implementación de WordPress en AWS |
| **Empresa simulada** | Comercial Nova |
| **Plataforma cloud** | AWS Academy / Amazon Web Services |
| **Región** | us-east-1 (Norte de Virginia) |
| **Repositorio** | Documentación técnica y evidencias del despliegue |

## 2. Integrantes y roles

| Integrante | Código | Rol |
|---|---|---|
| Dan Mayhuire Buendia | 201910784 | Arquitectura cloud / documentación |
| Pawel Armando Paricahua Adco | 202312725 | Implementación EC2 / WordPress |
| Rusbel Daniel Cari Mamani | 202310107 | Base de datos RDS / seguridad |
| Randy Emanuel Concha Valle | 202311899 | Monitoreo / costos / evidencias |

## 3. Problema planteado

Comercial Nova requiere implementar un portal web corporativo y de contenidos utilizando WordPress en la nube. La empresa busca mejorar la disponibilidad, seguridad, escalabilidad y control de costos de su plataforma web, sin depender de infraestructura on-premise.

## 4. Alcance de la solución

La solución contempla el despliegue de una arquitectura funcional en AWS para WordPress, incluyendo cómputo, red, almacenamiento, base de datos, seguridad, monitoreo y análisis de costos.

El alcance incluye:

- Implementación de instancias EC2 para la capa web/app.
- Configuración de una base de datos administrada en Amazon RDS.
- Uso de Amazon S3 para almacenamiento complementario y evidencias.
- Configuración de red mediante VPC, subredes y reglas de seguridad.
- Implementación de balanceo de carga y escalabilidad automática.
- Monitoreo mediante Amazon CloudWatch.
- Estimación y optimización de costos.
- Documentación técnica y evidencias del despliegue en GitHub.

## 5. Arquitectura propuesta

La arquitectura utiliza servicios administrados y recursos de infraestructura en AWS para desplegar WordPress de forma funcional, segura y escalable.

```text
Internet
    │
    ▼
Internet Gateway (igw-comercial-nova)
    │
    ▼
VPC vpc-comercial-nova (10.0.0.0/16)
    │
    ├── Subredes públicas (us-east-1a, us-east-1b)
    │       ├── Application Load Balancer (alb-comercial-nova)
    │       └── EC2 WordPress (ec2-wordpress-1a, ec2-wordpress-1b)
    │               └── Auto Scaling Group (asg-wordpress-comercial-nova)
    │
    └── Subredes privadas (us-east-1a, us-east-1b)
            └── RDS MySQL (rds-wordpress-comercial-nova)

Almacenamiento complementario: S3 (s3-comercial-nova-wordpress-dan)
Monitoreo: CloudWatch (dashboard-comercial-nova)
```

El diagrama de arquitectura se encuentra en:

```text
arquitectura/diagrama.png
```

Documentación detallada: [arquitectura/justificacion_arquitectura.md](arquitectura/justificacion_arquitectura.md)

## 6. Servicios AWS utilizados

| Servicio | Recurso principal | Función |
|---|---|---|
| Amazon VPC | vpc-comercial-nova | Segmentación de red |
| Subredes | subnet-publica-1a/1b, subnet-privada-1a/1b | Distribución multi-AZ |
| Internet Gateway | igw-comercial-nova | Acceso a Internet |
| Amazon EC2 | ec2-wordpress-1a, ec2-wordpress-1b | Capa web WordPress |
| Amazon RDS | rds-wordpress-comercial-nova | Base de datos MySQL |
| Amazon S3 | s3-comercial-nova-wordpress-dan | Almacenamiento complementario |
| Application Load Balancer | alb-comercial-nova | Distribución de tráfico HTTP |
| Target Group | tg-wordpress-comercial-nova | Registro de instancias EC2 |
| Auto Scaling | asg-wordpress-comercial-nova | Escalabilidad y resiliencia |
| Launch Template | lt-wordpress-comercial-nova | Plantilla de lanzamiento EC2 |
| Security Groups | alb / ec2 / rds | Control de acceso por capas |
| Amazon CloudWatch | dashboard-comercial-nova | Monitoreo y alarmas |
| IAM | Roles y permisos de laboratorio | Seguridad y acceso |

Inventario completo: [aws/inventario_recursos_aws.md](aws/inventario_recursos_aws.md)

## 7. Pasos de despliegue

1. **Red (VPC):** Crear VPC, subredes públicas y privadas, Internet Gateway y tablas de rutas.
2. **Seguridad:** Configurar Security Groups para ALB, EC2 y RDS aplicando mínimo privilegio.
3. **Base de datos:** Crear DB Subnet Group, instancia RDS MySQL con backups de 7 días y acceso no público.
4. **Almacenamiento:** Crear bucket S3 con bloqueo público, versionado y archivo de prueba.
5. **Cómputo:** Lanzar instancias EC2 con Amazon Linux 2023, Apache, PHP y WordPress.
6. **WordPress:** Completar instalación, conectar ambas EC2 al mismo RDS y publicar contenido.
7. **Balanceo:** Crear Target Group, Application Load Balancer y verificar targets healthy.
8. **Escalabilidad:** Configurar Launch Template, Auto Scaling Group y política de CPU al 70 %.
9. **Monitoreo:** Crear dashboard CloudWatch y alarma de CPU en EC2.
10. **Documentación:** Registrar evidencias en GitHub con commits descriptivos.

## 8. URL del Load Balancer

**Sitio público (HTTP):**

```text
http://alb-comercial-nova-1298470052.us-east-1.elb.amazonaws.com
```

El sitio WordPress *Comercial Nova* es accesible a través del Application Load Balancer. Ambas instancias EC2 comparten la misma base de datos RDS.

## 9. Evidencias

Las capturas de pantalla se encuentran en `evidencias/capturas_servicios/`:

| Área | Archivos de evidencia |
|---|---|
| VPC y red | `01_vpc_creada.png` — `06_tabla_rutas_privada.png` |
| Seguridad | `07_sg_alb_creado.png` — `09_sg_rds_wordpress_creado.png` |
| RDS | `10_db_subnet_group_rds.png` — `12_rds_disponible.png` |
| S3 | `13_s3_bucket_creado.png` — `16_s3_archivo_prueba_cargado.png` |
| EC2 / WordPress | `17_ec2_wordpress_1a_running.png` — `24_wordpress_ec2_1b_pantalla_inicial.png` |
| Load Balancer | `28_target_group_wordpress_creado.png` — `31_wordpress_desde_load_balancer.png` |
| CloudWatch | `32_cloudwatch_dashboard_metricas.png`, `33_cloudwatch_alarma_cpu_ec2.png` |
| Costos | `34_cost_explorer_estimacion.png` |
| Auto Scaling | `35_launch_template_wordpress.png` — `38_auto_scaling_target_group.png` |

Documentación de evidencias:

- [aws/evidencias_ec2_s3_rds_cloudwatch.md](aws/evidencias_ec2_s3_rds_cloudwatch.md)
- [wordpress/evidencia_publicacion_contenido.md](wordpress/evidencia_publicacion_contenido.md)

### Evidencia en GitHub

El repositorio contiene commits descriptivos que documentan cada fase del despliegue (red, RDS, S3, EC2, WordPress, ALB, CloudWatch, costos y Auto Scaling).

## 10. Seguridad

La arquitectura aplica el principio de mínimo privilegio mediante Security Groups separados por capa:

- **ALB:** HTTP (80) desde Internet. Sin HTTPS/443 configurado.
- **EC2:** En configuración final, HTTP (80) solo desde el SG del ALB; durante pruebas se habilitó temporalmente acceso directo por IP. SSH (22) restringido a IP del estudiante.
- **RDS:** MySQL (3306) solo desde el SG de EC2; sin acceso público.
- **S3:** Bloqueo de acceso público activado; acceso mediante IAM.

Detalle completo: [seguridad/matriz_accesos.md](seguridad/matriz_accesos.md)

## 11. Monitoreo

| Recurso | Nombre | Descripción |
|---|---|---|
| Dashboard | dashboard-comercial-nova | Métricas de CPU en EC2 y RDS |
| Alarma | alarma-cpu-ec2-wordpress | CPU EC2 > 70 % |

Detalle completo: [monitoreo/alertas_configuradas.md](monitoreo/alertas_configuradas.md)

## 12. Costos

Cost Explorer en AWS Academy reporta **0,00 USD** por el entorno académico. Se complementa con una estimación referencial mensual y acciones de optimización documentadas en [costos/optimizacion_costos.md](costos/optimizacion_costos.md).

## 13. Limitaciones AWS Academy

- **Cost Explorer:** Muestra 0,00 USD porque el laboratorio académico no factura al estudiante.
- **Single-AZ en RDS:** Se eligió configuración de una sola zona de disponibilidad para controlar costos y cuotas del laboratorio.
- **Cuotas y duración:** El entorno tiene límites de presupuesto, tiempo de sesión y servicios disponibles según AWS Academy.
- **HTTPS:** El sitio opera por HTTP; la configuración de certificado SSL/TLS queda como mejora futura.
- **SNS:** La alarma de CloudWatch requiere confirmación de suscripción SNS para enviar notificaciones.

## 14. Lecciones aprendidas

- La segmentación de red (subredes públicas/privadas y Security Groups por capa) reduce la superficie de ataque de forma efectiva.
- Centralizar WordPress en RDS permite que múltiples instancias EC2 compartan el mismo contenido sin replicar la base de datos manualmente.
- El Application Load Balancer simplifica el acceso público y facilita la verificación de alta disponibilidad entre zonas.
- Auto Scaling automatiza el reemplazo y escalado de instancias, pero requiere una Launch Template bien configurada.
- En AWS Academy, Cost Explorer no refleja costos reales; la estimación manual es necesaria para el análisis económico.
- Documentar cada paso con capturas y commits en GitHub facilita la evaluación académica y la trazabilidad del proyecto.

## 15. Mejoras futuras

- Configurar HTTPS con AWS Certificate Manager y redirección HTTP → HTTPS.
- Habilitar RDS Multi-AZ para mayor disponibilidad de la base de datos.
- Integrar Amazon EFS para almacenamiento compartido de archivos WordPress entre instancias.
- Configurar políticas de ciclo de vida en S3 para optimizar costos de almacenamiento.
- Implementar AWS WAF delante del ALB para protección adicional.
- Confirmar suscripciones SNS y agregar alarmas para RDS (CPU, almacenamiento libre).
- Automatizar el despliegue con Terraform o CloudFormation.

## 16. Estructura del repositorio

```text
wordpress-aws-comercial-nova/
├── README.md
├── arquitectura/
├── aws/
├── costos/
├── evidencias/
│   └── capturas_servicios/
├── infraestructura/
├── monitoreo/
├── seguridad/
└── wordpress/
```
