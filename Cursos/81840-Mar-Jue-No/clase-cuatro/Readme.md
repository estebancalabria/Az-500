# Clase Cuatro - 23 de Junio 2026

# Repaso

* Seguridad en Red
  * NSG (Network Security Group)
    * Funciona a nivel capa 4 (Puerto e IP)
  * Firewall para proteger los recursos publicos
    * DNAT 
    * Aplicacion Rules
    * Network Rules
  * Implentamos la arquitectura Hub and Spoke
  * VNet Peering

# Setup

* Crear el Resource Group

# Despliegue aplicaciones

* Opcion de despliegye de aplicaciones
  * Virtual Machine
  * App Services
  * Contenedores
      * App Services
      * Container Instances
      * Container Apps
      * Kubernetes
    
* Pagina de registro de  contenedores publica
  * https://hub.docker.com/

# Desplegando contenedores

* ACR : Azure Contanier Registry
* ACI : Azure Container Instance

* Creamos un ACI con la imagen de nginx
  * Nos Da una ip publica a la cual puedo acceder y ver que el contenedor esta desplegado
  * Usamos la imagen "nginx" que la descarga de dockerhub
 
* Crear un Container Registry

* Subir una imagen del contenedor publico de Microsoft a mi propio contenedor (Solamente por cloud shell)

```
az acr import --name acraz500casecatroesteban --source mcr.microsoft.com/azuredocs/aci-helloworld --image nginx
```


