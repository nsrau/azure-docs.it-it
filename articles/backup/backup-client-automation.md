<properties
	pageTitle="Distribuire e gestire il backup per Windows Server/Client mediante PowerShell | Microsoft Azure"
	description="Informazioni su come distribuire e gestire Backup di Azure mediante PowerShell"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/22/2016" ms.author="markgal"; "aashishr"; "jimpark"/>


# Distribuire e gestire il backup in Azure per server Windows/client Windows mediante PowerShell
Questo articolo illustra come usare PowerShell per configurare Backup di Azure in un server o un client Windows e per gestire le operazioni di backup e ripristino.

## Installare Azure PowerShell
A ottobre 2015 è stato rilasciato Azure PowerShell 1.0. Questa versione ha fatto seguito alla versione 0.9.8 introducendo alcune modifiche significative, in particolare nel modello di denominazione dei cmdlet. I cmdlet 1.0 seguono il criterio di denominazione {verb}-AzureRm{noun}, mentre i nomi 0.9.8 non includono **Rm** (ad esempio, New-AzureRmResourceGroup anziché New-AzureResourceGroup). Quando si usa Azure PowerShell 0.9.8, è innanzitutto necessario abilitare la modalità Gestione risorse eseguendo il comando **Switch-AzureMode AzureResourceManager**. Questo comando non è necessario nella versione di 1.0 o successiva.

Se si vogliono usare script scritti per l'ambiente 0.9.8 nell'ambiente 1.0 o versione successiva, occorre testare attentamente gli script in un ambiente di preproduzione prima di usarli nell'ambiente di produzione, per evitare un impatto non previsto.

[Scaricare la versione più recente di PowerShell](https://github.com/Azure/azure-powershell/releases) (la versione minima richiesta è: 1.0.0)


[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]


## Creare un insieme di credenziali per il backup

> [AZURE.WARNING] I clienti che usano il servizio Backup di Azure per la prima volta, dovranno registrare il provider di Backup di Azure da usare con la propria sottoscrizione. A tale scopo, eseguire il comando seguente: Register-AzureProvider -ProviderNamespace "Microsoft.Backup"

È possibile creare un nuovo insieme di credenziali per il backup usando il cmdlet **New-AzureRMBackupVault**. L’archivio di backup è una risorsa ARM, pertanto è necessario inserirlo all'interno di un gruppo di risorse. Eseguire i comandi seguenti in una console di Azure PowerShell con privilegi elevati:

```
PS C:\> New-AzureResourceGroup –Name “test-rg” -Region “West US”
PS C:\> $backupvault = New-AzureRMBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

Usare il cmdlet **Get-AzureRMBackupVault** per elencare gli insiemi di credenziali di backup in una sottoscrizione.


## Installazione dell'agente di Backup di Azure
Per installare l'agente di Backup di Azure, è necessario aver scaricato il programma di installazione nel server Windows. È possibile ottenere la versione più recente del programma di installazione dall'[Area download Microsoft](http://aka.ms/azurebackup_agent).o dalla pagina Dashboard dell’archivio di backup. Salvare il programma di installazione in un percorso facilmente accessibile come *C:\\Downloads*.

Per installare l'agente, eseguire il comando seguente in una console di Azure PowerShell con privilegi elevati:

```
PS C:\> MARSAgentInstaller.exe /q
```

L'agente verrà installato con tutte le opzioni predefinite. L'installazione richiede alcuni minuti in background. Se non si specifica l'opzione */nu*, la finestra **Windows Update** si aprirà al termine dell'installazione per verificare la presenza di eventuali aggiornamenti. Dopo essere stato installato, l'agente verrà visualizzato nell'elenco dei programmi installati.

Per visualizzare l'elenco dei programmi installati, andare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità**.

![Agente installato](./media/backup-client-automation/installed-agent-listing.png)

### Opzioni di installazione

Per visualizzare tutte le opzioni disponibili tramite la riga di comando, utilizzare il comando seguente:

```
PS C:\> MARSAgentInstaller.exe /?
```

Le opzioni disponibili includono:

| Opzione | Dettagli | Default |
| ---- | ----- | ----- |
| /q | Installazione non interattiva | - | | /p:"location" | Percorso della cartella di installazione dell'agente di Backup di Azure. | C:\\Programmi\\Microsoft Azure Recovery Services Agent | | /s:"location" | Percorso della cartella cache dell'agente di Backup di Azure. | C:\\Programmi\\Microsoft Azure Recovery Services Agente\\Scratch | | /m | Accetta Microsoft Update | - | | /nu | Non verificare la disponibilità di aggiornamenti al termine dell'installazione | - | | /d | Disinstalla Agente di Servizi di ripristino di Microsoft Azure | - | | /ph | Indirizzo host proxy | - | | /po | Numero porta host proxy | - | | /pu | Nome utente host proxy | - | | /pw | Password proxy | - |


## Registrazione del servizio Backup di Azure
Per poter eseguire la registrazione con il servizio Backup di Azure, è necessario assicurarsi che i [prerequisiti](backup-configure-vault.md) siano soddisfatti. È necessario:

- Disporre di una sottoscrizione di Azure valida
- Ottieni un archivio di backup

Per scaricare le credenziali dell'insieme di credenziali, eseguire il cmdlet **Get AzureRMBackupVaultCredentials** nella console di Azure PowerShell e archiviarle in una posizione pratica, ad esempio * C:\\Download*.

```
PS C:\> $credspath = "C:"
PS C:\> $credsfilename = Get-AzureRMBackupVaultCredentials -Vault $backupvault -TargetLocation $credspath
PS C:\> $credsfilename
f5303a0b-fae4-4cdb-b44d-0e4c032dde26_backuprg_backuprn_2015-08-11--06-22-35.VaultCredentials
```

La registrazione del computer con l'insieme di credenziali viene eseguita utilizzando il cmdlet [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx):

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration -VaultCredentials $cred -Confirm:$false

CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : test-vault
Region              : West US

Machine registration succeeded.
```

> [AZURE.IMPORTANT] Non utilizzare percorsi relativi per specificare il file dell'insieme di credenziali. È necessario fornire un percorso assoluto come input per il cmdlet.

## Impostazioni di rete
Quando il computer Windows si connette a Internet mediante un server proxy, le impostazioni del proxy possono essere fornite anche all'agente. In questo esempio non è presente alcun server proxy, pertanto sono state eliminate tutte le informazioni relative al proxy.

È anche possibile controllare l'utilizzo della larghezza di banda con le opzioni ```work hour bandwidth``` e ```non-work hour bandwidth``` per un dato set di giorni della settimana.

L'impostazione dei dettagli relativi a proxy e larghezza di banda viene eseguita mediante il cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx):

```
PS C:\> Set-OBMachineSetting -NoProxy
Server properties updated successfully.

PS C:\> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

## Impostazioni crittografia
I dati di backup inviati a Backup di Azure vengono crittografati per proteggere la riservatezza dei dati. La passphrase di crittografia è la "password" per decrittografare i dati in fase di ripristino.

```
PS C:\> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
Server properties updated successfully
```

> [AZURE.IMPORTANT] Dopo l'impostazione, conservare le informazioni sulla passphrase al sicuro. Non sarà possibile ripristinare i dati da Azure senza la passphrase.

## Eseguire il backup di file e cartelle
Tutti i backup dei server e dei client Windows in Backup di Azure sono regolati da un criterio, costituito da tre parti:

1. Una **pianificazione dei backup** che specifica quando è necessario eseguire i backup e sincronizzarli con il servizio.
2. Una **pianificazione di conservazione** che indica per quanto tempo è necessario conservare i punti di ripristino in Azure.
3. Una **specifica di inclusione/esclusione di file** che determina i contenuti di cui eseguire il backup.

Dal momento che in questo documento si esegue un backup automatico, si presuppone che non siano stati configurati elementi. Si inizia creando un nuovo criterio di backup tramite il cmdlet [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) e usando il criterio.

```
PS C:\> $newpolicy = New-OBPolicy
```

In questo momento il criterio è vuoto e sono necessari altri cmdlet per definire quali elementi verranno inclusi o esclusi, quando verranno eseguiti i backup e dove verranno archiviati.

### Configurazione della pianificazione dei backup
La prima delle tre parti di un criterio è la pianificazione dei backup, che viene creata tramite il cmdlet [New-OBSchedule](https://technet.microsoft.com/library/hh770401). La pianificazione dei backup definisce quando è necessario eseguire i backup. Quando si crea una pianificazione è necessario specificare due parametri di input:

- **Giorni della settimana** in cui deve essere eseguito il backup. È possibile eseguire il processo di backup in un solo giorno oppure tutti i giorni della settimana o specificando qualsiasi combinazione di giorni.
- **Orari della giornata** in cui deve essere eseguito il backup. È possibile definire fino a tre orari della giornata diversi in cui verrà attivato il backup.

Ad esempio, è possibile configurare un criterio di backup eseguito alle 16.00 ogni sabato e domenica.

```
PS C:\> $sched = New-OBSchedule -DaysofWeek Saturday, Sunday -TimesofDay 16:00
```

La pianificazione dei backup deve essere associata a un criterio ed è possibile eseguire questa operazione tramite il cmdlet [Set-OBSchedule](https://technet.microsoft.com/library/hh770407).

```
PS C:\> Set-OBSchedule -Policy $newpolicy -Schedule $sched
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### Configurazione di un criterio di conservazione
Il criterio di conservazione definisce il periodo di conservazione dei punti di ripristino creati dai processi di backup. Quando si crea un nuovo criterio di conservazione usando il cmdlet [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425), è possibile specificare il numero di giorni per cui conservare i punti di ripristino dei backup con Backup di Azure. L'esempio seguente imposta un criterio di conservazione di 7 giorni.

```
PS C:\> $retentionpolicy = New-OBRetentionPolicy -RetentionDays 7
```

Il criterio di conservazione deve essere associato al criterio principale usando il cmdlet [Set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405):

```
PS C:\> Set-OBRetentionPolicy -Policy $newpolicy -RetentionPolicy $retentionpolicy

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```
### Inclusione ed esclusione di file per il backup
Un oggetto ```OBFileSpec``` definisce i file da includere o escludere in un backup. Si tratta di un set di regole che definiscono l'ambito di cartelle e file protetti in un computer. È possibile disporre del numero desiderato di regole di inclusione o esclusione di file e associare le regole a un criterio. Quando si crea un nuovo oggetto OBFileSpec, è possibile:

- Specificare file e cartelle da includere
- Specificare file e cartelle da escludere
- Specificare un backup ricorsivo dei dati in una cartella (o) se eseguire il backup solo dei file di livello principale nella cartella specificata.

Quest'ultima impostazione si ottiene usando il flag -NonRecursive nel comando New-OBFileSpec.

Nell'esempio seguente viene eseguito il backup dei volumi C: e D: e vengono esclusi i file binari del sistema operativo nella cartella Windows e nelle cartelle temporanee. A tale scopo, vengono create due specifiche dei file usando il cmdlet [New-OBFileSpec](https://technet.microsoft.com/library/hh770408), una per l'inclusione e una per l'esclusione. Dopo essere state create, le specifiche dei file vengono associate al criterio usando il cmdlet [Add-OBFileSpec](https://technet.microsoft.com/library/hh770424).

```
PS C:\> $inclusions = New-OBFileSpec -FileSpec @("C:", "D:")

PS C:\> $exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude

PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $inclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid


PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $exclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### Applicazione del criterio
A questo punto l'oggetto criterio è completo e associato a una pianificazione dei backup, un criterio di conservazione e un elenco di inclusione o esclusione di file. È ora possibile eseguire il commit del criterio affinché venga usato in Backup di Azure. Prima di applicare il criterio appena creato, assicurarsi che non vi siano criteri di backup esistenti associati al server usando il cmdlet [Remove-OBPolicy](https://technet.microsoft.com/library/hh770415). Per la rimozione del criterio verrà richiesta una conferma. Per ignorare la conferma, usare il flag ```-Confirm:$false``` con il cmdlet.

```
PS C:\> Get-OBPolicy | Remove-OBPolicy
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Il commit dell'oggetto criterio viene eseguito usando il cmdlet [Set-OBPolicy](https://technet.microsoft.com/library/hh770421). Anche in questo caso verrà richiesto di confermare. Per ignorare la conferma, usare il flag ```-Confirm:$false``` con il cmdlet.

```
PS C:\> Set-OBPolicy -Policy $newpolicy
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
	}
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

È possibile visualizzare i dettagli del criterio di backup esistente usando il cmdlet [Get-OBPolicy](https://technet.microsoft.com/library/hh770406). È possibile eseguire ulteriormente il drill-down usando il cmdlet [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) per la pianificazione dei backup e il cmdlet[Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) per i criteri di conservazione.

```
PS C:\> Get-OBPolicy | Get-OBSchedule
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing

PS C:\> Get-OBPolicy | Get-OBRetentionPolicy
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing

PS C:\> Get-OBPolicy | Get-OBFileSpec
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### Esecuzione di un backup ad hoc
Una volta impostato un criterio di backup, i backup verranno eseguiti in base alla pianificazione. È possibile attivare un backup ad hoc anche tramite il cmdlet [Start-OBBackup](https://technet.microsoft.com/library/hh770426):

```
PS C:\> Get-OBPolicy | Start-OBBackup
Taking snapshot of volumes...
Preparing storage...
Estimating size of backup items...
Estimating size of backup items...
Transferring data...
Verifying backup...
Job completed.
The backup operation completed successfully.
```

## Ripristinare i dati da Backup di Azure
Questa sezione illustra i passaggi per l'automazione del ripristino dei dati da Backup di Azure. A tale scopo, sono necessari i passaggi seguenti:

1. Selezionare il volume di origine
2. Scegliere un punto di backup da ripristinare
3. Scegliere un elemento da ripristinare
4. Attivare il processo di ripristino

### Selezione del volume di origine
Per ripristinare un elemento da Backup di Azure, è necessario innanzitutto identificare l'origine dell'elemento. Poiché i comandi vengono eseguiti nel contesto di un server o un client Windows, il computer è già identificato. Il passaggio successivo nell'identificazione dell'origine consiste nell'identificare il volume che la contiene. È possibile recuperare un elenco dei volumi o delle origini di cui viene eseguito il backup per il computer eseguendo il cmdlet [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410). Questo comando restituisce una matrice di tutte le origini di cui viene eseguito il backup nel server/client.

```
PS C:\> $source = Get-OBRecoverableSource
PS C:\> $source
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### Scelta di un punto di backup da ripristinare
È possibile recuperare l'elenco dei punti di backup eseguendo il cmdlet [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) con i parametri appropriati. Nell'esempio viene scelto il punto di backup più recente per il volume di origine *D:*, che viene usato per ripristinare un file specifico.

```
PS C:\> $rps = Get-OBRecoverableItem -Source $source[1]
IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :

IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 17-Jun-15 6:31:31 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :
```
L'oggetto ```$rps``` è una matrice di punti di backup. Il primo elemento è il punto più recente e l'elemento N è il punto meno recente. Per scegliere il punto più recente, si userà ```$rps[0]```.

### Scelta di un elemento da ripristinare
Per identificare la cartella o il file esatto da ripristinare, usare in modo ricorsivo il cmdlet [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx). In questo modo, è possibile esplorare la gerarchia di cartelle usando esclusivamente ```Get-OBRecoverableItem```.

In questo esempio, se si desidera ripristinare il file *finances.xls*, è possibile farvi riferimento usando l'oggetto ```$filesFolders[1]```.

```
PS C:\> $filesFolders = Get-OBRecoverableItem $rps[0]
PS C:\> $filesFolders
IsDir : True
ItemNameFriendly : D:\MyData\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\
LocalMountPoint : D:\
MountPointName : D:\
Name : MyData
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime : 15-Jun-15 8:49:29 AM

PS C:\> $filesFolders = Get-OBRecoverableItem $filesFolders[0]
PS C:\> $filesFolders
IsDir : False
ItemNameFriendly : D:\MyData\screenshot.oxps
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\screenshot.oxps
LocalMountPoint : D:\
MountPointName : D:\
Name : screenshot.oxps
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 228313
ItemLastModifiedTime : 21-Jun-14 6:45:09 AM

IsDir : False
ItemNameFriendly : D:\MyData\finances.xls
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\finances.xls
LocalMountPoint : D:\
MountPointName : D:\
Name : finances.xls
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 96256
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM
```

È anche possibile cercare gli elementi da ripristinare usando il cmdlet ```Get-OBRecoverableItem```. Nell'esempio, per cercare *finances.xls* è possibile ottenere un handle sul file eseguendo il comando seguente:

```
PS C:\> $item = Get-OBRecoverableItem -RecoveryPoint $rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### Attivazione del processo di ripristino
Per attivare il processo di ripristino, è prima necessario specificare le opzioni di ripristino. A tale scopo, è possibile usare il cmdlet [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx). Per questo esempio, si supponga di voler ripristinare i file in *C:\\temp*. Si supponga inoltre di voler ignorare i file già presenti nella cartella di destinazione *C:\\temp*. Per creare un'opzione di ripristino di questo tipo, usare il comando seguente:

```
PS C:\> $recovery_option = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Attivare quindi il ripristino usando il comando [Start-OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) sull'oggetto ```$item``` selezionato dall'output del cmdlet ```Get-OBRecoverableItem```:

```
PS C:\> Start-OBRecovery -RecoverableItem $item -RecoveryOption $recover_option
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```


## Disinstallazione dell'agente di Backup di Azure
La disinstallazione dell'agente di Backup di Azure può essere eseguita mediante il seguente comando:

```
PS C:\> .\MARSAgentInstaller.exe /d /q
```

La disinstallazione dei file binari dell'agente dal computer comporta alcune conseguenze da tenere in considerazione:

- Il filtro di file viene rimosso dal computer e il rilevamento delle modifiche viene arrestato.
- Tutte le informazioni sui criteri vengono rimosse dal computer, ma continuano a essere archiviate nel servizio.
- Tutte le pianificazioni dei backup vengono rimosse e non vengono eseguiti ulteriori backup.

Tuttavia, i dati archiviati in Azure continueranno a rimanere presenti e verranno conservati in base alla configurazione del criterio di conservazione specificata. I punti meno recenti scadono automaticamente.

## Gestione remota
Tutte le operazioni di gestione di origini dati, criteri e agente di Backup di Azure possono essere eseguite in remoto mediante PowerShell. Il computer che verrà gestito in remoto deve essere preparato correttamente.

Per impostazione predefinita, il servizio Gestione remota Windows è configurato per l'avvio manuale. Il tipo di avvio deve essere impostato su *Automatico* e il servizio deve essere avviato. Per verificare che il servizio Gestione remota Windows sia in esecuzione, il valore della proprietà Status deve essere *In esecuzione*.

```
PS C:\> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

PowerShell deve essere configurato per la comunicazione remota.

```
PS C:\> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:\> Set-ExecutionPolicy unrestricted -force
```

È ora possibile gestire il computer in remoto, a partire dall'installazione dell'agente. Ad esempio, il seguente script copia l'agente nel computer remoto e lo installa.

```
PS C:\> $dloc = "\\REMOTESERVER01\c$\Windows\Temp"
PS C:\> $agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:\> $args = "/q"
PS C:\> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:\> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:\> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```

## Passaggi successivi
Per altre informazioni su Backup di Azure per Windows Server/Client, vedere

- [Introduzione a Backup di Azure](backup-configure-vault.md)
- [Backup di server Windows](backup-azure-backup-windows-server.md)

<!---HONumber=AcomDC_0128_2016-->