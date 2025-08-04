# Laboratorio 08 : Monitoring

- ## Pasos:

1. Crear el resource Group

```powershell
New-AzResourceGroup -Name rg-az500-clase-07 -Location westus
```

2. Crear un Log analitics Workspace
      * Observar Opcion Tables (Estan todas vacias)
      * Ver como consultar esas tablas con el lenguaje KQL (Kusto Query Language)
      * Ver la parte de Agents
     
3. Crear un storage account
     
4. Exportar todo lo que esta en el activity log del resource Grouo tanto en el Stroage Account como en el Log Analitics Workspace

5. Ejecutar el siguiente comando en el cli

```powershell
 Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace Microsoft.Compute 
```

6.  Crear una maquina virtual

```powershell
 New-AzVm -ResourceGroupName "rg-az500-clase-07" -Name "myVM" -Location 'westUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -PublicIpSku Standard -OpenPorts 80,3389 -Size Standard_D2s_v3 
```

7.  Conectarnos a la VM y chequear que este todo ok

8.  Verificar en el Storage Account y en el Log Analitics Workspace si hay informacion


