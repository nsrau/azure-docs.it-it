---
title: Automazione dei processi SQL di Azure | Microsoft Docs
description: Usare l'automazione dei processi per eseguire script Transact-SQL (T-SQL) su un set di uno o più database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: database-features
ms.custom: ''
ms.devlang: ''
ms.topic: overview
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 1fd524e858b20c75aef4101ad98ac54c4f485d1e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457208"
---
# <a name="automate-management-tasks-using-database-jobs"></a>Automatizzare le attività di gestione con processi di database

Database SQL di Azure consente di creare e pianificare processi eseguibili periodicamente su uno o più database per eseguire query T-SQL e attività di manutenzione. Ogni processo registra lo stato di esecuzione e ripete automaticamente le operazioni se si verificano errori.
È possibile definire il database o i gruppi di database SQL di Azure di destinazione in cui verrà eseguito il processo, nonché le pianificazioni per l'esecuzione.
Un processo gestisce l'attività di accesso al database di destinazione. È anche possibile definire, gestire e mantenere script Transact-SQL da eseguire su un gruppo di database SQL di Azure.

L'automazione dei processi può essere usata in diversi scenari:

- Automatizzare le attività di gestione e quindi pianificare l'esecuzione in ogni giorno feriale, fuori orario lavorativo e così via.
  - Distribuire le modifiche dello schema, la gestione delle credenziali, la raccolta dei dati sulle prestazioni o la raccolta dei dati di telemetria del tenant (cliente).
  - Aggiornare i dati di riferimento (ossia le informazioni comuni tra tutti i database) e caricare dati dall'archivio BLOB di Azure.
  - Ricompilazione degli indici per migliorare le prestazioni delle query. Configurare i processi per l'esecuzione in una raccolta di database su base periodica, ad esempio durante le fasce orarie non di punta.
  - Raccogliere i risultati di query da un set di database in una tabella centrale su base costante. Le query di prestazione possono essere eseguite continuamente e configurate per l'esecuzione di attività aggiuntive di trigger.
- Raccogliere i dati per i report
  - Aggregare i dati di una raccolta di database SQL di Azure in una singola tabella di destinazione.
  - Eseguire query di elaborazione dei dati più lunghe per una vasta serie di database, ad esempio la raccolta della telemetria del cliente. I risultati vengono raccolti in una tabella di destinazione singola per ulteriori analisi.
- Spostare dati
 - Creare processi che replicano le modifiche apportate ai database in altri database o raccolgono gli aggiornamenti effettuati in database remoti e applicano le modifiche nel database.
 - Creare processi che caricano dati da o verso i database usando SQL Server Integration Services (SSIS).

In Database SQL di Azure sono disponibili le tecnologie di pianificazione dei processi seguenti.

- **Processi di SQL Agent**: componente classico e testato sul campo per la pianificazione dei processi di SQL Server disponibile in Istanza gestita. I processi di SQL Agent non sono disponibili nei database singoli.
- **Processi di database elastico**: servizio di pianificazione dei processi che esegue processi personalizzati su uno o più database SQL di Azure.

È opportuno notare alcune differenze tra SQL Agent, disponibile in locale e come parte di Istanza gestita di database SQL, e l'agente dei processi di database elastico, disponibile per database singoli in Database SQL di Azure e per database in SQL Data Warehouse.

|  |Processi elastici  |SQL Agent |
|---------|---------|---------|
|Scope     |  Qualsiasi numero di database SQL di Azure e/o di data warehouse nello stesso cloud di Azure dell'agente di processo. Le destinazioni possono trovarsi in server di database SQL, sottoscrizioni e/o aree differenti. <br><br>I gruppi di destinazione possono essere composti da singoli database o data warehouse o da tutti i database in un server, pool o mappa delle partizioni (enumerati dinamicamente al momento dell'esecuzione del processo). | Qualsiasi database singolo nella stessa istanza di SQL Server di SQL Agent. |
|API e strumenti supportati     |  Portale, PowerShell, T-SQL, Azure Resource Manager      |   T-SQL, SQL Server Management Studio (SSMS)     |

## <a name="sql-agent-jobs"></a>Processi di SQL Agent

I processi di SQL Agent sono serie specificate di script T-SQL sul database. Usare i processi per definire un'attività amministrativa eseguibile una o più volte e monitorabile per verificarne l'esito positivo o negativo.
Un processo può essere eseguito in un server locale o in più server remoti. I processi di SQL Agent sono un componente interno del motore di database che viene eseguito nel servizio Istanza gestita.
Di seguito sono riportati i concetti chiave dei processi di SQL Agent:

- I **passaggi di processo** sono set di uno o più passaggi da eseguire all'interno del processo. Per ogni passaggio di processo è possibile definire la strategia di ripetizione dei tentativi e l'azione da eseguire in caso di esito positivo o negativo del passaggio.
- Le **pianificazioni** definiscono quando deve essere eseguito il processo.
- Le **notifiche** consentono di definire le regole da usare per inviare notifiche agli operatori tramite posta elettronica al termine del processo.

### <a name="job-steps"></a>Passaggi di processo

I passaggi di processo di SQL Agent sono sequenze di azioni che devono essere eseguite da SQL Agent. Ogni passaggio include il passaggio successivo da eseguire in caso di esito positivo o negativo e il numero di tentativi in caso di errore.
SQL Agent consente di creare tipi diversi di passaggi di processo, ad esempio passaggi di processo Transact-SQL per l'esecuzione di un singolo batch Transact-SQL sul database, passaggi PowerShell o di comandi del sistema operativo per l'esecuzione di uno script personalizzato del sistema operativo, passaggi di processo SSIS che consentono di caricare dati con il runtime SSIS o passaggi di [replica](sql-database-managed-instance-transactional-replication.md) per la pubblicazione di modifiche da un database ad altri.

La [replica transazionale](sql-database-managed-instance-transactional-replication.md) è una funzionalità del motore di database che consente di pubblicare le modifiche apportate a una o più tabelle di un database e pubblicarle/distribuirle in un set di database sottoscrittore. La pubblicazione delle modifiche viene implementata con i tipi di passaggi di processo di SQL Agent seguenti:

- Lettore di log delle transazioni.
- Snapshot.
- Database di distribuzione.

Altri tipi di passaggi dei processi non sono attualmente supportati, tra cui:

- Il passaggio del processo di replica di tipo merge non è supportato.
- La lettura coda non è supportata.
- Analysis Services non è supportato.

### <a name="job-schedules"></a>Pianificazioni dei processi

Una pianificazione specifica quando un processo viene eseguito. La stessa pianificazione può includere l'esecuzione di più processi e allo stesso processo possono essere applicate più pianificazioni.
Per quanto riguarda quando un processo deve essere eseguito, una pianificazione può definire le condizioni seguenti:

- Ogni volta che l'istanza viene riavviata o all'avvio di SQL Server Agent. Il processo viene attivato dopo ogni failover.
- Una sola volta in una data e un'ora specifiche. Questa opzione è utile per l'esecuzione posticipata di alcuni processi.
- In base a una pianificazione ricorrente.

> [!Note]
> Istanza gestita attualmente non consente di avviare un processo quando l'istanza è "inattiva".

### <a name="job-notifications"></a>Notifiche dei processi

I processi di SQL Agent consentono di ricevere notifiche al completamento o all'esito negativo del processo. È possibile ricevere una notifica tramite posta elettronica.

Per prima cosa, sarà necessario configurare l'account di posta elettronica che verrà usato per l'invio delle notifiche e assegnare l'account al profilo di posta elettronica denominato `AzureManagedInstance_dbmail_profile`, come illustrato nell'esempio seguente:

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,  
    @password = '$(password)' 

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.' ;

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

Sarà anche necessario abilitare Posta elettronica database in Istanza gestita:

```sql
GO
EXEC sp_configure 'show advanced options', 1;  
GO  
RECONFIGURE;  
GO  
EXEC sp_configure 'Database Mail XPs', 1;  
GO  
RECONFIGURE 
```

È possibile notificare all'operatore che si sono verificati determinati eventi nei processi di SQL Agent. Un operatore definisce le informazioni di contatto per la persona responsabile della manutenzione di una o più istanze gestite. Le responsabilità di operatore vengono talvolta assegnate a una sola persona.
Nei sistemi con più istanze di SQL Server o Istanza gestita, possono essere condivise da diverse persone. Un operatore non include informazioni di sicurezza e non definisce un'entità di sicurezza.

È possibile creare gli operatori usando SSMS o lo script Transact-SQL illustrato nell'esempio seguente:

```sql
EXEC msdb.dbo.sp_add_operator 
    @name=N'Mihajlo Pupun', 
        @enabled=1, 
        @email_address=N'mihajlo.pupin@contoso.com'
```

È possibile modificare qualsiasi processo e assegnare l'operatore che riceverà una notifica tramite posta elettronica in caso di completamento, esito negativo o esito positivo del processo usando SSMS o lo script Transact-SQL seguente:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS', 
        @notify_level_email=3,                        -- Options are: 1 on succeed, 2 on failure, 3 on complete
        @notify_email_operator_name=N'Mihajlo Pupun'
```

### <a name="sql-agent-job-limitations"></a>Limitazioni dei processi di SQL Agent

Alcune delle funzionalità di SQL Agent disponibili in SQL Server non sono supportate in Istanza gestita:
- Le impostazioni dell'agente SQL sono di sola lettura. La routine `sp_set_agent_properties` non è supportata in Istanza gestita.
- L'abilitazione/disabilitazione dell'agente non è attualmente supportata in Istanza gestita. SQL Agent è sempre in esecuzione.
- Le notifiche sono supportate in modo parziale.
 - Il cercapersone non è supportato.
 - NetSend non è supportato.
 - Gli avvisi non sono ancora supportati.
- I proxy non sono supportati.
- EventLog non è supportato.

Per informazioni su SQL Server Agent, vedere [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

## <a name="elastic-database-jobs"></a>Processi di database elastici

I **processi di database elastico** consentono di eseguire uno o più script T-SQL in parallelo, in un numero elevato di database, in una pianificazione o su richiesta.

**Eseguire processi su qualsiasi combinazione di database**: uno o più database, tutti i database in un server, tutti i database in un pool elastico o in una mappa delle partizioni, con in più la possibilità di includere o escludere database specifici. **I processi possono essere eseguiti in più server, in più pool e anche in database di sottoscrizioni differenti.** I server e i pool vengono enumerati in modo dinamico in fase di esecuzione, quindi i processi vengono eseguiti su tutti i database esistenti nel gruppo di destinazione al momento dell'esecuzione.

La figura seguente mostra un agente di processo che esegue processi tra diversi tipi di gruppi di destinazione:

![Modello concettuale dell'agente di processo elastico](media/elastic-jobs-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Componenti del processo elastico

|Componente  | Descrizione (altri dettagli sono disponibili sotto la tabella) |
|---------|---------|
|[**Agente di processo elastico**](#elastic-job-agent) |  Risorsa di Azure creata per eseguire e gestire i processi.   |
|[**Database di processo**](#job-database)    |    Database SQL di Azure usato dall'agente di processo per archiviare dati correlati ai processi, definizioni dei processi e così via.      |
|[**Gruppo di destinazione**](#target-group)      |  Il set di server, pool, database e mappe delle partizioni in cui eseguire un processo.       |
|[**Processo**](#job)  |  Un processo è un'unità di lavoro costituita da uno o più [passaggi](#job-step). I passaggi del processo specificano lo script T-SQL da eseguire, nonché altri dettagli necessari per eseguirlo.  |


#### <a name="elastic-job-agent"></a>Agente di processo elastico

Un agente di processo elastico è la risorsa di Azure per la creazione, l'esecuzione e la gestione dei processi. L'agente di processo elastico è una risorsa di Azure che viene creata nel portale (sono anche supportati [PowerShell](elastic-jobs-powershell.md) e REST). 

La creazione di un **agente di processo elastico** richiede un database SQL esistente. L'agente configura il database esistente come [*database di processo*](#job-database).

L'agente di processo elastico è gratuito. Il database di processo viene fatturato alla stessa tariffa di qualsiasi database SQL.

#### <a name="job-database"></a>Database di processo

Il *database di processo* viene usato per definire i processi e tracciare lo stato e la cronologia delle esecuzioni dei processi. Il *database di processo* viene anche usato per archiviare metadati dell'agente, log, risultati e definizioni dei processi e contiene anche molte utili stored procedure e altri oggetti del database per creare, eseguire e gestire i processi con T-SQL.

Per l'anteprima corrente, è necessario un database SQL di Azure esistente (S0 o superiore) per creare un agente di processo elastico.

Il *database di processo* non deve essere necessariamente nuovo, ma deve essere pulito, vuoto e con livello di servizio S0 o superiore. Il livello di servizio consigliato per il *database di processo* è S1 o superiore, ma in realtà dipende dalle prestazioni richieste dai processi, ovvero numero di passaggi e numero e frequenza delle esecuzioni. Un database S0 può essere ad esempio sufficiente per un agente di processo che esegue pochi processi ogni ora, mentre se esegue un processo ogni minuto le prestazioni potrebbero essere insufficienti e rendere opportuno un livello di servizio superiore.


##### <a name="job-database-permissions"></a>Autorizzazioni per il database di processo

Durante la creazione di un agente di processo vengono creati uno schema, tabelle e un ruolo denominato *jobs_reader* nel *database di processo*. Il ruolo viene creato con l'autorizzazione seguente ed è progettato per fornire agli amministratori un controllo di accesso più preciso per il monitoraggio dei processi:


|Nome del ruolo  |Autorizzazioni per lo schema "jobs"  |Autorizzazioni per lo schema 'jobs_internal'  |
|---------|---------|---------|
|**jobs_reader**     |    SELECT     |    Nessuna     |

> [!IMPORTANT]
> Prima di concedere l'accesso al *database di processo* come amministratore del database, considerare le implicazioni per la sicurezza. Un utente malintenzionato con le autorizzazioni di creazione o modifica dei processi potrebbe creare o modificare un processo che usa una credenziale archiviata per connettersi a un database con il controllo dell'utente malintenzionato, consentendo a questo utente di determinare la password della credenziale.



#### <a name="target-group"></a>Gruppo di destinazione

Un *gruppo di destinazione* definisce il set di database sui quali verrà eseguito un passaggio di processo. Un gruppo di destinazione può contenere qualsiasi numero e una combinazione degli elementi seguenti:

- **Server di database SQL**: se è specificato un server, tutti i database presenti nel server al momento dell'esecuzione del processo fanno parte del gruppo. È necessario fornire le credenziali del database master in modo che il gruppo possa essere enumerato e aggiornato prima dell'esecuzione del processo.
- **Pool elastico**: se è specificato un pool elastico, tutti i database presenti nel pool elastico al momento dell'esecuzione del processo fanno parte del gruppo. Come per un server, è necessario fornire le credenziali del database master in modo che il gruppo possa essere aggiornato prima dell'esecuzione del processo.
- **Database singolo**: specificare uno o più database singoli da includere nel gruppo.
- **Mappa delle partizioni**: database di una mappa delle partizioni.

> [!TIP]
> Al momento dell'esecuzione del processo, l'*enumerazione dinamica* rivaluta il set dei database nei gruppi di destinazione che includono server o pool. L'enumerazione dinamica assicura che i **processi vengano eseguiti in tutti i database esistenti nel server o nel pool al momento dell'esecuzione**. Rivalutare l'elenco dei database in fase di esecuzione è particolarmente utile per gli scenari in cui l'appartenenza al pool o al server cambia frequentemente.

I pool e i database singoli possono essere specificati come inclusi o esclusi dal gruppo. Ciò consente di creare un gruppo di destinazione con qualsiasi combinazione di database. È ad esempio possibile aggiungere un server a un gruppo di destinazione, ma escludere database specifici di un pool elastico o escludere un intero pool.

Un gruppo di destinazione può includere database in più sottoscrizioni e in più aree. Si noti che le esecuzioni tra più aree hanno una latenza maggiore rispetto alle esecuzioni nella stessa area.

Gli esempi seguenti illustrano come diverse definizioni del gruppo destinazione vengono enumerate in modo dinamico al momento dell'esecuzione del processo per determinare i database che verranno eseguiti:

![Esempi di gruppi di destinazione](media/elastic-jobs-overview/targetgroup-examples1.png)

L'**esempio 1** mostra un gruppo di destinazione costituito da un elenco di singoli database. Quando un passaggio del processo viene eseguito usando questo gruppo di destinazione, l'azione del passaggio verrà eseguita in ognuno di tali database.<br>
L'**esempio 2** mostra un gruppo di destinazione contenente un server di Azure SQL come destinazione. Quando un passaggio del processo viene eseguito usando questo gruppo di destinazione, il server viene enumerato in modo dinamico per determinare l'elenco dei database attualmente presenti nel server. L'azione del passaggio del processo verrà eseguita in ognuno di tali database.<br>
L'**esempio 3** mostra un gruppo di destinazione simile a quello dell'*esempio 2*, ma con l'esclusione specifica di un singolo database. L'azione del passaggio del processo *non* verrà eseguita nel database escluso.<br>
L'**esempio 4** mostra un gruppo di destinazione contenente un pool elastico come destinazione. Analogamente all'*esempio 2*, il pool verrà enumerato in modo dinamico in fase di esecuzione del processo per determinare l'elenco dei database nel pool.
<br><br>


![Esempi di gruppi di destinazione](media/elastic-jobs-overview/targetgroup-examples2.png)

L'**esempio 5** e l'**esempio 6** mostrano scenari avanzati in cui server di Azure SQL, pool elastici e database possono essere combinati usando regole di inclusione e di esclusione.<br>
L'**esempio 7** mostra che in fase di esecuzione del processo possono essere valutate anche le partizioni in una mappa delle partizioni.

#### <a name="job"></a>Processo

Un *processo* è un'unità di lavoro che viene eseguita in una pianificazione o come processo occasionale. Un processo è costituito da uno o più *passaggi*.

##### <a name="job-step"></a>Passaggio del processo

Ogni passaggio del processo specifica uno script T-SQL da eseguire, uno o più gruppi di destinazione in cui eseguire lo script T-SQL e le credenziali richieste dall'agente di processo per connettersi al database di destinazione. Ogni passaggio del processo ha criteri di timeout e ripetizione personalizzabili e può eventualmente specificare parametri di output.

#### <a name="job-output"></a>Output del processo

Il risultato dei passaggi di un processo in ciascun database di destinazione vengono registrati nei dettagli e l'output dello script può essere acquisito in una tabella specifica. È possibile specificare un database per salvare i dati restituiti da un processo.

#### <a name="job-history"></a>Cronologia dei processi

La cronologia dell'esecuzione dei processi viene archiviata nel *database di processo*. Un processo di pulizia del sistema elimina la cronologia dei processi eseguiti oltre 45 giorni prima. Per rimuovere la cronologia dei processi eseguiti entro i 45 giorni precedenti, richiamare la stored procedure **sp_purge_history** nel *database di processo*.

### <a name="agent-performance-capacity-and-limitations"></a>Prestazioni, capacità e limitazioni degli agenti

I processi elastici usano risorse di calcolo minime in attesa del completamento di processi di lunga durata.

A seconda delle dimensioni del gruppo di database di destinazione e del tempo di esecuzione desiderato per un processo (numero di processi simultanei), l'agente richiede prestazioni e risorse di calcolo differenti per il *database di processo*: maggiore è il numero di destinazioni e di processi, maggiore sarà la quantità di risorse di calcolo necessarie.

Attualmente, l'anteprima è limitata a 100 processi simultanei.

#### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Impedire ai processi di ridurre le prestazioni del database di destinazione

Per garantire che le risorse non siano sovraccariche quando si eseguono processi sul database in un pool elastico SQL, è possibile configurare i processi in modo da limitare il numero di database in cui un processo può essere eseguito contemporaneamente.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) 
- [Come creare e gestire processi elastici](elastic-jobs-overview.md) 
- [Creare e gestire processi elastici usando PowerShell](elastic-jobs-powershell.md) 
- [Creare e gestire processi elastici usando Transact-SQL (T-SQL)](elastic-jobs-tsql.md) 
