---
title: Abilitare la crittografia del disco per i cluster Azure Service Fabric Linux | Microsoft Docs
description: Questo articolo descrive come abilitare la crittografia del disco per i nodi del cluster Service Fabric di Azure in Linux usando Azure Resource Manager e Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: atsenthi
ms.openlocfilehash: 5bcfad63df69010851dde66b0c8935e63a509455
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599598"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Abilitare la crittografia del disco per i nodi del cluster Service Fabric di Azure in Linux 
> [!div class="op_single_selector"]
> * [Crittografia del disco per Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Crittografia del disco per Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Questa esercitazione illustra come abilitare la crittografia del disco nei nodi del cluster Service Fabric di Azure in Linux. È necessario seguire questa procedura per ogni tipo di nodo e set di scalabilità di macchine virtuali. Per la crittografia dei nodi, verrà usata la funzionalità di crittografia dischi di Azure nei set di scalabilità di macchine virtuali.

La guida descrive gli argomenti seguenti:

* Concetti chiave da tenere presente quando si Abilita la crittografia del disco nei set di scalabilità di macchine virtuali del cluster Service Fabric in Linux.
* Passaggi da seguire prima di abilitare la crittografia del disco nei nodi del cluster Service Fabric in Linux.
* Passaggi da seguire per abilitare la crittografia del disco nei nodi del cluster Service Fabric in Linux.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

 **Registrazione automatica**

L'anteprima della crittografia del disco per il set di scalabilità di macchine virtuali richiede la registrazione automatica. Seguire questa procedura:

1. Eseguire il comando seguente: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Attendere circa 10 minuti fino a quando non viene eseguita la lettura dello stato *registrato*. È possibile controllare lo stato eseguendo il comando seguente:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Insieme di credenziali chiave Azure**

1. Creare un insieme di credenziali nella stessa sottoscrizione e nella stessa area del set di scalabilità. Quindi selezionare il criterio di accesso **EnabledForDiskEncryption** nell'insieme di credenziali delle chiavi usando il relativo cmdlet di PowerShell. È anche possibile impostare il criterio usando l'interfaccia utente di Key Vault nel portale di Azure con il comando seguente:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Installare la versione più recente dell' [interfaccia](/cli/azure/install-azure-cli)della riga di comando di Azure, che include i nuovi comandi di crittografia.

3. Installare la versione più recente di [Azure SDK dalla versione Azure PowerShell](https://github.com/Azure/azure-powershell/releases) . Di seguito sono riportati i cmdlet di crittografia dischi di Azure per set di scalabilità di macchine virtuali per abilitare ([impostare](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) la crittografia, recuperare ([ottenere](/powershell/module/az.compute/get-azvmssvmdiskencryption)) lo stato della crittografia e rimuovere ([disabilitare](/powershell/module/az.compute/disable-azvmssdiskencryption)) la crittografia nell'istanza del set di scalabilità.


| Comando | Version |  Source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 o versione successiva | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 o versione successiva | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 o versione successiva | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 o versione successiva | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 o versione successiva | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 o versione successiva | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scenari supportati per la crittografia dei dischi
* La crittografia dei set di scalabilità di macchine virtuali è supportata solo per i set di scalabilità creati con Managed Disks. Non è supportata per i set di scalabilità di dischi nativi (o non gestiti).
* La crittografia e la disabilitazione della crittografia sono supportate per i volumi del sistema operativo e dei dati nei set di scalabilità di macchine virtuali in Linux. 
* Le operazioni di ricreazione dell'immagine e di aggiornamento delle macchine virtuali per i set di scalabilità di macchine virtuali non sono supportate nell'anteprima corrente.


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

Se è necessario creare un modello personalizzato, si consiglia vivamente di usare uno dei modelli nella pagina di esempi del [modello di creazione del cluster di Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) . 

Se si dispone già di un modello personalizzato, verificare che tutti e tre i parametri correlati al certificato nel modello e il file dei parametri siano denominati come indicato di seguito. Assicurarsi inoltre che i valori siano null come indicato di seguito:

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

Poiché per i set di scalabilità di macchine virtuali in Linux è supportata solo la crittografia del disco dati, è necessario aggiungere un disco dati usando un modello di Gestione risorse. Aggiornare il modello per il provisioning del disco dati come segue:

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

Ecco il comando CLI equivalente. Modificare i valori nelle istruzioni Declare con i valori appropriati. L'interfaccia della riga di comando supporta tutti gli altri parametri supportati dal precedente comando di PowerShell.

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Montare un disco dati in un'istanza di Linux
Prima di continuare con la crittografia in un set di scalabilità di macchine virtuali, verificare che il disco dati aggiunto sia montato correttamente. Accedere alla macchina virtuale del cluster Linux ed eseguire il comando **LSBLK** . L'output dovrebbe mostrare il disco dati aggiunto nella colonna **punto di montaggio** .


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Distribuire un'applicazione in un cluster Service Fabric in Linux
Per distribuire un'applicazione nel cluster, seguire i passaggi e le istruzioni disponibili [in Guida introduttiva: Distribuire contenitori Linux in Service Fabric](service-fabric-quickstart-containers-linux.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Abilitare la crittografia del disco per i set di scalabilità di macchine virtuali creati in precedenza
Per abilitare la crittografia del disco per i set di scalabilità di macchine virtuali creati nei passaggi precedenti, eseguire i comandi seguenti:
 
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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Verificare se la crittografia del disco è abilitata per un set di scalabilità di macchine virtuali in Linux
Per ottenere lo stato di un intero set di scalabilità di macchine virtuali o di qualsiasi istanza in un set di scalabilità, eseguire i comandi seguenti.
Inoltre, è possibile accedere alla macchina virtuale del cluster Linux ed eseguire il comando **LSBLK** . Nell'output dovrebbe essere visualizzato il disco dati aggiunto nella colonna **punto di montaggio** e la colonna **tipo** deve leggere *crypt*.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Disabilitare la crittografia del disco per un set di scalabilità di macchine virtuali in un cluster Service Fabric
Disabilitare la crittografia del disco per un set di scalabilità di macchine virtuali eseguendo i comandi seguenti. Si noti che la disabilitazione della crittografia del disco si applica all'intero set di scalabilità di macchine virtuali e non a una singola istanza.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Passaggi successivi
A questo punto, è necessario disporre di un cluster protetto e informazioni su come abilitare e disabilitare la crittografia del disco per i nodi del cluster Service Fabric e i set di scalabilità di macchine virtuali. Per istruzioni analoghe sui nodi del cluster Service Fabric in Linux, vedere [crittografia del disco per Windows](service-fabric-enable-azure-disk-encryption-windows.md). 
