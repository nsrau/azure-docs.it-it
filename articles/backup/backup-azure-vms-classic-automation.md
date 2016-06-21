<properties
	pageTitle="Distribuire e gestire il backup per le macchine virtuali di Azure tramite PowerShell | Microsoft Azure"
	description="Informazioni su come distribuire e gestire Backup di Azure mediante PowerShell"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/09/2016"
	ms.author="markgal;trinadhk;jimpark" />


# Distribuire e gestire il backup per le macchine virtuali di Azure tramite PowerShell

> [AZURE.SELECTOR]
- [ARM](backup-azure-vms-automation.md)
- [Classico](backup-azure-vms-classic-automation.md)

Questo articolo descrive come usare Azure PowerShell per il backup e il ripristino delle macchine virtuali dell'infrastruttura distribuita come servizio (IaaS) di Azure.

## Concetti

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

Vedere l'[introduzione al backup di macchine virtuali IaaS di Azure](backup-azure-vms-introduction.md) nella documentazione del servizio Backup di Azure,

> [AZURE.WARNING] Prima di iniziare, assicurarsi di avere gli elementi di base per i [prerequisiti](backup-azure-vms-prepare.md) necessari per usare il servizio Backup di Azure, e le [limitazioni](backup-azure-vms-prepare.md#limitations) della soluzione attuale di backup della macchina virtuale.

Per un utilizzo efficace di PowerShell, è necessario comprendere la gerarchia degli oggetti e da dove iniziare.

![Gerarchia oggetti](./media/backup-azure-vms-classic-automation/object-hierarchy.png)

I due flussi più importanti sono quelli relativi all'attivazione della protezione per una macchina virtuale e al ripristino dei dati da un punto di ripristino. Questo articolo fornisce le informazioni necessarie per acquisire familiarità con l'uso dei cmdlet di PowerShell per abilitare questi due scenari.


## Installazione e registrazione
Per iniziare:

1. [Scaricare la versione più recente di PowerShell](https://github.com/Azure/azure-powershell/releases) (la versione minima richiesta è: 1.0.0)

2. Cercare i cmdlet PowerShell di Azure Backup disponibili digitando il comando seguente:

```
PS C:\> Get-Command *azurermbackup*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmBackupItem                           1.0.1      AzureRM.Backup
Cmdlet          Disable-AzureRmBackupProtection                    1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupContainerReregistration        1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupProtection                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupContainer                         1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupItem                              1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJob                               1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJobDetails                        1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupRecoveryPoint                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVaultCredentials                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupRetentionPolicyObject             1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Register-AzureRmBackupContainer                    1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupProtectionPolicy               1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupVault                          1.0.1      AzureRM.Backup
Cmdlet          Restore-AzureRmBackupItem                          1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Stop-AzureRmBackupJob                              1.0.1      AzureRM.Backup
Cmdlet          Unregister-AzureRmBackupContainer                  1.0.1      AzureRM.Backup
Cmdlet          Wait-AzureRmBackupJob                              1.0.1      AzureRM.Backup
```

Le attività di installazione e registrazione seguenti possono essere automatizzate tramite PowerShell:

- Creare un insieme di credenziali per il backup
- Registrazione delle macchine virtuali nel servizio Backup di Azure

### Creare un insieme di credenziali per il backup

> [AZURE.WARNING] I clienti che usano il servizio Backup di Azure per la prima volta, dovranno registrare il provider di Backup di Azure da usare con la propria sottoscrizione. A tale scopo, eseguire il comando seguente: Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Backup"

È possibile creare un nuovo insieme di credenziali per il backup usando il cmdlet **New-AzureRmBackupVault**. L’archivio di backup è una risorsa ARM, pertanto è necessario inserirlo all'interno di un gruppo di risorse. Eseguire i comandi seguenti in una console di Azure PowerShell con privilegi elevati:

```
PS C:\> New-AzureRmResourceGroup –Name “test-rg” –Location “West US”
PS C:\> $backupvault = New-AzureRmBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

È possibile ottenere un elenco di tutti gli insiemi di credenziali per il backup in una determinata sottoscrizione usando il cmdlet **Get-AzureRmBackupVault**.

> [AZURE.NOTE] È consigliabile archiviare l'oggetto insieme di credenziali di backup in una variabile. L'oggetto insieme di credenziali è necessario come input per molti cmdlet di Azure Backup.


### Registrazione delle macchine virtuali
Il primo passaggio per la configurazione del backup nel servizio Backup di Azure prevede la registrazione del computer o della macchina virtuale in un insieme di credenziali del servizio Backup di Azure. Il cmdlet **Register-AzureRmBackupContainer** accetta l'informazione di input di una macchina virtuale IaaS di Azure e la registra nell'insieme di credenziali specificato. L'operazione di registrazione associa la macchina virtuale di Azure all'insieme di credenziali di backup e tiene traccia della macchina virtuale per la durata del backup.

L'operazione di registrazione della macchina virtuale con il servizio Backup di Azure crea un oggetto contenitore di livello superiore. Un contenitore in genere contiene più elementi di cui si può eseguire il backup, ma nel caso delle macchine virtuali sarà presente un solo elemento di backup per il contenitore.

```
PS C:\> $registerjob = Register-AzureRmBackupContainer -Vault $backupvault -Name "testvm" -ServiceName "testvm"
```

## Macchine virtuali del servizio Backup di Azure

### Creare i criteri di protezione
Non è obbligatorio creare nuovi criteri di protezione per avviare il backup delle macchine virtuali. Insieme di credenziali include 'criteri predefiniti' che possono essere usati per abilitare rapidamente la protezione e che possono essere modificati in un secondo momento per impostare i dettagli corretti. È possibile ottenere un elenco dei criteri disponibili usando il cmdlet **Get-AzureRmBackupProtectionPolicy**:

```
PS C:\> Get-AzureRmBackupProtectionPolicy -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DefaultPolicy             AzureVM            Daily              26-Aug-15 12:30:00 AM
```

> [AZURE.NOTE] Il fuso orario del campo BackupTime in PowerShell è UTC. Tuttavia, quando l'orario di backup viene visualizzato nel portale di Azure, il fuso orario è allineato a quello del sistema locale assieme alla differenza dall'ora UTC.

I criteri di backup sono associati ai criteri di conservazione. I criteri di conservazione definiscono per quanto tempo un punto di ripristino viene mantenuto nel servizio Backup di Azure. Il cmdlet **New-AzureRmBackupRetentionPolicy** crea oggetti PowerShell che contengono le informazioni relative ai criteri di conservazione. Questi oggetti di criteri di conservazione vengono usati come input per il cmdlet *New-AzureRmBackupProtectionPolicy* o direttamente con il cmdlet *Enable-AzureRmBackupProtection*.

I criteri di backup consentono di definire quando viene eseguito il backup di un elemento e con quale frequenza. Il cmdlet **New-AzureRmBackupProtectionPolicy** crea un oggetto PowerShell che contiene le informazioni relative ai criteri di backup. I criteri di backup vengono usati come input per il cmdlet *Enable-AzureRmBackupProtection*.

```
PS C:\> $Daily = New-AzureRmBackupRetentionPolicyObject -DailyRetention -Retention 30
PS C:\> $newpolicy = New-AzureRmBackupProtectionPolicy -Name DailyBackup01 -Type AzureVM -Daily -BackupTime ([datetime]"3:30 PM") -RetentionPolicy $Daily -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DailyBackup01             AzureVM            Daily              01-Sep-15 3:30:00 PM
```

### Abilitare la protezione
Per abilitare la protezione sono necessari due oggetti, l'elemento e i criteri, ed entrambi devono appartenere allo stesso insieme di credenziali. Dopo aver associato i criteri all'elemento, il flusso di lavoro di backup verrà avviato in base alla pianificazione del backup.

```
PS C:\> Get-AzureRmBackupContainer -Type AzureVM -Status Registered -Vault $backupvault | Get-AzureRmBackupItem | Enable-AzureRmBackupProtection -Policy $newpolicy
```

### Backup iniziale
La pianificazione del backup esegue la copia iniziale completa dell'elemento e la copia incrementale per i backup successivi. Se si vuole forzare il backup iniziale in modo che venga eseguito a una determinata ora o immediatamente, usare il cmdlet **Backup-AzureRmBackupItem**:

```
PS C:\> $container = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -Name "testvm"
PS C:\> $backupjob = Get-AzureRmBackupItem -Container $container | Backup-AzureRmBackupItem
PS C:\> $backupjob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

> [AZURE.NOTE] Il fuso orario dei campi StartTime ed EndTime visualizzati in PowerShell è UTC. Tuttavia, quando le informazioni corrispondenti vengono visualizzate nel portale di Azure, il fuso orario è allineato a quello dell'orologio del sistema locale.

### Monitoraggio di un processo di backup
La maggior parte delle operazioni a esecuzione prolungata in Azure Backup è modellata come processo. Questo consente di semplificare il monitoraggio dell'avanzamento senza dover tenere aperto continuamente il portale di Azure.

Per ottenere lo stato più recente di un processo in corso, usare il cmdlet **Get-AzureRmBackupJob**.

```
PS C:\> $joblist = Get-AzureRmBackupJob -Vault $backupvault -Status InProgress
PS C:\> $joblist[0]

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

Invece di eseguire il polling dei processi per ottenere lo stato di completamento, operazione non necessaria che prevede codice aggiuntivo, è più facile usare il cmdlet **Wait-AzureRmBackupJob**. Se usato in uno script, il cmdlet sospende l'esecuzione fino al completamento del processo o fino a quando non viene raggiunto il valore di timeout specificato.

```
PS C:\> Wait-AzureRmBackupJob -Job $joblist[0] -Timeout 43200
```


## Ripristinare una macchina virtuale di Azure

Per ripristinare i dati di backup, è necessario identificare l'elemento sottoposto a backup e il punto di ripristino che contiene i dati temporizzati. Queste informazioni vengono fornite al cmdlet Restore-AzureRmBackupItem per avviare un ripristino dei dati dall'insieme di credenziali all'account del cliente.

### Selezionare la macchina virtuale

Per ottenere l'oggetto PowerShell che individua l'elemento di backup corretto, è necessario iniziare dal contenitore nell'insieme di credenziali, quindi procedere verso il basso nella gerarchia degli oggetti. Per selezionare il contenitore che rappresenta la macchina virtuale, usare il cmdlet **Get-AzureRmBackupContainer** e inviarlo al cmdlet **Get-AzureRmBackupItem**.

```
PS C:\> $backupitem = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -name "testvm" | Get-AzureRmBackupItem
```

### Scegliere un punto di ripristino

A questo punto è possibile visualizzare l'elenco di tutti i punti di ripristino per l'elemento di backup usando il cmdlet **Get-AzureRmBackupRecoveryPoint** e scegliere il punto di ripristino. In genere, gli utenti selezionano il punto di ripristino *AppConsistent* più recente nell'elenco.

```
PS C:\> $rp =  Get-AzureRmBackupRecoveryPoint -Item $backupitem
PS C:\> $rp

RecoveryPointId    RecoveryPointType  RecoveryPointTime      ContainerName
---------------    -----------------  -----------------      -------------
15273496567119     AppConsistent      01-Sep-15 12:27:38 PM  iaasvmcontainer;testvm;testv...
```

La variabile ```$rp``` è una matrice di punti di ripristino per l'elemento di backup selezionato, in ordine cronologico inverso: il punto di ripristino più recente è all'indice 0. Per scegliere il punto di ripristino, usare l'indicizzazione standard della matrice di PowerShell. Ad esempio, ```$rp[0]``` selezionerà l'ultimo punto di ripristino.

### Ripristino dei dischi

Le operazioni di ripristino eseguite tramite il portale di Azure e tramite Azure PowerShell presentano una differenza importante. Con PowerShell, l'operazione di ripristino si arresta una volta ripristinati i dischi e le informazioni di configurazione dal punto di ripristino e non crea una macchina virtuale.

> [AZURE.WARNING] Il cmdlet Restore-AzureRmBackupItem non crea una macchina virtuale. Ripristina semplicemente i dischi nell'account di archiviazione specificato. L'operazione di ripristino tramite il portale di Azure prevede un comportamento diverso.

```
PS C:\> $restorejob = Restore-AzureRmBackupItem -StorageAccountName "DestAccount" -RecoveryPoint $rp[0]
PS C:\> $restorejob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Restore         InProgress      01-Sep-15 1:14:01 PM   01-Jan-01 12:00:00 AM
```

È possibile ottenere i dettagli dell'operazione di ripristino usando il cmdlet **Get-AzureRmBackupJobDetails** al termine del processo di ripristino. La proprietà *ErrorDetails* conterrà le informazioni necessarie per ricreare la macchina virtuale.

```
PS C:\> $restorejob = Get-AzureRmBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmBackupJobDetails -Job $restorejob
```

### Creare la macchina virtuale

È possibile creare la macchina virtuale dai dischi ripristinati tramite i cmdlet di PowerShell per la gestione del servizio Azure precedenti, i nuovi modelli di Azure Resource Manager o il portale di Azure. Questo esempio rapido mostra come eseguire l'operazione usando i cmdlet per la gestione del servizio Azure.

```
 $properties  = $details.Properties

 $storageAccountName = $properties["Target Storage Account Name"]
 $containerName = $properties["Config Blob Container Name"]
 $blobName = $properties["Config Blob Name"]

 $keys = Get-AzureStorageKey -StorageAccountName $storageAccountName
 $storageAccountKey = $keys.Primary
 $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey


 $destination_path = "C:\Users\admin\Desktop\vmconfig.xml"
 Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path -Context $storageContext


$obj = [xml](((Get-Content -Path $destination_path -Encoding UniCode)).TrimEnd([char]0x00))
 $pvr = $obj.PersistentVMRole
 $os = $pvr.OSVirtualHardDisk
 $dds = $pvr.DataVirtualHardDisks
 $osDisk = Add-AzureDisk -MediaLocation $os.MediaLink -OS $os.OS -DiskName "panbhaosdisk"
 $vm = New-AzureVMConfig -Name $pvr.RoleName -InstanceSize $pvr.RoleSize -DiskName $osDisk.DiskName

 if (!($dds -eq $null))
 {
	 foreach($d in $dds.DataVirtualHardDisk)
 	 {
		 $lun = 0
		 if(!($d.Lun -eq $null))
		 {
	 		 $lun = $d.Lun
		 }
		 $name = "panbhadataDisk" + $lun
     Add-AzureDisk -DiskName $name -MediaLocation $d.MediaLink
     $vm | Add-AzureDataDisk -Import -DiskName $name -LUN $lun
	}
}

New-AzureVM -ServiceName "panbhasample" -Location "SouthEast Asia" -VM $vm
```

Per altre informazioni su come creare una macchina virtuale dai dischi ripristinati, vedere i cmdlet seguenti:

- [Add-AzureDisk](https://msdn.microsoft.com/library/azure/dn495252.aspx)
- [New-AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx)
- [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)

## Esempi di codice

### 1\. Ottenere lo stato di completamento delle sottoattività dei processi

Per monitorare lo stato di completamento delle singole sottoattività si può usare il cmdlet **Get-AzureRmBackupJobDetails**:

```
PS C:\> $details = Get-AzureRmBackupJobDetails -JobId $backupjob.InstanceId -Vault $backupvault
PS C:\> $details.SubTasks

Name                                                        Status
----                                                        ------
Take Snapshot                                               Completed
Transfer data to Backup vault                               InProgress
```

### 2\. Creare un report settimanale o giornaliero dei processi di backup

In genere, gli amministratori vogliono sapere quali processi di backup sono stati eseguiti nelle ultime 24 ore e conoscerne lo stato. Inoltre, conoscere la quantità di dati trasferiti consente agli amministratori di valutare l'utilizzo mensile di dati. Lo script seguente estrae i dati non elaborati dal servizio Azure Backup e visualizza le informazioni nella console di PowerShell.

```
param(  [Parameter(Mandatory=$True,Position=1)]
        [string]$backupvaultname,

        [Parameter(Mandatory=$False,Position=2)]
        [int]$numberofdays = 7)


#Initialize variables
$DAILYBACKUPSTATS = @()
$backupvault = Get-AzureRmBackupVault -Name $backupvaultname
$enddate = ([datetime]::Today).AddDays(1)
$startdate = ([datetime]::Today)

for( $i = 1; $i -le $numberofdays; $i++ )
{
    # We query one day at a time because pulling 7 days of data might be too much
    $dailyjoblist = Get-AzureRmBackupJob -Vault $backupvault -From $startdate -To $enddate -Type AzureVM -Operation Backup
    Write-Progress -Activity "Getting job information for the last $numberofdays days" -Status "Day -$i" -PercentComplete ([int]([decimal]$i*100/$numberofdays))

    foreach( $job in $dailyjoblist )
    {
        #Extract the information for the reports
        $newstatsobj = New-Object System.Object
        $newstatsobj | Add-Member -Type NoteProperty -Name Date -Value $startdate
        $newstatsobj | Add-Member -Type NoteProperty -Name VMName -Value $job.WorkloadName
        $newstatsobj | Add-Member -Type NoteProperty -Name Duration -Value $job.Duration
        $newstatsobj | Add-Member -Type NoteProperty -Name Status -Value $job.Status

        $details = Get-AzureRmBackupJobDetails -Job $job
        $newstatsobj | Add-Member -Type NoteProperty -Name BackupSize -Value $details.Properties["Backup Size"]
        $DAILYBACKUPSTATS += $newstatsobj
    }

    $enddate = $enddate.AddDays(-1)
    $startdate = $startdate.AddDays(-1)
}

$DAILYBACKUPSTATS | Out-GridView
```

Se si vogliono aggiungere funzionalità per la creazione di grafici sull'output del report, leggere il post sulla [creazione di grafici con PowerShell](http://blogs.technet.com/b/richard_macdonald/archive/2009/04/28/3231887.aspx) nel blog TechNet

## Passaggi successivi

Se si preferisce utilizzare PowerShell per interagire con le risorse di Azure, consultare l’articolo di PowerShell per la protezione di Windows Server, [Distribuire e gestire il servizio di backup per Windows Server](./backup-client-automation-classic.md). È inoltre disponibile un articolo di PowerShell per la gestione dei backup di DPM, [Distribuire e gestire il servizio di backup per DPM](./backup-dpm-automation-classic.md). Entrambi gli articoli prevedono due versioni: una per la distribuzione con Resource Manager, l’altra per la distribuzione classica.

<!---HONumber=AcomDC_0608_2016-->