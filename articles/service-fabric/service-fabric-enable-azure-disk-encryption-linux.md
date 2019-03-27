---
title: Abilitare la crittografia del disco per i cluster Linux di Azure Service Fabric | Microsoft Docs
description: In questo articolo viene descritto come abilitare la crittografia del disco per il set di scalabilità del cluster di Service Fabric in Azure tramite Azure Resource Manager e Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: aljo
ms.openlocfilehash: 3de26efb74b9349282d36beb94e8a2a269227fbf
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488311"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>Abilitare la crittografia del disco per i nodi del cluster Linux di Service Fabric 
> [!div class="op_single_selector"]
> * [Crittografia del disco per Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Crittografia del disco per Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Procedere come segue per abilitare la crittografia del disco sui nodi del cluster Linux di Service Fabric. È necessario eseguire le operazioni seguenti per ogni tipo di nodo/set di scalabilità di macchine virtuali. Per crittografare i nodi, verrà usata la funzionalità Crittografia dischi di Azure nel set di scalabilità di macchine virtuali.

La guida illustra le procedure seguenti:

* Concetti principali da tenere in considerazione per abilitare la crittografia del disco nel set di scalabilità di macchine virtuali del cluster Linux di Service Fabric.
* Procedure da eseguire come prerequisiti prima di abilitare la crittografia del disco nel set di scalabilità di macchine virtuali del cluster Linux di Service Fabric.
* Procedure da eseguire per abilitare la crittografia del disco nel set di scalabilità di macchine virtuali del cluster Linux di Service Fabric.


## <a name="prerequisites"></a>Prerequisiti

* **Registrazione automatica**: per usare l'anteprima di Crittografia dischi del set di scalabilità di macchine virtuali, è necessaria la registrazione automatica
* È possibile eseguire la registrazione automatica della sottoscrizione procedendo nel modo seguente: 
```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* Attendere circa 10 minuti finché lo stato risulta "Registrato". È possibile controllare lo stato con il comando seguente: 
```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Azure Key Vault**: creare un insieme di credenziali delle chiavi nella stessa sottoscrizione e nella stessa area del set di scalabilità di macchine virtuali e impostare il criterio di accesso "EnabledForDiskEncryption" sull'insieme di credenziali delle chiavi usando il relativo cmdlet PS. È anche possibile impostare i criteri tramite l'interfaccia utente per l'insieme di credenziali delle chiavi nel portale di Azure: 
```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* Installare la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest), che include i nuovi comandi di crittografia.
* Installare la versione più recente di [Azure SDK da Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Di seguito sono riportati i cmdlet ADE per abilitare del set di scalabilità di macchina virtuale ([impostata](/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1)) la crittografia, recuperare ([ottenere](/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) lo stato della crittografia e Rimuovi ([disabilitare](/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) crittografia su scala istanza del set. 

| Comando | Versione |  Source (Sorgente)  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 o successiva | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryptionStatus   | 3.4.0 o successiva | AzureRM.Compute |
| Disable-AzureRmVmssDiskEncryption   | 3.4.0 o successiva | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 o successiva | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 o successiva | AzureRM.Compute |
| Set-AzureRmVmssDiskEncryptionExtension   | 3.4.0 o successiva | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scenari supportati per la crittografia dei dischi
* La crittografia del set di scalabilità di macchine virtuali è supportata solo per i set di scalabilità creati con dischi gestiti. Non è tuttavia supportata per i set di scalabilità di dischi nativi (o non gestiti).
* La crittografia del set di scalabilità di macchine virtuali è supportata per il volume dei dati nei set di scalabilità di macchine virtuali Linux. Nell'anteprima corrente la crittografia del disco del sistema operativo NON è supportata per Linux.
* Ricreazione dell'immagine della macchina virtuale del set di scalabilità di macchine virtuali e le operazioni di aggiornamento non sono supportate in fase di anteprima corrente.


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>Creare un nuovo cluster Linux e abilitare la crittografia dei dischi

Usare i comandi seguenti per creare cluster e abilitare la crittografia del disco usando il modello di Azure Resource Manager e certificato autofirmato.

### <a name="sign-in-to-azure"></a>Accedere ad Azure  

```powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Usare il modello personalizzato già disponibile 

Se è necessario creare un modello personalizzato specifico per le proprie esigenze, è consigliabile iniziare con uno di quelli disponibili tra gli [esempi di modelli di Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) per il cluster Linux. 

Se si dispone già di un modello personalizzato, verificare che tutti e tre i parametri correlati al certificato nel modello e il file dei parametri siano denominati come segue e che i valori siano Null come indicato di seguito.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Poiché per il set di scalabilità di macchine virtuali Linux è supportata solo la crittografia del disco dati, è necessario aggiungere il disco dati tramite il modello di Azure Resource Manager. Aggiornare il modello per il provisioning del disco dati come indicato di seguito:

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
```
 

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Ecco il comando equivalente dell'interfaccia della riga di comando per eseguire la stessa operazione. Modificare i valori nelle istruzioni declare specificando i valori appropriati. L'interfaccia della riga di comando supporta tutti gli altri parametri supportati dal precedente comando di PowerShell.

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

#### <a name="linux-data-disk-mounting"></a>Montaggio del disco dati Linux
Prima di procedere con la crittografia nel set di scalabilità di macchine virtuali Linux, è necessario verificare che il disco dati aggiunto sia stato montato correttamente. Accedere alla macchina virtuale di Cluster Linux ed eseguire comandi LSBLK. L'output dovrebbe indicare il disco dati aggiunto nella colonna del punto di montaggio.


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>Distribuire l'applicazione in un cluster Linux di Service Fabric
Seguire le procedure e le indicazioni per [distribuire l'applicazione nel cluster](service-fabric-quickstart-containers-linux.md)


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>Abilitare la crittografia dei dischi per il set di scalabilità di macchine virtuali del cluster Linux di Service Fabric creato in precedenza
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>Verificare se la crittografia dei dischi è abilitata per il set di scalabilità di macchine virtuali Linux.
Ottenere lo stato di un intero set di scalabilità di macchine virtuali o di qualsiasi macchina virtuale dell'istanza nel set di scalabilità. Vedere i comandi seguenti.
Inoltre utente può accedere alla macchina virtuale di Cluster Linux ed eseguire il comando LSBLK. L'output dovrebbe indicare il disco dati aggiunto nella colonna del punto di montaggio e Crypt nella colonna del tipo per il disco dati aggiunto.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Disabilitare la crittografia dei dischi per il set di scalabilità di macchine virtuali del cluster di Service Fabric 
La disabilitazione della crittografia dei dischi si applica all'intero set di scalabilità di macchine virtuali e non alle singole istanze 

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Passaggi successivi
A questo punto, si dispone di un cluster protetto, con la possibilità di abilitare o disabilitare la crittografia dei dischi per il set di scalabilità di macchine virtuali del cluster Linux di Service Fabric. L'argomento successivo è [Crittografia dischi per Windows](service-fabric-enable-azure-disk-encryption-windows.md) 
