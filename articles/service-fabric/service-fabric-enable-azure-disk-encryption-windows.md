---
title: Abilitare la crittografia del disco per i cluster di WindowsEnable disk encryption for Windows clusters
description: Questo articolo descrive come abilitare la crittografia del disco per i nodi del cluster di Azure Service Fabric usando l'insieme di credenziali delle chiavi di Azure in Azure Resource Manager.This article describes how to enable disk encryption for Azure Service Fabric cluster nodes by using Azure Key Vault in Azure Resource Manager.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: b08cdb63aa6f334c5a6f7c230b1624d232206c3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251808"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Abilitare la crittografia del disco per i nodi del cluster di Azure Service Fabric in WindowsEnable disk encryption for Azure Service Fabric cluster nodes in Windows 
> [!div class="op_single_selector"]
> * [Crittografia del disco per Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Crittografia del disco per Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

In this tutorial, you'll learn how to enable disk encryption on Service Fabric cluster nodes in Windows. È necessario seguire questi passaggi per ognuno dei tipi di nodo e dei set di scalabilità delle macchine virtuali. Per crittografare i nodi, useremo la funzionalità Crittografia disco di Azure nei set di scalabilità delle macchine virtuali.

La guida tratta i seguenti argomenti:

* Concetti chiave da tenere presenti quando si abilita la crittografia del disco nei set di scalabilità di macchine virtuali del cluster di Service Fabric in Windows.Key concepts to be aware of when enabling disk encryption on Service Fabric cluster virtual machine set in Windows.
* Passaggi da seguire prima di abilitare la crittografia del disco nei nodi del cluster di Service Fabric in Windows.
* Passaggi da seguire per abilitare la crittografia del disco nei nodi del cluster di Service Fabric in Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

**Autoregistrazione** 

L'anteprima della crittografia del disco per il set di scalabilità della macchina virtuale richiede l'autoregistrazione. Eseguire la procedura descritta di seguito: 

1. In primo luogo, eseguire il comando seguente:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Attendere circa 10 minuti fino a quando lo stato viene letto *Registered*. È possibile controllare lo stato eseguendo il comando seguente: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Archivio chiavi di AzureAzure Key Vault** 

1. Creare un insieme di credenziali delle chiavi nella stessa sottoscrizione e nella stessa area del set di scalabilità, quindi selezionare il criterio di accesso **EnabledForDiskEncryption** nell'insieme di credenziali delle chiavi utilizzando il cmdlet PowerShell. È anche possibile impostare i criteri usando l'interfaccia utente dell'insieme di credenziali delle chiavi nel portale di Azure con il comando seguente:You can also set the policy by using the Key Vault UI in the Azure portal with the following command:
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
* La crittografia è supportata per il sistema operativo e i volumi di dati nei set di scalabilità delle macchine virtuali in Windows.Encryption is supported for OS and data volumes in virtual machine scale sets in Windows. Disable encryption is also supported for OS and data volumes for virtual machine scale sets in Windows.
* Le operazioni di riimageee e aggiornamento della macchina virtuale per i set di scalabilità delle macchine virtuali non sono supportate nell'anteprima corrente.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Creare un nuovo cluster e abilitare la crittografia del discoCreate a new cluster and enable disk encryption

Usare i comandi seguenti per creare un cluster e abilitare la crittografia dei dischi tramite un modello di Azure Resource Manager e un certificato autofirmato.

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

Se è necessario creare un modello personalizzato in base alle proprie esigenze, è consigliabile iniziare con uno dei modelli disponibili nella pagina degli esempi di modelli di creazione cluster di Azure Service Fabric.If you need to author a custom template to suit your needs, we recommend that you start with one of the templates that are available on the [Azure Service Fabric cluster creation template samples.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) Per personalizzare la sezione del [modello di cluster,][customize-your-cluster-template] vedere le indicazioni seguenti.

Se si dispone già di un modello personalizzato, verificare che tutti e tre i parametri relativi ai certificati nel modello e nel file dei parametri siano denominati come segue e che i valori siano null come indicato di seguito:

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Distribuire un'applicazione in un cluster di Service Fabric in WindowsDeploy an application to a Service Fabric cluster in Windows
Per distribuire un'applicazione nel cluster, seguire i passaggi e le istruzioni in [Distribuire e rimuovere applicazioni tramite PowerShell](service-fabric-deploy-remove-applications.md).


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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Verificare se la crittografia del disco è abilitata per un set di scalabilità di macchine virtuali in WindowsValidate if disk encryption is enabled for a virtual machine scale set in Windows
Ottenere lo stato di un intero set di scalabilità della macchina virtuale o di qualsiasi istanza in un set di scalabilità eseguendo i comandi seguenti.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Inoltre, è possibile accedere al set di scalabilità della macchina virtuale e assicurarsi che le unità siano crittografate.

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
A questo punto, è necessario disporre di un cluster sicuro e sapere come abilitare e disabilitare la crittografia del disco per i nodi del cluster di Service Fabric e i set di scalabilità di macchine virtuali. Per indicazioni simili sui nodi del cluster di Service Fabric in Linux, vedere [Crittografia del disco per Linux.](service-fabric-enable-azure-disk-encryption-linux.md)

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
