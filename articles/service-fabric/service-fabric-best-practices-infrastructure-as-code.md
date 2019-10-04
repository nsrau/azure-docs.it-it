---
title: Procedure consigliate per Infrastructure as Code (IaC) in Azure Service Fabric | Microsoft Docs
description: Procedure consigliate per la gestione di Service Fabric come Infrastructure as Code (IaC).
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 83439a913989875c5e26ee51901f10558e5debd5
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955605"
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

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Configurazione dell'aggiornamento automatico del sistema operativo della macchina virtuale di Azure 
L'aggiornamento delle macchine virtuali è un'operazione avviata dall'utente ed è consigliabile usare l' [aggiornamento automatico del sistema operativo del set di scalabilità di macchine virtuali](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) per i cluster di Azure Service Fabric host gestione delle patch; Patch Orchestration Application è una soluzione alternativa destinata a se ospitata all'esterno di Azure, sebbene il POA possa essere usato in Azure, con un sovraccarico del POA di hosting in Azure, un motivo comune per preferire l'aggiornamento automatico del sistema operativo della macchina virtuale su POA. Di seguito è riportato il set di scalabilità di macchine virtuali di calcolo Gestione risorse le proprietà del modello per abilitare l'aggiornamento automatico del sistema operativo:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Quando si usano gli aggiornamenti automatici del sistema operativo con Service Fabric, la nuova immagine del sistema operativo viene implementata un dominio di aggiornamento alla volta per mantenere la disponibilità elevata dei servizi in esecuzione in Service Fabric. Per usare gli aggiornamenti automatici del sistema operativo in Service Fabric, il cluster deve essere configurato per usare il livello di durabilità Silver o superiore.

Verificare che la seguente chiave del registro di sistema sia impostata su false per impedire che i computer host Windows avviino gli aggiornamenti non coordinati: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

Di seguito è riportato il set di scalabilità di macchine virtuali di calcolo Gestione risorse le proprietà del modello per impostare la chiave del registro di sistema WindowsUpdate su false:
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

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Configurazione dell'aggiornamento del cluster di Azure Service Fabric
Di seguito è riportata la proprietà Service Fabric cluster Gestione risorse template per abilitare l'aggiornamento automatico:
```json
"upgradeMode": "Automatic",
```
Per aggiornare manualmente il cluster, scaricare la distribuzione CAB/deb in una macchina virtuale del cluster e quindi richiamare il seguente PowerShell:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Passaggi successivi

* Creare un cluster nelle macchine virtuali o nei computer che eseguono Windows Server: [Creazione del cluster di Service Fabric per Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Creare un cluster nelle macchine virtuali o nei computer che eseguono Linux: [Creare un cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md)
