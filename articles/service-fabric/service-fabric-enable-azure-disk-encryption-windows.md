---
title: Abilitare la crittografia del disco per i cluster Azure Service Fabric Windows | Microsoft Docs
description: In questo articolo viene descritto come abilitare la crittografia del disco per i nodi del cluster di Service Fabric in Azure tramite Azure Resource Manager e Azure Key Vault.
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
ms.openlocfilehash: 2e9c41409c1f528947e3bef281e9a3c34da39e9b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050443"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Abilitare la crittografia del disco per i nodi del cluster Windows di Service Fabric 
> [!div class="op_single_selector"]
> * [Crittografia del disco per Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Crittografia del disco per Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Procedere come segue per abilitare la crittografia del disco sui nodi del cluster Windows di Service Fabric. È necessario eseguire le operazioni seguenti per ogni tipo di nodo/set di scalabilità di macchine virtuali. Per crittografare i nodi, verrà usata la funzionalità Crittografia dischi di Azure nel set di scalabilità di macchine virtuali.

La guida illustra le procedure seguenti:

* Concetti principali da tenere in considerazione per abilitare la crittografia del disco nel set di scalabilità di macchine virtuali del cluster Windows di Service Fabric.
* Procedure da eseguire come prerequisiti prima di abilitare la crittografia del disco nel set di scalabilità di macchine virtuali del cluster Windows di Service Fabric.
* Procedure da eseguire per abilitare la crittografia del disco nel set di scalabilità di macchine virtuali del cluster Windows di Service Fabric.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti
* **Registrazione automatica**: per usare l'anteprima di Crittografia dischi del set di scalabilità di macchine virtuali, è necessaria la registrazione automatica
* È possibile eseguire la registrazione automatica della sottoscrizione procedendo nel modo seguente: 
```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* Attendere circa 10 minuti finché lo stato risulta "Registrato". È possibile controllare lo stato con il comando seguente: 
```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Azure Key Vault**: creare un insieme di credenziali delle chiavi nella stessa sottoscrizione e nella stessa area del set di scalabilità e impostare il criterio di accesso "EnabledForDiskEncryption" sull'insieme di credenziali delle chiavi usando il relativo cmdlet PS. È anche possibile impostare i criteri tramite l'interfaccia utente per l'insieme di credenziali delle chiavi nel portale di Azure: 
```powershell
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* Installare una versione più recente [CLI Azure](/cli/azure/install-azure-cli) , che include i nuovi comandi di crittografia.
* Installare la versione più recente di [Azure SDK da Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Di seguito sono indicati i cmdlet ADE del set di scalabilità di macchine virtuali per abilitare ([Set](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) la crittografia, recuperare ([Get](/powershell/module/az.compute/get-azvmssvmdiskencryption)) lo stato di crittografia e rimuovere ([disable](/powershell/module/az.compute/disable-azvmssdiskencryption)) la crittografia nell'istanza del set di scalabilità.

| Comando | Versione |  Source (Sorgente)  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 o versione successiva | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 o versione successiva | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 o versione successiva | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 o versione successiva | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 o versione successiva | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 o versione successiva | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scenari supportati per la crittografia dei dischi
* La crittografia del set di scalabilità di macchine virtuali è supportata solo per i set di scalabilità creati con dischi gestiti. Non è tuttavia supportata per i set di scalabilità di dischi nativi (o non gestiti).
* Virtual machine scale set encryption è supportata per i volumi del sistema operativo e i dati per set di scalabilità di macchine virtuali di Windows. La disabilitazione della crittografia è supportata per volumi di dati e sistema operativo nel set di scalabilità Windows.
* Ricreazione dell'immagine della macchina virtuale del set di scalabilità di macchine virtuali e le operazioni di aggiornamento non sono supportate in fase di anteprima corrente.


### <a name="create-new-cluster-and-enable-disk-encryption"></a>Creare un nuovo cluster e abilitare la crittografia dei dischi

Usare i comandi seguenti per creare cluster e abilitare la crittografia del disco usando il modello di Azure Resource Manager e certificato autofirmato.

### <a name="sign-in-to-azure"></a>Accedi ad Azure 

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Usare il modello personalizzato già disponibile 

Se è necessario creare un modello personalizzato specifico per le proprie esigenze, è consigliabile iniziare con uno di quelli disponibili tra gli [esempi di modelli di Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Attenersi alle indicazioni e alle spiegazioni per [personalizzare il modello di cluster][customize-your-cluster-template] riportate nella sezione seguente.

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

#### <a name="deploy-application-to-windows-service-fabric-cluster"></a>Distribuire l'applicazione in un cluster Windows di Service Fabric
Seguire le procedure e le indicazioni per [distribuire l'applicazione nel cluster](service-fabric-deploy-remove-applications.md)


#### <a name="enable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set-created-above"></a>Abilitare la crittografia dei dischi per il set di scalabilità di macchine virtuali del cluster di Service Fabric creato in precedenza
 
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


#### <a name="validate-if-disk-encryption-enabled-for-windows-virtual-machine-scale-set"></a>Verificare se la crittografia dei dischi è abilitata per il set di scalabilità di macchine virtuali Windows.
Ottenere lo stato di un intero set di scalabilità di macchine virtuali o di qualsiasi istanza nel set di scalabilità. Vedere i comandi seguenti.
Inoltre l'utente può accedere alla macchina virtuale nel set di scalabilità e assicurarsi che le unità vengono crittografate

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Disabilitare la crittografia dei dischi per il set di scalabilità di macchine virtuali del cluster di Service Fabric 
La disabilitazione della crittografia dei dischi si applica all'intero set di scalabilità di macchine virtuali e non alle singole istanze 

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Passaggi successivi
A questo punto, si dispone di un cluster protetto, con la possibilità di abilitare o disabilitare la crittografia dei dischi per il set di scalabilità di macchine virtuali del cluster di Service Fabric. L'argomento successivo è [Crittografia dischi per Linux](service-fabric-enable-azure-disk-encryption-linux.md) 

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
