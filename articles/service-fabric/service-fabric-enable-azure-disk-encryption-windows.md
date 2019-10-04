---
title: Abilitare la crittografia del disco per i cluster di Azure Service Fabric Windows | Microsoft Docs
description: Questo articolo descrive come abilitare la crittografia del disco per i nodi del cluster di Azure Service Fabric usando Azure Key Vault in Azure Resource Manager.
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
ms.openlocfilehash: 64abc48d57196fe20466032652c4b9bfb2e6c71f
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599539"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Abilitare la crittografia del disco per i nodi del cluster Service Fabric di Azure in Windows 
> [!div class="op_single_selector"]
> * [Crittografia del disco per Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Crittografia del disco per Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Questa esercitazione illustra come abilitare la crittografia del disco nei nodi Service Fabric cluster in Windows. È necessario seguire questa procedura per ogni tipo di nodo e set di scalabilità di macchine virtuali. Per la crittografia dei nodi, verrà usata la funzionalità di crittografia dischi di Azure nei set di scalabilità di macchine virtuali.

La guida descrive gli argomenti seguenti:

* Concetti chiave da tenere presente quando si Abilita la crittografia del disco nei set di scalabilità di macchine virtuali di Service Fabric cluster in Windows.
* Passaggi da seguire prima di abilitare la crittografia del disco nei nodi Service Fabric cluster in Windows.
* Passaggi da seguire per abilitare la crittografia del disco nei nodi Service Fabric cluster in Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

**Registrazione automatica** 

L'anteprima della crittografia del disco per il set di scalabilità di macchine virtuali richiede la registrazione automatica. Seguire questa procedura: 

1. Eseguire prima di tutto il comando seguente:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Attendere circa 10 minuti fino a quando non viene eseguita la lettura dello stato *registrato*. È possibile controllare lo stato eseguendo il comando seguente: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Insieme di credenziali chiave Azure** 

1. Creare un insieme di credenziali delle chiavi nella stessa sottoscrizione e nella stessa area del set di scalabilità, quindi selezionare il criterio di accesso **EnabledForDiskEncryption** nell'insieme di credenziali delle chiavi usando il relativo cmdlet di PowerShell. È anche possibile impostare il criterio usando l'interfaccia utente di Key Vault nel portale di Azure con il comando seguente:
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
* La crittografia è supportata per i volumi del sistema operativo e dei dati nei set di scalabilità di macchine virtuali in Windows. Disabilitare la crittografia è supportata anche per i volumi di dati e del sistema operativo per i set di scalabilità di macchine virtuali in Windows.
* Le operazioni di ricreazione dell'immagine e aggiornamento della macchina virtuale per i set di scalabilità di macchine virtuali non sono supportate nell'anteprima corrente.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Creare un nuovo cluster e abilitare la crittografia del disco

Usare i comandi seguenti per creare un cluster e abilitare la crittografia dei dischi tramite un modello di Azure Resource Manager e un certificato autofirmato.

### <a name="sign-in-to-azure"></a>Accedi ad Azure 
Accedere con i comandi seguenti:
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Usare il modello personalizzato già disponibile 

Se è necessario creare un modello personalizzato in base alle proprie esigenze, è consigliabile iniziare con uno dei modelli disponibili nella pagina degli [esempi del modello di creazione del cluster di Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) . Per [personalizzare la][customize-your-cluster-template] sezione del modello di cluster, vedere le indicazioni seguenti.

Se si dispone già di un modello personalizzato, verificare che tutti e tre i parametri correlati al certificato nel modello e il file dei parametri siano denominati come segue e che i valori siano null come indicato di seguito:

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Distribuire un'applicazione in un cluster Service Fabric in Windows
Per distribuire un'applicazione nel cluster, seguire i passaggi e le linee guida disponibili in [distribuire e rimuovere applicazioni con PowerShell](service-fabric-deploy-remove-applications.md).


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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Verificare se la crittografia del disco è abilitata per un set di scalabilità di macchine virtuali in Windows
Ottenere lo stato di un intero set di scalabilità di macchine virtuali o di qualsiasi istanza in un set di scalabilità eseguendo i comandi seguenti.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Inoltre, è possibile accedere al set di scalabilità di macchine virtuali e verificare che le unità siano crittografate.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Disabilitare la crittografia del disco per un set di scalabilità di macchine virtuali in un cluster Service Fabric 
Disabilitare la crittografia del disco per un set di scalabilità di macchine virtuali eseguendo i comandi seguenti. Si noti che la disabilitazione della crittografia del disco si applica all'intero set di scalabilità di macchine virtuali e non a una singola istanza.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Passaggi successivi
A questo punto, è necessario disporre di un cluster protetto e informazioni su come abilitare e disabilitare la crittografia del disco per i nodi del cluster Service Fabric e i set di scalabilità di macchine virtuali. Per istruzioni analoghe sui nodi del cluster Service Fabric in Linux, vedere [crittografia del disco per Linux](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
