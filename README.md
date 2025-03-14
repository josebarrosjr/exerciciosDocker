# Desafios Docker - PB JAN 2025 DevSecOps

## Execício 1

`docker pull nginx`

`docker run -d -p 8080:80 --name ex-nginx nginx`

Acessar o localhost `http://localhost:8080`

Copiar o index baixado do github

`docker cp index.html ex-facil:/usr/share/nginx/html`

## Exercício 2

`d`ocker run -d --name ex-ubuntu ubuntu`

`docker exec -it ex-ubuntu /bin/bash`

`apt update`

ou

`docker exec -it ex-ubuntu apt update`

## Exercício 3

`docker ps -a`

`docker stop <container_id_ou_nome>`

`docker rm <container_id_ou_nome>`

⚠️Remover todos os containers parados
`docker container prune`

## Exercício 4

Criar um diretório para o exercício
`mkdir flask-app`
`cd flask-app`

Criar um app.py
`nano app.py`

`from flask import Flask`
```
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, Docker!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

`nano requirements.txt`
```
Flask==2.0.1
Werkzeug==2.0.3
```

`nano Dockerfile`
```
# Use uma imagem base do Python
FROM python:3.9-slim
# Defina o diretório de trabalho dentro do contêiner
WORKDIR /app
# Copie os arquivos de requisitos para o diretório de trabalho
COPY requirements.txt .
# Instale as dependências
RUN pip install --no-cache-dir -r requirements.txt
# Copie o restante do código da aplicação
COPY . .
# Exponha a porta que a aplicação vai rodar
EXPOSE 5000
# Comando para rodar a aplicação
CMD ["python", "app.py"]
```

Construir a imagem Docker
`docker build -t flask-app .`

Executar o container
`docker run -p 5000:5000 flask-app`

Testar no navegador
`http://localhost:5000`

## Exercício 5

Instalar PHP, Dependencias, Node.js
`sudo apt update && sudo apt install -y php-cli php-mbstring php-xml php-bcmath php-curl unzip curl git composer php-mysql`
`curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -`
`sudo apt install -y nodejs`

Criar um volume Docker
`docker volume create mysql_data`

`docker run -d --name mysql_container -e MYSQL_ROOT_PASSWORD=senha_root -e MYSQL_DATABASE=laravel -e MYSQL_USER=laravel_user -e MYSQL_PASSWORD=senha_user -v mysql_data:/var/lib/mysql -p 3306:3306 mysql:latest`

Verificar execucao
`docker ps`

Criar o projeto Laravel
`composer create-project laravel/laravel laravel-breeze && cd laravel-breeze`

Configurar o Banco de Dados (.env)
`sudo nano .env`
 
Código:
```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=laravel
DB_PASSWORD=secret
```

Instalar o Laravel Breeze
`composer require laravel/breeze --dev`
`php artisan breeze:install`
`php artisan migrate`
`npm install && npm run dev`

Rodar o Servidor Laravel
`php artisan serve`
`http://127.0.0.1:8000`


## Exercicio 6

`docker pull golang`
`docker pull alpine`

`nano app.go`
```
package main
import (
    "fmt"
)

func main() {
  fmt.Println("Qual é o seu nome:? ")
  var name string
  fmt.Scanln(&name)
  fmt.Printf("Oi, %s! Eu sou a linguagem Go! ", name)
}
```

`nano Dockerfile`
```
FROM golang AS builder

COPY app.go /go/src/app/

ENV GO111MODULE=auto

WORKDIR /go/src/app

RUN go build -o app .

FROM alpine

WORKDIR /appexec

COPY --from=builder /go/src/app/app /appexec/

RUN chmod 755 /appexec/app

ENTRYPOINT ["./app"]
```

`docker image build -t app-go:1.0 .`
`docker run -ti  app-go:1.0`

# Exercicio 7



# Exercicio 8

`nano Dockerfile`
```
FROM python:3.9-slim

WORKDIR /app

# Instala as dependências para o psycopg2
RUN apt-get update && apt-get install -y \
    gcc \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

# Copia o arquivo requirements.txt e instala as dependências
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copia o restante do código da aplicação
COPY . .

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]

```

`nano requirements.txt`
```
Django>=3.2,<4
psycopg2>=2.9,<3
```

`nano settings.py`
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'pollsdb',
        'USER': 'postgres',
        'PASSWORD': 'password',
        'HOST': 'db',
        'PORT': '5432',
    }
}
```

`nano docker-compose.yml`
```
version: '3.8'

services:
  web:
    build: .
    command: ["python", "manage.py", "runserver", "0.0.0.0:8000"]
    volumes:
      - .:/app
    ports:
      - "8000:8000"
    depends_on:
      - db

  db:
    image: postgres:13
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      POSTGRES_DB: pollsdb
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
    ports:
      - "5432:5432"

volumes:
  postgres_data:
```

`docker-compose run web django-admin startproject polls .`

Testar a URL: `http://localhost:8000`

## Exercicio 9

Baixar o index da LandingPage

Navegar ate o diretorio onde está baixado via PowerShell C:\Docker\exercicio9

wsl:

`nano Dockerfile`
```
# Usando a imagem oficial do Nginx
FROM nginx:alpine

# Copiar os arquivos da página estática para o diretório de conteúdo do Nginx
COPY ./landing-page /usr/share/nginx/html

# Expor a porta 80 (padrão do Nginx)
EXPOSE 80

# O Nginx por padrão já serve os arquivos de /usr/share/nginx/html
CMD ["nginx", "-g", "daemon off;"]
```

`docker build -t landing-page-image .`
`docker run -d -p 8080:80 landing-page-image`

Verificar URL no navegador: `http://localhost:8080`
