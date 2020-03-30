---
title: Abilitare la crittografia del disco per i cluster LinuxEnable disk encryption for Linux clusters
description: Questo articolo descrive come abilitare la crittografia del disco per i nodi del cluster di Azure Service Fabric in Linux usando Azure Resource Manager e Azure Key Vault.This article describes how to enable disk encryption for Azure Service Fabric cluster nodes in Linux by using Azure Resource Manager and Azure Key Vault.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: c600d822d20b0e5a0ca613935b1dfa4be838fcec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252823"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Abilitare la crittografia del disco per i nodi del cluster di Azure Service Fabric in LinuxEnable disk encryption for Azure Service Fabric cluster nodes in Linux 
> [!div class="op_single_selector"]
> * [Crittografia del disco per Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Crittografia del disco per Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

In this tutorial, you'll learn how to enable disk encryption on Azure Service Fabric cluster nodes in Linux. È necessario seguire questi passaggi per ognuno dei tipi di nodo e dei set di scalabilità delle macchine virtuali. Per crittografare i nodi, useremo la funzionalità Crittografia disco di Azure nei set di scalabilità delle macchine virtuali.

La guida tratta i seguenti argomenti:

* Concetti chiave da tenere presenti quando si abilita la crittografia del disco nei set di scalabilità delle macchine virtuali del cluster di Service Fabric in Linux.Key concepts to be aware of when enabling disk encryption on Service Fabric cluster virtual machine scale sets in Linux.
* Passaggi da seguire prima di abilitare la crittografia del disco nei nodi del cluster di Service Fabric in Linux.Steps to be followed before enabling disk encryption on Service Fabric cluster nodes in Linux.
* Passaggi da seguire per abilitare la crittografia del disco nei nodi del cluster di Service Fabric in Linux.Steps to be followed to enable disk encryption on Service Fabric cluster nodes in Linux.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

 **Autoregistrazione**

L'anteprima della crittografia del disco per il set di scalabilità della macchina virtuale richiede l'autoregistrazione. Eseguire la procedura descritta di seguito:

1. Eseguire il comando seguente: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Attendere circa 10 minuti fino a quando lo stato viene letto *Registered*. È possibile controllare lo stato eseguendo il comando seguente:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Archivio chiavi di AzureAzure Key Vault**

1. Creare un insieme di credenziali nella stessa sottoscrizione e nella stessa area del set di scalabilità. Selezionare quindi il criterio di accesso **EnabledForDiskEncryption** nell'insieme di credenziali delle chiavi utilizzando il relativo cmdlet PowerShell. È anche possibile impostare i criteri usando l'interfaccia utente dell'insieme di credenziali delle chiavi nel portale di Azure con il comando seguente:You can also set the policy by using the Key Vault UI in the Azure portal with the following command:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Installare la versione più recente dell'interfaccia della riga di comando di [Azure,](/cli/azure/install-azure-cli)con i nuovi comandi di crittografia.

3. Installare la versione più recente di Azure SDK dalla versione di Azure PowerShell.Install the latest version of the [Azure SDK from Azure PowerShell](https://github.com/Azure/azure-powershell/releases) release. Di seguito sono riportati i cmdlet di Crittografia disco di Azure per abilitare la crittografia[set](/powershell/module/az.compute/set-azvmssdiskencryptionextension), recuperare ([ottenere](/powershell/module/az.compute/get-azvmssvmdiskencryption)) lo stato di crittografia e rimuovere ([disabilitare](/powershell/module/az.compute/disable-azvmssdiskencryption)) la crittografia nell'istanza del set di scalabilità.


| Comando | Versione |  Source (Sorgente)  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 o versione successiva | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 o versione successiva | Az.Compute |
| Disabilita-AzVmssDiskEncryption   | 1.0.0 o versione successiva | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 o versione successiva | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 o versione successiva | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 o versione successiva | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scenari supportati per la crittografia dei dischi
* La crittografia per i set di scalabilità delle macchine virtuali è supportata solo per i set di scalabilità creati con dischi gestiti. Non è supportata per i set di scalabilità di dischi nativi (o non gestiti).
* Sia la crittografia che la crittografia disabilitare sono supportate per il sistema operativo e i volumi di dati nei set di scalabilità delle macchine virtuali in Linux.Both encryption and disable encryption are supported for OS and data volumes in virtual machine scale sets in Linux. 
* Le operazioni di ricreazione e aggiornamento della macchina virtuale (VM) per i set di scalabilità delle macchine virtuali non sono supportate nell'anteprima corrente.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Creare un nuovo cluster e abilitare la crittografia del discoCreate a new cluster and enable disk encryption

Usare i comandi seguenti per creare un cluster e abilitare la crittografia del disco usando un modello di Azure Resource Manager e un certificato autofirmato.

### <a name="sign-in-to-azure"></a>Accedere ad Azure  

Accedere con i seguenti comandi:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Usare il modello personalizzato già disponibile 

Se è necessario creare un modello personalizzato, è consigliabile usare uno dei modelli nella pagina degli esempi di esempi di modelli di creazione cluster di Azure Service Fabric.If you need to author a custom template, we recommend that you use one of the templates on the [Azure Service Fabric cluster creation template samples.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) 

Se si dispone già di un modello personalizzato, verificare che tutti e tre i parametri relativi ai certificati nel modello e nel file dei parametri siano denominati come segue. Assicurarsi inoltre che i valori siano null come indicato di seguito:Also ensure that the values are null as follows:

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

Poiché solo la crittografia del disco dati è supportata per i set di scalabilità di macchine virtuali in Linux, è necessario aggiungere un disco dati usando un modello di Resource Manager.Because only data disk encryption is supported for virtual machine scale sets in Linux, you must add a data disk by using a Resource Manager template. Aggiornare il modello per il provisioning del disco dati come segue:Update your template for data disk provision as follows:

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

Di seguito è riportato il comando CLI equivalente. Modificare i valori nelle istruzioni declare con i valori appropriati. L'interfaccia della riga di comando supporta tutti gli altri parametri supportati dal comando PowerShell precedente.

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Montare un disco dati in un'istanza LinuxMount a data disk to a Linux instance
Prima di continuare con la crittografia in un set di scalabilità di macchine virtuali, verificare che il disco dati aggiunto sia montato correttamente. Accedere alla macchina virtuale del cluster Linux ed eseguire il comando **LSBLK.** L'output dovrebbe indicare che il disco dati aggiunto nella colonna Punto di **montaggio.**


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Distribuire l'applicazione in un cluster di Service Fabric in LinuxDeploy application to a Service Fabric cluster in Linux
Per distribuire un'applicazione nel cluster, seguire i passaggi e le istruzioni in [Guida introduttiva: Distribuire contenitori Linux in Service Fabric](service-fabric-quickstart-containers-linux.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Abilitare la crittografia del disco per i set di scalabilità della macchina virtuale creati in precedenzaEnable disk encryption for the virtual machine scale sets created previously
Per abilitare la crittografia del disco per i set di scalabilità delle macchine virtuali creati tramite i passaggi precedenti, eseguire i comandi seguenti:To enable disk encryption for the virtual machine scale sets you created through the previous steps, run the following commands:
 
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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Verificare se la crittografia del disco è abilitata per un set di scalabilità di macchine virtuali in LinuxValidate if disk encryption is enabled for a virtual machine scale set in Linux
Per ottenere lo stato di un intero set di scalabilità della macchina virtuale o di qualsiasi istanza in un set di scalabilità, eseguire i comandi seguenti.
Inoltre, è possibile accedere alla macchina virtuale del cluster Linux ed eseguire il comando **LSBLK.** L'output dovrebbe mostrare il disco dati aggiunto nella colonna Punto di **montaggio** e la colonna **Tipo** dovrebbe essere *Crittografata*.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Disabilitare la crittografia del disco per un set di scalabilità di macchine virtuali in un cluster di Service FabricDisable disk encryption for a virtual machine scale set in a Service Fabric cluster
Disabilitare la crittografia del disco per un set di scalabilità di macchine virtuali eseguendo i comandi seguenti. Si noti che la disabilitazione della crittografia del disco si applica all'intero set di scalabilità della macchina virtuale e non a una singola istanza.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Passaggi successivi
A questo punto, è necessario disporre di un cluster sicuro e sapere come abilitare e disabilitare la crittografia del disco per i nodi del cluster di Service Fabric e i set di scalabilità di macchine virtuali. Per indicazioni simili sui nodi del cluster di Service Fabric in Linux, vedere [Crittografia disco per Windows.](service-fabric-enable-azure-disk-encryption-windows.md) 
