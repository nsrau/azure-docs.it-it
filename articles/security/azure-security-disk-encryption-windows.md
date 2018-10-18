---
title: Abilitare Crittografia dischi di Azure per le macchine virtuali IaaS Windows | Microsoft Docs
description: Questo articolo offre informazioni sull'abilitazione di Crittografia dischi di Microsoft Azure per le macchine virtuali IaaS Windows.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 09/19/2018
ms.openlocfilehash: 04077c9acbd9556a66e8337ab8f415de86df1d5a
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498199"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>Abilitare Crittografia dischi di Azure per le macchine virtuali IaaS Windows 

È possibile abilitare molti scenari di crittografia dei dischi e la procedura può variare in base allo scenario. Le sezioni seguenti illustrano in modo più dettagliato gli scenari per le macchine virtuali IaaS Windows. Prima di poter usare crittografia dei dischi, è necessario soddisfare i [prerequisiti di Crittografia dischi di Azure](../security/azure-security-disk-encryption-prerequisites.md). 

Catturare uno [snapshot](../virtual-machines/windows/snapshot-copy-managed-disk.md) e/o eseguire il backup prima che i dischi vengono crittografati. I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Le macchine virtuali con dischi gestiti richiedono il backup prima della crittografia. Dopo aver eseguito il backup, è possibile usare il cmdlet Set-AzureRmVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro -skipVmBackup. Per altre informazioni su come eseguire il backup e il ripristino di macchine virtuali crittografate, vedere l'articolo [Backup di Azure](../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
> Per Crittografia dischi di Azure è necessario che l'insieme di credenziali delle chiavi e le macchine virtuali si trovino nella stessa area. Creare e usare un insieme di credenziali delle chiavi nella stessa area della macchina virtuale da crittografare. 


## <a name="bkmk_RunningWinVM"></a> Abilitare la crittografia nelle macchine virtuali IaaS Windows esistenti o in esecuzione
In questo scenario è possibile abilitare la crittografia usando un modello, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. Le sezioni seguenti illustrano in modo più approfondito come abilitare Crittografia dischi di Azure. Per informazioni relative allo schema dell'estensione della macchina virtuale, leggere l'articolo [Crittografia dischi di Azure per Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).

>[!IMPORTANT]
 >È obbligatorio catturare uno snapshot e/o eseguire il backup di un'istanza di macchina virtuale basata su un disco gestito all'esterno di Crittografia dischi di Azure e prima di abilitarla. È possibile catturare uno snapshot del disco gestito dal portale o tramite [Backup di Azure](../backup/backup-azure-vms-encryption.md). I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Dopo aver eseguito un backup, è possibile usare il cmdlet Set-AzureRmVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro -skipVmBackup. Il comando Set-AzureRmVMDiskEncryptionExtension ha esito negativo su una macchina virtuale basata su un disco gestito finché non viene eseguito un backup e non viene specificato il parametro. 
>
>La crittografia o la disabilitazione della crittografia può causare il riavvio della macchina virtuale. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Abilitare la crittografia in una macchina virtuale esistente o in esecuzione con Azure PowerShell 
Usare il cmdlet [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) per abilitare la crittografia in una macchina virtuale IaaS in esecuzione in Azure. 

-  **Crittografare una macchina virtuale in esecuzione:** lo script di seguito inizializza le variabili ed esegue il cmdlet Set-AzureRmVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale e l'insieme di credenziali delle chiavi dovrebbero essere già stati creati come prerequisiti. Sostituire MySecureRg, MySecureVM e MySecureVault con i propri valori.

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Crittografare una macchina virtuale in esecuzione usando una chiave di crittografia della chiave:** 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
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
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
     ```
    
- **Disabilitare la crittografia del disco:** per disabilitare la crittografia, usare il cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). La procedura per disabilitare la crittografia del disco dati nella macchina virtuale di Windows, quando sono stati crittografati sia il disco del sistema operativo sia il disco dati, non funziona come previsto. Disabilitare la crittografia in tutti i dischi.

     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Abilitare la crittografia in macchine virtuali esistenti o in esecuzione con l'interfaccia della riga di comando di Azure
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
     > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa identificatore completa: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> La sintassi per il valore del parametro key-encryption-key è l'URI della chiave di crittografia della chiave, come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verificare che i dischi siano crittografati:** per controllare lo stato della crittografia di una macchina virtuale IaaS, usare il comando [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Disabilitare la crittografia:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). La procedura per disabilitare la crittografia del disco dati nella macchina virtuale di Windows, quando sono stati crittografati sia il disco del sistema operativo sia il disco dati, non funziona come previsto. Disabilitare la crittografia in tutti i dischi.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
 
 > [!NOTE]
 >È obbligatorio catturare uno snapshot e/o eseguire il backup di un'istanza di macchina virtuale basata su un disco gestito all'esterno di Crittografia dischi di Azure e prima di abilitarla. È possibile catturare uno snapshot del disco gestito dal portale o tramite [Backup di Azure](../backup/backup-azure-vms-encryption.md). I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Dopo aver eseguito un backup, è possibile usare il cmdlet Set-AzureRmVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro -skipVmBackup. Questo comando ha esito negativo sulle macchine virtuali basate su dischi gestiti finché non viene eseguito un backup e non viene specificato il parametro. 
>
>La crittografia o la disabilitazione della crittografia può causare il riavvio della macchina virtuale. 

### <a name="bkmk_RunningWinVMwRM"> </a>Uso del modello di Resource Manager
È possibile abilitare la crittografia dei dischi nelle macchine virtuali IaaS Windows esistenti o in esecuzione in Azure usando il [modello di Resource Manager per crittografare una macchina virtuale Windows in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. Nel modello di avvio rapido di Azure fare clic su **Deploy to Azure**.

2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione, le impostazioni, i termini legali e il contratto. Fare clic su **Acquista** per abilitare la crittografia in una macchina IaaS esistente o in esecuzione.

La tabella seguente elenca i parametri del modello di Resource Manager per macchine virtuali esistenti o in esecuzione:

| Parametro | Descrizione |
| --- | --- |
| vmName | Nome della macchina virtuale per eseguire l'operazione di crittografia. |
| keyVaultName | Nome dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave BitLocker. È possibile ottenerlo usando il cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <MyResourceGroupName>). Vaultname` o il comando dell'interfaccia della riga di comando di Azure `az keyvault list --resource-group "MySecureGroup" |Convertfrom-JSON`|
| keyVaultResourceGroup | Nome del gruppo di risorse che contiene l'insieme di credenziali delle chiavi.|
|  keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave BitLocker generata. Questo parametro è facoltativo se si seleziona **nokek** dall'elenco a discesa UseExistingKek. Se si seleziona **kek** dall'elenco a discesa UseExistingKek, è necessario immettere il valore _keyEncryptionKeyURL_. |
| volumeType | Tipo del volume in cui viene eseguita l'operazione di crittografia. I valori validi sono _OS_, _Data_ e _All_. 
| forceUpdateTag | Ogni volta che è necessario forzare l'esecuzione dell'operazione, passare un valore univoco, ad esempio un GUID. |
| resizeOSDisk | La partizione del sistema operativo deve essere ridimensionata in modo da occupare il disco rigido virtuale completo del sistema operativo prima della divisione del volume di sistema. |
| location | Posizione per tutte le risorse. |

## <a name="encrypt-virtual-machine-scale-sets"></a>Crittografare i set di scalabilità di macchine virtuali
I [set di scalabilità di macchine virtuali di Azure](../virtual-machine-scale-sets/overview.md) consentono di creare e gestire un gruppo di macchine virtuali identiche con bilanciamento del carico. Il numero di istanze di macchine virtuali può aumentare o diminuire automaticamente in risposta alla domanda o a una pianificazione definita. Per crittografare i set di scalabilità di macchine virtuali, usare l'interfaccia della riga di comando o Azure PowerShell.


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
    ```azurepowershell-interactive
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:

    ```azurepowershell-interactive
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
    
    ```azurepowershell-interactive
    get-AzureRmVmssVMDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

- **Disabilitare la crittografia su un set di scalabilità di macchine virtuali:** usare il cmdlet [Disable-AzureRmVmssDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption). 

    ```azurepowershell-interactive
    Disable-AzureRmVmssDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

### <a name="register-for-disk-encryption-preview-using-azure-cli"></a>Eseguire la registrazione per l'anteprima di Crittografia dischi tramite l'interfaccia della riga di comando di Azure

Per usare l'anteprima di Crittografia dischi di Azure per i set di scalabilità di macchine virtuali è necessario eseguire la registrazione automatica della sottoscrizione con il comando [az feature register](/cli/azure/feature#az_feature_register). È sufficiente eseguire la procedura seguente la prima volta che si usa la funzionalità di anteprima per la crittografia dei dischi:

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

Possono essere necessari fino a 10 minuti per la propagazione della richiesta di registrazione. È possibile verificare lo stato della registrazione con il comando [az feature show](/cli/azure/feature#az_feature_show). Quando `State` indica il valore *Registered*, registrare nuovamente il provider *Microsoft.Compute* con il comando [az provider register](/cli/azure/provider#az_provider_register):

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

- **Disabilitare la crittografia su un set di scalabilità di macchine virtuali:** usare [az vmss encryption disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MySecureRG" --name "MySecureVmss"
    ```

## <a name="bkmk_VHDpre"> </a>Nuove macchine virtuali IaaS create da chiavi di crittografia e disco rigido virtuale crittografato dal cliente
In questo scenario è possibile abilitare la crittografia usando i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. 

Usare le istruzioni nell'appendice per la preparazione delle immagini pre-crittografate che possono essere usate in Azure. Dopo aver creato l'immagine, è possibile usare i passaggi della sezione successiva per creare una VM di Azure crittografata.

* [Preparare un disco rigido virtuale Windows pre-crittografato](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Preparare un disco rigido virtuale Linux pre-crittografato](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >È obbligatorio catturare uno snapshot e/o eseguire il backup di un'istanza di macchina virtuale basata su un disco gestito all'esterno di Crittografia dischi di Azure e prima di abilitarla. È possibile catturare uno snapshot del disco gestito dal portale o tramite [Backup di Azure](../backup/backup-azure-vms-encryption.md). I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Dopo aver eseguito un backup, è possibile usare il cmdlet Set-AzureRmVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro -skipVmBackup. Il comando Set-AzureRmVMDiskEncryptionExtension ha esito negativo su una macchina virtuale basata su un disco gestito finché non viene eseguito un backup e non viene specificato il parametro. 
>
>La crittografia o la disabilitazione della crittografia può causare il riavvio della macchina virtuale. 


### <a name="bkmk_VHDprePSH"> </a> Crittografare le macchine virtuali con dischi rigidi virtuali pre-crittografati con Azure PowerShell
È possibile abilitare la crittografia dei dischi nel disco rigido virtuale crittografato usando il cmdlet [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples) di PowerShell. L'esempio seguente offre alcuni parametri comuni. 

```azurepowershell-interactive
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResouceGroupName "MySecureRG"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Abilitare la crittografia in un disco dati appena aggiunto
È possibile [aggiungere un nuovo disco a una macchina virtuale Windows con PowerShell](../virtual-machines/windows/attach-disk-ps.md) o [tramite il portale di Azure](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Abilitare la crittografia in un disco appena aggiunto con Azure PowerShell
 Quando si usa Powershell per crittografare un nuovo disco per le macchine virtuali Windows, è necessario specificare una nuova versione della sequenza. La versione della sequenza deve essere univoca. Lo script seguente genera un GUID per la versione della sequenza. In alcuni casi un disco dati appena aggiunto potrebbe essere crittografato automaticamente dall'estensione di Crittografia dischi di Azure. In questo caso, è consigliabile eseguire di nuovo il cmdlet Set-AzureRmVmDiskEncryptionExtension con la nuova versione della sequenza.
 

-  **Crittografare una macchina virtuale in esecuzione:** lo script di seguito inizializza le variabili ed esegue il cmdlet Set-AzureRmVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale e l'insieme di credenziali delle chiavi dovrebbero essere già stati creati come prerequisiti. Sostituire MySecureRg, MySecureVM e MySecureVault con i propri valori. Questo esempio usa "All" per il parametro -VolumeType, che include i volumi dei dati e del sistema operativo. Se si vuole crittografare solo il volume del sistema operativo, usare "OS" per il parametro -VolumeType. 

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Crittografare una macchina virtuale in esecuzione tramite la chiave di crittografia della chiave:** questo esempio usa "All" per il parametro -VolumeType, che include i volumi dei dati e del sistema operativo. Se si vuole crittografare solo il volume del sistema operativo, usare "OS" per il parametro -VolumeType.

     ```azurepowershell-interactive
     $sequenceVersion = [Guid]::NewGuid();
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa identificatore completa: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> La sintassi per il valore del parametro key-encryption-key è l'URI della chiave di crittografia della chiave, come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Abilitare la crittografia in un disco appena aggiunto con l'interfaccia della riga di comando di Azure
 Il comando dell'interfaccia della riga di comando di Azure fornisce automaticamente una nuova versione della sequenza quando si esegue il comando per abilitare la crittografia. L'esempio usa "All" per il parametro volume-type. Può essere necessario impostare il parametro volume-type su OS se si sta crittografando solo il disco del sistema operativo. A differenza della sintassi di PowerShell, l'interfaccia della riga di comando non richiede all'utente di specificare una versione di sequenza univoca quando si abilita la crittografia. L'interfaccia della riga di comando genera e usa automaticamente uno specifico valore di versione di sequenza univoco.   

-  **Crittografare una macchina virtuale in esecuzione:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Crittografare una macchina virtuale in esecuzione usando una chiave di crittografia della chiave:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Disabilitare la crittografia
È possibile disabilitare la crittografia usando Azure PowerShell, l'interfaccia della riga di comando di Azure oppure un modello di Resource Manager. La procedura per disabilitare la crittografia del disco dati nella macchina virtuale di Windows, quando sono stati crittografati sia il disco del sistema operativo sia il disco dati, non funziona come previsto. Disabilitare la crittografia in tutti i dischi.

- **Disabilitare la crittografia del disco con Azure PowerShell:** per disabilitare la crittografia, usare il cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Disabilitare la crittografia con l'interfaccia della riga di comando di Azure:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type "all"
     ```
- **Disabilitare la crittografia con un modello di Resource Manager:** 

    1. Fare clic su **Deploy to Azure** (Distribuisci in Azure) dal modello per [disabilitare la crittografia dischi in una macchina virtuale Windows in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad).
    2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione, la macchina virtuale, il tipo di volume, i termini legali e il contratto.
    3.  Fare clic su **Acquista** per disabilitare la crittografia dischi in una macchina virtuale Windows in esecuzione. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Abilitare Crittografia dischi di Azure per Linux](azure-security-disk-encryption-linux.md)
