# Inventario de Recursos AWS

## 1. Datos generales

| Campo | Valor |
|---|---|
| Proyecto | Implementación de WordPress en AWS — Comercial Nova |
| Plataforma | AWS Academy (Vocareum) |
| Región AWS | us-east-1 (Norte de Virginia) |
| Cuenta / Laboratorio | 529701299932 |
| Fecha de implementación | 2–3 de julio de 2026 |
| Responsable | Equipo Comercial Nova |

## 2. Recursos de red

| Recurso | Nombre | ID | Zona / Región | Estado | Observación |
|---|---|---|---|---|---|
| VPC | vpc-comercial-nova | vpc-07c24d855da60ed3c | us-east-1 | Available | CIDR 10.0.0.0/16 |
| Subred pública 1 | subnet-publica-1a | subnet-0fe30e8a38c9e22e6 | us-east-1a | Available | CIDR 10.0.1.0/24 |
| Subred pública 2 | subnet-publica-1b | subnet-077258b4482861f6d | us-east-1b | Available | CIDR 10.0.2.0/24 |
| Subred privada 1 | subnet-privada-1a | subnet-07db14ee636ebe0fe | us-east-1a | Available | CIDR 10.0.11.0/24 |
| Subred privada 2 | subnet-privada-1b | subnet-016124bcd51f3f847 | us-east-1b | Available | CIDR 10.0.12.0/24 |
| Internet Gateway | igw-comercial-nova | igw-0201cc01ccef30968 | us-east-1 | Attached | Asociado a vpc-comercial-nova |
| Tabla de rutas pública | rtb-publica-comercial-nova | rtb-0eccd3b0b8af4153d | us-east-1 | Activa | Ruta 0.0.0.0/0 → IGW; 2 subredes asociadas |
| Tabla de rutas privada | rtb-privada-comercial-nova | rtb-0a56c1a17b4b6b8fa | us-east-1 | Activa | Solo ruta local; 2 subredes asociadas |

**Evidencias:** `evidencias/capturas_servicios/01_vpc_creada.png` — `06_tabla_rutas_privada.png`

## 3. Recursos de cómputo

| Recurso | Nombre | ID | Tipo | Zona | Estado | Observación |
|---|---|---|---|---|---|---|
| EC2 WordPress 1 | ec2-wordpress-1a | i-056e8e4ef10e4173d | t2.micro | us-east-1a | running | Amazon Linux 2023; IP pública 98.81.194.44 |
| EC2 WordPress 2 | ec2-wordpress-1b | i-0d1322dbfcfaef7fc | t2.micro | us-east-1b | running | Amazon Linux 2023; IP pública 3.237.91.36 |
| Launch Template | lt-wordpress-comercial-nova | lt-059d92a19e60e2cba | t2.micro | us-east-1 | Activo | AMI ami-06067086cf86c58e6; versión 1 |
| Auto Scaling Group | asg-wordpress-comercial-nova | Pendiente de completar | t2.micro | us-east-1a, us-east-1b | En capacidad deseada | Desired 2, Min 1, Max 3 |

**Evidencias:** `evidencias/capturas_servicios/17_ec2_wordpress_1a_running.png`, `23_ec2_wordpress_1b_running.png`, `35_launch_template_wordpress.png`, `36_auto_scaling_group_creado.png`, `37_auto_scaling_instancias.png`

## 4. Recursos de base de datos

| Recurso | Nombre | Motor | ID / Endpoint | Estado | Backup | Observación |
|---|---|---|---|---|---|---|
| RDS WordPress | rds-wordpress-comercial-nova | MySQL Community | rds-wordpress-comercial-nova.cuh9d2ijmqfs.us-east-1.rds.amazonaws.com | Disponible | 7 días (habilitado) | db.t3.micro; Single-AZ; acceso público: No |
| DB Subnet Group | db-subnet-wordpress-comercial-nova | — | — | Completado | — | Subredes privadas en vpc-comercial-nova |
| Base de datos | wordpressdb | MySQL | — | Activa | — | Usuario: adminwp |

**Evidencias:** `evidencias/capturas_servicios/10_db_subnet_group_rds.png` — `12_rds_disponible.png`

## 5. Recursos de almacenamiento

| Recurso | Nombre | Región | Versionado | Acceso público | Observación |
|---|---|---|---|---|---|
| S3 Bucket | s3-comercial-nova-wordpress-dan | us-east-1 | Activado | Bloqueado | Archivo de prueba cargado |

**Evidencias:** `evidencias/capturas_servicios/13_s3_bucket_creado.png` — `16_s3_archivo_prueba_cargado.png`

## 6. Recursos de balanceo y escalabilidad

| Recurso | Nombre | Estado | Configuración | Observación |
|---|---|---|---|---|
| Application Load Balancer | alb-comercial-nova | Activo | Internet-facing; HTTP:80 | DNS: alb-comercial-nova-1298470052.us-east-1.elb.amazonaws.com |
| Target Group | tg-wordpress-comercial-nova | Activo | HTTP:80; 2 targets healthy | Asociado al ALB y ASG |
| Auto Scaling Group | asg-wordpress-comercial-nova | En capacidad deseada | Desired 2, Min 1, Max 3 | Política CPU objetivo 70 % |
| Launch Template | lt-wordpress-comercial-nova | Activo | Versión 1 (default) | Key pair: key-comercial-nova |

**Evidencias:** `evidencias/capturas_servicios/28_target_group_wordpress_creado.png` — `31_wordpress_desde_load_balancer.png`, `35_launch_template_wordpress.png` — `38_auto_scaling_target_group.png`

## 7. Seguridad

| Recurso | Nombre | Uso | Reglas principales | Observación |
|---|---|---|---|---|
| Security Group ALB | alb-comercial-nova (sg-036b79e42e104f761) | Acceso HTTP | TCP 80 desde 0.0.0.0/0 | Punto de entrada público |
| Security Group EC2 | ec2-wordpress-comercial-nova (sg-0f9eb9a84f8b7a3e6) | Acceso web/SSH | TCP 80 desde SG-ALB (final); TCP 80 desde 0.0.0.0/0 (pruebas); TCP 22 desde IP estudiante | Ver matriz de accesos |
| Security Group RDS | rds-wordpress-comercial-nova (sg-03e7f7f02380b2d72) | Acceso MySQL | TCP 3306 desde SG-EC2 | Solo desde capa de aplicación |
| Rol IAM EC2 | Pendiente de completar | Permisos mínimos | Pendiente de completar | Laboratorio AWS Academy |

**Evidencias:** `evidencias/capturas_servicios/07_sg_alb_creado.png` — `09_sg_rds_wordpress_creado.png`

## 8. Monitoreo

| Recurso | Nombre | Métricas | Estado | Observación |
|---|---|---|---|---|
| CloudWatch Dashboard | dashboard-comercial-nova | CPUUtilization EC2 y RDS; DatabaseConnections; FreeStorageSpace | Activo | 2 instancias EC2 monitoreadas |
| CloudWatch Alarm | alarma-cpu-ec2-wordpress | CPUUtilization > 70 % | Creada | SNS pendiente de confirmación |

**Evidencias:** `evidencias/capturas_servicios/32_cloudwatch_dashboard_metricas.png`, `33_cloudwatch_alarma_cpu_ec2.png`

## 9. Costos

| Componente | Costo estimado mensual (referencial) | Observación |
|---|---|---|
| EC2 (2 × t2.micro) | ~16–20 USD | Rightsizing aplicado |
| RDS (db.t3.micro, Single-AZ) | ~15–18 USD | Backups 7 días incluidos |
| S3 | < 1 USD | Uso mínimo en laboratorio |
| Load Balancer | ~16–22 USD | ALB con tráfico bajo |
| CloudWatch | < 3 USD | Dashboard y 1 alarma |
| Transferencia de datos | < 5 USD | Tráfico de laboratorio |
| **Total estimado** | **~50–70 USD** | Cost Explorer Academy: 0,00 USD |

**Evidencia:** `evidencias/capturas_servicios/34_cost_explorer_estimacion.png`

## 10. Limitaciones AWS Academy

- Cost Explorer reporta **0,00 USD** en el entorno académico; los costos de la tabla son estimaciones referenciales.
- RDS se configuró en **Single-AZ** por control de costos y cuotas del laboratorio.
- El laboratorio tiene límites de duración de sesión, presupuesto y servicios disponibles.
- Algunos IDs de recursos dinámicos (ARN de ASG) pueden variar entre sesiones.

## 11. Lecciones aprendidas

- Mantener un inventario actualizado con nombres descriptivos facilita la gestión y la evaluación del proyecto.
- Los IDs de recursos deben extraerse de la consola o capturas; no deben inventarse.
- La nomenclatura consistente (`comercial-nova` como sufijo) permite identificar rápidamente los recursos del proyecto.
