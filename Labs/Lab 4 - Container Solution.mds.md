# Probando las soluciones de contenedores

1. Crear el resource Group
```bash
   az group create --name rg-az500-clase-05 --location westus  
```

## Probar los Container Instances

2. Crear Contaienr Instance

> Utilizar Imagen : mcr.microsoft.com/azuredocs/aci-helloworld

3 Verificar el deployment

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

>  az acr login --name adr4trainner --expose-token
> az acr build --registry adr4trainner  --image webapp:latest .   

