---
title: Scenari di crittografia dischi di Azure nelle macchine virtuali Linux
description: Questo articolo fornisce istruzioni su come abilitare la crittografia del disco Microsoft Azure per le macchine virtuali Linux per diversi scenari
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0a1515144f340938cddfd5ca9f2ac4803bcb3f77
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174726"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Scenari di crittografia dischi di Azure nelle macchine virtuali Linux

Crittografia dischi di Azure usa la funzionalità DM-Crypt di Linux per fornire la crittografia del volume per il sistema operativo e i dischi dati delle macchine virtuali (VM) di Azure ed è integrato con Azure Key Vault per facilitare il controllo e la gestione delle chiavi e dei segreti di crittografia del disco. Per una panoramica del servizio, vedere [crittografia dischi di Azure per macchine virtuali Linux](disk-encryption-overview.md).

Esistono molti scenari di crittografia dei dischi e i passaggi possono variare in base allo scenario. Le sezioni seguenti illustrano in modo più dettagliato gli scenari per le macchine virtuali Linux.

È possibile applicare la crittografia del disco solo alle macchine virtuali di [dimensioni e sistemi operativi supportati](disk-encryption-overview.md#supported-vms-and-operating-systems)per le VM. È inoltre necessario soddisfare i seguenti prerequisiti:

- [Requisiti aggiuntivi per le macchine virtuali](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisiti di rete](disk-encryption-overview.md#networking-requirements)
- [Requisiti di archiviazione delle chiavi di crittografia](disk-encryption-overview.md#encryption-key-storage-requirements)

In tutti i casi, è necessario [eseguire uno snapshot](snapshot-copy-managed-disk.md) e/o creare un backup prima che i dischi vengano crittografati. I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Le macchine virtuali con dischi gestiti richiedono il backup prima della crittografia. Una volta eseguito un backup, è possibile usare il [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) per crittografare i dischi gestiti specificando il parametro-skipVmBackup. Per altre informazioni su come eseguire il backup e il ripristino di macchine virtuali crittografate, vedere l'articolo [Backup di Azure](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
> - Se in precedenza è stato usato crittografia dischi di Azure con Azure AD per crittografare una macchina virtuale, è necessario continuare a usare questa opzione per crittografare la macchina virtuale. Per informazioni dettagliate, vedere [crittografia dischi di Azure con Azure ad (versione precedente)](disk-encryption-overview-aad.md) . 
>
> - Quando si esegue la crittografia dei volumi del sistema operativo Linux, la macchina virtuale deve essere considerata non disponibile. È consigliabile evitare gli accessi SSH mentre è in corso la crittografia per evitare problemi di blocco di file aperti a cui sarà necessario accedere durante il processo di crittografia. Per controllare lo stato di avanzamento, usare il cmdlet di PowerShell [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) o il comando [VM Encryption Show](/cli/azure/vm/encryption#az-vm-encryption-show) cli. Questo processo può richiedere alcune ore per un volume di sistema operativo da 30 GB, più un tempo aggiuntivo per la crittografia dei volumi di dati. Il tempo per la crittografia del volume di dati è proporzionale alla dimensione e quantità dei volumi di dati a meno che non venga usata l'opzione "encrypt format all". 
> - La disabilitazione della crittografia nelle macchine virtuali Linux è supportata solo per i volumi di dati. Non è supportata nei dati o nei volumi del sistema operativo, se il volume del sistema operativo è stato crittografato.  

## <a name="install-tools-and-connect-to-azure"></a>Installare gli strumenti e connettersi ad Azure

Crittografia dischi di Azure può essere abilitata e gestita tramite l'interfaccia della riga di comando di [Azure](/cli/azure) e [Azure PowerShell](/powershell/azure/new-azureps-module-az). A tale scopo, è necessario installare gli strumenti localmente e connettersi alla sottoscrizione di Azure.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'[interfaccia della riga di comando di Azure 2.0](/cli/azure) è uno strumento da riga di comando per la gestione delle risorse di Azure. L'interfaccia della riga di comando è progettata per eseguire query sui dati in modo flessibile, supportare operazioni a esecuzione prolungata come processi non bloccanti e semplificare la creazione di script. È possibile installarlo in locale seguendo i passaggi descritti in [installare l'interfaccia della](/cli/azure/install-azure-cli?view=azure-cli-latest)riga di comando di Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per [accedere al proprio account Azure con l'interfaccia della](/cli/azure/authenticate-azure-cli)riga di comando di Azure, usare il comando [AZ login](/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```azurecli
az login
```

Per selezionare un tenant per l'accesso, usare:
    
```azurecli
az login --tenant <tenant>
```

In caso di più sottoscrizioni, per specificarne una in particolare è necessario ottenere l'elenco di sottoscrizioni tramite [az account list](/cli/azure/account#az-account-list) (elenco account di accesso di AZ) e specificarla da [az account set](/cli/azure/account#az-account-set) (imposta account di accesso di AZ).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Per altre informazioni, vedere [Azure Service Fabric e interfaccia della riga di comando di Azure 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
Il [Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az) fornisce un set di cmdlet che usa il modello di [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) per la gestione delle risorse di Azure. È possibile usarlo nel browser con [Azure cloud Shell](../../cloud-shell/overview.md)oppure è possibile installarlo nel computer locale seguendo le istruzioni riportate in [Install the Azure PowerShell Module](/powershell/azure/install-az-ps). 

Se è già installato in locale, assicurarsi di usare la versione più recente di Azure PowerShell SDK per configurare Crittografia dischi di Azure. Scaricare la versione più recente di [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

Per [accedere al proprio account Azure con Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0), usare il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```powershell
Connect-AzAccount
```

Se si dispone di più sottoscrizioni e si vuole specificarne una, usare il cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) per elencarle, seguito dal cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Eseguendo il cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) , viene verificato che sia stata selezionata la sottoscrizione corretta.

Per verificare che i cmdlet di crittografia dischi di Azure siano installati, usare il cmdlet [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) :
     
```powershell
Get-command *diskencryption*
```
Per ulteriori informazioni, vedere [Introduzione a Azure PowerShell](/powershell/azure/get-started-azureps). 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Abilitare la crittografia in una VM Linux esistente o in esecuzione
In questo scenario è possibile abilitare la crittografia usando il modello di Resource Manager, i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. Per informazioni sullo schema dell'estensione della macchina virtuale, leggere l'articolo [Crittografia dischi di Azure per Linux](../extensions/azure-disk-enc-linux.md).

>[!IMPORTANT]
 >È obbligatorio catturare uno snapshot e/o eseguire il backup di un'istanza di macchina virtuale basata su un disco gestito all'esterno di Crittografia dischi di Azure e prima di abilitarla. Uno snapshot del disco gestito può essere tratto dal portale o tramite [backup di Azure](../../backup/backup-azure-vms-encryption.md). I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Una volta eseguito un backup, è possibile usare il cmdlet Set-AzVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro-skipVmBackup. Il comando set-AzVMDiskEncryptionExtension avrà esito negativo rispetto alle VM basate su disco gestito fino a quando non viene eseguito un backup e questo parametro è stato specificato. 
>
>La crittografia o la disabilitazione della crittografia può causare il riavvio della macchina virtuale. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Abilitare la crittografia in una VM Linux esistente o in esecuzione usando l'interfaccia della riga di comando 

È possibile abilitare la crittografia del disco nel VHD crittografato installando e usando lo strumento da riga di comando dell'interfaccia della riga di comando di [Azure](/cli/azure/?view=azure-cli-latest) . È possibile usarlo nel browser con [Azure Cloud Shell](../../cloud-shell/overview.md) oppure installarlo nel computer locale e usarlo in una sessione di PowerShell. Per abilitare la crittografia in macchine virtuali Linux esistenti o in esecuzione in Azure, usare i comandi dell'interfaccia della riga di comando seguenti:

Usare il comando [AZ VM Encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) per abilitare la crittografia in una macchina virtuale in esecuzione in Azure.

- **Crittografare una macchina virtuale in esecuzione:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Crittografare una macchina virtuale in esecuzione usando una chiave di crittografia della chiave:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa identificatore completa: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
La sintassi per il valore del parametro key-encryption-key è l'URI della chiave di crittografia della chiave, come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verificare che i dischi siano crittografati:** Per verificare lo stato di crittografia di una macchina virtuale, usare il comando [AZ VM Encryption Show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Disabilitare la crittografia:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). È possibile disabilitare la crittografia solo nei volumi di dati per le macchine virtuali Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Abilitare la crittografia in una VM Linux esistente o in esecuzione usando PowerShell
Usare il cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) per abilitare la crittografia in una macchina virtuale in esecuzione in Azure. Eseguire uno [snapshot](snapshot-copy-managed-disk.md) e/o eseguire il backup della macchina virtuale con [backup di Azure](../../backup/backup-azure-vms-encryption.md) prima che i dischi siano crittografati. Il parametro-skipVmBackup è già specificato negli script di PowerShell per crittografare una VM Linux in esecuzione.

-  **Crittografare una macchina virtuale in esecuzione:** Lo script seguente inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale e l'insieme di credenziali delle chiavi sono stati creati come prerequisiti. Sostituire MyVirtualMachineResourceGroup, MySecureVM e MySecureVault con i valori. Modificare il parametro-VolumeType per specificare i dischi da crittografare.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Crittografare una macchina virtuale in esecuzione usando una chiave di crittografia della chiave:** Se si esegue la crittografia dei dischi dati e non del disco del sistema operativo, può essere necessario aggiungere il parametro -VolumeType. 

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > La sintassi per il valore del parametro disk-encryption-keyvault è la stringa identificatore completa: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> La sintassi per il valore del parametro key-encryption-key è l'URI della chiave di crittografia della chiave, come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Verificare che i dischi siano crittografati:** Per verificare lo stato di crittografia di una macchina virtuale, usare il cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Disabilitare la crittografia del disco:** Per disabilitare la crittografia, usare il cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . È possibile disabilitare la crittografia solo nei volumi di dati per le macchine virtuali Linux.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Abilitare la crittografia in una VM Linux esistente o in esecuzione con un modello

È possibile abilitare la crittografia dei dischi in una VM Linux esistente o in esecuzione in Azure usando il [modello di gestione risorse](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Fare clic su **Deploy to Azure** nel modello di avvio rapido di Azure.

2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, i parametri, i termini legali e il contratto. Fare clic su **Crea** per abilitare la crittografia nella VM esistente o in esecuzione.

La tabella seguente elenca i parametri del modello di Resource Manager per macchine virtuali esistenti o in esecuzione:

| Parametro | Descrizione |
| --- | --- |
| vmName | Nome della macchina virtuale per eseguire l'operazione di crittografia. |
| keyVaultName | Nome dell'insieme di credenziali delle chiavi in cui deve essere caricata la chiave di crittografia. È possibile ottenerlo usando il cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` o il comando dell'interfaccia della riga di comando di Azure `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`.|
| keyVaultResourceGroup | Nome del gruppo di risorse che contiene l'insieme di credenziali delle chiavi. |
|  keyEncryptionKeyURL | URL della chiave di crittografia della chiave usata per crittografare la chiave di crittografia. Questo parametro è facoltativo se si seleziona **nokek** dall'elenco a discesa UseExistingKek. Se si seleziona **kek** dall'elenco a discesa UseExistingKek, è necessario immettere il valore _keyEncryptionKeyURL_. |
| volumeType | Tipo del volume in cui viene eseguita l'operazione di crittografia. I valori validi sono _OS_, _Data_ e _All_. 
| forceUpdateTag | Ogni volta che è necessario forzare l'esecuzione dell'operazione, passare un valore univoco, ad esempio un GUID. |
| resizeOSDisk | La partizione del sistema operativo deve essere ridimensionata in modo da occupare il disco rigido virtuale completo del sistema operativo prima della divisione del volume di sistema. |
| location | Posizione per tutte le risorse. |


## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Usare la funzionalità EncryptFormatAll per i dischi dati nelle macchine virtuali Linux

Il parametro **EncryptFormatAll** riduce la durata per la crittografia dei dischi dati Linux. Le partizioni che soddisfano determinati criteri verranno formattate (con la file system corrente), quindi rimontate nella posizione in cui si trovava prima dell'esecuzione del comando. Se si desidera escludere un disco dati che soddisfa i criteri, è possibile smontarlo prima di eseguire il comando.

 Dopo aver eseguito questo comando, tutte le unità montate in precedenza verranno formattate e il livello di crittografia verrà avviato sopra l'unità ora vuota. Quando questa opzione è selezionata, viene crittografato anche il disco risorse temporaneo collegato alla macchina virtuale. Se l'unità temporanea viene reimpostata, viene riformattata e crittografata di nuovo per la macchina virtuale dalla soluzione Crittografia dischi di Azure alla successiva opportunità. Una volta crittografato il disco delle risorse, l' [agente Linux Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) non sarà in grado di gestire il disco delle risorse e di abilitare il file di scambio, ma è possibile configurare manualmente il file di scambio.

>[!WARNING]
> EncryptFormatAll non deve essere usato quando i volumi di dati di una macchina virtuale contengono dati necessari. Per escludere i dischi dalla crittografia, è possibile smontarli. È innanzitutto necessario provare EncryptFormatAll in una macchina virtuale di test, poi comprendere il parametro della funzione e la sua implicazione prima di provarlo nella macchina virtuale di produzione. L'opzione EncryptFormatAll formatta il disco dati e tutti i dati che contiene andranno persi. Prima di procedere, verificare che i dischi da escludere siano smontati correttamente. </br></br>
 >Se si imposta questo parametro durante l'aggiornamento delle impostazioni di crittografia, si potrebbe verificare un riavvio prima che la crittografia venga effettivamente eseguita. In questo caso, è anche possibile rimuovere dal file fstab il disco da non formattare. Analogamente, è necessario aggiungere al file fstab la partizione che si desidera formattare con crittografia e poi avviare l'operazione di crittografia. 

### <a name="encryptformatall-criteria"></a>Criteri di EncryptFormatAll
Il parametro esamina tutte le partizioni e le crittografa, a patto che soddisfino **tutti** i criteri seguenti: 
- Non è una partizione di avvio/del sistema operativo/radice
- Non è già stata crittografata
- Non è un volume BEK
- Non è un volume RAID
- Non è un volume LVM
- È montata

Crittografare i dischi che compongono il volume RAID o LVM anziché il volume stesso.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Usare il parametro EncryptFormatAll con l'interfaccia della riga di comando Azure
Usare il comando [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) per abilitare la crittografia in una macchina virtuale in esecuzione in Azure.

-  **Crittografare una macchina virtuale in esecuzione usando EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Usare il parametro EncryptFormatAll con un cmdlet di PowerShell
Usare il cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) con il parametro EncryptFormatAll. 

**Crittografare una macchina virtuale in esecuzione usando EncryptFormatAll:** Ad esempio, lo script seguente inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension con il parametro EncryptFormatAll. Il gruppo di risorse, la macchina virtuale e l'insieme di credenziali delle chiavi sono stati creati come prerequisiti. Sostituire MyVirtualMachineResourceGroup, MySecureVM e MySecureVault con i valori.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Usare il parametro EncryptFormatAll con Logical Volume Manager (LVM) 
È consigliabile usare una configurazione LVM-on-crypt. Per tutti gli esempi seguenti, sostituire il percorso del dispositivo e i punti di montaggio con qualunque elemento sia appropriato al caso d'uso. La configurazione può essere eseguita nel seguente modo:

- Aggiungere i dischi dati che compongono la macchina virtuale.
- Formattare, montare e aggiungere i dischi al file fstab.

    1. Formattare il disco appena aggiunto. Qui vengono usati i collegamenti simbolici generati da Azure. L'uso di collegamenti simbolici evita i problemi legati alla modifica dei nomi dei dispositivi. Per altre informazioni, vedere l'articolo [Risolvere il problema dei nomi di dispositivo](troubleshoot-device-names-problems.md).
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    1. Montare i dischi.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    1. Aggiungerli al file fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    1. Eseguire il cmdlet di PowerShell set-AzVMDiskEncryptionExtension con-EncryptFormatAll per crittografare questi dischi.

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. Configurare LVM sui nuovi dischi. Si noti che le unità crittografate vengono sbloccate dopo il termine dell'avvio della macchina virtuale. Pertanto, anche il montaggio di LVM dovrà essere posticipato.


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Nuove macchine virtuali create da chiavi di crittografia e VHD crittografati dal cliente
In questo scenario è possibile abilitare la crittografia usando i cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando. 

Usare le istruzioni nello stesso script di crittografia dischi di Azure per preparare immagini pre-crittografate che possono essere usate in Azure. Dopo aver creato l'immagine, è possibile usare i passaggi della sezione successiva per creare una VM di Azure crittografata.

* [Preparare un disco rigido virtuale Linux pre-crittografato](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >È obbligatorio catturare uno snapshot e/o eseguire il backup di un'istanza di macchina virtuale basata su un disco gestito all'esterno di Crittografia dischi di Azure e prima di abilitarla. È possibile catturare uno snapshot del disco gestito dal portale o tramite [Backup di Azure](../../backup/backup-azure-vms-encryption.md). I backup garantiscono la disponibilità di un'opzione di ripristino nel caso si verifichi un errore imprevisto durante la crittografia. Una volta eseguito un backup, è possibile usare il cmdlet Set-AzVMDiskEncryptionExtension per crittografare i dischi gestiti specificando il parametro-skipVmBackup. Il comando set-AzVMDiskEncryptionExtension avrà esito negativo rispetto alle VM basate su disco gestito fino a quando non viene eseguito un backup e questo parametro è stato specificato. 
>
> La crittografia o la disabilitazione della crittografia può causare il riavvio della macchina virtuale. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Usare Azure PowerShell per crittografare le macchine virtuali con dischi rigidi virtuali pre-crittografati 
È possibile abilitare la crittografia dei dischi nel disco rigido virtuale crittografato usando il cmdlet di PowerShell [set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). L'esempio seguente offre alcuni parametri comuni. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Abilitare la crittografia in un disco dati appena aggiunto

È possibile aggiungere un nuovo disco dati usando [az vm disk attach](add-disk.md) o [tramite il portale di Azure](attach-disk-portal.md). Prima che sia possibile eseguire la crittografia, è necessario montare il disco appena collegato. È necessario richiedere la crittografia del disco dati poiché l'unità non sarà utilizzabile mentre la crittografia è in corso. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Abilitare la crittografia in un disco appena aggiunto con l'interfaccia della riga di comando di Azure

 Se la macchina virtuale è stata precedentemente crittografata con "All", il parametro--volume-Type deve rimanere "All". Tale parametro include il disco del sistema operativo e il disco dati. Se la macchina virtuale è stata precedentemente crittografata con un tipo di volume "sistema operativo", il parametro--volume-Type deve essere impostato su "All" in modo che sia il sistema operativo sia il nuovo disco dati verranno inclusi. Se la macchina virtuale è stata crittografata solo con il tipo di volume "Data", è possibile lasciare invariato il parametro "Data", come illustrato di seguito. L'aggiunta e il collegamento di un nuovo disco dati a una macchina virtuale non sono condizioni sufficienti alla preparazione per la crittografia. Il disco appena collegato deve anche essere formattato e montato in modo corretto nella macchina virtuale prima dell'abilitazione della crittografia. In Linux il disco deve essere montato in /etc/fstab con un [nome del dispositivo a blocchi permanente](troubleshoot-device-names-problems.md).  

A differenza della sintassi di PowerShell, l'interfaccia della riga di comando non richiede all'utente di specificare una versione di sequenza univoca quando si abilita la crittografia. L'interfaccia della riga di comando genera e usa automaticamente uno specifico valore di versione di sequenza univoco.

-  **Crittografare i volumi di dati di una macchina virtuale in esecuzione:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Crittografare i volumi di dati di una macchina virtuale in esecuzione tramite una chiave di crittografia della chiave:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Abilitare la crittografia in un disco appena aggiunto con Azure PowerShell
 Quando si usa PowerShell per crittografare un nuovo disco per Linux, è necessario specificare una nuova versione della sequenza. La versione della sequenza deve essere univoca. Lo script seguente genera un GUID per la versione della sequenza. Eseguire uno [snapshot](snapshot-copy-managed-disk.md) e/o eseguire il backup della macchina virtuale con [backup di Azure](../../backup/backup-azure-vms-encryption.md) prima che i dischi siano crittografati. Il parametro-skipVmBackup è già specificato negli script di PowerShell per crittografare un disco dati appena aggiunto.
 

-  **Crittografare i volumi di dati di una macchina virtuale in esecuzione:** Lo script seguente inizializza le variabili ed esegue il cmdlet Set-AzVMDiskEncryptionExtension. Il gruppo di risorse, la macchina virtuale e l'insieme di credenziali delle chiavi dovrebbero essere già stati creati come prerequisiti. Sostituire MyVirtualMachineResourceGroup, MySecureVM e MySecureVault con i valori. I valori accettabili per il parametro -VolumeType sono All, OS e Data. Se la macchina virtuale è stata precedentemente crittografata con un tipo di volume "sistema operativo" o "tutti", il parametro-VolumeType deve essere impostato su "All" in modo che sia il sistema operativo sia il nuovo disco dati verranno inclusi.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Crittografare i volumi di dati di una macchina virtuale in esecuzione tramite una chiave di crittografia della chiave:** I valori accettabili per il parametro -VolumeType sono All, OS e Data. Se la macchina virtuale è stata precedentemente crittografata con un tipo di volume "OS" o "All", il parametro -VolumeType deve essere modificato in All, in modo che vengano inclusi sia il disco del sistema operativo sia il nuovo disco dati.

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > La sintassi per il valore del parametro Disk-Encryption-Vault è la stringa dell'identificatore completo:/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[KVresource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nome-insieme di credenziali]</br> La sintassi per il valore del parametro key-encryption-key è l'URI della chiave di crittografia della chiave, come in: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Disabilitare la crittografia per le macchine virtuali Linux
È possibile disabilitare la crittografia usando Azure PowerShell, l'interfaccia della riga di comando di Azure oppure un modello di Resource Manager. 

>[!IMPORTANT]
>La disabilitazione della crittografia con Crittografia dischi di Azure nelle macchine virtuali Linux è supportata solo per i volumi di dati. Non è supportata nei dati o nei volumi del sistema operativo, se il volume del sistema operativo è stato crittografato.  

- **Disabilitare la crittografia dei dischi con Azure PowerShell:** Per disabilitare la crittografia, usare il cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Disabilitare la crittografia con l'interfaccia della riga di comando di Azure:** per disabilitare la crittografia, usare il comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Disabilitare la crittografia con un modello di Resource Manager:** usare il modello per [disabilitare la crittografia in una macchina virtuale Linux in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad).
     1. Fare clic su **Distribuzione in Azure**.
     2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione, la macchina virtuale, i termini legali e il contratto.

## <a name="unsupported-scenarios"></a>Scenari non supportati

Crittografia dischi di Azure non funziona per gli scenari, le funzionalità e la tecnologia Linux seguenti:

- Crittografia di VM di livello Basic o macchine virtuali create tramite il metodo di creazione della macchina virtuale classica.
- Disabilitare la crittografia in un'unità del sistema operativo o in un'unità dati di una VM Linux quando l'unità del sistema operativo è crittografata.
- Crittografia dell'unità del sistema operativo per i set di scalabilità di macchine virtuali Linux.
- Crittografia di immagini personalizzate in macchine virtuali Linux.
- Integrazione con un sistema di gestione delle chiavi locale.
- File di Azure (file system condiviso).
- NFS (Network File System).
- Volumi dinamici.
- Dischi del sistema operativo effimeri.
- Crittografia dei file system condivisi/distribuiti, ad esempio (ma non limitati): DFS, GFS, DRDB e CephFS.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su Crittografia dischi di Azure](disk-encryption-overview.md)
- [Script di esempio di crittografia dischi di Azure](disk-encryption-sample-scripts.md)
- [Risoluzione dei problemi di crittografia dischi di Azure](disk-encryption-troubleshooting.md)
