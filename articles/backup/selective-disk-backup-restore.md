---
title: Backup e ripristino di dischi selettivi per macchine virtuali di Azure
description: Questo articolo illustra come eseguire il backup e il ripristino dei dischi selettivi con la soluzione di backup della macchina virtuale di Azure.
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: references_regions
ms.openlocfilehash: 21e4ead8b3302ceef4cc53c126b9eab5784544b4
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92174112"
---
# <a name="selective-disk-backup-and-restore-for-azure-virtual-machines"></a>Backup e ripristino di dischi selettivi per macchine virtuali di Azure

Backup di Azure supporta il backup di tutti i dischi (sistema operativo e dati) in una macchina virtuale insieme usando la soluzione di backup della macchina virtuale. A questo punto, usando la funzionalità di backup e ripristino dei dischi selettivi, è possibile eseguire il backup di un subset di dischi dati in una macchina virtuale. Questa è una soluzione efficiente ed economica per soddisfare le esigenze di backup e ripristino. Ogni punto di ripristino contiene solo i dischi inclusi nell'operazione di backup. In questo modo è possibile avere un subset di dischi ripristinati dal punto di ripristino specificato durante l'operazione di ripristino. Questo vale sia per il ripristino da snapshot che per l'insieme di credenziali.

## <a name="scenarios"></a>Scenari

Questa soluzione è particolarmente utile negli scenari seguenti:

1. Se è necessario eseguire il backup dei dati critici in un solo disco o in un subset di dischi e non si vuole eseguire il backup dei restanti dischi collegati a una macchina virtuale per ridurre al minimo i costi di archiviazione di backup.
2. Se sono presenti altre soluzioni di backup per una parte della VM o dei dati. Ad esempio, se si esegue il backup dei database o dei dati usando una soluzione di backup del carico di lavoro diversa e si vuole usare il backup a livello di VM di Azure per i restanti dati o dischi, per creare un sistema efficiente e affidabile usando le funzionalità migliori disponibili.

Tramite PowerShell o l'interfaccia della riga di comando di Azure è possibile configurare il backup selettivo del disco della VM di Azure.  Usando uno script, è possibile includere o escludere dischi dati usando i numeri di LUN.  Attualmente, la possibilità di configurare il backup dei dischi selettivi tramite il portale di Azure è limitata all'opzione **solo disco del sistema operativo di backup** . È quindi possibile configurare il backup della macchina virtuale di Azure con il disco del sistema operativo ed escludere tutti i dischi dati collegati.

>[!NOTE]
> Per impostazione predefinita, il disco del sistema operativo viene aggiunto al backup della macchina virtuale e non può essere escluso.

## <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

Assicurarsi di usare AZ CLI Version 2.0.80 o versione successiva. È possibile ottenere la versione dell'interfaccia della riga di comando con questo comando:

```azurecli
az --version
```

Accedere all'ID sottoscrizione in cui si trovano l'insieme di credenziali dei servizi di ripristino e la macchina virtuale:

```azurecli
az account set -s {subscriptionID}
```

>[!NOTE]
>In ogni comando riportato di seguito è necessario solo il nome **ResourceGroup** , non l'oggetto, corrispondente all'insieme di credenziali.

### <a name="configure-backup-with-azure-cli"></a>Configurare backup con l'interfaccia della riga di comando di Azure

Durante l'operazione di configurazione della protezione, è necessario specificare l'impostazione dell'elenco di dischi con un parametro di esclusione dell' **inclusione**  /  **exclusion** , assegnando i numeri LUN dei dischi da includere o escludere nel backup.

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting exclude --diskslist 0 1
```

Se la macchina virtuale non si trova nello stesso gruppo di risorse dell'insieme di credenziali, **ResourceGroup** fa riferimento al gruppo di risorse in cui è stato creato l'insieme di credenziali. Al posto del nome della macchina virtuale, specificare l'ID della macchina virtuale come indicato di seguito.

```azurecli
az backup protection enable-for-vm  --resource-group {ResourceGroup} --vault-name {vaultname} --vm $(az vm show -g VMResourceGroup -n MyVm --query id --output tsv) --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

### <a name="modify-protection-for-already-backed-up-vms-with-azure-cli"></a>Modificare la protezione per le VM già sottoposte a backup con interfaccia della riga di comando

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting exclude --diskslist {LUN number(s) separated by space}
```

### <a name="backup-only-os-disk-during-configure-backup-with-azure-cli"></a>Backup solo del disco del sistema operativo durante la configurazione del backup con l'interfaccia della riga

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --exclude-all-data-disks
```

### <a name="backup-only-os-disk-during-modify-protection-with-azure-cli"></a>Backup solo del disco del sistema operativo durante la modifica della protezione con l'interfaccia della riga

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --exclude-all-data-disks
```

### <a name="restore-disks-with-azure-cli"></a>Ripristinare dischi con l'interfaccia della riga di comando di Azure

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --diskslist {LUN number of the disk(s) to be restored}
```

### <a name="restore-only-os-disk-with-azure-cli"></a>Ripristinare solo il disco del sistema operativo con l'interfaccia della riga

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} } --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-only-osdisk
```

### <a name="get-protected-item-to-get-disk-exclusion-details-with-azure-cli"></a>Ottenere l'elemento protetto per ottenere i dettagli di esclusione disco con l'interfaccia della riga di comando

```azurecli
az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

È stato aggiunto un parametro **diskExclusionProperties** aggiuntivo all'elemento protetto, come illustrato di seguito:

```azurecli
"extendedProperties": {
      "diskExclusionProperties": {
        "diskLunList": [
          0,
          1
        ],
        "isInclusionList": true
      }
```

### <a name="get-backup-job-with-azure-cli"></a>Ottenere un processo di backup con l'interfaccia della riga di comando

```azurecli
az backup job show --vault-name {vaultname} --resource-group {resourcegroup} -n {BackupJobID}
```

Questo comando consente di ottenere i dettagli dei dischi sottoposti a backup e dei dischi esclusi, come illustrato di seguito:

```output
   "Backed-up disk(s)": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b; diskextest_DataDisk_0; diskextest_DataDisk_1",  "Backup Size": "0 MB",
   "Excluded disk(s)": "diskextest_DataDisk_2",
```

### <a name="list-recovery-points-with-azure-cli"></a>Elencare punti di ripristino con l'interfaccia della riga di comando

```azurecli
az backup recoverypoint list --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM
```

In questo modo vengono fornite informazioni sul numero di dischi collegati e sottoposti a backup nella macchina virtuale.

```azurecli
      "recoveryPointDiskConfiguration": {
        "excludedDiskList": null,
        "includedDiskList": null,
        "numberOfDisksAttachedToVm": 4,
        "numberOfDisksIncludedInBackup": 3
};
```

### <a name="get-recovery-point-with-azure-cli"></a>Ottenere il punto di ripristino con l'interfaccia della riga di comando

```azurecli
az backup recoverypoint show --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -n {recoverypointID}
```

Ogni punto di ripristino contiene le informazioni dei dischi inclusi ed esclusi:

```azurecli
  "recoveryPointDiskConfiguration": {
      "excludedDiskList": [
        {
          "lun": 2,
          "name": "diskextest_DataDisk_2"
        }
      ],
      "includedDiskList": [
        {
          "lun": -1,
          "name": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b"
        },
        {
          "lun": 0,
          "name": "diskextest_DataDisk_0"
        },
        {
          "lun": 1,
          "name": "diskextest_DataDisk_1"
        }
      ],
      "numberOfDisksAttachedToVm": 4,
      "numberOfDisksIncludedInBackup": 3
```

### <a name="remove-disk-exclusion-settings-and-get-protected-item-with-azure-cli"></a>Rimuovere le impostazioni di esclusione disco e ottenere un elemento protetto con l'interfaccia della riga di comando

```azurecli
az backup protection update-for-vm --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting resetexclusionsettings

az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

Quando si eseguono questi comandi, verrà visualizzato `"diskExclusionProperties": null` .

## <a name="using-powershell"></a>Uso di PowerShell

Assicurarsi di usare Azure PowerShell versione 3.7.0 o successiva.

### <a name="enable-backup-with-powershell"></a>Abilitare il backup con PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -DiskListSetting "Include"/"Exclude" -DisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-configure-backup-with-powershell"></a>Backup solo del disco del sistema operativo durante la configurazione del backup con PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="get-backup-item-object-to-be-passed-in-modify-protection-with-powershell"></a>Ottenere un oggetto elemento di backup da passare in modificare la protezione con PowerShell

```azurepowershell
$item= Get-AzRecoveryServicesBackupItem -BackupManagementType "AzureVM" -WorkloadType "AzureVM" -VaultId $Vault.ID -FriendlyName "V2VM"
```

È necessario passare l'oggetto precedente ottenuto **$Item** al parametro **– Item** nei cmdlet seguenti.

### <a name="modify-protection-for-already-backed-up-vms-with-powershell"></a>Modificare la protezione per le VM già sottoposte a backup con PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -DiskListSetting "Include"/"Exclude" -DisksList[Strings]   -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-modify-protection-with-powershell"></a>Backup solo del disco del sistema operativo durante la modifica della protezione con PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="reset-disk-exclusion-setting-with-powershell"></a>Reimposta l'impostazione di esclusione del disco con PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -DiskListSetting "Reset" -VaultId $targetVault.ID
```

### <a name="restore-selective-disks-with-powershell"></a>Ripristinare i dischi selettivi con PowerShell

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreDiskList [Strings]
```

### <a name="restore-only-os-disk-with-powershell"></a>Ripristinare solo il disco del sistema operativo con PowerShell

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreOnlyOSDisk
```

## <a name="using-the-azure-portal"></a>Uso del portale di Azure

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

Usando il portale di Azure, è possibile visualizzare i dischi inclusi ed esclusi dal riquadro dei dettagli del backup della macchina virtuale e dal riquadro dei dettagli del processo di backup.  Durante il ripristino, quando si seleziona il punto di ripristino da cui eseguire il ripristino, è possibile visualizzare i dischi di cui è stato eseguito il backup in tale punto di ripristino.

Qui è possibile visualizzare i dischi inclusi ed esclusi per una macchina virtuale nel portale dal riquadro dei dettagli del backup della VM:

![Visualizzare i dischi inclusi ed esclusi dal riquadro dei dettagli del backup](./media/selective-disk-backup-restore/backup-details.png)

Qui è possibile visualizzare i dischi inclusi ed esclusi in un backup dal riquadro dei dettagli del processo:

![Visualizzare i dischi inclusi ed esclusi dal riquadro dei dettagli del processo](./media/selective-disk-backup-restore/job-details.png)

Qui è possibile visualizzare i dischi di cui è stato eseguito il backup durante il ripristino, quando si seleziona il punto di ripristino da cui eseguire il ripristino:

![Visualizzare i dischi di cui è stato eseguito il backup durante il ripristino](./media/selective-disk-backup-restore/during-restore.png)

La configurazione dell'esperienza di backup dei dischi selettivi per una macchina virtuale tramite il portale di Azure è limitata **solo al disco del sistema operativo di backup** . Per usare il backup di dischi selettivi in una VM già sottoposta a backup o per l'inclusione o l'esclusione di dischi dati specifici di una VM, usare PowerShell o l'interfaccia della riga di comando di Azure.

>[!NOTE]
>Se i dati si estendono su più dischi, assicurarsi che tutti i dischi dipendenti siano inclusi nel backup. Se non si esegue il backup di tutti i dischi dipendenti in un volume, durante il ripristino non verrà creato il volume che comprende alcuni dischi non sottoposti a backup.

### <a name="backup-os-disk-only-in-the-azure-portal"></a>Backup del disco del sistema operativo solo nel portale di Azure

Quando si Abilita il backup con portale di Azure, è possibile scegliere l'opzione **solo disco del sistema operativo di backup** . È quindi possibile configurare il backup della macchina virtuale di Azure con il disco del sistema operativo ed escludere tutti i dischi dati collegati.

![Configurare il backup solo per il disco del sistema operativo](./media/selective-disk-backup-restore/configure-backup-operating-system-disk.png)

## <a name="using-azure-rest-api"></a>Uso dell'API REST di Azure

È possibile configurare il backup delle VM di Azure con pochi dischi selezionati oppure modificare la protezione di una macchina virtuale esistente per includere/escludere pochi dischi, come descritto [qui](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup).

## <a name="selective-disk-restore"></a>Ripristino del disco selettivo

Il ripristino del disco selettivo è una funzionalità aggiunta che si ottiene quando si Abilita la funzionalità di backup dei dischi selettivi. Con questa funzionalità è possibile ripristinare i dischi selettivi da tutti i dischi di cui è stato eseguito il backup in un punto di ripristino. È più efficiente e consente di risparmiare tempo negli scenari in cui si sa quale disco deve essere ripristinato.

- Il disco del sistema operativo è incluso per impostazione predefinita nel backup e nel ripristino della macchina virtuale e non può essere escluso.
- Il ripristino del disco selettivo è supportato solo per i punti di ripristino creati dopo l'abilitazione della funzionalità di esclusione del disco.
- I backup con l'impostazione Escludi disco **in** supportano solo l'opzione di **ripristino del disco** . In questo caso non sono supportate le opzioni di ripristino delle **macchine virtuali** o **Sostituisci esistenti** .

![L'opzione per ripristinare la macchina virtuale e sostituire esistente non è disponibile durante l'operazione di ripristino](./media/selective-disk-backup-restore/options-not-available.png)

## <a name="limitations"></a>Limitazioni

La funzionalità di backup dei dischi selettivi non è supportata per le macchine virtuali classiche e per le macchine virtuali Quindi, le macchine virtuali di Azure crittografate con crittografia dischi di Azure (ADE) con BitLocker per la crittografia della macchina virtuale Windows e la funzionalità dm-crypt per le macchine virtuali Linux non sono supportate.

Le opzioni di ripristino per **creare una nuova macchina virtuale** e **Sostituisci esistente** non sono supportate per la macchina virtuale per cui è abilitata la funzionalità di backup dei dischi selettivi

Attualmente, il backup delle VM di Azure non supporta le macchine virtuali con dischi rigidi o dischi condivisi collegati. Il backup su disco selettivo non può essere usato in questi casi, che escludono il disco e il backup della macchina virtuale.

## <a name="billing"></a>Fatturazione

Il backup della macchina virtuale di Azure segue il modello di determinazione dei prezzi esistente, illustrato in dettaglio [qui](https://azure.microsoft.com/pricing/details/backup/).

Il **costo dell'istanza protetta (PI)** viene calcolato per il disco del sistema operativo solo se si sceglie di eseguire il backup usando solo l'opzione del **disco del sistema operativo** .  Se si configura il backup e si seleziona almeno un disco dati, il costo PI verrà calcolato per tutti i dischi collegati alla macchina virtuale. Il **costo di archiviazione dei backup** viene calcolato in base ai soli dischi inclusi e consente di risparmiare sui costi di archiviazione. Il **costo dello snapshot** viene sempre calcolato per tutti i dischi della macchina virtuale (dischi inclusi ed esclusi).

Se è stata scelta la funzionalità Ripristino tra aree (CRR), i [prezzi di CRR](https://azure.microsoft.com/pricing/details/backup/) si applicano al costo di archiviazione dei backup dopo aver escluso il disco.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-is-protected-instance-pi-cost-calculated-for-only-os-disk-backup-in-windows-and-linux"></a>Come viene calcolato il costo dell'istanza protetta (PI) per solo il backup del disco del sistema operativo in Windows e Linux?

Il costo PI viene calcolato in base alle dimensioni effettive (usate) della macchina virtuale.

- Per Windows: il calcolo dello spazio usato si basa sull'unità che archivia il sistema operativo, in genere C:).
- Per Linux: il calcolo dello spazio usato si basa sul dispositivo in cui è montato il file system radice (/).

### <a name="i-have-configured-only-os-disk-backup-why-is-the-snapshot-happening-for-all-the-disks"></a>Ho configurato solo il backup del disco del sistema operativo, perché lo snapshot viene eseguita per tutti i dischi?

Le funzionalità di backup su disco selettivo consentono di risparmiare sui costi di archiviazione dell'insieme di credenziali di backup grazie alla protezione avanzata dei dischi inclusi che fanno parte del backup. Tuttavia, lo snapshot viene utilizzato per tutti i dischi collegati alla macchina virtuale. Quindi, il costo dello snapshot viene sempre calcolato per tutti i dischi della macchina virtuale (dischi inclusi ed esclusi). Per ulteriori informazioni, vedere [fatturazione](#billing).

### <a name="i-cant-configure-backup-for-the-azure-virtual-machine-by-excluding-ultra-disk-or-shared-disks-attached-to-the-vm"></a>Non è possibile configurare il backup per la macchina virtuale di Azure escludendo il disco Ultra o i dischi condivisi collegati alla VM

La funzionalità di backup su disco selettivo è una funzionalità disponibile nella soluzione di backup della macchina virtuale di Azure. Attualmente, il backup delle VM di Azure non supporta le macchine virtuali con disco rigido o dischi condivisi collegati.

## <a name="next-steps"></a>Passaggi successivi

- [Matrice di supporto per il backup di macchine virtuali di Azure](backup-support-matrix-iaas.md)
- [Domande frequenti-eseguire il backup di macchine virtuali di Azure](backup-azure-vm-backup-faq.md)
