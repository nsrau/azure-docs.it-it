---
title: Scenari di crittografia dischi di Azure per macchine virtuali Windows
description: Questo articolo fornisce istruzioni sull'abilitazione della crittografia del disco Microsoft Azure per le macchine virtuali Windows per diversi scenari
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 951c1fd89f9e943b72c32492ff40dae3bd07bb61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88794487"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Scenari di crittografia dischi di Azure per macchine virtuali Windows

Crittografia dischi di Azure per macchine virtuali Windows utilizza la funzionalità BitLocker di Windows per fornire la crittografia completa del disco del sistema operativo e del disco dati. Offre inoltre la crittografia del disco temporaneo quando il parametro VolumeType è All.

Crittografia dischi di Azure è [integrato con Azure Key Vault](disk-encryption-key-vault.md) per consentire il controllo e la gestione dei segreti e delle chiavi di crittografia dei dischi. Per una panoramica del servizio, vedere [crittografia dischi di Azure per macchine virtuali Windows](disk-encryption-overview.md).

È possibile applicare la crittografia del disco solo alle macchine virtuali con [dimensioni e sistemi operativi supportati](disk-encryption-overview.md#supported-vms-and-operating-systems). È anche necessario soddisfare i prerequisiti seguenti:

- [Requisiti di rete](disk-encryption-overview.md#networking-requirements)
- [Requisiti di Criteri di gruppo](disk-encryption-overview.md#group-policy-requirements)
- [Requisiti di archiviazione delle chiavi di crittografia](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Se in precedenza è stata usata la funzionalità Crittografia dischi di Azure con Azure AD per crittografare una macchina virtuale, sarà necessario continuare a usare questa opzione per crittografarla. Per altre informazioni, vedere [Crittografia dischi di Azure con Azure AD (versione precedente)](disk-encryption-overview-aad.md). 
>
> - Prima di crittografare i dischi, è necessario [eseguire uno snapshot](snapshot-copy-managed-disk.md) e/o creare un backup. I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Le macchine virtuali con dischi gestiti richiedono il backup prima della crittografia. Dopo aver eseguito un backup, è possibile usare il [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) per crittografare i dischi gestiti specificando il parametro -skipVmBackup. Per altre informazioni su come eseguire il backup e il ripristino di macchine virtuali crittografate, vedere [eseguire il backup e il ripristino di VM di Azure crittografate](../../backup/backup-azure-vms-encryption.md). 
>
> - La crittografia o la disabilitazione della crittografia può causare il riavvio di una macchina virtuale.

## <a name="install-tools-and-connect-to-azure"></a>Installare gli strumenti e connettersi ad Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Abilitare la crittografia in una VM Windows esistente o in esecuzione
In questo scenario è possibile abilitare la crittografia usando il modello di Resource Manager, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. Per informazioni relative allo schema dell'estensione della macchina virtuale, leggere l'articolo [Crittografia dischi di Azure per Windows](../extensions/azure-disk-enc-windows.md).

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a> Abilitare la crittografia in una macchina virtuale esistente o in esecuzione con Azure PowerShell 
Usare il cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) per abilitare la crittografia in una macchina virtuale IaaS in esecuzione in Azure. 

-  **Crittografare una macchina virtuale in esecuzione:** lo script di seguito inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale e l'insieme di credenziali delle chiavi dovrebbero essere già stati creati come prerequisiti. Sostituire MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM e MySecureVault con i valori.

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

- **Verificare che i dischi siano crittografati:** Per verificare lo stato della crittografia di una VM IaaS, usare il cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Disabilitare la crittografia del disco:** per disabilitare la crittografia, usare il cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption). La disabilitazione della crittografia dei dischi dati nella macchina virtuale Windows quando sia i dischi dati che il disco del sistema operativo sono stati crittografati non funziona come previsto. Disabilitare la crittografia in tutti i dischi.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Abilitare la crittografia nelle VM esistenti o in esecuzione con l'interfaccia della riga di comando di Azure
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

- **Verificare che i dischi siano crittografati:** Per verificare lo stato della crittografia di una VM IaaS, usare il comando [AZ VM Encryption Show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

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
|  keyEncryptionKeyURL | URL della chiave di crittografia della chiave, nel formato https:// &lt; chiave Vault-Name &gt; . Vault.Azure.NET/Key/ &lt; chiave-nome &gt; . Se non si vuole usare un KEK, lasciare vuoto questo campo. |
| volumeType | Tipo del volume in cui viene eseguita l'operazione di crittografia. I valori validi sono _OS_, _Data_ e _All_. 
| forceUpdateTag | Ogni volta che è necessario forzare l'esecuzione dell'operazione, passare un valore univoco, ad esempio un GUID. |
| resizeOSDisk | La partizione del sistema operativo deve essere ridimensionata in modo da occupare il disco rigido virtuale completo del sistema operativo prima della divisione del volume di sistema. |
| posizione | Posizione per tutte le risorse. |

## <a name="enable-encryption-on-nvme-disks-for-lsv2-vms"></a>Abilitare la crittografia nei dischi NVMe per le macchine virtuali Lsv2

Questo scenario descrive l'abilitazione di crittografia dischi di Azure nei dischi NVMe per le VM serie Lsv2.  La serie Lsv2 offre archiviazione NVMe locale. I dischi NVMe locali sono temporanei e i dati andranno persi su questi dischi se si arresta/dealloca la VM (vedere: [serie Lsv2](../lsv2-series.md)).

Per abilitare la crittografia nei dischi NVMe:

1. Inizializzare i dischi NVMe e creare volumi NTFS.
1. Abilitare la crittografia nella macchina virtuale con il parametro VolumeType impostato su All. In questo modo verrà abilitata la crittografia per tutti i dischi del sistema operativo e dei dati, inclusi i volumi supportati dai dischi NVMe. Per informazioni, vedere [abilitare la crittografia in una VM Windows esistente o in esecuzione](#enable-encryption-on-an-existing-or-running-windows-vm).

La crittografia viene mantenute nei dischi NVMe negli scenari seguenti:
- Riavvio VM
- Ricreazione dell'immagine di VMSS
- Scambia sistema operativo

I dischi NVMe verranno non inizializzati negli scenari seguenti:

- Avvia macchina virtuale dopo la deallocazione
- Correzione del servizio
- Backup

In questi scenari, i dischi NVMe devono essere inizializzati dopo l'avvio della macchina virtuale. Per abilitare la crittografia nei dischi NVMe, eseguire il comando per abilitare di nuovo crittografia dischi di Azure dopo l'inizializzazione dei dischi NVMe.

Oltre agli scenari elencati nella sezione [scenari](#unsupported-scenarios) non supportati, la crittografia dei dischi NVMe non è supportata per:

- VM crittografate con crittografia dischi di Azure con AAD (versione precedente)
- Dischi NVMe con spazi di archiviazione
- Azure Site Recovery di SKU con dischi NVMe (vedere la [matrice di supporto per il ripristino di emergenza delle VM di Azure tra aree di Azure: macchine replicate-archiviazione](../../site-recovery/azure-to-azure-support-matrix.md#replicated-machines---storage)).

## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a> Nuove macchine virtuali IaaS create da chiavi di crittografia e disco rigido virtuale crittografato dal cliente

In questo scenario è possibile creare una nuova macchina virtuale da un disco rigido virtuale pre-crittografato e le chiavi di crittografia associate usando i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. 

Usare le istruzioni riportate in [preparare un disco rigido virtuale Windows pre-crittografato](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd). Dopo aver creato l'immagine, è possibile usare i passaggi della sezione successiva per creare una VM di Azure crittografata.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a> Crittografare le macchine virtuali con dischi rigidi virtuali pre-crittografati con Azure PowerShell
È possibile abilitare la crittografia del disco nel disco rigido virtuale crittografato usando il cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples) di PowerShell. L'esempio seguente offre alcuni parametri comuni. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Abilitare la crittografia in un disco dati appena aggiunto
È possibile [aggiungere un nuovo disco a una macchina virtuale Windows con PowerShell](attach-disk-ps.md) o [tramite il portale di Azure](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Abilitare la crittografia in un disco appena aggiunto con Azure PowerShell
 Quando si usa PowerShell per crittografare un nuovo disco per le macchine virtuali Windows, è necessario specificare una nuova versione della sequenza. La versione della sequenza deve essere univoca. Lo script seguente genera un GUID per la versione della sequenza. In alcuni casi un disco dati appena aggiunto potrebbe essere crittografato automaticamente dall'estensione di Crittografia dischi di Azure. La crittografia automatica viene in genere eseguita quando la macchina virtuale viene riavviata dopo che il nuovo disco viene portato online. Ciò è solitamente dovuto al fatto che è stata specificata l'opzione corrispondente a tutti i tipi di volume quando la crittografia dei dischi è stata eseguita in precedenza nella macchina virtuale. Se la crittografia automatica viene eseguita in un disco dati appena aggiunto, è consigliabile eseguire di nuovo il cmdlet Set-AzVmDiskEncryptionExtension con la nuova versione della sequenza. Se il nuovo disco dati viene crittografato automaticamente ma non si vuole che sia crittografato, decrittografare prima tutte le unità e quindi rieseguire la crittografia con una nuova versione della sequenza specificando il sistema operativo per il tipo di volume. 
  
 

-  **Crittografare una macchina virtuale in esecuzione:** lo script di seguito inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale e l'insieme di credenziali delle chiavi dovrebbero essere già stati creati come prerequisiti. Sostituire MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM e MySecureVault con i valori. Questo esempio usa "All" per il parametro -VolumeType, che include i volumi dei dati e del sistema operativo. Se si vuole crittografare solo il volume del sistema operativo, usare "OS" per il parametro -VolumeType. 

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
[!INCLUDE [disk-encryption-disable-encryption-powershell](../../../includes/disk-encryption-disable-powershell.md)]

## <a name="unsupported-scenarios"></a>Scenari non supportati

Crittografia dischi di Azure non funziona per gli scenari, le funzionalità e le tecnologie seguenti:

- Crittografia di una macchina virtuale o di macchine virtuali di livello Basic create tramite il metodo di creazione classico.
- Crittografia delle macchine virtuali configurate con sistemi RAID basati su software.
- Crittografia di macchine virtuali configurate con Spazi di archiviazione diretta (S2D) o versioni di Windows Server precedenti alla 2016 configurate con spazi di archiviazione Windows.
- Integrazione con un sistema di gestione delle chiavi locale.
- File di Azure (file system condiviso).
- NFS (Network File System).
- Volumi dinamici.
- Contenitori di Windows Server, che creano volumi dinamici per ogni contenitore.
- Dischi del sistema operativo temporanei.
- Crittografia di file system condivisi/distribuiti come, ma non limitati, DFS, GFS, DRDB e CephFS.
- Trasferimento di macchine virtuali crittografate in un'altra sottoscrizione o in un'altra area.
- Creare un'immagine o uno snapshot di una macchina virtuale crittografata e usarla per distribuire altre macchine virtuali.
- Macchine virtuali Gen2 (vedere: [supporto per le macchine virtuali di seconda generazione in Azure](generation-2.md#generation-1-vs-generation-2-capabilities))
- VM serie M con dischi acceleratore di scrittura.
- Applicazione di ADE a una macchina virtuale con dischi crittografati con la [crittografia lato server con chiavi gestite dal cliente](disk-encryption.md) (SSE + CMK). Applicare anche SSE + CMK a un disco dati in una macchina virtuale crittografata con ADE è uno scenario non supportato.
- Migrazione di una macchina virtuale crittografata con ADE oppure crittografata con ADE, per la [crittografia lato server con chiavi gestite dal cliente](disk-encryption.md). **ever**

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su Crittografia dischi di Azure](disk-encryption-overview.md)
- [Script di esempio per la crittografia dischi di Azure](disk-encryption-sample-scripts.md)
- [Guida alla risoluzione dei problemi di crittografia dischi di Azure](disk-encryption-troubleshooting.md)
