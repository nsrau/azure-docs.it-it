<properties
   pageTitle="Distribuire e gestire il backup per le macchine virtuali ARM tramite PowerShell | Microsoft Azure"
   description="Usare PowerShell per distribuire e gestire i backup in Azure per le macchine virtuali ARM"
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="05/09/2016"
   ms.author="markgal; trinadhk"/>

# Distribuire e gestire il backup per le macchine virtuali ARM tramite PowerShell

> [AZURE.SELECTOR]
- [PowerShell di Resource Manager](backup-azure-vms-automation.md)
- [PowerShell classico](backup-azure-vms-classic-automation.md)

In questo articolo viene illustrato come usare PowerShell di Azure per eseguire il backup e ripristinare una macchina virtuale (VM) di Azure da un insieme di credenziali dei servizi di ripristino. Un insieme di credenziali dei servizi di ripristino è una risorsa di Resource Manager di Azure (ARM) e viene usato per proteggere i dati e asset nei servizi di Backup di Azure e di Azure Site Recovery. Usare un insieme di credenziali dei servizi di ripristino quando si lavora in una distribuzione ARM. È possibile usare un insieme di credenziali dei servizi di ripristino per proteggere le macchine virtuali distribuite da Service Manager di Azure (ASM) e da ARM.

>[AZURE.NOTE] Azure offre due modelli di distribuzione per creare e usare le risorse: [Resource Manager e distribuzione classica](../resource-manager-deployment-model.md). Questo articolo si riferisce alle VM create tramite il modello Resource Manager.

In questo articolo viene illustrato come usare PowerShell per proteggere una macchina virtuale e ripristinare i dati da un punto di ripristino.

## Concetti

Se non si ha familiarità con il servizio di Backup di Azure, vedere l'argomento [Che cos'è Backup di Azure?](backup-introduction-to-azure-backup.md) per una panoramica sul servizio. Prima di iniziare, assicurarsi di avere gli elementi di base per i prerequisiti necessari per usare il servizio Backup di Azure, e le limitazioni della soluzione attuale di backup della macchina virtuale.

Per un utilizzo efficace di PowerShell, è necessario comprendere la gerarchia degli oggetti e da dove iniziare.

![Gerarchia di oggetti dei servizi di ripristino](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## Installazione e registrazione

Per iniziare:

1. [Scaricare la versione più recente di PowerShell](https://github.com/Azure/azure-powershell/releases) (la versione minima richiesta è: 1.0.0)

2. Cercare i cmdlet PowerShell di Azure Backup disponibili digitando il comando seguente:

```
PS C:\WINDOWS\system32> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.0.7      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.0.7      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.0.7      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.0.7      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.0.7      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.0.7      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.0.7      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.0.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.0.0      AzureRM.RecoveryServices.Backup
```


Le attività seguenti possono essere automatizzate tramite PowerShell:

- Creare un insieme di credenziali di Servizi di ripristino
- Eseguire il backup o proteggere le macchine virtuali di Azure
- Attivare un processo di backup
- Monitorare un processo di backup
- Ripristinare una macchina virtuale di Azure

## Creare un insieme di credenziali di Servizi di ripristino

> [AZURE.TIP] Per i clienti che usano Backup di Azure per la prima volta, è necessario registrare il provider dei servizi di ripristino di Azure da usare con la sottoscrizione. A tale scopo, eseguire il comando seguente: Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"

È possibile creare un nuovo insieme di credenziali dei servizi di ripristino usando il cmdlet **New AzureRmRecoveryServicesVault**. L'insieme di credenziali dei servizi di ripristino è una risorsa ARM, pertanto è necessario inserirlo all'interno di un gruppo di risorse. Eseguire i comandi seguenti in una console di Azure PowerShell con privilegi elevati:

```
PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
```

Per visualizzare un elenco degli insiemi di credenziali dei servizi di ripristino in una sottoscrizione usare il cmdlet **Get-AzureRmRecoveryServicesVault**.

### Impostare la ridondanza di archiviazione
Quando si crea un insieme di credenziali dei servizi di ripristino, specificare il tipo di ridondanza di archiviazione da usare, ad esempio archiviazione con ridondanza locale (LRS) o archiviazione con ridondanza geografica (GRS). Nell'esempio seguente l'opzione BackupStorageRedundancy per testVault è impostata su GeoRedundant.

```
PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```


> [AZURE.TIP] Molti cmdlet di Backup di Azure richiedono l'oggetto dell'insieme di credenziali dei servizi di ripristino come input. Per questo motivo, è utile archiviare l'oggetto dell'insieme di credenziali dei servizi di ripristino del backup in una variabile.


## Macchine virtuali del servizio Backup di Azure

Prima di abilitare la protezione per una macchina virtuale, è necessario impostare il contesto dell'insieme di credenziali. Il contesto viene applicato a tutti i cmdlet successivi.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name testvault | Set-AzureRmRecoveryServicesVaultContext
```

### Creare i criteri di protezione

Quando si crea un nuovo insieme di credenziali, questo viene presentato con un criterio predefinito. Questo criterio attiva un processo di backup ogni giorno alle 9:30 PM. Lo snapshot di backup viene mantenuto per 30 giorni. È possibile usare i criteri predefiniti per proteggere rapidamente la macchina virtuale e modificare i criteri in un secondo momento con dettagli diversi.

Per visualizzare l'elenco disponibile dei criteri nell'insieme di credenziali usare il cmdlet Get-AzureRmRecoveryServicesBackupProtectionPolicy:

```
PS C:\WINDOWS\system32> get-AzureRMRecoveryServicesBackupProtectionPolicy -WorkloadType AzureVM
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [AZURE.NOTE] Il fuso orario del campo BackupTime in PowerShell è UTC. Tuttavia, l'orario di backup nel portale di Azure è allineato al fuso orario locale.

I criteri di protezione del backup sono associati almeno a un criterio di conservazione. I criteri di conservazione definiscono per quanto tempo un punto di ripristino viene mantenuto con Backup di Azure. Usare Get-AzureRmRecoveryServicesBackupRetentionPolicyObject per visualizzare i criteri di conservazione predefiniti. È possibile ottenere i criteri di pianificazione predefinita usando anche Get-AzureRmRecoveryServicesBackupSchedulePolicyObject. Gli oggetti dei criteri di conservazione e di pianificazione vengono usati come input per il cmdlet New-AzureRmRecoveryServicesBackupProtectionPolicy.

I criteri di protezione del backup definiscono quando viene eseguito il backup di un elemento e con quale frequenza. Il cmdlet New-AzureRmRecoveryServicesBackupProtectionPolicy crea un oggetto di PowerShell che contiene le informazioni relative ai criteri di backup. I criteri di backup vengono usati come input per il cmdlet Enable-AzureRmRecoveryServicesBackupProtection.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\>  $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\>  New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType AzureVM -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### Abilitare la protezione

Per abilitare la protezione sono necessari due oggetti, l'elemento e i criteri. Entrambi gli oggetti sono necessari per abilitare la protezione per l'insieme di credenziali. Dopo aver associato i criteri all'elemento, il flusso di lavoro di backup verrà attivato al momento definito nella pianificazione dei criteri.

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Per macchine virtuali ASM

```
PS C:\>  $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\>  Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### Modificare i criteri di protezione

Per fare questo, modificare l'oggetto BackupSchedulePolicyObject o BackupRetentionPolicy e modificare i criteri con Set-AzureRmRecoveryServicesBackupProtectionPolicy

Nell'esempio seguente viene modificato il periodo di conservazione a 365.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRMRecoveryServicesBackupProtectionPolicy -Name NewPolicy
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy  $RetPol
```

## Eseguire un backup iniziale

La pianificazione del backup attiva un backup completo per il backup iniziale dell'elemento. Nei backup successivi verrà eseguita una copia incrementale. Se si vuole forzare il backup iniziale in modo che venga eseguito a una determinata ora o immediatamente, usare il cmdlet Backup-AzureRmRecoveryServicesBackupItem:

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "V2VM";
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM";
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item;
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Backup               InProgress            4/23/2016 5:00:30 PM            cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [AZURE.NOTE: Il fuso orario dei campi StartTime e EndTime in PowerShell è UTC. Tuttavia, l'orario visualizzato nel portale di Azure è allineato al fuso orario locale.

## Monitoraggio di un processo di backup

La maggior parte delle operazioni a esecuzione prolungata in Azure Backup è modellata come processo. Questo consente di semplificare il monitoraggio dell'avanzamento senza dover tenere aperto continuamente il portale di Azure.

Per ottenere lo stato più recente di un processo in corso, usare il cmdlet Get-AzureRMRecoveryservicesBackupJob.

```
PS C:\ > $joblist = Get-AzureRMRecoveryservicesBackupJob –Status InProgress
PS C:\ > $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Invece di eseguire il polling dei processi per ottenere lo stato di completamento, operazione non necessaria che prevede codice aggiuntivo, è più facile usare il cmdlet Wait-AzureRmRecoveryServicesBackupJob. Se usato in uno script, il cmdlet sospende l'esecuzione fino al completamento del processo o fino a quando non viene raggiunto il valore di timeout specificato.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## Ripristinare una macchina virtuale di Azure

Per ripristinare i dati di backup, è necessario identificare l'elemento sottoposto a backup e il punto di ripristino che contiene i dati temporizzati. Queste informazioni vengono specificate con il cmdlet Restore-AzureRMRecoveryServicesBackupItem per avviare un ripristino dei dati dall'insieme di credenziali all'account del cliente.

### Selezionare la macchina virtuale

Per ottenere l'oggetto di PowerShell che identifica l'elemento di backup corretto, iniziare dal contenitore nell'insieme di credenziali e procedere verso il basso nella gerarchia degli oggetti. Per selezionare il contenitore che rappresenta la macchina virtuale, usare il cmdlet Get-AzureRmRecoveryServicesBackupContainer e inviarlo al cmdlet Get-AzureRmRecoveryServicesBackupItem.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType AzureVM –Status Registered -Name 'V2VM'
PS C:\> $backupitem=Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### Scegliere un punto di ripristino

A questo punto è possibile visualizzare l'elenco di tutti i punti di ripristino per l'elemento di backup usando il cmdlet Get-AzureRMRecoveryServicesBackupRecoveryPoint e scegliere il punto di ripristino. In genere, gli utenti selezionano il punto di ripristino AppConsistent più recente nell'elenco.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRMRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM
                              /recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

La variabile $rp è una matrice di punti di ripristino per l'elemento di backup selezionato e viene visualizzata in ordine cronologico inverso: il punto di ripristino più recente occupa la posizione 0 nell'indice. Per scegliere il punto di ripristino, usare l'indicizzazione standard della matrice di PowerShell. Ad esempio: $rp[0] seleziona il punto di ripristino più recente.

### Ripristino dei dischi

Le operazioni di ripristino eseguite tramite il portale di Azure e tramite Azure PowerShell presentano una differenza importante. Con PowerShell, l'operazione di ripristino si ferma quando deve ripristinare dischi e informazioni di configurazione dal punto di ripristino e non crea una macchina virtuale.

> [AZURE.WARNING] Restore-AzureRMRecoveryServicesBackupItem non crea una macchina virtuale, ma ripristina solo i dischi per l'account di archiviazione specificato. Questa operazione è diversa da quella che si verifica nel portale di Azure.

```
PS C:\> $restorejob = Restore-AzureRMRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName DestAccount
 -StorageAccountResourceGroupName DestRG
PS C:\> $restorejob
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM        Restore               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

È possibile ottenere i dettagli dell'operazione di ripristino usando il cmdlet Get-AzureRmRecoveryServicesBackupJobDetails al termine del processo di ripristino. La proprietà JobDetails contiene le informazioni necessarie per ricreare la macchina virtuale.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails
```

## Registrazione di Windows Server o Data Protection Manager con l'insieme di credenziali dei servizi di ripristino

Dopo aver creato l'insieme di credenziali dei servizi di ripristino, scaricare l'ultimo agente e le credenziali dell'insieme di credenziali e archiviarli in un percorso semplice da ricordare, ad esempio C:\\Downloads.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

In Windows Server o nel server DPM, eseguire il cmdlet [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) per registrare il computer con l'insieme di credenziali.

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

<!---HONumber=AcomDC_0518_2016-->