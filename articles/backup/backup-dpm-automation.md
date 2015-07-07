<properties
	pageTitle="Backup di Azure: distribuire e gestire il backup per DPM mediante Windows PowerShell | Microsoft Azure"
	description="Informazioni su come distribuire e gestire Backup di Azure per i server Data Protection Manager (DPM) usando Windows PowerShell"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="jimpark"/>


# Distribuire e gestire il backup su Azure per i server Data Protection Manager (DPM) usando Windows PowerShell
In questo articolo viene illustrato come utilizzare l'interfaccia della riga di comando di Windows PowerShell® per configurare Backup di Azure su un server DPM e per gestire il backup e il ripristino.

## Configurazione dell'ambiente di Windows PowerShell
Prima di poter utilizzare Windows PowerShell per gestire Backup di Azure, sarà necessario disporre della versione corretta di Windows PowerShell e della corretta configurazione delle variabili d'ambiente.

Verificare che la versione di Windows PowerShell in uso sia 3.0 o 4.0. Per individuare la versione di Windows PowerShell, digitare il comando seguente al prompt dei comandi di Windows PowerShell/DPM.

```
$PSVersionTable
```

Verrà visualizzato il tipo di informazioni seguente:

| Nome | Valore |
| ---- | ----- |
| PSVersion | 3.0 |
| WSManStackVersion | 3.0 |
| SerializationVersion | 1.1.0.1 |
| CLRVersion | 4.0.30319.18444 |
| BuildVersion | 6.2.9200.16481 |
| PSCompatibleVersions | {1.0, 2.0, 3.0} |
| PSRemotingProtocolVersion | 2.2 |

Verificare che il valore di **PSVersion** sia 3.0 o 4.0. In caso contrario, vedere [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

## Configurazione e registrazione
### Installazione dell'agente di Backup di Azure sul server DPM
Per installare l'agente di Backup di Azure, è necessario aver scaricato il programma di installazione in Windows Server. È possibile ottenere la versione più recente del programma di installazione dall'[Area download Microsoft](aka.ms/azurebackup_agent). Salvare il programma di installazione in un percorso facilmente accessibile come *C:*.

Per installare l'agente, eseguire il comando seguente in una console di Windows PowerShell del server DPM con privilegi elevati:

```
PS C:> MARSAgentInstaller.exe /q
```

L'agente verrà installato con tutte le opzioni predefinite. L'installazione richiede alcuni minuti in background. Se non si specifica l'opzione */nu*, la finestra Windows Update si aprirà al termine dell'installazione per verificare la presenza di eventuali aggiornamenti.

L'agente verrà visualizzato nell'elenco dei programmi installati. Per visualizzare l'elenco dei programmi installati, andare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità**.

![Agente installato](./media/backup-dpm-automation/installed-agent-listing.png)

#### Opzioni di installazione
Per visualizzare tutte le opzioni disponibili tramite la riga di comando, utilizzare il comando seguente:

```
PS C:> MARSAgentInstaller.exe /?
```

Le opzioni disponibili includono:

| Opzione | Dettagli | Default |
| ---- | ----- | ----- |
| /q | Installazione non interattiva | - | | /p:"location" | Percorso della cartella di installazione dell'agente di Backup di Azure. | C:\Program Files\Agente di Servizi di ripristino di Microsoft Azure | | /s:"location" | Percorso della cartella cache dell'agente di Backup di Azure. | C:\Program Files\Agente di Servizi di ripristino di Microsoft Azure\Area di lavoro | | /m | Accetta Microsoft Update | - | | /nu | Non verificare la disponibilità di aggiornamenti al termine dell'installazione | - | | /d | Disinstalla Agente di Servizi di ripristino di Microsoft Azure | - | | /ph | Indirizzo host proxy | - | | /po | Numero porta host proxy | - | | /pu | Nome utente host proxy | - | | /pw | Password proxy | - |

### Registrazione del servizio Backup di Azure
Prima di registrare il servizio Backup di Azure, verificare che siano soddisfatti i seguenti prerequisiti: - Disporre di una sottoscrizione Azure valida - Creare credenziali di backup - Scaricare l'insieme di credenziali e salvarlo in un percorso comodo (ad esempio *C:\Download*). È possibile rinominare l'insieme di credenziali per comodità.

La registrazione del computer con l'insieme di credenziali viene eseguita utilizzando il cmdlet [Start-DPMCloudRegistration](https://technet.microsoft.com/library/jj612787):

```
PS C:> Start-DPMCloudRegistration -DPMServerName "TestingServer" -VaultCredentialsFilePath "C:\DPMTESTVault_Friday, September 5, 2014.VaultCredentials"
```

In questo modo, il server DPM denominato "TestingServer" verrà registrato con l'insieme di credenziali di Microsoft Azure utilizzando quello posizionato in *c: *.

> [AZURE.IMPORTANT]Non utilizzare percorsi relativi per specificare il file dell'insieme di credenziali. È necessario fornire un percorso assoluto come input per il cmdlet.

### Configurazione delle opzioni di backup DPM (impostazioni di sottoscrizione) per il backup online
Una volta registrato il server DPM per Online Protection, verranno definite le impostazioni di sottoscrizione predefinite, le quali specificano diverse opzioni di backup. Se si desidera modificare le impostazioni di sottoscrizione, è necessario visualizzare l'impostazione esistente, quindi modificarla in base alle proprie esigenze.

#### Visualizzazione delle impostazioni di sottoscrizione esistenti per il server DPM
Per configurare le impostazioni di sottoscrizione relative a DPM Online, è innanzitutto necessario visualizzare quelle esistenti tramite il cmdlet [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793.aspx):

```
$Setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

È necessario eseguire questo comando per visualizzare l'oggetto relativo all'impostazione di sottoscrizione che può essere utilizzato per configurare diverse opzioni; in seguito, salvare le impostazioni per concludere la procedura. L'esecuzione del comando per un nuovo server DPM consente di visualizzare le impostazioni predefinite.

#### Modifica delle impostazioni di sottoscrizione
È possibile configurare le seguenti impostazioni relative alla sottoscrizione DPM per il backup online utilizzando il cmdlet [Set DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791):

##### Area di staging
L'agente del servizio Backup di Azure in esecuzione nel server DPM deve disporre di archiviazione temporanea per i dati ripristinati dal cloud (area di staging locale). È possibile utilizzare il seguente comando per configurare l'area di staging. In questo modo, l'area di staging verrà impostata come "C:\StagingArea" per **$setting**; tuttavia, la configurazione non viene ancora applicata al server DPM, a meno che non si confermino le impostazioni.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -StagingAreaPath "C:\StagingArea"
```

> [AZURE.NOTE]Assicurarsi che la cartella specificata nel comando precedente esista già. In caso contrario, verrà visualizzato un messaggio di errore durante il salvataggio delle impostazioni di sottoscrizione

##### Rete
La connettività tra il computer DPM e Internet viene stabilita tramite un server proxy. Le impostazioni proxy possono essere fornite all'agente. In questo esempio non esiste alcun server proxy, pertanto sono state eliminate tutte le informazioni relative al proxy.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoProxy
```

È inoltre possibile controllare l'utilizzo della larghezza di banda con le opzioni relative alla *larghezza di banda durante le ore di lavoro* e alla *larghezza di banda durante le ore non lavorative* per un dato set di giorni della settimana. In questo esempio, non viene impostata alcuna limitazione

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoThrottle
```

##### Impostazioni crittografia
I dati di backup inviati a Backup di Azure vengono crittografati per proteggere la riservatezza dei dati. La passphrase di crittografia è la "password" per decrittografare i dati in fase di ripristino.

> [AZURE.IMPORTANT]Conservare questa informazione al sicuro e proteggerla dopo averla impostata.

Nel seguente esempio, il primo comando consente di convertire la passphrase della stringa 123456789 in una stringa protetta; inoltre, consente di assegnare la stringa protetta alla variabile denominata $Passphrase. Il secondo comando consente di impostare la stringa protetta in $Passphrase come password per crittografare i backup

```
PS C:> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force
```

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -EncryptionPassphrase $Passphrase
```

#### Salvataggio delle impostazioni di sottoscrizione
È necessario confermare le modifiche apportate al server DPM utilizzando l'opzione *– Commit*

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -Commit
```

> [AZURE.NOTE]Le applicazioni vengono applicate soltanto dopo la conferma.

## Protezione dei dati relativi a Backup di Azure
La procedura seguente illustra come aggiungere un server di produzione a DPM e come proteggere i dati dei dischi e online su Azure. Negli esempi, viene descritto come eseguire il backup di file e cartelle. È possibile estendere la logica al fine di eseguire il backup di qualsiasi origine di dati supportata. Come prerequisito, è necessario installare e registrare il server DPM per la protezione online.

Tutti i backup DPM sono regolati da un gruppo protezione dati (PG) che comprende quattro parti

1. **Membri del gruppo**: elenco di tutti gli oggetti che si desidera proteggere nel gruppo protezione dati. Ad esempio, è possibile proteggere le macchine virtuali di produzione in un gruppo protezione dati e verificarle in un altro, dal momento che dispongono di requisiti di backup differenti.
2. **Metodo protezione dati**: utilizzato per specificare dove si desidera proteggere i dati. In questo esempio, i dati presenti su disco e nella protezione online verranno protetti nel cloud di Azure.
3. Una **pianificazione di backup** che indica il momento di esecuzione del backup e la frequenza di sincronizzazione dei dati tra il server DPM e quello di produzione.
4. Una **pianificazione di conservazione** che indica per quanto tempo è necessario conservare i punti di ripristino in Azure.

### Creare un gruppo protezione dati ed eseguire il backup dei dati ad intervalli determinati
#### Aggiunta di un server di produzione a un server DPM
Prima di eseguire i dati di backup su un server di produzione, è necessario assicurarsi che l'agente DPM sia installato sul server di produzione e che sia gestito da DPM. Attenersi alla procedura riportata [qui](https://technet.microsoft.com/library/bb870935.aspx) per installare l'agente DPM e configurarlo affinché venga sottoposto a backup dal server DPM appropriato.

#### Creazione di un gruppo protezione dati
Dal momento che in questo articolo si esegue un backup automatico, si presuppone che non siano stati configurati elementi. Innanzitutto, viene creato un gruppo protezione dati utilizzando il cmdlet [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722.aspx).

```
PS C:> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Prima di apportare modifiche al gruppo protezione dati, è necessario abilitare la modalità di modifica tramite il cmdlet [Get DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713.aspx).

```
PS C:> $MPG = Get-ModifiableProtectionGroup $PG
```

I cmdlet precedenti consentono di creare il gruppo protezione dati denominato "ProtectGroup01", ma non consentono di eseguire backup in questa fase. È necessario definire gli elementi da sottoporre a backup, quando eseguire tale operazione e dove archiviare i backup.

### Aggiunta di membri al gruppo protezione dati
È necessario eseguire qualche passaggio per aggiungere

#### Visualizzazione del server di produzione da sottoporre a backup
È possibile recuperare tutti i server di produzione per i quali è installato l'agente DPM e che sono gestiti dal server DPM tramite il cmdlet [Get DPMProductionServer](https://technet.microsoft.com/library/hh881600.aspx). In questo esempio, verrà filtrato e configurato per il backup soltanto il server di produzione denominato "productionserver01".

```
PS C:> $PS = Get-ProductionServer -DPMServerName "TestingServer" |where {($_.servername) –contains “productionserver01”
```

### Eseguire una richiesta di informazioni al fine di ottenere un elenco di origini dati sul server di produzione
È possibile eseguire una richiesta di informazioni sul computer del server di produzione al fine di visualizzare un elenco di origine dati che possono essere sottoposte a backup da parte di DPM utilizzando il cmdlet [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605.aspx). In questo esempio, è possibile filtrare l'origine dati "D:\" sul server di produzione per il quale si desidera configurare la protezione.

```
PS C:> $DS = Get-Datasource -ProductionServer $PS -Inquire | where { $_.Name -contains “D:\” }
```

#### Aggiunta dell'origine dati per la protezione
È possibile aggiungere l'origine dati al gruppo protezione dati "ProtectGroup01" creato nell'esempio precedente utilizzando il cmdlet [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx).

```
PS C:> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

### Selezione del metodo di protezione dati
Dopo aver aggiunto un'origine dati al gruppo protezione dati, è necessario indicare il metodo di protezione. In questo caso, viene impostata la protezione a breve termine del gruppo protezione dati sul disco e quella a lungo termine sul nastro.

```
PS C:> Set-DPMProtectionType -ProtectionGroup $PG -ShortTerm Disk –LongTerm Online
```

### Specificare gli obiettivi del punto di ripristino
#### Impostazione del periodo di mantenimento dati
È possibile impostare il periodo di mantenimento dati relativo al gruppo protezione dati utilizzando il cmdlet [DPMPolicyObjective Set](https://technet.microsoft.com/library/hh881762.aspx). Il seguente comando consente di impostare il *periodo di mantenimento dati* e la *frequenza di sincronizzazione* per i punti di ripristino basati su disco.

```
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequency 360
```

Per quanto riguarda i punti di ripristino online, è possibile configurare i periodi di mantenimento dati per vari schemi GFS (ad esempio, quotidiano, settimanale, mensile e annuale). In questo esempio, viene creato un oggetto con vari periodi di mantenimento dati; in seguito, viene configurato il periodo di mantenimento dati online utilizzando l'oggetto creato, come descritto nel seguente esempio.

```
PS C:> $RRlist = @()
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

#### Impostazione relativa alle pianificazioni di backup
DPM imposta automaticamente le pianificazioni predefinite, se si specifica l'obiettivo di protezione utilizzando il cmdlet **Set-PolicyObjective**. Per modificare le pianificazioni predefinite, utilizzare il cmdlet [Get DPMPolicySchedule](https://technet.microsoft.com/library/hh881749.aspx), seguito da quello [Set DPMPolicySchedule](https://technet.microsoft.com/library/hh881723.aspx).

```
PS C:> $onlineSch=Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTermOnline
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
```

#### Creazione della replica
Se si sta eseguendo il backup dell'origine dati per la prima volta, DPM deve creare una replica iniziale che a sua volta consente di creare una copia dell'origine dati da proteggere sul volume della replica DPM. È possibile pianificare la creazione di una replica automaticamente, a un orario specifico oppure manualmente, utilizzando il cmdlet [Set DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715.aspx).

```
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

#### Conferma del gruppo protezione dati
Infine, è necessario confermare le modifiche prima che DPM possa eseguire il backup per la configurazione del gruppo protezione dati utilizzando il [Set DPMProtectionGroup](https://technet.microsoft.com/library/hh881758.aspx).

```
PS C:> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## Visualizzazione di un elenco relativo ai punti di ripristino online
È possibile utilizzare il cmdlet [Get DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746.aspx) per visualizzare un elenco di tutti i punti di ripristino per un'origine dati. In questo esempio: - Vengono recuperati tutti i gruppi protezione dati nel server DPM che verranno archiviati in una matrice $PG - Vengono visualizzate le origini dati corrispondenti a $PG [0] - Vengono visualizzati tutti i punti di ripristino per un'origine dati.

```
PS C:> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## Ripristino dei dati protetti su Azure
In questo esempio, viene descritto come ripristinare una macchina virtuale Hyper-V da un punto di ripristino online; tuttavia i comandi possono essere estesi facilmente a ogni tipo di origine dati.

È innanzitutto necessario creare un'opzione di ripristino con il cmdlet [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592.aspx). Nell'esempio seguente, si esegue il ripristino di un'origine dati di Hyper-V in un percorso alternativo.

```
PS C:> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “c:\VMRecovery”
```

Dopo aver configurato l'opzione di ripristino, verrà recuperato il punto di ripristino online che è stato recuperato nella sezione [Visualizzazione di un elenco relativo ai punti di ripristino online](#Getting-a-list-of-all-Online-Recovery-Points).

```
PS C:> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints -RecoveryOption $RecoveryOption
```
## Passaggi successivi
Per ulteriori informazioni su Backup di Azure per DPM, vedere [Introduzione al backup di Azure DPM](backup-azure-dpm-introduction.md)

<!---HONumber=62-->