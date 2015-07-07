<properties
	pageTitle="Distribuire e gestire il backup per Windows server/client mediante Windows PowerShell | Microsoft Azure"
	description="Informazioni su come distribuire e gestire Backup di Azure mediante Windows PowerShell"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="aashishr"/>


# Distribuire e gestire il backup in Azure per Windows Server/client Windows mediante Windows PowerShell
In questo articolo viene illustrato come utilizzare l'interfaccia della riga di comando di Windows PowerShell® per configurare Backup di Azure in Windows Server o client Windows e per gestire i backup e ripristini.

## Configurazione dell'ambiente di Windows PowerShell
Prima di poter utilizzare Windows PowerShell per gestire Backup di Azure, sarà necessario disporre della versione corretta di Windows PowerShell e della corretta configurazione delle variabili d'ambiente.

## Configurazione e registrazione
Le seguenti attività di configurazione e registrazione possono essere automatizzate mediante Windows PowerShell - Installazione di Azure Backup Agent - Registrazione con il servizio Backup di Azure - Reti

### Installazione di Azure Backup Agent
Per installare l'agente di Backup di Azure, è necessario aver scaricato il programma di installazione in Windows Server. È possibile ottenere la versione più recente del programma di installazione dall'[Area download Microsoft](aka.ms/azurebackup_agent). Salvare il programma di installazione in un percorso facilmente accessibile come *C:\Download*. Per installare l'agente, eseguire il comando seguente in una console di Windows PowerShell con privilegi elevati:

```
PS C:> MARSAgentInstaller.exe /q
```

L'agente verrà installato con tutte le opzioni predefinite. L'installazione richiede alcuni minuti in background. Se non si specifica l'opzione **/nu**, la finestra **Windows Update** si aprirà al termine dell'installazione per verificare la presenza di eventuali aggiornamenti.

L'agente verrà visualizzato nell'elenco dei programmi installati. Per visualizzare l'elenco dei programmi installati, andare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità**.

![Agente installato](./media/backup-client-automation/installed-agent-listing.png)

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
Prima di registrare il servizio Backup di Azure, verificare che i [prerequisiti](backup-try-azure-backup-in-10-mins.md) siano soddisfatti: - Disporre di una sottoscrizione Azure valida - Creare un insieme di credenziali di backup - Scaricare l'insieme di credenziali e salvarlo in un percorso comodo (ad esempio *C:\Download*). È possibile rinominare l'insieme di credenziali per comodità.

La registrazione del computer con l'insieme di credenziali viene eseguita utilizzando il cmdlet [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx):

```
PS C:> Start-OBRegistration -VaultCredentials "C:\Downloads\register.vaultcredentials" -Confirm:$false

CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : test-vault
Region              : Australia East

Machine registration succeeded.
```

> [AZURE.IMPORTANT]Non utilizzare percorsi relativi per specificare il file dell'insieme di credenziali. È necessario fornire un percorso assoluto come input per il cmdlet.


### Rete
Quando il computer Windows si connette a Internet mediante un server proxy, le impostazioni del proxy possono essere fornite anche all'agente. In questo esempio non esiste alcun server proxy, pertanto sono state eliminate tutte le informazioni relative al proxy.

È inoltre possibile controllare l'utilizzo della larghezza di banda con le opzioni relative alla *larghezza di banda durante le ore di lavoro* e alla *larghezza di banda durante le ore non lavorative* per un dato set di giorni della settimana.

L'impostazione dei dettagli relativi a proxy e larghezza di banda viene eseguita mediante il cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx):

```
PS C:> Set-OBMachineSetting -NoProxy
Server properties updated successfully.
```

```
PS C:> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

### Impostazioni crittografia
I dati di backup inviati a Backup di Azure vengono crittografati per proteggere la riservatezza dei dati. La passphrase di crittografia è la "password" per decrittografare i dati in fase di ripristino. È importante conservarla al sicuro e proteggerla dopo averla impostata.

```
PS C:> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
Server properties updated successfully
```

## Disinstallazione di Azure Backup Agent
La disinstallazione di Azure Backup Agent può essere eseguita mediante il seguente comando:

```
PS C:> .\MARSAgentInstaller.exe /d /q
```

Disinstallare i file binari dell'agente dal computer comporta altre conseguenze: rimuove il filtro file dal computer e il rilevamento delle modifiche viene arrestato. -Tutte le informazioni sui criteri vengono rimosse dal computer, ma le informazioni sui criteri continuano a essere archiviate nel servizio. -Tutte le pianificazioni di backup vengono rimosse e non verranno eseguiti altri backup.

Tuttavia, i dati archiviati in Azure continueranno a rimanere e a essere conservati secondo la configurazione dei criteri di conservazione da parte dell'utente. I punti meno recenti scadono automaticamente.

## Gestione remota
Tutta la gestione relativa all'agente, ai criteri e alle origini dati di Backup di Azure può essere eseguita in remoto mediante Windows PowerShell. Il computer che verrà gestito in remoto deve essere preparato correttamente.

Per impostazione predefinita, il servizio Gestione remota Windows è configurato per l'avvio manuale. Il tipo di avvio deve essere impostato su *Automatico* e il servizio deve essere avviato. Per verificare che il servizio Gestione remota Windows sia in esecuzione, il valore della proprietà Status deve essere *In esecuzione*.

```
PS C:> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...

- Windows PowerShell should be configured for remoting.
```

```
PS C:> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:> Set-ExecutionPolicy unrestricted -force
```

È ora possibile gestire il computer in remoto, a partire dall'installazione dell'agente. Ad esempio, il seguente script copia l'agente nel computer remoto e lo installa.

```
PS C:> $dloc = "\REMOTESERVER01\c$\Windows\Temp"
PS C:> $agent = "\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:> $args = "/q"
PS C:> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```
## Passaggi successivi
Per ulteriori informazioni su Backup di Azure per Windows server/client, vedere [Introduzione a Backup di Azure](backup-introduction-to-azure-backup.md)

<!---HONumber=62-->