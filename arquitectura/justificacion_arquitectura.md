# Justificación de Arquitectura

## 1. Enfoque general

La solución para Comercial Nova consiste en desplegar un sitio WordPress funcional sobre AWS, utilizando una arquitectura orientada a disponibilidad, seguridad, escalabilidad y control de costos.

La arquitectura separa la capa de aplicación, base de datos, almacenamiento y monitoreo para evitar dependencia de un único componente y facilitar futuras mejoras.

## 2. Componentes principales

| Componente | Servicio AWS | Recurso | Justificación |
|---|---|---|---|
| Red | Amazon VPC | vpc-comercial-nova | Aislamiento lógico de recursos del proyecto |
| Subredes públicas | VPC Subnets | subnet-publica-1a, subnet-publica-1b | ALB y EC2 con acceso a Internet |
| Subredes privadas | VPC Subnets | subnet-privada-1a, subnet-privada-1b | RDS sin exposición a Internet |
| Internet Gateway | VPC IGW | igw-comercial-nova | Conectividad saliente/entrante controlada |
| Capa web/app | Amazon EC2 | ec2-wordpress-1a, ec2-wordpress-1b | Aloja WordPress con Apache y PHP |
| Base de datos | Amazon RDS MySQL | rds-wordpress-comercial-nova | Base administrada con backups automáticos |
| Almacenamiento | Amazon S3 | s3-comercial-nova-wordpress-dan | Evidencias y archivos complementarios |
| Balanceo | Application Load Balancer | alb-comercial-nova | Distribución de tráfico HTTP entre EC2 |
| Target Group | ELB Target Group | tg-wordpress-comercial-nova | Health checks y registro de instancias |
| Escalabilidad | Auto Scaling Group | asg-wordpress-comercial-nova | Reemplazo y escalado automático |
| Plantilla | Launch Template | lt-wordpress-comercial-nova | Configuración estandarizada de EC2 |
| Seguridad | Security Groups + IAM | alb / ec2 / rds | Control de acceso por capas |
| Monitoreo | Amazon CloudWatch | dashboard-comercial-nova | Métricas y alarmas operacionales |

## 3. Diseño de red

La VPC `vpc-comercial-nova` (10.0.0.0/16) distribuye recursos en dos zonas de disponibilidad:

| Subred | CIDR | AZ | Uso |
|---|---|---|---|
| subnet-publica-1a | 10.0.1.0/24 | us-east-1a | ALB, EC2 WordPress |
| subnet-publica-1b | 10.0.2.0/24 | us-east-1b | ALB, EC2 WordPress |
| subnet-privada-1a | 10.0.11.0/24 | us-east-1a | RDS (DB Subnet Group) |
| subnet-privada-1b | 10.0.12.0/24 | us-east-1b | RDS (DB Subnet Group) |

- **Tabla de rutas pública** (`rtb-publica-comercial-nova`): ruta 0.0.0.0/0 → Internet Gateway.
- **Tabla de rutas privada** (`rtb-privada-comercial-nova`): solo ruta local (10.0.0.0/16).

Esta segmentación impide que RDS sea accesible directamente desde Internet.

**Evidencias:** `evidencias/capturas_servicios/01_vpc_creada.png` — `06_tabla_rutas_privada.png`

## 4. Capa de aplicación

WordPress se ejecuta sobre instancias EC2 con Amazon Linux 2023, Apache, PHP y los módulos necesarios.

| Decisión | Justificación |
|---|---|
| Dos instancias EC2 | Alta disponibilidad y distribución de carga |
| Distribución en 1a y 1b | Tolerancia a fallo de una zona de disponibilidad |
| t2.micro | Rightsizing para control de costos en laboratorio |
| Mismo RDS para ambas EC2 | Contenido unificado sin replicación manual |

**Evidencias:** `evidencias/capturas_servicios/17_ec2_wordpress_1a_running.png`, `23_ec2_wordpress_1b_running.png`

## 5. Base de datos

| Decisión | Justificación |
|---|---|
| Amazon RDS MySQL | Administración automática, parches y backups |
| db.t3.micro | Instancia mínima funcional para WordPress |
| Single-AZ | Control de costos en AWS Academy |
| Backups 7 días | Recuperación ante fallos sin costo excesivo de almacenamiento |
| Acceso no público | Seguridad: solo EC2 puede conectarse vía Security Group |
| DB Subnet Group en subredes privadas | Cumple buenas prácticas de aislamiento |

**Evidencias:** `evidencias/capturas_servicios/10_db_subnet_group_rds.png` — `12_rds_disponible.png`

## 6. Almacenamiento persistente

Amazon S3 almacena evidencias del proyecto y archivos complementarios.

| Decisión | Justificación |
|---|---|
| Bloqueo de acceso público | Previene exposición accidental de datos |
| Versionado activado | Permite recuperar versiones anteriores de archivos |
| Región us-east-1 | Coherencia con el resto de la infraestructura |

**Evidencias:** `evidencias/capturas_servicios/13_s3_bucket_creado.png` — `16_s3_archivo_prueba_cargado.png`

## 7. Balanceo de carga y escalabilidad

| Decisión | Justificación |
|---|---|
| Application Load Balancer | Punto de entrada único; health checks automáticos |
| Target Group HTTP:80 | Protocolo acorde a la configuración actual (sin HTTPS) |
| Auto Scaling (1–3 instancias) | Resiliencia ante fallos y escalado por CPU |
| Política CPU 70 % | Alineada con la alarma de CloudWatch |
| Launch Template | Garantiza que nuevas instancias tengan la misma configuración |

**URL del ALB:** http://alb-comercial-nova-1298470052.us-east-1.elb.amazonaws.com

**Evidencias:** `evidencias/capturas_servicios/28_target_group_wordpress_creado.png` — `31_wordpress_desde_load_balancer.png`, `35_launch_template_wordpress.png` — `38_auto_scaling_target_group.png`

## 8. Seguridad

La arquitectura aplica el principio de mínimo privilegio con Security Groups por capa:

- ALB: HTTP/80 desde Internet (sin HTTPS/443).
- EC2: HTTP/80 desde ALB en configuración final; acceso directo temporal en pruebas; SSH restringido a IP del estudiante.
- RDS: MySQL solo desde EC2.
- S3: Sin acceso público.

Detalle: [seguridad/matriz_accesos.md](../seguridad/matriz_accesos.md)

## 9. Monitoreo

| Recurso | Función |
|---|---|
| dashboard-comercial-nova | Visualización de CPU EC2/RDS y almacenamiento |
| alarma-cpu-ec2-wordpress | Alerta cuando CPU EC2 supera 70 % |

Detalle: [monitoreo/alertas_configuradas.md](../monitoreo/alertas_configuradas.md)

## 10. Costos

La arquitectura prioriza instancias pequeñas (t2.micro, db.t3.micro), Single-AZ en RDS y Auto Scaling con mínimo de 1 instancia para equilibrar funcionalidad y costo.

Estimación referencial: ~50–70 USD/mes. Cost Explorer en AWS Academy: 0,00 USD.

Detalle: [costos/optimizacion_costos.md](../costos/optimizacion_costos.md)

## 11. Limitaciones del entorno AWS Academy

| Limitación | Impacto | Decisión tomada |
|---|---|---|
| Cost Explorer muestra 0,00 USD | No permite validar costos reales | Estimación referencial manual |
| Presupuesto y cuotas limitados | Restringe tipos de instancia y servicios | Rightsizing a t2.micro / db.t3.micro |
| Duración de sesión finita | Recursos deben crearse y documentarse en tiempo limitado | Priorizar servicios obligatorios del curso |
| Single-AZ obligatorio por costos | Menor disponibilidad de BD | Documentado como trade-off consciente |
| Sin HTTPS en laboratorio | Tráfico HTTP sin cifrar | Mejora futura con ACM |

## 12. Lecciones aprendidas

- Diseñar la red (VPC, subredes, rutas) como primer paso evita reconfiguraciones posteriores.
- Centralizar la base de datos en RDS simplifica el despliegue multi-instancia de WordPress.
- El ALB demuestra alta disponibilidad de forma tangible al distribuir tráfico entre zonas.
- Auto Scaling requiere una Launch Template idéntica a las instancias manuales para mantener coherencia.
- Documentar limitaciones del laboratorio demuestra comprensión del entorno, no solo de la tecnología.

## 13. Mejoras futuras

- HTTPS con AWS Certificate Manager.
- RDS Multi-AZ para mayor disponibilidad.
- Amazon EFS para archivos WordPress compartidos entre instancias.
- AWS WAF para protección de aplicaciones web.
- Infraestructura como código (Terraform / CloudFormation).
- Políticas de ciclo de vida en S3.
