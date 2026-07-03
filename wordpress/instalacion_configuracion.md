# Instalación y Configuración de WordPress

## 1. Objetivo

Documentar el proceso de instalación y configuración de WordPress sobre instancias EC2 en AWS, utilizando una base de datos administrada en Amazon RDS.

## 2. Componentes utilizados

| Componente | Servicio / Tecnología | Uso |
|---|---|---|
| Sistema operativo | Amazon Linux / Ubuntu Server | Base del servidor |
| Servidor web | Apache o Nginx | Publicación del sitio WordPress |
| PHP | PHP y extensiones necesarias | Ejecución de WordPress |
| Base de datos | Amazon RDS MySQL/MariaDB | Almacenamiento de datos del sitio |
| Almacenamiento adicional | Amazon S3 | Medios, respaldos o logs |
| Seguridad | Security Groups | Control de tráfico permitido |

## 3. Preparación de la instancia EC2

Pasos generales:

1. Crear una instancia EC2 en una subred pública.
2. Asociar un Security Group que permita HTTP y SSH restringido.
3. Conectarse por SSH o Session Manager.
4. Actualizar paquetes del sistema.
5. Instalar servidor web, PHP y dependencias.
6. Descargar WordPress.
7. Configurar conexión hacia RDS.
8. Completar instalación desde navegador.

## 4. Comandos base de instalación

> Los comandos pueden variar según el sistema operativo usado en EC2.

### Amazon Linux

```bash
sudo dnf update -y
sudo dnf install -y httpd php php-mysqli php-json php-gd php-mbstring wget unzip
sudo systemctl enable httpd
sudo systemctl start httpd