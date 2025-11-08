# ActividadCloud

# 🐳 Docker Compose - WordPress + MariaDB

Este proyecto va a levantar un servicio de wordpress con mariadb por medio de docker.

---

## 📂 Estructura de archivos

📁 proyecto-wordpress/
├── docker-compose.yml
└── README.md


---

## Iniciar el entorno

1. Asegúrate de tener **Docker Desktop** instalado y corriendo.  
2. Desde la carpeta del proyecto, ejecuta:

```bash
docker compose up -d

```
3. Una vez iniciado ingresa a  Http://Localhost:8082

##Configuracion de DockerCompose
# Servicio de base de datos
```bash
db:
  image: mariadb:11
  container_name: db
  environment:
    - MYSQL_ROOT_PASSWORD=12345
    - MYSQL_DATABASE=wordpress
    - MYSQL_USER=wordpress
    - MYSQL_PASSWORD=wordpress123
  volumes:
    - db_data:/var/lib/mysql
  restart: unless-stopped
```
Explicación:

MYSQL_ROOT_PASSWORD: contraseña del usuario root de MariaDB.

MYSQL_DATABASE: crea automáticamente una base de datos llamada wordpress.

MYSQL_USER y MYSQL_PASSWORD: credenciales que WordPress usará para conectarse.

volumes: guarda los datos de la base de datos en el volumen db_data, para que no se pierdan al reiniciar el contenedor.

🔸 El bloque healthcheck fue desactivado temporalmente para evitar errores de inicialización durante el arranque.

# Servicio de Wordpress
```bash
wordpress:
  image: wordpress:6-apache
  container_name: wp
  ports:
    - "8082:80"
  environment:
    - WORDPRESS_DB_HOST=db:3306
    - WORDPRESS_DB_USER=wordpress
    - WORDPRESS_DB_PASSWORD=wordpress123
    - WORDPRESS_DB_NAME=wordpress
  volumes:
    - wp_data:/var/www/html
  restart: unless-stopped
  healthcheck:
    test: ["CMD", "curl", "-f", "http://localhost/wp-login.php"]
    interval: 30s
    timeout: 10s
    retries: 5
```
Explicación:

WORDPRESS_DB_HOST=db:3306: indica a WordPress que la base de datos está en el contenedor db (nombre del servicio) y usa el puerto 3306.

WORDPRESS_DB_USER, WORDPRESS_DB_PASSWORD, WORDPRESS_DB_NAME: deben coincidir con los valores del servicio db.

ports: expone el puerto 80 del contenedor (Apache) en el 8082 de tu máquina local.

volumes: guarda los archivos de WordPress (temas, plugins, uploads) en wp_data.

healthcheck: verifica cada 30 segundos que la página de login de WordPress responda correctamente.

# Volúmenes persistentes

``` bash
volumes:
  db_data:
  wp_data:
```

Esto garantiza que:

La base de datos (db_data) y los archivos de WordPress (wp_data) no se pierdan al eliminar o reiniciar los contenedores.

# Comandos Utiles
```bash
docker compose down #Detener los contenedores:
docker compose down -v #Detener y eliminar volúmenes (¡borra la base de datos!):
docker compose logs -f #Ver logs:
docker compose restart wordpress #Reiniciar solo WordPress:
```
