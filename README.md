# go

para agregar varios proyectos de go en visual estudio dentro de una misma carpeta, utilizar:
go work init

para agregar las carpetas a usar que contendran un archivo main:
go work use -r */

para compilar un archivo go se usa:
go build nombre_archivo.go
para ejecutar el archivo basta con:
./nombre_archivo

para ejecutar sin compilar:
go run nombre_archivo.go

Para generar un archivo go.mod:
go mod init ruta/proyectos

para ejecutar desde terminal docker y actualizar nuestra imagen:
posicionarnos en donde se encuentre nuentro archivo docker-compose.yml y ejecutar:

docker compose up -d

Para ejecutar makefile:
make start # este inicializa el front end
make stop # detiene el front end
make up_build # construye los proyectos para docker 


Archivo makefile para windows:

SHELL=cmd.exe
FRONT_END_BINARY=frontApp.exe
BROKER_BINARY=brokerApp

## up: starts all containers in the background without forcing build
up:
    @echo Starting Docker images...
    docker-compose up -d
    @echo Docker images started!

## up_build: stops docker-compose (if running), builds all projects and starts docker compose
up_build: build_broker
    @echo Stopping docker images (if running...)
    docker-compose down
    @echo Building (when required) and starting docker images...
    docker-compose up --build -d
    @echo Docker images built and started!

## down: stop docker compose
down:
    @echo Stopping docker compose...
    docker-compose down
    @echo Done!

## build_broker: builds the broker binary as a linux executable
build_broker:
    @echo Building broker binary...
    chdir ..\broker-service && set GOOS=linux&& set GOARCH=amd64&& set CGO_ENABLED=0 && go build -o ${BROKER_BINARY} ./cmd/api
    @echo Done!

## build_front: builds the frone end binary
build_front:
    @echo Building front end binary...
    chdir ..\front-end && set CGO_ENABLED=0&& set GOOS=windows&& go build -o ${FRONT_END_BINARY} ./cmd/web
    @echo Done!

## start: starts the front end
start: build_front
    @echo Starting front end
    chdir ..\front-end && start /B ${FRONT_END_BINARY} &

## stop: stop the front end
stop:
    @echo Stopping front end...
    @taskkill /IM "${FRONT_END_BINARY}" /F
    @echo "Stopped front end!"


docker-compose.yml para postgres:
postgres:
    image: 'postgres:14.0'
    ports:
      - "5432:5432"
    deploy:
      mode: replicated
      replicas: 1
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
      POSTGRES_DB: users
    volumes:
      - ./db-data/postgres/:/var/lib/postgresql/data/



postgres:
    image: 'postgres:14.2'
    ports:
      - "5432:5432"
    deploy:
      mode: replicated
      replicas: 1
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
      POSTGRES_DB: users
    volumes:
      - ./db-data/postgres/:/var/lib/postgresql/data/


librerías para mongo
go get go.mongodb.org/mongo-driver/mongo
go get go.mongodb.org/mongo-driver/mongo/options
