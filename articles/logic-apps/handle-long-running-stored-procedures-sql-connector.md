---
title: Gestire stored procedure con esecuzione prolungata nel connettore SQL
description: Come gestire le stored procedure che si verificano in timeout quando si usa il connettore SQL in app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: camerost, logicappspm
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f5b04c563dc81497f591788dc4890d379c0f898f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102966"
---
# <a name="handle-stored-procedure-timeouts-in-the-sql-connector-for-azure-logic-apps"></a>Gestire i timeout dei stored procedure nel connettore SQL per app per la logica di Azure

Quando l'app per la logica funziona con i set di risultati così grandi che il [connettore SQL](../connectors/connectors-create-api-sqlazure.md) non restituisce tutti i risultati contemporaneamente o se si desidera un maggiore controllo sulle dimensioni e sulla struttura per i set di risultati, è possibile creare un [stored procedure](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) che organizza i risultati nel modo desiderato. Il connettore SQL fornisce molte funzionalità di back-end a cui è possibile accedere usando app per la [logica di Azure](../logic-apps/logic-apps-overview.md) , in modo che sia possibile automatizzare più facilmente le attività aziendali che funzionano con le tabelle del database SQL.

Ad esempio, quando si recuperano o si inseriscono più righe, l'app per la logica può scorrere queste righe usando un [ciclo **until**](../logic-apps/logic-apps-control-flow-loops.md#until-loop) entro questi [limiti](../logic-apps/logic-apps-limits-and-config.md). Tuttavia, quando l'app per la logica deve lavorare con migliaia o milioni di righe, è necessario ridurre al minimo i costi derivanti dalle chiamate al database. Per altre informazioni, vedere [gestire i dati in blocco usando il connettore SQL](../connectors/connectors-create-api-sqlazure.md#handle-bulk-data).

<a name="timeout-limit"></a>

## <a name="timeout-limit-on-stored-procedure-execution"></a>Limite di timeout per l'esecuzione stored procedure

Il connettore SQL ha un limite di timeout di stored procedure [inferiore a 2 minuti](/connectors/sql/#known-issues-and-limitations). Per il completamento di alcune stored procedure potrebbe essere necessario più tempo di questo limite, causando un `504 Timeout` errore. A volte questi processi a esecuzione prolungata vengono codificati come stored procedure in modo esplicito a questo scopo. A causa del limite di timeout, la chiamata di queste procedure da app per la logica di Azure potrebbe creare problemi. Sebbene il connettore SQL non supporti in modo nativo una modalità asincrona, è possibile aggirare questo problema e simulare questa modalità usando un trigger di completamento SQL, una query pass-through SQL nativa, una tabella di stato e processi sul lato server. Per questa attività, è possibile usare l' [agente processo elastico di Azure](../azure-sql/database/elastic-jobs-overview.md) per il [database SQL di Azure](../azure-sql/database/sql-database-paas-overview.md). Per [SQL Server in locale](/sql/sql-server/sql-server-technical-documentation) e [istanza gestita di Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md), è possibile usare il [SQL Server Agent](/sql/ssms/agent/sql-server-agent).

Si supponga, ad esempio, di avere il seguente stored procedure a esecuzione prolungata, che richiede più tempo del limite di timeout per completare l'esecuzione. Se si esegue questa stored procedure da un'app per la logica usando il connettore SQL, viene visualizzato un `HTTP 504 Gateway Timeout` errore come risultato.

```sql
CREATE PROCEDURE [dbo].[WaitForIt]
   @delay char(8) = '00:03:00'
AS
BEGIN
   SET NOCOUNT ON;
   WAITFOR DELAY @delay
END
```

Anziché chiamare direttamente il stored procedure, è possibile eseguire la procedura in modo asincrono in background utilizzando un *agente processo* . È possibile archiviare gli input e gli output in una tabella di stato con cui è possibile interagire tramite l'app per la logica. Se gli input e gli output non sono necessari o se si stanno già scrivendo i risultati in una tabella del stored procedure, è possibile semplificare questo approccio.

> [!IMPORTANT]
> Verificare che il stored procedure e tutti i processi siano *idempotente* , il che significa che possono essere eseguiti più volte senza influire sui risultati. Se l'elaborazione asincrona ha esito negativo o scade, l'agente processo può ripetere il passaggio e quindi il stored procedure più volte. Per evitare di duplicare l'output, prima di creare oggetti, esaminare le [procedure consigliate e gli approcci](../azure-sql/database/elastic-jobs-overview.md#idempotent-scripts).

La sezione successiva descrive come è possibile usare l'agente processo elastico di Azure per il database SQL di Azure. Per SQL Server e Istanza gestita SQL di Azure, è possibile usare il SQL Server Agent. Alcuni dettagli di gestione sono diversi, ma i passaggi fondamentali rimangono invariati rispetto alla configurazione di un agente processo per il database SQL di Azure.

<a name="azure-sql-database"></a>

## <a name="job-agent-for-azure-sql-database"></a>Agente processo per il database SQL di Azure

Per creare un processo in grado di eseguire il stored procedure per il [database SQL di Azure](../azure-sql/database/sql-database-paas-overview.md), usare l' [agente processo elastico di Azure](../azure-sql/database/elastic-jobs-overview.md). Creare l'agente processo nell'portale di Azure. Questo approccio consente di aggiungere diverse stored procedure al database utilizzato dall'agente, noto anche come *database di Agent* . È quindi possibile creare un processo che esegue il stored procedure nel database di destinazione e acquisisce l'output al termine dell'operazione.

Prima di poter creare il processo, è necessario configurare le autorizzazioni, i gruppi e le destinazioni come descritto nella [documentazione completa per l'agente processo elastico di Azure](../azure-sql/database/elastic-jobs-overview.md). È anche necessario creare una tabella di supporto nel database di destinazione come descritto nelle sezioni seguenti.

<a name="create-state-table"></a>

### <a name="create-state-table-for-registering-parameters-and-storing-inputs"></a>Creare una tabella di stato per la registrazione dei parametri e l'archiviazione degli input

I processi di SQL Agent non accettano parametri di input. Al contrario, nel database di destinazione creare una tabella di stato in cui registrare i parametri e archiviare gli input da usare per chiamare le stored procedure. Tutti i passaggi del processo di Agent vengono eseguiti sul database di destinazione, ma le stored procedure del processo vengono eseguite sul database di Agent. 

Per creare la tabella di stato, utilizzare questo schema:

```sql
CREATE TABLE [dbo].[LongRunningState](
   [jobid] [uniqueidentifier] NOT NULL,
   [rowversion] [timestamp] NULL,
   [parameters] [nvarchar](max) NULL,
   [start] [datetimeoffset](7) NULL,
   [complete] [datetimeoffset](7) NULL,
   [code] [int] NULL,
   [result] [nvarchar](max) NULL,
   CONSTRAINT [PK_LongRunningState] PRIMARY KEY CLUSTERED
      (   [jobid] ASC
      )WITH (STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF) ON [PRIMARY]
      ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
```

Ecco il modo in cui la tabella risultante Cerca in [SQL Server Management Studio (SMS)](/sql/ssms/download-sql-server-management-studio-ssms):

![Screenshot che mostra la tabella di stato creata che archivia gli input per stored procedure.](media/handle-long-running-stored-procedures-sql-connector/state-table-input-parameters.png)

Per garantire prestazioni ottimali e assicurarsi che il processo di Agent possa trovare il record associato, nella tabella viene utilizzato l'ID di esecuzione del processo ( `jobid` ) come chiave primaria. Se lo si desidera, è anche possibile aggiungere singole colonne per i parametri di input. Lo schema descritto in precedenza è in grado di gestire più parametri, ma è limitato alle dimensioni calcolate da `NVARCHAR(MAX)` .

<a name="create-top-level-job"></a>

### <a name="create-a-top-level-job-to-run-the-stored-procedure"></a>Creare un processo di livello superiore per eseguire il stored procedure

Per eseguire la stored procedure con esecuzione prolungata, creare questo agente processo di primo livello nel database dell'agente:

```sql
EXEC jobs.sp_add_job 
   @job_name='LongRunningJob',
   @description='Execute Long-Running Stored Proc',
   @enabled = 1
```

A questo punto, aggiungere i passaggi al processo che parametrizzano, eseguono e completano il stored procedure. Per impostazione predefinita, un passaggio del processo scade dopo 12 ore. Se il stored procedure necessita di più tempo o se si desidera che la procedura venga impostata in precedenza, è possibile impostare il `step_timeout_seconds` parametro su un altro valore specificato in secondi. Per impostazione predefinita, un passaggio prevede 10 tentativi predefiniti con un timeout backoff tra ogni nuovo tentativo, che può essere usato per sfruttare i vantaggi.

Ecco i passaggi da eseguire per aggiungere:

1. Attendere che i parametri vengano visualizzati nella `LongRunningState` tabella.

   Il primo passaggio attende che i parametri vengano aggiunti nella `LongRunningState` tabella, che si verifica subito dopo l'avvio del processo. Se l'ID di esecuzione del processo ( `jobid` ) non viene aggiunto alla `LongRunningState` tabella, il passaggio ha esito negativo e il timeout predefinito per tentativi o backoff è in attesa:

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name= 'Parameterize WaitForIt',
      @step_timeout_seconds = 30,
      @command= N'
         IF NOT EXISTS(SELECT [jobid] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
            THROW 50400, ''Failed to locate call parameters (Step1)'', 1',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Eseguire una query sui parametri della tabella di stato e passarli al stored procedure. Questo passaggio esegue anche la procedura in background. 

   Se il stored procedure non richiede parametri, è sufficiente chiamare direttamente il stored procedure. In caso contrario, per passare il `@timespan` parametro, utilizzare `@callparams` , che è possibile estendere anche per passare parametri aggiuntivi.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Execute WaitForIt',
      @command=N'
         DECLARE @timespan char(8)
         DECLARE @callparams NVARCHAR(MAX)
         SELECT @callparams = [parameters] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
         SET @timespan = @callparams
         EXECUTE [dbo].[WaitForIt] @delay = @timespan', 
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Completare il processo e registrare i risultati.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Complete WaitForIt',
      @command=N'
         UPDATE [dbo].[LongRunningState]
            SET [complete] = GETUTCDATE(),
               [code] = 200,
               [result] = ''Success''
            WHERE jobid = $(job_execution_id)',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

<a name="start-job-pass-parameters"></a>

### <a name="start-the-job-and-pass-the-parameters"></a>Avviare il processo e passare i parametri

Per avviare il processo, usare una query nativa pass-through con l' [azione **Esegui query SQL**](/connectors/sql/#execute-a-sql-query-(v2)) e quindi eseguire immediatamente il push dei parametri del processo nella tabella di stato. Per fornire input all' `jobid` attributo nella tabella di destinazione, app per la logica aggiunge un ciclo **For Each** che scorre l'output della tabella dall'azione precedente. Per ogni ID di esecuzione del processo, eseguire un'azione **Inserisci riga** che usa l'output dei dati dinamici, `ResultSets JobExecutionId` , per aggiungere i parametri per il processo da decomprimere e passare al stored procedure di destinazione.

![Screenshot che mostra le azioni da usare per l'avvio del processo e il passaggio di parametri al stored procedure.](media/handle-long-running-stored-procedures-sql-connector/start-job-actions.png)

Al termine del processo, il processo aggiorna la `LongRunningState` tabella in modo che sia possibile attivare facilmente il risultato usando il [trigger **quando viene modificato un elemento**](/connectors/sql/#when-an-item-is-modified-(v2)). Se l'output non è necessario o se è già presente un trigger che monitora una tabella di output, è possibile ignorare questa parte.

![Screenshot che mostra il trigger SQL per la modifica di un elemento.](media/handle-long-running-stored-procedures-sql-connector/trigger-on-results-after-job-completes.png)

<a name="sql-on-premises-or-managed-instance"></a>

## <a name="job-agent-for-sql-server-or-azure-sql-managed-instance"></a>Agente processo per SQL Server o Azure SQL Istanza gestita

Per lo stesso scenario, è possibile usare la [SQL Server Agent](/sql/ssms/agent/sql-server-agent) per [SQL Server locale](/sql/sql-server/sql-server-technical-documentation) e [istanza gestita SQL di Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). Anche se alcuni dettagli di gestione sono diversi, i passaggi fondamentali rimangono invariati rispetto alla configurazione di un agente processo per il database SQL di Azure.

## <a name="next-steps"></a>Passaggi successivi

[Connettersi a SQL Server, al database SQL di Azure o ad Azure SQL Istanza gestita](../connectors/connectors-create-api-sqlazure.md)

