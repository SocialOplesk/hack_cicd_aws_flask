# SOCIAL OPLESK
### 🏴‍☠️ HACK 
<br/>

## ⚡️ FLASK + CI/CD + AWS⚡️

📚 docs [comandos git 1](https://gist.github.com/dasdo/9ff71c5c0efa037441b6) | [comandos git 2](https://github.com/joshnh/Git-Commands/blob/master/READMEes.md) | [comandos git 3](https://confluence.atlassian.com/bitbucketserver/basic-git-commands-776639767.html) | [config cuenta](https://git-scm.com/book/es/v2/Inicio---Sobre-el-Control-de-Versiones-Configurando-Git-por-primera-vez) 
---

```diff
- NOTA HACER LA PRÁCTICA MEDIANTE LA CONSOLA DE LINUX
```
|Hacks | Details | 
|----------|---------|
| H-1      | CONECTAR CI/CD INTEGRADO CON EC2 DE AWS PARA UN SERVER EN FLASK |
<br/> 

## 🏆 H-1 (CI/CD + AWS)

1. Crear un repositorio en github, con el nombre: "cicd_aws_flask"
   con su archivo README.md

2. Crear y anexar el archivo: "app.py" en el repositorio con el siguiente código:
```
from flask import Flask, jsonify
from flask_cors import CORS

app = Flask(__name__)
CORS(app)

@app.route('/api/hello', methods=['GET'])
def hello():
    return jsonify({"message": "Hello, World!"})

@app.route('/api/data', methods=['GET'])
def data():
    return jsonify({"data": [1, 2, 3, 4, 5]})

if __name__ == '__main__':
    app.run(debug=True)
```

3. Crear el archivo requirements.txt en el root del proyecto con el comando: freeze
   luego subirlo al repositorio.
```
pip freeze > requirements.txt
```

4. Ir a la consola de aws y crear un servidor ec2 con ubuntu:

5. Ingresar a la instancia de ubuntu anteriormente creada y hacer lo siguiente: 
```
sudo apt update
sudo apt install python3
sudo apt install python3-pip
sudo apt install python3.12-venv
```

6. El siguiente paso es crear la credencial de acceso rsa para el github actions,
   abajo el código y adicional guarda la key.
```
ssh-keygen -t rsa -b 4096 -C "nombre de tú proyecto"
```

7. Descargar el repositorio dentro de tú instancia puede ser en la ruta /opt
   además otogar permiso al usuario ubuntu al directorio del proyecto:
```
git clone ruta-proyecto
sudo chown -R ubuntu:ubuntu /nombre_del_directorio_de_tu_proyecto
```

8. Dentro del directorio del proyecto crear un entorno virtual y descargar las dependencias:
```
python3 -m venv venv
source venv/bin/actívate
sudo venv/bin/python3 -m pip install -r requirements.txt
```

9. Crear un servicio para la ejecución del servidor en la ruta: /etc/systemd/system/nombre-del-servicio.service | con nano en modo super usuario 
```
[Unit]
Description=Nombre de tú Servicio Aquí
After=network.target

[Service]
User=ubuntu
WorkingDirectory=/opt/nombre_del_directorio_de_tu_proyecto
Environment="PATH=/opt/nombre_del_directorio_de_tu_proyecto/venv/bin:/usr/bin"
ExecStart=/opt/nombre_del_directorio_de_tu_proyecto/venv/bin/python3 /opt/nombre_del_directorio_de_tu_proyecto/app.py
Restart=always

[Install]
WantedBy=multi-user.target
```

10. Recargar los servicios para que linux reconozca el nuevo servicio antes creado:
```
sudo systemctl daemon-reload
sudo systemctl start my-flask-app.service 
```

11. ir a github y crear las credenciales:
    - EC2_PRIVATE_KEY = pega la key generada del rsa
    - EC2_HOST = dirección pública de la instancia de ec2
    - EC2_USER_NAME = al ser ubuntu el usuario se llama igual: ubuntu

12. Ir a github actions y crear un workflow con el siguiente código:
```
name: Deploy to EC2

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Deploy to EC2
      env:
        PRIVATE_KEY: ${{ secrets.EC2_PRIVATE_KEY }}
        HOST_NAME: ${{ secrets.EC2_HOST }}
        USER_NAME: ${{ secrets.EC2_USER_NAME }}
      run: |
        echo "$PRIVATE_KEY" > private_key && chmod 600 private_key
        ssh -v -o StrictHostKeyChecking=no -i private_key ${USER_NAME}@${HOST_NAME} '
          cd /opt/nombre_del_directorio_de_tu_proyecto &&
          git pull origin main &&
          source venv/bin/activate &&
          sudo venv/bin/python3 -m pip install -r requirements.txt &&
          sudo systemctl restart my-flask-app
        '
```

13. Crea un nuevo endpoint llamado: /api/foo el cuál retorna un "hello foo", abajo el código:
```
@app.route('/api/foo', methods=['GET'])
def foo():
    return jsonify({"message": "Hello, Foo"})
```

14. Verificar si el endpoint fue anexado en el ec2 y que se puede consumir, si todo sale bien, es un Éxito 🏆 

---
<h3 align="center">SOCIAL OPLESK</h3>
