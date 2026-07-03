# Evidencias de EC2, S3, RDS y CloudWatch

## 1. Objetivo

Documentar con evidencias visuales la implementación de los servicios de cómputo, almacenamiento, base de datos y monitoreo para el proyecto WordPress en AWS — Comercial Nova.

## 2. Amazon RDS (MySQL)

### Configuración implementada

| Parámetro | Valor |
|---|---|
| Identificador | rds-wordpress-comercial-nova |
| Motor | MySQL Community |
| Clase de instancia | db.t3.micro |
| Base de datos | wordpressdb |
| Usuario | adminwp |
| Zona de disponibilidad | us-east-1b |
| Acceso público | No |
| Backups automáticos | Habilitado (7 días) |
| DB Subnet Group | db-subnet-wordpress-comercial-nova |
| Endpoint | rds-wordpress-comercial-nova.cuh9d2ijmqfs.us-east-1.rds.amazonaws.com |

### Evidencias

| Captura | Descripción |
|---|---|
| ![DB Subnet Group](../evidencias/capturas_servicios/10_db_subnet_group_rds.png) | Grupo de subredes en VPC privada |
| ![RDS Backup](../evidencias/capturas_servicios/11_rds_backup_configurado.png) | Configuración de backups automáticos |
| ![RDS Disponible](../evidencias/capturas_servicios/12_rds_disponible.png) | Instancia RDS en estado Disponible |

## 3. Amazon S3

### Configuración implementada

| Parámetro | Valor |
|---|---|
| Nombre del bucket | s3-comercial-nova-wordpress-dan |
| Región | us-east-1 |
| Bloqueo de acceso público | Activado |
| Versionado | Activado |
| Archivo de prueba | Cargado |

### Evidencias

| Captura | Descripción |
|---|---|
| ![S3 Bucket](../evidencias/capturas_servicios/13_s3_bucket_creado.png) | Bucket creado correctamente |
| ![S3 Bloqueo público](../evidencias/capturas_servicios/14_s3_bloqueo_publico.png) | Bloqueo de acceso público activado |
| ![S3 Versionado](../evidencias/capturas_servicios/15_s3_versionado_activado.png) | Versionado habilitado |
| ![S3 Archivo prueba](../evidencias/capturas_servicios/16_s3_archivo_prueba_cargado.png) | Archivo de prueba cargado |

## 4. Amazon EC2

### Configuración implementada

| Parámetro | ec2-wordpress-1a | ec2-wordpress-1b |
|---|---|---|
| ID de instancia | i-056e8e4ef10e4173d | i-0d1322dbfcfaef7fc |
| Tipo | t2.micro | t2.micro |
| SO | Amazon Linux 2023 | Amazon Linux 2023 |
| Zona | us-east-1a | us-east-1b |
| Subred | subnet-publica-1a | subnet-publica-1b |
| Estado | running (2/2 checks) | running (2/2 checks) |
| Software | Apache, PHP, WordPress | Apache, PHP, WordPress |
| Security Group | ec2-wordpress-comercial-nova | ec2-wordpress-comercial-nova |

### Evidencias

| Captura | Descripción |
|---|---|
| ![EC2 1a](../evidencias/capturas_servicios/17_ec2_wordpress_1a_running.png) | Instancia ec2-wordpress-1a en ejecución |
| ![EC2 1b](../evidencias/capturas_servicios/23_ec2_wordpress_1b_running.png) | Instancia ec2-wordpress-1b en ejecución |
| ![WordPress BD](../evidencias/capturas_servicios/18_wordpress_pantalla_configuracion_bd.png) | Pantalla de configuración de base de datos |
| ![Instalación](../evidencias/capturas_servicios/19_wordpress_instalacion_completada.png) | Instalación de WordPress completada |
| ![Panel admin](../evidencias/capturas_servicios/20_wordpress_panel_admin.png) | Panel de administración funcionando |
| ![EC2 1b inicial](../evidencias/capturas_servicios/24_wordpress_ec2_1b_pantalla_inicial.png) | Segunda instancia conectada al mismo RDS |

## 5. Application Load Balancer y Target Group

| Parámetro | Valor |
|---|---|
| ALB | alb-comercial-nova |
| DNS | alb-comercial-nova-1298470052.us-east-1.elb.amazonaws.com |
| Target Group | tg-wordpress-comercial-nova |
| Protocolo | HTTP:80 |
| Targets | 2 healthy |

### Evidencias

| Captura | Descripción |
|---|---|
| ![Target Group](../evidencias/capturas_servicios/28_target_group_wordpress_creado.png) | Target Group con 2 instancias registradas |
| ![ALB](../evidencias/capturas_servicios/29_application_load_balancer_creado.png) | Application Load Balancer activo |
| ![Targets healthy](../evidencias/capturas_servicios/30_target_group_targets_healthy.png) | Ambos targets en estado healthy |
| ![WordPress ALB](../evidencias/capturas_servicios/31_wordpress_desde_load_balancer.png) | Sitio accesible vía Load Balancer |

## 6. Auto Scaling

| Parámetro | Valor |
|---|---|
| Launch Template | lt-wordpress-comercial-nova (lt-059d92a19e60e2cba) |
| Auto Scaling Group | asg-wordpress-comercial-nova |
| Capacidad deseada | 2 |
| Mínimo | 1 |
| Máximo | 3 |
| Política | CPU promedio objetivo 70 % |
| Target Group asociado | tg-wordpress-comercial-nova |

### Evidencias

| Captura | Descripción |
|---|---|
| ![Launch Template](../evidencias/capturas_servicios/35_launch_template_wordpress.png) | Plantilla de lanzamiento configurada |
| ![ASG creado](../evidencias/capturas_servicios/36_auto_scaling_group_creado.png) | Auto Scaling Group creado |
| ![ASG instancias](../evidencias/capturas_servicios/37_auto_scaling_instancias.png) | 2/2 instancias en buen estado |
| ![ASG Target Group](../evidencias/capturas_servicios/38_auto_scaling_target_group.png) | Integración con Target Group |

## 7. Amazon CloudWatch

| Parámetro | Valor |
|---|---|
| Dashboard | dashboard-comercial-nova |
| Métricas EC2 | CPUUtilization (i-056e8e4ef10e4173d, i-0d1322dbfcfaef7fc) |
| Métricas RDS | CPUUtilization, DatabaseConnections, FreeStorageSpace |
| Alarma | alarma-cpu-ec2-wordpress |
| Umbral | CPU > 70 % |

### Evidencias

| Captura | Descripción |
|---|---|
| ![Dashboard](../evidencias/capturas_servicios/32_cloudwatch_dashboard_metricas.png) | Dashboard con métricas EC2 y RDS |
| ![Alarma](../evidencias/capturas_servicios/33_cloudwatch_alarma_cpu_ec2.png) | Alarma de CPU creada |

## 8. Limitaciones AWS Academy

- Cost Explorer muestra 0,00 USD; no refleja costos reales del laboratorio.
- RDS en Single-AZ por control de costos en AWS Academy.

## 9. Lecciones aprendidas

- Verificar que ambas instancias EC2 apunten al mismo endpoint RDS antes de completar la instalación de WordPress.
- El health check del Target Group confirma que la capa web responde correctamente antes de asociar el ALB.
- CloudWatch permite correlacionar métricas de EC2 y RDS en un solo dashboard para diagnóstico rápido.
- Auto Scaling requiere que la Launch Template incluya la misma configuración de seguridad y AMI que las instancias originales.
