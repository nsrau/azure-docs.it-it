---
title: Abilitare Crittografia dischi di Azure per le macchine virtuali IaaS Linux
description: Questo articolo offre informazioni sull'abilitazione di Crittografia dischi di Microsoft Azure per le macchine virtuali IaaS Linux.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 12/17/2018
ms.custom: seodec18
ms.openlocfilehash: 608cc7a9e7c3b09c4b033397cbae6ac68e0a503a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478441"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms"></a>Abilitare Crittografia dischi di Azure per le macchine virtuali IaaS Linux 

È possibile abilitare molti scenari di crittografia dei dischi e la procedura può variare in base allo scenario. Le sezioni seguenti illustrano in modo più dettagliato gli scenari per le macchine virtuali IaaS Linux. Prima di poter usare la crittografia dischi, è necessario soddisfare i [prerequisiti di Crittografia dischi di Azure](azure-security-disk-encryption-prerequisites.md) e consultare la sezione [Additional prerequisites for Linux IaaS VMs](azure-security-disk-encryption-prerequisites.md#bkmk_LinuxPrereq) (Altri prerequisiti per le macchine virtuali IaaS Linux).

Catturare uno [snapshot](../virtual-machines/windows/snapshot-copy-managed-disk.md) e/o eseguire il backup prima che i dischi vengono crittografati. I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Le macchine virtuali con dischi gestiti richiedono il backup prima della crittografia. Dopo aver eseguito il backup, è possibile usare il cmdlet Set-AzureRmVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro -skipVmBackup. Per altre informazioni su come eseguire il backup e il ripristino di macchine virtuali crittografate, vedere l'articolo [Backup di Azure](../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
> - Se in precedenza è stata usata la [Crittografia dischi di Azure con l'app Azure AD](azure-security-disk-encryption-prerequisites-aad.md) per crittografare la macchina virtuale, sarà necessario continuare a usare questa opzione per crittografare la macchina virtuale. Non è possibile usare la [Crittografia dischi di Azure](azure-security-disk-encryption-prerequisites.md) in questa macchina virtuale crittografata poiché non è uno scenario supportato ovvero non è ancora supportato il trasferimento dall'applicazione AAD per questa macchina virtuale.
 > - Per Crittografia dischi di Azure è necessario che l'insieme di credenziali delle chiavi e le macchine virtuali si trovino nella stessa area. Creare e usare un insieme di credenziali delle chiavi nella stessa area della macchina virtuale da crittografare.
> - Durante la crittografia dei volumi del sistema operativo Linux, la macchina virtuale sarà non disponibile e SSH verrà disabilitato. Per controllare lo stato di avanzamento, si possono usare i comandi [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) oppure [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). Questo processo può richiedere alcune ore per un volume di sistema operativo da 30 GB, più un tempo aggiuntivo per la crittografia dei volumi di dati. Il tempo per la crittografia del volume di dati è proporzionale alla dimensione e quantità dei volumi di dati a meno che non venga usata l'opzione "encrypt format all". 
> - La disabilitazione della crittografia nelle macchine virtuali Linux è supportata solo per i volumi di dati. Non è supportata nei dati o nei volumi del sistema operativo, se il volume del sistema operativo è stato crittografato.  


## <a name="bkmk_RunningLinux"> </a> Abilitare la crittografia in una macchina virtuale IaaS Linux esistente o in esecuzione
In questo scenario è possibile abilitare la crittografia usando il modello di Resource Manager, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. Per informazioni sullo schema dell'estensione della macchina virtuale, leggere l'articolo [Crittografia dischi di Azure per Linux](../virtual-machines/extensions/azure-disk-enc-linux.md).

>[!IMPORTANT]
 >È obbligatorio catturare uno snapshot e/o eseguire il backup di un'istanza di macchina virtuale basata su un disco gestito all'esterno di Crittografia dischi di Azure e prima di abilitarla. È possibile catturare uno snapshot del disco gestito dal portale o tramite [Backup di Azure](../backup/backup-azure-vms-encryption.md). I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Dopo aver eseguito un backup, è possibile usare il cmdlet Set-AzureRmVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro -skipVmBackup. Il comando Set-AzureRmVMDiskEncryptionExtension ha esito negativo su una macchina virtuale basata su un disco gestito finché non viene eseguito un backup e non viene specificato il parametro. 
>
>La crittografia o la disabilitazione della crittografia può causare il riavvio della macchina virtuale. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Abilitare la crittografia in una macchina virtuale Linux esistente o in esecuzione usando l'interfaccia della riga di comando di Azure 
È possibile abilitare la crittografia dischi nel disco rigido virtuale crittografato installando e usando lo strumento da riga di comando [Azure CLI 2.0](/cli/azure). È possibile usarlo nel browser con [Azure Cloud Shell](../cloud-shell/overview.md) oppure installarlo nel computer locale e usarlo in una sessione di PowerShell. Per abilitare la crittografia in macchine virtuali IaaS Linux esistenti o in esecuzione in Azure, usare i comandi seguenti dell'interfaccia della riga di comando:

Usare il comando [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) per abilitare la crittografia in una macchina virtuale IaaS in esecuzione in Azure.

-  **Crittografare una macchina virtuale in esecuzione:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Crittografare una macchina virtuale in esecuzione usando una chiave di crittografia della chiave:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa identificatore completa: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
La sintassi per il valore del parametro key-encryption-key è l'URI della chiave di crittografia della chiave, come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verificare che i dischi siano crittografati:** per controllare lo stato della crittografia di una macchina virtuale IaaS, usare il comando [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Disabilitare la crittografia:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). È possibile disabilitare la crittografia solo nei volumi di dati per le macchine virtuali Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a> Abilitare la crittografia in una macchina virtuale Linux esistente o in esecuzione usando PowerShell
Usare il cmdlet [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) per abilitare la crittografia in una macchina virtuale IaaS in esecuzione in Azure. 

-  **Crittografare una macchina virtuale in esecuzione:** lo script di seguito inizializza le variabili ed esegue il cmdlet Set-AzureRmVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale e l'insieme di credenziali delle chiavi dovrebbero essere già stati creati come prerequisiti. Sostituire MySecureRg, MySecureVM e MySecureVault con i propri valori. Se si esegue la crittografia dei dischi dati e non del disco del sistema operativo, può essere necessario aggiungere il parametro -VolumeType. 

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Crittografare una macchina virtuale in esecuzione usando una chiave di crittografia della chiave:** Se si esegue la crittografia dei dischi dati e non del disco del sistema operativo, può essere necessario aggiungere il parametro -VolumeType. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

    >[!NOTE]
    > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa identificatore completa: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> La sintassi per il valore del parametro key-encryption-key è l'URI della chiave di crittografia della chiave, come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Verificare che i dischi siano crittografati:** per controllare lo stato della crittografia di una macchina virtuale IaaS, usare il cmdlet [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus). 
    
     ```azurepowershell-interactive 
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName MySecureRg -VMName MySecureVM
     ```
    
- **Disabilitare la crittografia del disco:** per disabilitare la crittografia, usare il cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). È possibile disabilitare la crittografia solo nei volumi di dati per le macchine virtuali Linux.
     
     ```azurepowershell-interactive 
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Abilitare la crittografia in una macchina virtuale IaaS Linux esistente o in esecuzione con un modello

È possibile abilitare la crittografia dei dischi nelle macchine virtuali IaaS Linux esistenti o in esecuzione usando il modello di [Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Fare clic su **Deploy to Azure** nel modello di avvio rapido di Azure.

2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, i parametri, i termini legali e il contratto. Fare clic su **Crea** per abilitare la crittografia in una macchina IaaS esistente o in esecuzione.

La tabella seguente elenca i parametri del modello di Resource Manager per macchine virtuali esistenti o in esecuzione:

| Parametro | DESCRIZIONE |
| --- | --- |
| vmName | Nome della macchina virtuale per eseguire l'operazione di crittografia. |
| keyVaultName | Nome dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave BitLocker. È possibile ottenerlo usando il cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <MyResourceGroupName>). Vaultname` o il comando dell'interfaccia della riga di comando di Azure `az keyvault list --resource-group "MySecureGroup" |ConvertFrom-JSON`|
| keyVaultResourceGroup | Nome del gruppo di risorse che contiene l'insieme di credenziali delle chiavi.|
|  keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave BitLocker generata. Questo parametro è facoltativo se si seleziona **nokek** dall'elenco a discesa UseExistingKek. Se si seleziona **kek** dall'elenco a discesa UseExistingKek, è necessario immettere il valore _keyEncryptionKeyURL_. |
| volumeType | Tipo del volume in cui viene eseguita l'operazione di crittografia. I valori validi sono _OS_, _Data_ e _All_. 
| forceUpdateTag | Ogni volta che è necessario forzare l'esecuzione dell'operazione, passare un valore univoco, ad esempio un GUID. |
| resizeOSDisk | La partizione del sistema operativo deve essere ridimensionata in modo da occupare il disco rigido virtuale completo del sistema operativo prima della divisione del volume di sistema. |
| location | Posizione per tutte le risorse. |



## <a name="encrypt-virtual-machine-scale-sets"></a>Crittografare i set di scalabilità di macchine virtuali
I [set di scalabilità di macchine virtuali di Azure](../virtual-machine-scale-sets/overview.md) consentono di creare e gestire un gruppo di macchine virtuali identiche con bilanciamento del carico. Il numero di istanze di macchine virtuali può aumentare o diminuire automaticamente in risposta alla domanda o a una pianificazione definita. Per crittografare i set di scalabilità di macchine virtuali, usare l'interfaccia della riga di comando o Azure PowerShell.

Un esempio di file batch per la crittografia dei dischi dati del set di scalabilità di Linux è disponibile [qui](https://github.com/Azure-Samples/azure-cli-samples/tree/master/disk-encryption/vmss). In questo esempio vengono creati un gruppo di risorse e il set di scalabilità di Linux, viene montato un disco dati di 5 GB e viene crittografato il set di scalabilità di macchine virtuali.

### <a name="register-for-disk-encryption-preview-using-azure-cli"></a>Eseguire la registrazione per l'anteprima di Crittografia dischi tramite l'interfaccia della riga di comando di Azure

Per usare l'anteprima di Crittografia dischi di Azure per i set di scalabilità di macchine virtuali è necessario eseguire la registrazione automatica della sottoscrizione con il comando [az feature register](/cli/azure/feature#az-feature-register). È sufficiente eseguire la procedura seguente la prima volta che si usa la funzionalità di anteprima per la crittografia dei dischi:

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

Possono essere necessari fino a 10 minuti per la propagazione della richiesta di registrazione. È possibile verificare lo stato della registrazione con il comando [az feature show](/cli/azure/feature#az-feature-show). Quando `State` indica il valore *Registered*, registrare nuovamente il provider *Microsoft.Compute* con il comando [az provider register](/cli/azure/provider#az-provider-register):

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

###  <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Crittografare i set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure
Usare [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable) per abilitare la crittografia in un set di scalabilità di macchine virtuali Windows. Se nel set di scalabilità si impostano criteri di aggiornamento manuali, avviare la crittografia con [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). Il gruppo di risorse, la macchina virtuale e l'insieme di credenziali delle chiavi dovrebbero essere già stati creati come prerequisiti. 

-  **Crittografare un set di scalabilità di macchine virtuali in esecuzione**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **Crittografare un set di scalabilità di macchine virtuali in esecuzione usando una chiave di crittografia della chiave per eseguire il wrapping della chiave**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
- **Ottenere lo stato della crittografia per un set di scalabilità di macchine virtuali:** usare [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
     az vmss encryption show --resource-group "MySecureRG" --name "MySecureVmss"
    ```

- **Disabilitare la crittografia su un set di scalabilità di macchine virtuali**: usare [az vmss encryption disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MySecureRG" --name "MySecureVmss"
    ```

### <a name="register-for-disk-encryption-preview-using-azure-powershell"></a>Eseguire la registrazione per l'anteprima di Crittografia dischi tramite Azure PowerShell

Per usare l'anteprima di Crittografia dischi di Azure per i set di scalabilità di macchine virtuali è necessario eseguire la registrazione automatica della sottoscrizione con [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature). È sufficiente eseguire la procedura seguente la prima volta che si usa la funzionalità in anteprima di crittografia dei dischi:

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Possono essere necessari fino a 10 minuti per la propagazione della richiesta di registrazione. È possibile controllare lo stato di registrazione con [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Quando `RegistrationState` indica *Registered*, registrare nuovamente il provider *Microsoft.Compute* con [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Crittografare i set di scalabilità di macchine virtuali con Azure PowerShell

Usare il cmdlet [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension) per abilitare la crittografia su un set di scalabilità di macchine virtuali Windows. Il gruppo di risorse, la macchina virtuale e l'insieme di credenziali delle chiavi dovrebbero essere già stati creati come prerequisiti.

-  **Crittografare un set di scalabilità di macchine virtuali in esecuzione**:
    ```powershell
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```

-  **Crittografare un set di scalabilità di macchine virtuali in esecuzione usando una chiave di crittografia della chiave (KEK) per eseguire il wrapping della chiave**:
    ```powershell
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

- **Ottenere lo stato della crittografia per un set di scalabilità di macchine virtuali:** usare il cmdlet [Get-AzureRmVmssVMDiskEncryption](/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption).
    
    ```powershell
    get-AzureRmVmssVMDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

- **Disabilitare la crittografia su un set di scalabilità di macchine virtuali**: usare il cmdlet [Disable-AzureRmVmssDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption). 

    ```powershell
    Disable-AzureRmVmssDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-linux-virtual-machine-scale-sets"></a>Modelli di Azure Resource Manager per set di scalabilità di macchine virtuali Linux

Per crittografare o decrittografare set di scalabilità di macchine virtuali Linux, usare i modelli di Azure Resource Manager e le istruzioni di seguito:

- [Abilitare la crittografia in un set di scalabilità di macchine virtuali Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)
- [Distribuire un set di scalabilità di macchine virtuali Linux con una jumpbox e abilitare la crittografia nel set di scalabilità](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)
- [Disabilitare la crittografia in un set di scalabilità di macchine virtuali Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

     1. Fare clic su **Distribuzione in Azure**.
     2. Compilare i campi obbligatori quindi accettare i termini e le condizioni.
     3. Fare clic su **Acquista** per distribuire il modello.

## <a name="bkmk_EFA"> </a>Usare la funzione EncryptFormatAll per i dischi dati nelle macchine virtuali IaaS Linux
Il parametro **EncryptFormatAll** riduce la durata per la crittografia dei dischi dati Linux. Le partizioni che soddisfano determinati criteri verranno formattate (con il file system corrente). Successivamente verranno rimontate nella posizione in cui si trovavano prima dell'esecuzione del comando. Se si desidera escludere un disco dati che soddisfa i criteri, è possibile smontarlo prima di eseguire il comando.

 Dopo aver eseguito questo comando, tutti i dischi precedentemente montati verranno formattati. In seguito verrà avviato il livello di crittografia sul disco ora vuoto. Quando questa opzione è selezionata, viene crittografato anche il disco risorse temporaneo collegato alla macchina virtuale. Se l'unità temporanea viene reimpostata, viene riformattata e crittografata di nuovo per la macchina virtuale dalla soluzione Crittografia dischi di Azure alla successiva opportunità.

>[!WARNING]
> EncryptFormatAll non deve essere usato quando i volumi di dati di una macchina virtuale contengono dati necessari. Per escludere i dischi dalla crittografia, è possibile smontarli. È innanzitutto necessario provare EncryptFormatAll in una macchina virtuale di test, poi comprendere il parametro della funzione e la sua implicazione prima di provarlo nella macchina virtuale di produzione. L'opzione EncryptFormatAll formatta il disco dati e tutti i dati che contiene andranno persi. Prima di procedere, verificare che i dischi da escludere siano smontati correttamente. </br></br>
 >Se si imposta questo parametro durante l'aggiornamento delle impostazioni di crittografia, si potrebbe verificare un riavvio prima che la crittografia venga effettivamente eseguita. In questo caso, è anche possibile rimuovere dal file fstab il disco da non formattare. Analogamente, è necessario aggiungere al file fstab la partizione che si desidera formattare con crittografia e poi avviare l'operazione di crittografia. 

### <a name="bkmk_EFACriteria"> </a> Criteri EncryptFormatAll
Il parametro esamina tutte le partizioni e le crittografa, a patto che soddisfino **tutti** i criteri seguenti: 
- Non è una partizione di avvio/del sistema operativo/radice
- Non è già stata crittografata
- Non è un volume BEK
- Non è un volume RAID
- Non è un volume LVM
- È montata

Crittografare i dischi che compongono il volume RAID o LVM anziché il volume stesso.

### <a name="bkmk_EFAPSH"> </a> Usare il parametro EncryptFormatAll con l'interfaccia della riga di comando di Azure
Usare il comando [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) per abilitare la crittografia in una macchina virtuale IaaS in esecuzione in Azure.

-  **Crittografare una macchina virtuale in esecuzione usando EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="bkmk_EFAPSH"> </a> Usare il parametro EncryptFormatAll con un cmdlet di PowerShell
Usare il cmdlet [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) con il [parametro EncryptFormatAll](https://www.powershellgallery.com/packages/AzureRM/5.0.0). 

**Crittografare una macchina virtuale in esecuzione usando EncryptFormatAll:** ad esempio, lo script di seguito inizializza le variabili ed esegue il cmdlet Set-AzureRmVMDiskEncryptionExtension con il parametro EncryptFormatAll. Il gruppo di risorse, la macchina virtuale e l'insieme di credenziali delle chiavi dovrebbero essere già stati creati come prerequisiti. Sostituire MySecureRg, MySecureVM e MySecureVault con i propri valori.
  
   ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MySecureVM';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="bkmk_EFALVM"> </a> Usare il parametro EncryptFormatAll con Logical Volume Manager (LVM) 
È consigliabile usare una configurazione LVM-on-crypt. Per tutti gli esempi seguenti, sostituire il percorso del dispositivo e i punti di montaggio con qualunque elemento sia appropriato al caso d'uso. La configurazione può essere eseguita nel seguente modo:

- Aggiungere i dischi dati che compongono la macchina virtuale.
- Formattare, montare e aggiungere i dischi al file fstab.

    1. Formattare il disco appena aggiunto. Qui vengono usati i collegamenti simbolici generati da Azure. L'uso di collegamenti simbolici evita i problemi legati alla modifica dei nomi dei dispositivi. Per altre informazioni, vedere l'articolo [Risolvere il problema dei nomi di dispositivo](../virtual-machines/linux/troubleshoot-device-names-problems.md).
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Montare i dischi.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Aggiungerli al file fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Eseguire il cmdlet Set-AzureRmVMDiskEncryptionExtension di PowerShell con -EncryptFormatAll per crittografare i dischi.
         ```azurepowershell-interactive
         Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Configurare LVM sui nuovi dischi. Si noti che le unità crittografate vengono sbloccate dopo il termine dell'avvio della macchina virtuale. Pertanto, anche il montaggio di LVM dovrà essere posticipato.


## <a name="bkmk_VHDpre"> </a> Nuove macchine virtuali IaaS create da chiavi di crittografia e disco rigido virtuale crittografato dal cliente
In questo scenario è possibile abilitare la crittografia usando i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. 

Usare le istruzioni nell'appendice per la preparazione delle immagini pre-crittografate che possono essere usate in Azure. Dopo aver creato l'immagine, è possibile usare i passaggi della sezione successiva per creare una VM di Azure crittografata.

* [Preparare un disco rigido virtuale Windows pre-crittografato](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Preparare un disco rigido virtuale Linux pre-crittografato](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >È obbligatorio catturare uno snapshot e/o eseguire il backup di un'istanza di macchina virtuale basata su un disco gestito all'esterno di Crittografia dischi di Azure e prima di abilitarla. È possibile catturare uno snapshot del disco gestito dal portale o tramite [Backup di Azure](../backup/backup-azure-vms-encryption.md). I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Dopo aver eseguito un backup, è possibile usare il cmdlet Set-AzureRmVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro -skipVmBackup. Il comando Set-AzureRmVMDiskEncryptionExtension ha esito negativo su una macchina virtuale basata su un disco gestito finché non viene eseguito un backup e non viene specificato il parametro. 
>
>La crittografia o la disabilitazione della crittografia può causare il riavvio della macchina virtuale. 



### <a name="bkmk_VHDprePSH"> </a> Usare Azure PowerShell per crittografare le macchine virtuali IaaS con dischi rigidi virtuali pre-crittografati 
È possibile abilitare la crittografia dei dischi nel disco rigido virtuale crittografato usando il cmdlet [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples) di PowerShell. L'esempio seguente offre alcuni parametri comuni. 

```azurepowershell-interactive
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName "MySecureRG"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Abilitare la crittografia in un disco dati appena aggiunto

È possibile aggiungere un nuovo disco dati usando [az vm disk attach](../virtual-machines/linux/add-disk.md) o [tramite il portale di Azure](../virtual-machines/linux/attach-disk-portal.md). Prima che sia possibile eseguire la crittografia, è necessario montare il disco appena collegato. È necessario richiedere la crittografia del disco dati poiché l'unità non sarà utilizzabile mentre la crittografia è in corso. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Abilitare la crittografia in un disco appena aggiunto con l'interfaccia della riga di comando di Azure

 Se la macchina virtuale è stata precedentemente crittografata con "All", il parametro --volume-type deve rimanere All. Tale parametro include il disco del sistema operativo e il disco dati. Se la macchina virtuale è stata precedentemente crittografata con il tipo di volume "OS", impostare il parametro --volume-type su All, in modo che vengano inclusi sia il disco del sistema operativo che il nuovo disco dati. Se la macchina virtuale è stata crittografata solo con il tipo di volume "Data", è possibile lasciare invariato il parametro "Data", come illustrato di seguito. L'aggiunta e il collegamento di un nuovo disco dati a una macchina virtuale non sono condizioni sufficienti alla preparazione per la crittografia. Il disco appena collegato deve anche essere formattato e montato in modo corretto nella macchina virtuale prima dell'abilitazione della crittografia. In Linux il disco deve essere montato in /etc/fstab con un [nome del dispositivo a blocchi permanente](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems).  

A differenza della sintassi di PowerShell, l'interfaccia della riga di comando non richiede all'utente di specificare una versione di sequenza univoca quando si abilita la crittografia. L'interfaccia della riga di comando genera e usa automaticamente uno specifico valore di versione di sequenza univoco.

-  **Crittografare i volumi di dati di una macchina virtuale in esecuzione:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Crittografare i volumi di dati di una macchina virtuale in esecuzione tramite una chiave di crittografia della chiave:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Abilitare la crittografia in un disco appena aggiunto con Azure PowerShell
 Quando si usa PowerShell per crittografare un nuovo disco per Linux, è necessario specificare una nuova versione della sequenza. La versione della sequenza deve essere univoca. Lo script seguente genera un GUID per la versione della sequenza. 
 

-  **Crittografare i volumi di dati di una macchina virtuale in esecuzione:** lo script di seguito inizializza le variabili ed esegue il cmdlet Set-AzureRmVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale e l'insieme di credenziali delle chiavi dovrebbero essere già stati creati come prerequisiti. Sostituire MySecureRg, MySecureVM e MySecureVault con i propri valori. I valori accettabili per il parametro -VolumeType sono All, OS e Data. Se la macchina virtuale è stata precedentemente crittografata con un tipo di volume "OS" o "All", il parametro -VolumeType deve essere modificato in All, in modo che vengano inclusi sia il disco del sistema operativo sia il nuovo disco dati.

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
    ```
- **Crittografare i volumi di dati di una macchina virtuale in esecuzione tramite una chiave di crittografia della chiave:** I valori accettabili per il parametro -VolumeType sono All, OS e Data. Se la macchina virtuale è stata precedentemente crittografata con un tipo di volume "OS" o "All", il parametro -VolumeType deve essere modificato in All, in modo che vengano inclusi sia il disco del sistema operativo sia il nuovo disco dati.

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data';

     ```

    >[!NOTE]
    > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa identificatore completa: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> La sintassi per il valore del parametro key-encryption-key è l'URI della chiave di crittografia della chiave, come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Disabilitare la crittografia per le macchine virtuali Linux
È possibile disabilitare la crittografia usando Azure PowerShell, l'interfaccia della riga di comando di Azure oppure un modello di Resource Manager. 

>[!IMPORTANT]
>La disabilitazione della crittografia con Crittografia dischi di Azure nelle macchine virtuali Linux è supportata solo per i volumi di dati. Non è supportata nei dati o nei volumi del sistema operativo, se il volume del sistema operativo è stato crittografato.  

- **Disabilitare la crittografia dei dischi con Azure PowerShell:** per disabilitare la crittografia, usare il cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Disabilitare la crittografia con l'interfaccia della riga di comando di Azure:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **Disabilitare la crittografia con un modello di Resource Manager:** usare il modello per [disabilitare la crittografia in una macchina virtuale Linux in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad).
     1. Fare clic su **Distribuzione in Azure**.
     2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione, la macchina virtuale, i termini legali e il contratto.
     3.  Fare clic su **Acquista** per disabilitare la crittografia dischi in una macchina virtuale Windows in esecuzione. 


## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Abilitare Crittografia dischi di Azure per Windows](azure-security-disk-encryption-windows.md)
