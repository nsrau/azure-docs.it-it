---
title: Usare dati di riferimento da un database SQL per un processo di Analisi di flusso di Azure (anteprima)
description: Questo articolo descrive come usare un database SQL come input dei dati di riferimento per un processo di Analisi di flusso di Azure nel portale di Azure e in Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: d79e423dd617a498aee09cff6b774bbe90eeaa83
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55701591"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job-preview"></a>Usare dati di riferimento da un database SQL per un processo di Analisi di flusso di Azure (anteprima)

Analisi di flusso di Azure supporta il database SQL di Azure come origine di input per i dati di riferimento. È possibile usare i dati contenuti in un database SQL come dati di riferimento per il processo di Analisi di flusso nel portale di Azure e in Visual Studio con gli strumenti di Analisi di flusso. Questo articolo illustra come fare con entrambi i metodi.

## <a name="azure-portal"></a>Portale di Azure

Seguire questa procedura per aggiungere il database SQL di Azure come origine di input di riferimento tramite il portale di Azure:

### <a name="portal-prerequisites"></a>Prerequisiti del portale

1. Creare un processo di Analisi di flusso.

2. Creare un account di archiviazione che verrà usato dal processo di Analisi di flusso.

3. Creare il database SQL di Azure con un set di dati da usare come dati di riferimento per il processo di Analisi di flusso.

### <a name="define-sql-database-reference-data-input"></a>Definire l'input dei dati di riferimento del database SQL

1. Nel processo di Analisi di flusso selezionare **Input** sotto **Topologia processo**. Fare clic su **Aggiungi input di riferimento** e scegliere **Database SQL**.

   ![Input del processo di Analisi di flusso](./media/sql-reference-data/stream-analytics-inputs.png)

2. Compilare le configurazioni di input di Analisi di flusso. Scegliere il nome del database, il nome del server, il nome utente e la password. Se si vuole che l'input dei dati di riferimento venga aggiornato periodicamente, scegliere "Attivato" per specificare la frequenza di aggiornamento in GG:HH:MM. Se si hanno grandi set di dati con una frequenza di aggiornamento veloce, è possibile usare una [query delta](sql-reference-data.md#delta-query).

   ![Configurazione del riferimento del database SQL](./media/sql-reference-data/sql-input-config.png)

3. Testare la query snapshot nell'editor di query SQL. Per altre informazioni, vedere [Usare l'editor di query SQL del portale di Azure per connettersi ed eseguire query sui dati](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Specificare l'account di archiviazione nella configurazione del processo

Passare a **Impostazioni account di archiviazione** sotto **Configura** e selezionare **Aggiungi account di archiviazione**.

   ![Impostazioni dell'account di archiviazione di Analisi di flusso](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Avviare il processo

Dopo aver configurato gli altri input, output e query, è possibile avviare il processo di Analisi di flusso.

## <a name="tools-for-visual-studio"></a>Strumenti per Visual Studio

Seguire questa procedura per aggiungere il database SQL di Azure come origine di input di riferimento tramite Visual Studio:

### <a name="visual-studio-prerequisites"></a>Prerequisiti di Visual Studio

1. Se si usa Visual Studio 2017, aggiornare alla versione 15.8.2 o successiva. Si noti che le versioni 16.0 e successive non sono attualmente supportate.

2. [Installare gli strumenti di Analisi di flusso per Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Sono supportate le versioni seguenti di Visual Studio:

   * Visual Studio 2015
   * Visual Studio 2017

3. Acquisire familiarità con l'argomento di avvio rapido sugli [strumenti di Analisi di flusso per Visual Studio](stream-analytics-quick-create-vs.md).

4. Creare un account di archiviazione.

### <a name="create-a-sql-database-table"></a>Creare una tabella del database SQL

Usare SQL Server Management Studio per creare una tabella in cui archiviare i dati di riferimento. Per informazioni dettagliate, vedere [Progettare il primo database SQL di Azure con SSMS](../sql-database/sql-database-design-first-database.md).

La tabella di esempio usata nell'esempio seguente è stata creata dall'istruzione seguente:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Scegliere la sottoscrizione

1. In Visual Studio dal menu **Visualizza** scegliere **Esplora server**.

2. Fare clic con il pulsante destro del mouse su **Azure**, selezionare **Connessione alla sottoscrizione di Microsoft Azure** e quindi accedere con l'account di Azure.

### <a name="create-a-stream-analytics-project"></a>Creare un progetto di Analisi di flusso

1. Fare clic su **File > Nuovo progetto**. 

2. Nell'elenco dei modelli a sinistra selezionare **Analisi di flusso** e quindi **Applicazione Analisi di flusso di Azure**. 

3. Inserire i valori appropriati per il progetto in **Nome**, **Percorso** e **Nome soluzione** e scegliere **OK**.

   ![Nuovo progetto di Analisi di flusso in Visual Studio](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Definire l'input dei dati di riferimento del database SQL

1. Creare un nuovo input.

   ![Nuovo input di Analisi di flusso in Visual Studio](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Fare doppio clic su **Input.json** in **Esplora soluzioni**.

3. Compilare **Stream Analytics Input Configuration** (Configurazione input Analisi di flusso). Scegliere nome del database, nome del server, tipo di aggiornamento e frequenza di aggiornamento. Specificare la frequenza di aggiornamento nel formato `DD:HH:MM`.

   ![Configurazione dell'input di Analisi di flusso in Visual Studio](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Se si sceglie "Execute only once"(Esegui una sola volta) o "Execute periodically" (Esegui periodicamente), un file SQL CodeBehind denominato **[Alias di input].snapshot.sql** viene generato nel progetto sotto il nodo del file **Input.json**.

   ![Code-behind dell'input in Visual Studio](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Se si sceglie "Refresh Periodically with Delta" (Aggiorna periodicamente con delta), verranno generati due file SQL CodeBehind: **[Alias di Input].snapshot.sql** e **[Alias di Input].delta.sql**.

   ![Code-behind in Esplora soluzioni](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Aprire il file SQL nell'editor e scrivere la query SQL.

5. Se si usa Visual Studio 2017 ed è installato SQL Server Data Tools, è possibile testare la query facendo clic su **Esegui**. Verrà visualizzata una finestra della procedura guidata per eseguire la connessione al database SQL e il risultato della query comparirà nella parte inferiore della finestra.

### <a name="specify-storage-account"></a>Specificare l'account di archiviazione

Aprire **JobConfig.json** per specificare l'account di archiviazione in cui archiviare gli snapshot di riferimento SQL.

   ![Configurazione del processo di Analisi di flusso in Visual Studio](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Testare in locale e distribuire in Azure

Prima di distribuire il processo in Azure, è possibile testare la logica di query in locale con dati di input live. Per altre informazioni su questa funzionalità, vedere [Testare i dati live in locale usando gli strumenti di Analisi di flusso di Azure per Visual Studio (anteprima)](stream-analytics-live-data-local-testing.md). Dopo aver completato i test, fare clic su **Invia ad Azure**. Per informazioni su come avviare, il processo vedere l'argomento di avvio rapido [Creare un processo di Analisi di flusso con gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="delta-query"></a>Query delta

Quando si usa la query delta, è consigliabile usare le [tabelle temporali nel database SQL di Azure](../sql-database/sql-database-temporal-tables.md).

1. Creare la query snapshot. 

   Usare il parametro **@snapshotTime** per indicare al runtime di Analisi di flusso di ottenere il set di dati di riferimento dalla tabella temporale del database SQL valida nell'ora di sistema. Se non si specifica questo parametro si rischia di ottenere un set di dati di riferimento di base non accurato, a causa degli sfasamenti di orario. Di seguito è riportata una query snapshot completa di esempio:

   ![Query snapshot di Analisi di flusso](./media/sql-reference-data/snapshot-query.png)
 
2. Creare la query delta. 
   
   Questa query recupera tutte le righe nel database SQL che sono state inserite tra un'ora di inizio **@deltaStartTime**e un'ora di fine **@deltaEndTime**. La query delta deve restituire le stesse colonne della query snapshot, nonché la colonna **_operation_**. Questa colonna definisce se la riga è stata inserita o eliminata tra **@deltaStartTime** e **@deltaEndTime**. Le righe risultanti vengono contrassegnate con **1** se i record sono stati inseriti, con **2** se sono stati eliminati. 

   Per i record aggiornati, la tabella temporale registra un'operazione di inserimento ed eliminazione. Il runtime di Analisi di flusso applicherà quindi i risultati della query delta allo snapshot precedente per mantenere aggiornati i dati di riferimento. Un esempio di query delta è illustrato di seguito:

   ![Query delta di Analisi di flusso](./media/sql-reference-data/delta-query.png)
 
  Si noti che il runtime di Analisi di flusso può eseguire periodicamente la query snapshot oltre alla query delta per archiviare i checkpoint.

## <a name="faqs"></a>Domande frequenti

**Vengono addebitati costi aggiuntivi usando un input dei dati di riferimento SQL in Analisi di flusso di Azure?**

Non sono previsti [costi per unità di streaming](https://azure.microsoft.com/pricing/details/stream-analytics/) aggiuntivi nel processo di Analisi di flusso. Tuttavia, il processo di Analisi di flusso deve avere un account di archiviazione di Azure associato. Il processo di Analisi di flusso esegue una query sul database SQL (all'avvio del processo e nell'intervallo di aggiornamento) per recuperare il set di dati di riferimento e archivia lo snapshot nell'account di archiviazione. Per l'archiviazione di questi snapshot verranno addebitati costi aggiuntivi, descritti in dettaglio nella [pagina dei prezzi](https://azure.microsoft.compricing/details/storage/) per l'account di archiviazione di Azure.

**Come si capisce se lo snapshot dei dati di riferimento viene interrogato dal database SQL e usato nel processo di Analisi di flusso di Azure?**

Nel portale di Azure sono presenti due metriche, filtrate in base al nome logico, che è possibile usare per monitorare l'integrità dell'input dei dati di riferimento del database SQL.

   * InputEvents: questa metrica misura il numero di record caricati dal set di dati di riferimento del database SQL.
   * InputEventBytes: questa metrica misura le dimensioni dello snapshot dei dati di riferimento caricato nella memoria del processo di Analisi di flusso. 

La combinazione delle due metriche può essere usata per dedurre se il processo sta eseguendo query sul database SQL per recuperare il set di dati di riferimento e quindi caricarlo in memoria.

**È necessario un tipo particolare di database SQL di Azure?**

Analisi di flusso di Azure funziona con qualsiasi tipo di database SQL di Azure. Tuttavia, è importante comprendere che la frequenza di aggiornamento impostata per l'input dei dati di riferimento potrebbe incidere sul carico di query. Per usare l'opzione della query delta, è consigliabile usare le tabelle temporali nel database SQL di Azure.

**È possibile campionare input dall'input dei dati di riferimento del database SQL?**

Questa funzionalità non è disponibile.

**Perché Analisi di flusso di Azure archivia gli snapshot nell'account di archiviazione di Azure?**

Analisi di flusso garantisce un'elaborazione di eventi di tipo exactly-once e il recapito degli eventi almeno una volta. Nei casi in cui problemi temporanei incidano sul processo, per ripristinare lo stato è necessaria una breve riproduzione. Per abilitare la riproduzione è necessario aver archiviato questi snapshot in un account di archiviazione di Azure. Per altre informazioni sulla riproduzione dei checkpoint, vedere [Concetti di checkpoint e riproduzione nei processi di Analisi di flusso di Azure](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Passaggi successivi

* [Uso dei dati di riferimento per le ricerche in Analisi di flusso](stream-analytics-use-reference-data.md)
* [Guida introduttiva: Creare un processo di Analisi di flusso con gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-quick-create-vs.md)
* [Testare i dati live in locale usando gli strumenti di Analisi di flusso di Azure per Visual Studio (anteprima)](stream-analytics-live-data-local-testing.md)
