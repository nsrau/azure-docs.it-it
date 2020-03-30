---
title: Scenari di crittografia dischi di Azure per macchine virtuali Windows
description: Questo articolo fornisce istruzioni sull'abilitazione di Crittografia disco di Microsoft Azure per macchine virtuali Windows per vari scenari
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: ed64ee3d0e024c32be08ed4e010a6933033c3f87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476519"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Scenari di crittografia dischi di Azure per macchine virtuali Windows

Crittografia disco di Azure usa la protezione della chiave esterna di BitLocker per fornire la crittografia del volume per il sistema operativo e i dischi dati delle macchine virtuali (VM) di Azure ed è integrata con L'insieme di credenziali delle chiavi di Azure per controllare e gestire le chiavi e i segreti della crittografia del disco. Per una panoramica del servizio, vedere Crittografia disco di [Azure per macchine virtuali Windows.](disk-encryption-overview.md)

Esistono molti scenari di crittografia del disco e i passaggi possono variare a seconda dello scenario. The following sections cover the scenarios in greater detail for Windows VMs.

È possibile applicare la crittografia del disco solo alle macchine virtuali con [dimensioni vme e sistemi operativi supportati.](disk-encryption-overview.md#supported-vms-and-operating-systems) È inoltre necessario soddisfare i prerequisiti seguenti:You must also meet the following prerequisites:

- [Requisiti di rete](disk-encryption-overview.md#networking-requirements)
- [Requisiti di Criteri di gruppo](disk-encryption-overview.md#group-policy-requirements)
- [Requisiti di archiviazione delle chiavi di crittografiaEncryption key storage requirements](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Se in precedenza è stato usato Crittografia disco di Azure con Azure AD per crittografare una macchina virtuale, è necessario continuare a usare questa opzione per crittografare la macchina virtuale. Per informazioni dettagliate, vedere Crittografia del disco di [Azure con Azure AD (versione precedente).](disk-encryption-overview-aad.md) 
>
> - È necessario [creare uno snapshot](snapshot-copy-managed-disk.md) e/o creare un backup prima di crittografare i dischi. I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Le macchine virtuali con dischi gestiti richiedono il backup prima della crittografia. Una volta eseguito un backup, è possibile utilizzare il [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) per crittografare i dischi gestiti specificando il parametro -skipVmBackup. Per altre informazioni su come eseguire il backup e il ripristino delle macchine virtuali crittografate, vedere Eseguire il backup e il ripristino di macchine virtuali di [Azure crittografate.](../../backup/backup-azure-vms-encryption.md) 
>
> - La crittografia o la disabilitazione della crittografia può causare il riavvio di una macchina virtuale.

## <a name="install-tools-and-connect-to-azure"></a>Installare gli strumenti e connettersi ad AzureInstall tools and connect to Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Abilitare la crittografia in una macchina virtuale Windows esistente o in esecuzioneEnable encryption on an existing or running Windows VM
In questo scenario è possibile abilitare la crittografia usando il modello di Resource Manager, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. Per informazioni relative allo schema dell'estensione della macchina virtuale, leggere l'articolo [Crittografia dischi di Azure per Windows](../extensions/azure-disk-enc-windows.md).

## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a> Abilitare la crittografia nelle macchine virtuali IaaS Windows esistenti o in esecuzione
È possibile abilitare la crittografia utilizzando un modello, cmdlet di PowerShell o comandi dell'interfaccia della riga di comando. Per informazioni relative allo schema dell'estensione della macchina virtuale, leggere l'articolo [Crittografia dischi di Azure per Windows](../extensions/azure-disk-enc-windows.md).

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a> Abilitare la crittografia in una macchina virtuale esistente o in esecuzione con Azure PowerShell 
Usare il cmdlet Set-AzVMDiskEncryptionExtension per abilitare la crittografia in una macchina virtuale IaaS in esecuzione in Azure.Use the [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet to enable encryption on a running IaaS virtual machine in Azure. 

-  **Crittografare una macchina virtuale in esecuzione:Encrypt a running VM:** Lo script seguente inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale e l'insieme di credenziali delle chiavi dovrebbero essere già stati creati come prerequisiti. Sostituire MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM e MySecureVault con i valori specificati.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Crittografare una macchina virtuale in esecuzione usando una chiave di crittografia della chiave:** 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa identificatore completa: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> La sintassi per il valore del parametro key-encryption-key è l'URI della chiave di crittografia della chiave, come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verificare che i dischi siano crittografati:** Per verificare lo stato di crittografia di una macchina virtuale IaaS, utilizzare il cmdlet [Get-AzVmDiskEncryptionStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Disabilitare la crittografia del disco:** Per disabilitare la crittografia, utilizzare il cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) La disabilitazione della crittografia dei dischi dati nella macchina virtuale Windows quando sia i dischi dati che il disco del sistema operativo sono stati crittografati non funziona come previsto. Disabilitare la crittografia in tutti i dischi.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Abilitare la crittografia nelle macchine virtuali esistenti o in esecuzione con l'interfaccia della riga di comando di AzureEnable encryption on existing or running VMs with the Azure CLI
Usare il comando [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) per abilitare la crittografia in una macchina virtuale IaaS in esecuzione in Azure.

- **Crittografare una macchina virtuale in esecuzione:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Crittografare una macchina virtuale in esecuzione usando una chiave di crittografia della chiave:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa identificatore completa: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> La sintassi per il valore del parametro key-encryption-key è l'URI della chiave di crittografia della chiave, come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verificare che i dischi siano crittografati:** Per verificare lo stato di crittografia di una macchina virtuale IaaS, usare il comando [az vm encryption show.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Disabilitare la crittografia:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). La disabilitazione della crittografia dei dischi dati nella macchina virtuale Windows quando sia i dischi dati che il disco del sistema operativo sono stati crittografati non funziona come previsto. Disabilitare la crittografia in tutti i dischi.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>Uso del modello di Resource Manager

È possibile abilitare la crittografia dei dischi nelle macchine virtuali IaaS Windows esistenti o in esecuzione in Azure usando il [modello di Resource Manager per crittografare una macchina virtuale Windows in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. Nel modello di avvio rapido di Azure fare clic su **Deploy to Azure**.

2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione, le impostazioni, i termini legali e il contratto. Fare clic su **Acquista** per abilitare la crittografia in una macchina IaaS esistente o in esecuzione.

La tabella seguente elenca i parametri del modello di Resource Manager per macchine virtuali esistenti o in esecuzione:

| Parametro | Descrizione |
| --- | --- |
| vmName | Nome della macchina virtuale per eseguire l'operazione di crittografia. |
| keyVaultName | Nome dell'insieme di credenziali delle chiavi in cui dovrà essere caricata la chiave BitLocker. È possibile ottenerlo usando il cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` o il comando dell'interfaccia della riga di comando di Azure `az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Nome del gruppo di risorse che contiene l'insieme di credenziali delle chiavi.|
|  keyEncryptionKeyURL | URL della chiave di crittografia della&lt;chiave, nel&gt;formato&lt;https://&gt;keyvault-name .vault.azure.net/key/ nome-chiave . Se non si desidera utilizzare una keK, lasciare vuoto questo campo. |
| volumeType | Tipo del volume in cui viene eseguita l'operazione di crittografia. I valori validi sono _OS_, _Data_ e _All_. 
| forceUpdateTag | Ogni volta che è necessario forzare l'esecuzione dell'operazione, passare un valore univoco, ad esempio un GUID. |
| resizeOSDisk | La partizione del sistema operativo deve essere ridimensionata in modo da occupare il disco rigido virtuale completo del sistema operativo prima della divisione del volume di sistema. |
| posizione | Posizione per tutte le risorse. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a> Nuove macchine virtuali IaaS create da chiavi di crittografia e disco rigido virtuale crittografato dal cliente

In questo scenario, è possibile creare una nuova macchina virtuale da un disco rigido virtuale pre-crittografato e le chiavi di crittografia associate utilizzando i cmdlet di PowerShell o comandi dell'interfaccia della riga di comando. 

Usare le istruzioni in [Preparare un disco rigido virtuale di Windows pre-crittografato.](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd) Dopo aver creato l'immagine, è possibile usare i passaggi della sezione successiva per creare una VM di Azure crittografata.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a> Crittografare le macchine virtuali con dischi rigidi virtuali pre-crittografati con Azure PowerShell
È possibile abilitare la crittografia del disco nel disco rigido virtuale crittografato utilizzando il cmdlet di PowerShell [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). L'esempio seguente offre alcuni parametri comuni. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Abilitare la crittografia in un disco dati appena aggiunto
È possibile [aggiungere un nuovo disco a una macchina virtuale Windows con PowerShell](attach-disk-ps.md) o [tramite il portale di Azure](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Abilitare la crittografia in un disco appena aggiunto con Azure PowerShell
 Quando si usa Powershell per crittografare un nuovo disco per le macchine virtuali Windows, è necessario specificare una nuova versione della sequenza. La versione della sequenza deve essere univoca. Lo script seguente genera un GUID per la versione della sequenza. In alcuni casi un disco dati appena aggiunto potrebbe essere crittografato automaticamente dall'estensione di Crittografia dischi di Azure. La crittografia automatica viene in genere eseguita quando la macchina virtuale viene riavviata dopo che il nuovo disco viene portato online. Ciò è solitamente dovuto al fatto che è stata specificata l'opzione corrispondente a tutti i tipi di volume quando la crittografia dei dischi è stata eseguita in precedenza nella macchina virtuale. Se la crittografia automatica viene eseguita in un disco dati appena aggiunto, è consigliabile eseguire nuovamente il cmdlet Set-AzVmDiskEncryptionExtension con una nuova versione di sequenza. Se il nuovo disco dati viene crittografato automaticamente ma non si vuole che sia crittografato, decrittografare prima tutte le unità e quindi rieseguire la crittografia con una nuova versione della sequenza specificando il sistema operativo per il tipo di volume. 
  
 

-  **Crittografare una macchina virtuale in esecuzione:Encrypt a running VM:** Lo script seguente inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale e l'insieme di credenziali delle chiavi dovrebbero essere già stati creati come prerequisiti. Sostituire MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM e MySecureVault con i valori specificati. Questo esempio usa "All" per il parametro -VolumeType, che include i volumi dei dati e del sistema operativo. Se si vuole crittografare solo il volume del sistema operativo, usare "OS" per il parametro -VolumeType. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Crittografare una macchina virtuale in esecuzione tramite la chiave di crittografia della chiave:** questo esempio usa "All" per il parametro -VolumeType, che include i volumi dei dati e del sistema operativo. Se si vuole crittografare solo il volume del sistema operativo, usare "OS" per il parametro -VolumeType.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa identificatore completa: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> La sintassi per il valore del parametro key-encryption-key è l'URI della chiave di crittografia della chiave, come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Abilitare la crittografia in un disco appena aggiunto con l'interfaccia della riga di comando di Azure
 Il comando dell'interfaccia della riga di comando di Azure fornisce automaticamente una nuova versione della sequenza quando si esegue il comando per abilitare la crittografia. L'esempio usa "All" per il parametro volume-type. Può essere necessario impostare il parametro volume-type su OS se si sta crittografando solo il disco del sistema operativo. A differenza della sintassi di PowerShell, l'interfaccia della riga di comando non richiede all'utente di specificare una versione di sequenza univoca quando si abilita la crittografia. L'interfaccia della riga di comando genera e usa automaticamente uno specifico valore di versione di sequenza univoco.   

-  **Crittografare una macchina virtuale in esecuzione:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Crittografare una macchina virtuale in esecuzione usando una chiave di crittografia della chiave:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Disabilitare la crittografia
È possibile disabilitare la crittografia usando Azure PowerShell, l'interfaccia della riga di comando di Azure oppure un modello di Resource Manager. La disabilitazione della crittografia dei dischi dati nella macchina virtuale Windows quando sia i dischi dati che il disco del sistema operativo sono stati crittografati non funziona come previsto. Disabilitare la crittografia in tutti i dischi.

- **Disabilitare la crittografia del disco con Azure PowerShell:Disable disk encryption with Azure PowerShell:** Per disabilitare la crittografia, utilizzare il cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Disabilitare la crittografia con l'interfaccia della riga di comando di Azure:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Disabilitare la crittografia con un modello di Resource Manager:** 

    1. Fare clic su **Deploy to Azure** (Distribuisci in Azure) dal modello per [disabilitare la crittografia dischi in una macchina virtuale Windows in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad).
    2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione, la macchina virtuale, il tipo di volume, i termini legali e il contratto.
    3.  Fare clic su **Acquista** per disabilitare la crittografia dischi in una macchina virtuale Windows in esecuzione. 

## <a name="unsupported-scenarios"></a>Scenari non supportati

Crittografia disco di Azure non funziona per gli scenari, le funzionalità e la tecnologia seguenti:Azure Disk Encryption does not work for the following scenarios, features, and technology:

- Crittografia di macchine virtuali o macchine virtuali di livello base create tramite il metodo di creazione della macchina virtuale classica.
- Crittografia di macchine virtuali configurate con sistemi RAID basati su software.
- Crittografia delle macchine virtuali configurate con spazi di archiviazione diretta (S2D) o versioni di Windows Server precedenti al 2016 configurate con Spazi di archiviazione Windows.Encrypting VMs configured with Storage Spaces Direct (S2D) or Windows Server versions before 2016 configured with Windows Storage Spaces.
- Integrazione con un sistema di gestione delle chiavi locale.
- File di Azure (file system condiviso).
- NFS (Network File System).
- Volumi dinamici.
- Contenitori di Windows Server, che creano volumi dinamici per ogni contenitore.
- Dischi del sistema operativo effimeri.
- Crittografia di file system condivisi/distribuiti come (ma non solo) DFS, GFS, DRDB e CephFS.
- Spostamento di macchine virtuali crittografate in un'altra sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su Crittografia dischi di Azure](disk-encryption-overview.md)
- [Script di esempio per la crittografia dischi di Azure](disk-encryption-sample-scripts.md)
- [Guida alla risoluzione dei problemi di crittografia dischi di Azure](disk-encryption-troubleshooting.md)
