# Probando las soluciones de contenedores

1. Crear el resource Group
```bash
   az group create --name rg-az500-clase-05 --location westus  
```

## Probar los Container Instances

1. Crear Contaienr Instance

> Utilizar Imagen : mcr.microsoft.com/azuredocs/aci-helloworld

2 Verificar el deployment

> En la parte de Overview copiar la direccion ip en una nueva solapar del navegador

## Probar Container Registries con Container Instances

1. Crear un Container Registry

2. En la consola crear el archivo server.js con este contenido

```javascript
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('Hola Mundo');
});

app.listen(80, () => {
  console.log('Servidor escuchando en el puerto 80');
});
```

3. Instalar paquete express

> npm init -y
> npm install express

3. Crear el archivo Dockerfile en la consola con este contenido

```dockerfile
# Imagen base
FROM node:18

# Crear directorio de trabajo
WORKDIR /app

# Copiar archivos
COPY package*.json ./
COPY server.js ./

# Instalar dependencias
RUN npm install

# Exponer el puerto 80
EXPOSE 80

# Comando para iniciar la app
CMD ["node", "server.js"]

```

4. Subir el programa al acr

Hacer Login en el ACR
```bash
  az acr login --name adr4trainner --expose-token
```

Subir la app al acr
```bash
  az acr build --registry adr4trainner  --image webapp:latest .   
  (En el comando anterior no olvidar el . final)
```

5. En el ACR agregarle un admin Username

> Anotar <USERNAME> <PASSWORD>

7. Crear un container instance con la imagen del ACR

```bash
az container create --resource-group rg-az500-clase-05 --name aci-webapp --image adr4trainner.azurecr.io/webapp:latest  --ports 80 --ip-address Public --os-type Linux --cpu 1 --memory 2 --registry-username <USERNAME> --registry-password <PASSWORD>
```

8. Probar la imagen desplegada

> copiar la ip publica del contenedor y en una nueva solapa poner http://<IP_PUBLICA_SERVIDOR>

## Probar Container Apps

1. Crear el container App Enviroment

2. Crear el container App
> Utilizar la imagen que subimos al ACR antes  
> Configurar bien el Ingress  
> En la parte de Ingress asegurarte que sea public "allow traffic from anywhere" y que la url no diga .internal

## Probar Kubernetes

1. Subir una imagen al acr

En una carpeta crear el archivo Dobkerfile
```bash
 echo FROM nginx > Dockerfile
```
Subir la imagen al ACR
```bash
 az acr build --registry adr4trainner  --image new-nginx:latest .    
```
 
2. 
