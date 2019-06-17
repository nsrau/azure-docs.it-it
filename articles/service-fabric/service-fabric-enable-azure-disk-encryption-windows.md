---
title: Abilitare la crittografia del disco per i cluster Azure Service Fabric Windows | Microsoft Docs
description: Questo articolo descrive come abilitare la crittografia del disco per i nodi del cluster Azure Service Fabric tramite Azure Key Vault in Azure Resource Manager.
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
ms.openlocfilehash: c31fc43729bcb58c755959db0c8bc5185b8197f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66471407"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Abilitare la crittografia del disco per i nodi del cluster di Azure Service Fabric in Windows 
> [!div class="op_single_selector"]
> * [Crittografia del disco per Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Crittografia del disco per Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

In questa esercitazione si apprenderà come abilitare la crittografia del disco sui nodi del cluster di Service Fabric in Windows. È necessario seguire questa procedura per ciascuno dei tipi di nodo e set di scalabilità di macchine virtuali. Per crittografare i nodi, si userà la funzionalità crittografia dischi di Azure nei set di scalabilità di macchine virtuali.

La Guida include gli argomenti seguenti:

* Concetti chiave da tenere presenti quando imposta di abilitare la crittografia del disco nella scalabilità di macchine virtuali del cluster di Service Fabric in Windows.
* Procedura da seguire prima di abilitare la crittografia del disco in Service Fabric cluster i nodi Windows.
* Passaggi da seguire per abilitare la crittografia del disco sui nodi del cluster di Service Fabric in Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

**Registrazione automatica** 

L'anteprima di crittografia del disco per il set di scalabilità di macchine virtuali richiede la registrazione automatica. Seguire questa procedura: 

1. In primo luogo, eseguire il comando seguente:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Attendere circa 10 minuti fino a quando lo stato è indicato *Registered*. È possibile controllare lo stato eseguendo il comando seguente: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Insieme di credenziali chiave Azure** 

1. Creare un insieme di credenziali delle chiavi nella stessa sottoscrizione e area del set di scalabilità, quindi selezionare il **EnabledForDiskEncryption** criterio nell'insieme di credenziali chiave di accesso usando il cmdlet di PowerShell. È anche possibile impostare i criteri usando l'interfaccia utente dell'insieme di credenziali chiave nel portale di Azure con il comando seguente:
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
* La crittografia è supportata per il sistema operativo e imposta i volumi di dati di scalabilità di macchine virtuali in Windows. Disabilitare la crittografia è supportata anche per il sistema operativo e imposta i volumi di dati per la scalabilità di macchine virtuali in Windows.
* Operazioni di aggiornamento e di ricreazione dell'immagine della macchina virtuale per set di scalabilità di macchine virtuali non sono supportate nell'anteprima corrente.


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

Se è necessario creare un modello personalizzato in base alle esigenze, è consigliabile iniziare con uno dei modelli disponibili nel [esempi di modelli di Azure Service Fabric cluster creazione](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) pagina. Per [personalizzare il modello di cluster] [ customize-your-cluster-template] sezione, vedere il materiale sussidiario seguente.

Se si dispone già di un modello personalizzato, è necessario accertarsi che tutti i tre parametri di relative ai certificati nel modello e il file dei parametri siano denominati come segue e che i valori siano null come indicato di seguito:

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Distribuire un'applicazione in un cluster di Service Fabric in Windows
Per distribuire un'applicazione nel cluster, seguire i passaggi e le indicazioni fornite in [distribuire e rimuovere applicazioni con PowerShell](service-fabric-deploy-remove-applications.md).


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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>La convalida se crittografia dischi è abilitata per la scalabilità di macchine virtuali impostato in Windows
Ottenere lo stato di un set di scalabilità di macchina virtuale intera o qualsiasi istanza in un set eseguendo i comandi seguenti di scalabilità.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Inoltre, è possibile accedere al set di scalabilità di macchine virtuali e verificare che le unità vengono crittografate.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Disabilitare la crittografia del disco per la scalabilità di macchine virtuali impostato in un cluster di Service Fabric 
Disabilitare la crittografia del disco per la scalabilità di macchine virtuali impostare eseguendo i comandi seguenti. Si noti che la disabilitazione della crittografia del disco sono valide per il set di scalabilità di intera macchina virtuale e non una singola istanza.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Passaggi successivi
A questo punto, si deve avere un cluster sicuro e sapere come abilitare e disabilitare la crittografia del disco per i nodi del cluster Service Fabric e set di scalabilità di macchine virtuali. Per indicazioni simili nei nodi di cluster di Service Fabric in Linux, vedere [crittografia dischi per Linux](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
