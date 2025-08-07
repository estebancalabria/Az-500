# Laboratorio 09 : Microsoft Defender

1. Crear el Resource Group
2. Crear una VM
    
```powershell
 New-AzVm -ResourceGroupName "rg-az500-clase-07" -Name "myVM" -Location 'westUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -PublicIpSku Standard -OpenPorts 80,3389 -Size Standard_D2s_v3 
```

3. Crear un Log Analitics Worskpace

4. Habilitar el insights de la VM y crear un DCR (Data Collection Rule) que lo gurarde en log del paso anterior
        * Check en la opcion Enable processes and dependencies (Map) 

5. Instalar Performance Diagnosgtics
        * Luego de que lo instale ver VM/Insights/Performance graficos del desempenio de la VM

6. Mirar las nuevas tablas del Log Analitics Workspace y hacer consults

7.  En la VM ir a la sola Security / Microsoft Defender For Cloud
     * Ver las recomendaciones en mi caso aparecio la siguiente: EDR configuration issues should be resolved on virtual machines
     * Para solucionarlo me conecte en la vitual por rdp e hice lo siguiente
     * Pueden apareer otras recomendaciones consultar a ChatGPT de que se trata y como remediarlo


8.  Para simular un archivo malicioso creamos el archivo eider.txt con el siguiente contenido:

```txt
X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*
```
Lo va a detectar como archivo malicioso el defender local de la VM o el defender de Azure en la siguiente opcion:
Poder ver lo que se detecto localmente con el comando
```powershell
Get-MPThreatDetection
```

9.  Ir a la opcion Defender For Cloud y ver las Security Alerts

10.  Ir a la opcion Defender For Cloud \ Subcriptions \ VM y ahi vemos los planes de Defender For Cloud Habilitados en la VM
           * Aca le habilitamos el Plan Defender for Cloud Server por 15 por mes
           * Este plan incluye mas chequesos de seguridad y recomendaciones avanzadas
           * Si veo las reomendaciones en la virtual veo que ahora me aparecen mas recomendaciones
     
11.  Ir a la opcion Defender For Cloud y ver Assesed Resources
           * Ver las recomendaciones y las alertas sobre la subscripcion
           * Ver las recomendaciones y las alertas sobre la VM
                 * Recomendaciones como "Machines should be configured to periodically check for missing system updates" se pueden solucionar directamente entrando a la recomendacion y presionando "Fix"
13.     
