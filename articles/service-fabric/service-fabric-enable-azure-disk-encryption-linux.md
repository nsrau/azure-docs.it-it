---
title: Abilitare la crittografia del disco per i cluster Linux di Azure Service Fabric | Microsoft Docs
description: Questo articolo descrive come abilitare la crittografia del disco per i nodi del cluster di Azure Service Fabric in Linux usando Azure Resource Manager e Azure Key Vault.
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
ms.openlocfilehash: 47b07188d1757708fb494c6a66e93379657e806a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258771"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Abilitare la crittografia del disco per i nodi del cluster di Azure Service Fabric in Linux 
> [!div class="op_single_selector"]
> * [Crittografia del disco per Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Crittografia del disco per Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

In questa esercitazione si apprenderà come abilitare la crittografia del disco sui nodi del cluster di Azure Service Fabric in Linux. È necessario seguire questa procedura per ciascuno dei tipi di nodo e set di scalabilità di macchine virtuali. Per crittografare i nodi, si userà la funzionalità crittografia dischi di Azure nei set di scalabilità di macchine virtuali.

La Guida include gli argomenti seguenti:

* Concetti chiave da tenere presenti quando imposta di abilitare la crittografia del disco nella scalabilità di macchine virtuali del cluster di Service Fabric in Linux.
* Procedura da seguire prima di abilitare la crittografia del disco in Service Fabric cluster i nodi Linux.
* Passaggi da seguire per abilitare la crittografia del disco sui nodi del cluster di Service Fabric in Linux.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

 **Registrazione automatica**

L'anteprima di crittografia del disco per il set di scalabilità di macchine virtuali richiede la registrazione automatica. Seguire questa procedura:

1. Eseguire il comando seguente: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Attendere circa 10 minuti fino a quando lo stato è indicato *Registered*. È possibile controllare lo stato eseguendo il comando seguente:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Insieme di credenziali chiave Azure**

1. Creare un insieme di credenziali nella stessa sottoscrizione e nella stessa area del set di scalabilità. Quindi selezionare il **EnabledForDiskEncryption** criterio nell'insieme di credenziali chiave di accesso usando il cmdlet di PowerShell. È anche possibile impostare i criteri usando l'interfaccia utente dell'insieme di credenziali chiave nel portale di Azure con il comando seguente:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Installare la versione più recente del [CLI Azure](/cli/azure/install-azure-cli), che include i nuovi comandi di crittografia.

3. Installare la versione più recente del [SDK di Azure da Azure PowerShell](https://github.com/Azure/azure-powershell/releases) release. Di seguito sono cmdlet di crittografia dischi di Azure per abilitare del set di scalabilità di macchine virtuali ([impostata](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) crittografia, recuperare ([ottenere](/powershell/module/az.compute/get-azvmssvmdiskencryption)) lo stato della crittografia e remove ([disabilitare](/powershell/module/az.compute/disable-azvmssdiskencryption)) istanza del set di crittografia sulla scala.


| Comando | Version |  `Source`  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 o versione successiva | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 o versione successiva | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 o versione successiva | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 o versione successiva | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 o versione successiva | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 o versione successiva | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scenari supportati per la crittografia dei dischi
* Crittografia per il set di scalabilità di macchine virtuali è supportata solo per i set di scalabilità creati con dischi gestiti. Non è supportata per i set di scalabilità di dischi nativi (o non gestiti).
* Disabilitazione della crittografia sia la crittografia sono supportate per i volumi del sistema operativo e i dati nel set di scalabilità di macchine virtuali in Linux. 
* Operazioni di ricreazione dell'immagine e aggiornamento macchina virtuale (VM) per il set di scalabilità di macchine virtuali non sono supportate nell'anteprima corrente.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Creare un nuovo cluster e abilitare la crittografia del disco

Usare i comandi seguenti per creare un cluster e abilitare la crittografia del disco usando un modello di Azure Resource Manager e un certificato autofirmato.

### <a name="sign-in-to-azure"></a>Accedi ad Azure  

Accedere con i comandi seguenti:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Usare il modello personalizzato già disponibile 

Se è necessario creare un modello personalizzato, è consigliabile usare uno dei modelli nel [esempi di modelli di Azure Service Fabric cluster creazione](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) pagina. 

Se si dispone già di un modello personalizzato, è necessario accertarsi che tutti i tre parametri di relative ai certificati nel modello e il file dei parametri siano denominati come segue. Assicurarsi anche che i valori siano null come indicato di seguito:

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

Poiché è supportato solo per la crittografia del disco dati per set di scalabilità di macchine virtuali in Linux, è necessario aggiungere un disco dati usando un modello di Resource Manager. Aggiornare il modello per effettuare il provisioning del disco dati come indicato di seguito:

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


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Ecco la riga di comando equivalente. Modificare i valori nelle istruzioni declare sui valori appropriati. L'interfaccia CLI supporta tutti gli altri parametri che supporta il comando di PowerShell precedente.

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Montare un disco dati a un'istanza di Linux
Prima di continuare con la crittografia in un set di scalabilità di macchine virtuali, assicurarsi che il disco di dati aggiunti sia installato correttamente. Accedere al cluster Linux della macchina virtuale ed eseguire la **LSBLK** comando. L'output dovrebbe mostrare quel disco dati aggiunto nel **punto di montaggio** colonna.


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Distribuire l'applicazione in un cluster di Service Fabric in Linux
Per distribuire un'applicazione nel cluster, seguire i passaggi e le indicazioni fornite in [Guida introduttiva: Distribuire contenitori Linux in Service Fabric](service-fabric-quickstart-containers-linux.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Abilitare la crittografia del disco per i set di scalabilità di macchina virtuale creata in precedenza
Per abilitare la crittografia del disco per la scalabilità di macchine virtuali i set creati tramite i passaggi precedenti, Esegui i comandi seguenti:
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>La convalida se crittografia dischi è abilitata per la scalabilità di macchine virtuali impostato in Linux
Per ottenere lo stato di un set di scalabilità di macchina virtuale intera o qualsiasi istanza in un set di scalabilità, eseguire i comandi seguenti.
Inoltre, è possibile accedere a cluster Linux della macchina virtuale ed eseguire la **LSBLK** comando. L'output dovrebbe mostrare il disco dati aggiunto nel **punto di montaggio** colonna e il **tipo** colonna deve leggere *Crypt*.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Disabilitare la crittografia del disco per la scalabilità di macchine virtuali impostato in un cluster di Service Fabric
Disabilitare la crittografia del disco per la scalabilità di macchine virtuali impostare eseguendo i comandi seguenti. Si noti che la disabilitazione della crittografia del disco sono valide per il set di scalabilità di intera macchina virtuale e non una singola istanza.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Passaggi successivi
A questo punto, si deve avere un cluster sicuro e sapere come abilitare e disabilitare la crittografia del disco per i nodi del cluster Service Fabric e set di scalabilità di macchine virtuali. Per indicazioni simili nei nodi di cluster di Service Fabric in Linux, vedere [crittografia del disco per Windows](service-fabric-enable-azure-disk-encryption-windows.md). 
