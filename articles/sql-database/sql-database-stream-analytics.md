---
title: Eseguire lo streaming dei dati utilizzando l'integrazione di Analisi di flusso (anteprima)Stream data using Stream Analytics integration (preview)
description: Usare Analisi di flusso di Azure per trasmettere dati in un database SQL di Azure.Use Azure Stream Analytics to stream data into an Azure SQL database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: d233d3c98cc495e4b9e84142781f5eb9faa7eec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820833"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>Trasmettere i dati tramite l'integrazione di Azure SQL Database Stream Analytics (anteprima)Stream data by using Azure SQL Database Stream Analytics integration (preview)

Gli utenti possono ora inserire, elaborare, visualizzare e analizzare i dati di streaming in tempo reale in una tabella direttamente da un database SQL nel portale di Azure usando Analisi di flusso di [Azure.](../stream-analytics/stream-analytics-introduction.md) Questa esperienza consente un'ampia gamma di scenari, ad esempio auto connessa, monitoraggio remoto, rilevamento di frodi e molti altri. Nel portale di Azure è possibile selezionare un'origine eventi (Hub eventi/Hub IoT), visualizzare gli eventi in tempo reale in ingresso e selezionare una tabella per archiviare gli eventi. È anche possibile scrivere query del linguaggio di query del linguaggio di query di Analisi di flusso nel portale per trasformare gli eventi in ingresso e archiviarli nella tabella selezionata. Questo nuovo punto di ingresso si aggiunge alle esperienze di creazione e configurazione già esistenti in Analisi di flusso. Questa esperienza inizia dal contesto del database, consentendo di configurare rapidamente un processo di Analisi di flusso e passare senza problemi tra le esperienze di Analisi del database SQL di Azure e Analisi di flusso.

![Flusso di Analisi di flusso](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>Vantaggi principali

- Cambio di contesto minimo: è possibile iniziare da un database SQL nel portale e iniziare a inserire dati in tempo reale in una tabella senza passare a nessun altro servizio. 
- Numero ridotto di passaggi: il contesto del database e della tabella viene usato per preconfigurare un processo di Analisi di flusso.
- Ulteriore facilità d'uso con i dati di anteprima: anteprima dei dati in ingresso dall'origine eventi (Hub eventi/Hub IoT) nel contesto della tabella selezionata 


## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, sono necessarie le risorse seguenti:

- Una sottoscrizione di Azure. Se non si dispone di una sottoscrizione di Azure, [creare un account gratuito.](https://azure.microsoft.com/free/) 
- Un database SQL. Per informazioni dettagliate, vedere Creare un singolo database nel database SQL di [Azure.](sql-database-single-database-get-started.md)
- Una regola del firewall che consente al computer di connettersi al server SQL di Azure.A firewall rule allowing your computer to connect to the Azure SQL server. Per informazioni dettagliate, consultate Creare una regola firewall a livello di [server.](sql-database-server-level-firewall-rule.md)


## <a name="configure-stream-analytics-integration"></a>Configurare l'integrazione di Analisi di flussoConfigure Stream analytics integration

1. Accedere al portale di Azure. 
2. Passare al database SQL in cui si desidera inserire i dati di streaming. Selezionare Analisi di flusso **(anteprima).** 

    ![Analisi di flusso](media/sql-database-stream-analytics/stream-analytics.png)

3. Per iniziare a inserire i dati di streaming in questo database SQL, selezionare **Crea** e assegnare un nome al processo di streaming e quindi selezionare **Avanti: Input**. 

    ![creare un processo di Analisi di flusso](media/sql-database-stream-analytics/create-job.png)

4. Immettere i dettagli dell'origine degli eventi e quindi selezionare **Avanti: Output**.

   - **Tipo di ingresso**: Hub/IoT evento
   - **Alias di input**: Immettere un nome per identificare l'origine degli eventi 
   - Sottoscrizione : uguale alla sottoscrizione del database **SQLSubscription**: Same as SQL Database Subscription 
   - **Spazio dei nomi dell'hub eventi:** nome dello spazio dei nomiEvent Hub namespace : Name for namespace 
   - **Nome hub eventi**: Nome dell'hub eventi all'interno dello spazio dei nomi selezionato 
   - **Nome criterio hub eventi** (impostazione predefinita per creare nuovo): assegnare un nome di criterioEvent Hub policy name (Default to create new): Give a policy name 
   - **Gruppo di consumer Hub eventi** (impostazione predefinita per creare nuovo): assegnare un nome di gruppo di consumerEvent Hub consumer group (Default to create new): Give a consumer group name  
     - È consigliabile creare un gruppo di consumer e un criterio per ogni nuovo processo di Analisi di flusso di Azure creato da qui. I gruppi di consumer consentono solo cinque lettori simultanei, pertanto fornire un gruppo di consumer dedicato per ogni processo eviterà eventuali errori che potrebbero derivare dal superamento di tale limite. Un criterio dedicato consente di ruotare la chiave o revocare le autorizzazioni senza influire sulle altre risorse.

    ![creare un processo di Analisi di flusso](media/sql-database-stream-analytics/create-job-output.png)

5. Seleziona la tabella in cui vuoi inserire i dati in streaming. Al termine, selezionare **Crea**.
   - **Nome utente**, **Password**: Immettere le credenziali per l'autenticazione di SQL Server. Selezionare **Convalida**.
   - **Tabella**: selezionare **Crea nuovo** o **Usa esistente**. In questo flusso selezioniamo **Crea**. Verrà creata una nuova tabella all'avvio del processo di analisi del flusso.

    ![creare un processo di Analisi di flusso](media/sql-database-stream-analytics/create.png)

6. Viene visualizzata una pagina di query con i seguenti dettagli:

   - **L'input** (origine degli eventi di input) da cui inserire i dati  
   - Il tuo **Output** (tabella di output) in cui verranno archiviati i dati trasformati 
   - Query [SAQL di esempio](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) con istruzione SELECT. 
   - **Anteprima di input**: Mostra lo snapshot degli ultimi dati in ingresso dall'origine degli eventi di input.  
     - Il tipo di serializzazione nei dati viene rilevato automaticamente (JSON/CSV). È anche possibile modificarlo manualmente in JSON/CSV/AVRO. 
     - È possibile visualizzare in anteprima i dati in entrata nel formato Tabella o Non elaborato. 
     - Se i dati visualizzati non sono aggiornati, selezionare **Aggiorna** per visualizzare gli eventi più recenti. 
     - Selezionare **Seleziona intervallo** di tempo per testare la query rispetto a un intervallo di tempo specifico di eventi in ingresso. 
     - Selezionare **Carica input di esempio** per testare la query caricando un file JSON/CSV di esempio. Per altre informazioni sul test di una query SAQL, vedere [Testare un processo](../stream-analytics/stream-analytics-test-query.md)di Analisi di flusso di Azure con dati di esempio. 

    ![query di test](media/sql-database-stream-analytics/test-query.png)


   - **Risultati del test**: Selezionare **Test query** per visualizzare i risultati della query di streaming 

    ![risultati test](media/sql-database-stream-analytics/test-results.png)

   - **Schema dei risultati**del test : Mostra lo schema dei risultati della query di streaming dopo il test. Assicurarsi che lo schema dei risultati del test corrisponda allo schema di output. 

    ![schema dei risultati dei test](media/sql-database-stream-analytics/test-results-schema.png)


   - **Schema di output**: contiene lo schema della tabella selezionata nel passaggio 5 (nuovo o esistente).
     - Crea nuovo: se è stata selezionata questa opzione nel passaggio 5, lo schema non verrà ancora visualizzato finché non si avvia il processo di streaming. Quando si crea una nuova tabella, selezionare l'indice di tabella appropriato. Per ulteriori informazioni sull'indicizzazione delle tabelle, vedere [Indici cluster e non cluster descritti](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/).
    - Usa esistente: se è stata selezionata questa opzione nel passaggio 5, verrà visualizzato lo schema della tabella selezionata. 
 
7. Dopo aver creato & testare la query, selezionare **Salva query**. Selezionare **Avvia processo di Analisi di flusso** per iniziare a inserire i dati trasformati nella tabella SQL. Una volta finalizzati i seguenti campi, **avviare** il processo. 
   - **Ora di inizio output**: Definisce il tempo del primo output del processo.  
     - Ora: il processo inizierà ora ed elaborerà nuovi dati in entrata.
     - Personalizzato: il processo verrà avviato ora, ma elaborerà i dati da un momento specifico (che può essere nel passato o nel futuro). Per altre informazioni, vedere [Come avviare un processo](../stream-analytics/start-job.md)di Analisi di flusso di Azure.For more information, see How to start an Azure Stream Analytics job .
   - **Unità di streaming:** Analisi di flusso di Azure ha un prezzo in base al numero di unità di streaming necessarie per elaborare i dati nel servizio. Per altre informazioni, vedere Prezzi di Analisi di flusso di Azure .For more information, see [Azure Stream Analytics pricing](https://azure.microsoft.com/pricing/details/stream-analytics/). 
   - **Gestione degli errori dei dati di output:**  
     - Riprova: quando si verifica un errore, Analisi flusso di Azure tenta di scrivere l'evento per un tempo indefinito fino al successo della scrittura. Non è impostato alcun timeout per i tentativi. Alla fine, l'elaborazione di tutti gli eventi successivi viene bloccata dall'evento in fase di esecuzione. Questa opzione è il criterio di gestione degli errori di output predefinito. 
     - Eliminazione: Analisi di flusso di Azure esorcherà qualsiasi evento di output che genera un errore di conversione dei dati. Gli eventi rilasciati non possono essere recuperati per essere rielaborati in seguito. Tutti gli errori temporanei (ad esempio, gli errori di rete) vengono ritentati indipendentemente dalla configurazione dei criteri di gestione degli errori di output. 
   - **Impostazioni di output del database SQL:** un'opzione per ereditare lo schema di partizionamento del passaggio di query precedente, per abilitare la topologia completamente parallela con più writer alla tabella. Per altre informazioni, consultare [Output di Analisi di flusso di Azure in Database SQL di Azure](../stream-analytics/stream-analytics-sql-output-perf.md).
   - **Numero massimo di batch**: il limite superiore consigliato per il numero di record inviati con ogni transazione di inserimento bulk.  
    Per altre informazioni sulla gestione degli errori di output, vedere Criteri di errore di output in Analisi di flusso di Azure.For more information about output error [handling,](../stream-analytics/stream-analytics-output-error-policy.md)see Output error policies in Azure Stream Analytics .  

    ![iniziare il lavoro](media/sql-database-stream-analytics/start-job.png)

8. Una volta avviato il processo, verrà visualizzato il processo in esecuzione nell'elenco ed è possibile eseguire le azioni seguenti: 
   - **Avvia/arresta il processo:** se il processo è in esecuzione, è possibile arrestarlo. Se il processo viene arrestato, è possibile avviarlo. 
   - **Modifica processo**: È possibile modificare la query. Se vuoi apportare più modifiche al processo, aggiungi altri input/output, quindi apri il processo in Analisi di flusso. L'opzione Modifica è disabilitata quando il processo è in esecuzione. 
   - **Anteprima tabella di output**: È possibile visualizzare in anteprima la tabella nell'editor di query SQL. 
   - **Apri in Analisi di flusso:** aprire il processo nel servizio Analisi di flusso per visualizzare i dettagli di monitoraggio e debug del processo. 


    ![processi di analisi dei flussi](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>Passaggi successivi

- [Documentazione di Analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/)
- [Modelli di soluzioni di Analisi di flusso di Azure](../stream-analytics/stream-analytics-solution-patterns.md)
