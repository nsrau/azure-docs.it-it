---
title: Azure Service Fabric infrastructure as Code Best Practices
description: Procedure consigliate e considerazioni di progettazione per la gestione di Azure Service Fabric come infrastruttura come codice.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 1c044d5fd973d3c577088a887f2fac413d2ab79d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551829"
---
# <a name="infrastructure-as-code"></a>Infrastructure as code

In uno scenario di produzione, creare i cluster Azure Service Fabric usando i modelli di Resource Manager. I modelli di Resource Manager offrono un maggiore controllo delle proprietà delle risorse e assicurano di avere a disposizione un modello di risorse coerente.

Tra gli [esempi di Azure su GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates) sono disponibili alcuni modelli di Resource Manager di esempio per Windows e Linux. Questi modelli possono essere usati come punto di partenza per il modello del cluster. Scaricare `azuredeploy.json` e `azuredeploy.parameters.json` e modificarli in modo da soddisfare i requisiti personalizzati.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per distribuire i modelli `azuredeploy.json` e `azuredeploy.parameters.json` scaricati in precedenza, usare i comandi dell'interfaccia della riga di comando di Azure seguenti:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Creazione di una risorsa tramite PowerShell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Risorse di Azure Service Fabric

È possibile distribuire applicazioni e servizi in un cluster di Service Fabric tramite Azure Resource Manager. Per altri dettagli, vedere [Gestire applicazioni e servizi come risorse di Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource). Il codice seguente include le risorse specifiche dell'applicazione Service Fabric consigliate da includere nelle risorse dei modelli di Resource Manager.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Per distribuire l'applicazione usando Azure Resource Manager, è prima di tutto necessario [creare un pacchetto dell'applicazione Service Fabric con estensione sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg). Lo script Python seguente è un esempio di come creare un pacchetto sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Configurazione dell'aggiornamento automatico del sistema operativo della macchina virtuale di AzureAzure Virtual Machine Operating System Automatic Upgrade Configuration 
L'aggiornamento delle macchine virtuali è un'operazione avviata dall'utente ed è consigliabile usare l'aggiornamento automatico del sistema operativo virtual [machine Scale Set](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) per i cluster di Azure Service Fabric host per la gestione delle patch. Patch Orchestration Application è una soluzione alternativa che è destinata a quando ospitato al di fuori di Azure, anche se POA può essere utilizzato in Azure, con l'overhead di hosting POA in Azure è un motivo comune per preferire l'aggiornamento automatico del sistema operativo macchina virtuale rispetto a POA. Di seguito sono riportate le proprietà del modello Calcola set di macchine virtuali Resource Manager per abilitare l'aggiornamento automatico del sistema operativo:The following are the Compute Virtual Machine Scale Set Resource Manager template properties to enable Auto OS upgrade:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Quando si usa Aggiornamenti automatici del sistema operativo con Service Fabric, la nuova immagine del sistema operativo viene implementata un dominio di aggiornamento alla volta per mantenere un'elevata disponibilità dei servizi in esecuzione in Service Fabric. Per usare gli aggiornamenti automatici del sistema operativo in Service Fabric, il cluster deve essere configurato per usare il livello di durabilità Silver o superiore.

Assicurarsi che la seguente chiave del Registro di sistema sia impostata su false per impedire ai computer host Windows di accedere agli aggiornamenti non coordinati: HKEY_LOCAL_MACHINE .

Di seguito sono riportate le proprietà del modello Calcola set di macchine virtuali resource manager per impostare la chiave del Registro di sistema di WindowsUpdate su false:The following are the Compute Virtual Machine Scale Set Resource Manager template properties to set windows Key key to false:
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Azure Service Fabric Cluster Upgrade Configuration
Di seguito è riportata la proprietà del modello di Resource Manager del cluster di Service Fabric per abilitare l'aggiornamento automatico:The following is the Service Fabric cluster Resource Manager template property to enable automatic upgrade:
```json
"upgradeMode": "Automatic",
```
Per aggiornare manualmente il cluster, scaricare la distribuzione cab/deb in una macchina virtuale del cluster e quindi richiamare PowerShell seguente:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Passaggi successivi

* Creare un cluster nelle VM o nei computer che eseguono Windows Server: [Creazione di cluster di Service Fabric per Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Creare un cluster nelle VM o nei computer che eseguono Linux: [Creare un cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Informazioni sulle opzioni di supporto di [Service Fabric](service-fabric-support.md)
