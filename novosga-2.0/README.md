# NovoSGA 2.0

## Docker image milestones
[x] Pre-flight check  
[x] Auto setup  
[ ] docker secrets support

## How to use

The container will check if the required settings are set and will start the database setup automatically.  
You can set the system as you like with environment variables (using docker-compose.yml or `-e` flags at `docker container run`)

### Settings (environment variables)

| Option                         | Default setting   | Description                      | Optional? |
| ------------------------------ | ----------------- | -------------------------------- | :-------: |
| APP_ENV                        | prod              | Environment running the app      | yes       |
| DATABASE_URL                   | *blank*             | Database connection string       | ***no***      |
| DATABASE_PASS                  | *blank*             | Database password                | ***no***      |
| NOVOSGA_ADMIN_USERNAME         | admin             | Admin username                   | yes       |
| NOVOSGA_ADMIN_PASSWORD         | 123456            | Admin password                   | yes       |
| NOVOSGA_ADMIN_FIRSTNAME        | Administrator     | Administrator first name         | yes       |
| NOVOSGA_ADMIN_LASTNAME         | Global            | Administrador last name          | yes       |
| NOVOSGA_UNITY_NAME             | My Unity          | Unity name                       | yes       |
| NOVOSGA_UNITY_CODE             | U01               | Unity code                       | yes       |
| NOVOSGA_NOPRIORITY_DESCRIPTION | Normal service    | Non-Priority service description | yes       |
| NOVOSGA_PRIORITY_NAME          | Priority          | Priority service name            | yes       |
| NOVOSGA_PRIORITY_DESCRIPTION   | Priority service  | Priority service description     | yes       |
| NOVOSGA_PLACE_NAME             | Box               | Place name                       | yes       |
| TZ                             | UTC               | Timezone                         | yes       |

### CLI

Minimal setting:

```shell
 docker container run [--rm|-d] \
  -e DATABASE_URL="connection_string" \
  -e DATABASE_PASS="database_password" \
  novosga/novosga:latest
```

### Compose file

```yaml
version: '2'

services:
  novosga:
    image: novosga/novosga:latest
    restart: always
    depends_on:
      - mysqldb
    ports:
      - "80:80"
    environment:
      APP_ENV: 'prod'
      # database connection
      DATABASE_URL: 'mysql://novosga@mysqldb:3306/novosga2?charset=utf8mb4&serverVersion=5.7'
      DATABASE_PASS: 'MySQL_App_P@ssW0rd!'
      # default admin user
      NOVOSGA_ADMIN_USERNAME: 'admin'
      NOVOSGA_ADMIN_PASSWORD: '123456'
      NOVOSGA_ADMIN_FIRSTNAME: 'Administrator'
      NOVOSGA_ADMIN_LASTNAME: 'Global'
      # default unity
      NOVOSGA_UNITY_NAME: 'My Unity'
      NOVOSGA_UNITY_CODE: 'U01'
      # default no-priority
      NOVOSGA_NOPRIORITY_NAME: 'Normal'
      NOVOSGA_NOPRIORITY_DESCRIPTION: 'Normal service'
      # default priority
      NOVOSGA_PRIORITY_NAME: 'Priority'
      NOVOSGA_PRIORITY_DESCRIPTION: 'Priority service'
      # default place
      NOVOSGA_PLACE_NAME: 'Box'
      # Set TimeZone
      TZ: 'America/Sao_Paulo'
  mysqldb:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_USER: 'novosga'
      MYSQL_DATABASE: 'novosga2'
      MYSQL_ROOT_PASSWORD: 'MySQL_r00t_P@ssW0rd!'
      MYSQL_PASSWORD: 'MySQL_App_P@ssW0rd!'
```