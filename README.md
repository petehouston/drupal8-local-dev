# Drupal 8 - Quick local development setup

Get Drupal 8 local development up and running without manual and complicated setup on dev machine.

## Specifications

**Images & Tools**
- [nginx latest](https://hub.docker.com/_/nginx)
- [PHP 7.4.29](https://hub.docker.com/layers/php/library/php/7.4.29-fpm/images/sha256-6e47401ba6138ab9b71e3612e1ac6855dcbe471f61981c841d601420cf941601?context=explore)
- [composer v1.10.26](https://getcomposer.org/download/1.10.26/composer.phar)
- [MariaDB latest](https://hub.docker.com/_/mariadb)

**Drupal 8**

```
"drupal/core": "^8.9.20",
"drupal/devel": "^4.1",
"drupal/material_admin": "^2.0",
"drush/drush": "^9.7.0",
```

## Usage

First clone and navigate to project

```
$ git clone git@github.com:petehouston/drupal8-local-dev.git
$ cd drupal8-local-dev
```

Navigate to `docker` directory and start using `docker-compose`:

```
$ cd docker
$ docker-compose up --build
```

*FYI: first time running will take quite a time for image pulls and builds.* 


Confirm PHP container name:

```
$ docker-compose ps
      Name                     Command               State           Ports
-----------------------------------------------------------------------------------
docker_database_1   docker-entrypoint.sh mariadbd    Up      0.0.0.0:3306->3306/tcp
docker_web_1        /docker-entrypoint.sh ngin ...   Up      0.0.0.0:8080->80/tcp
php                 docker-php-entrypoint php-fpm    Up      0.0.0.0:9000->9000/tcp
```

By default, its name is `php`, let's get a shell access into the container. 

```
$ docker exec -it php sh
```

Install PHP/Drupal dependencies from the shell prompt:

```
$ COMPOSER_MEMORY_LIMIT=-1 composer install
```

The env `COMPOSER_MEMORY_LIMIT=-1` set is to avoid memory limit during installation.

Open this file `src/web/sites/default/settings.php`, append this code block

```
/**
 * Database config
 */
$databases['default']['default'] = array (
  'database' => 'drupal8',
  'username' => 'docker',
  'password' => 'docker',
  'prefix' => '',
  'host' => 'database',
  'port' => '3306',
  'namespace' => 'Drupal\\Core\\Database\\Driver\\mysql',
  'driver' => 'mysql',
);
```

**FYI: you can also add into `default.settings.php` or `settings.local.php`, just to make sure they are included properly.**

It's done, launch Drupal in web browser at `http://localhost:8080`.

## Misc.

### 1. Create admin user

Access into PHP container shell prompt:

```
$ pwd
/var/www/html
$ ./vendor/bin/drush user-create YOUR_USERNAME --mail="YOUR_MAIL" --password="YOUR_PASS"
$ ./vendor/bin/drush user-add-role "administrator" YOUR_USERNAME
```

Replace `YOUR_USERNAME` with your username, `YOUR_MAIL` with your email, and `YOUR_PASS` with your password

Then you can login at `http://localhost:8080/user/login`.

### 2. Enable `devel` package

It is already packed into `composer.json`, you need to enable it via admin dashboard.

Navigate to `http://localhost:8080/admin/modules`, locate `Devel` package and click on Install button.

### 3. Change default MariaDB credentials

You can do that by changing the value in `docker/docker-compose.yml`:

```
  database:
    image: mariadb:latest
    environment:
      - MYSQL_ROOT_PASSWORD=docker
      - MYSQL_USER=docker
      - MYSQL_PASSWORD=docker
      - MYSQL_DATABASE=drupal8
```

Replace environment values with your values.

**Important: if you do this, make sure to change Drupal database settings as well (ex. `settings.php`, `default.settings.php`...).**