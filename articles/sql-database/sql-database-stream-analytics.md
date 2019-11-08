---
title: Trasmettere dati tramite l'integrazione di analisi di flusso (anteprima)
description: Usare analisi di flusso di Azure per trasmettere i dati in un database SQL di Azure.
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820833"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>Trasmettere dati tramite l'integrazione di analisi di flusso del database SQL di Azure (anteprima)

Ora gli utenti possono inserire, elaborare, visualizzare e analizzare i dati in streaming in tempo reale in una tabella direttamente da un database SQL nel portale di Azure usando [analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md). Questa esperienza consente un'ampia gamma di scenari, ad esempio auto connesse, monitoraggio remoto, rilevamento delle frodi e molto altro ancora. Nel portale di Azure è possibile selezionare un'origine eventi (hub eventi/Hub eventi), visualizzare gli eventi in tempo reale in ingresso e selezionare una tabella per archiviare gli eventi. È anche possibile scrivere query sul linguaggio di query di analisi di flusso nel portale per trasformare gli eventi in ingresso e archiviarli nella tabella selezionata. Questo nuovo punto di ingresso è in aggiunta alle esperienze di creazione e configurazione già esistenti in analisi di flusso. Questa esperienza inizia dal contesto del database, consentendo di configurare rapidamente un processo di analisi di flusso e di spostarsi agevolmente tra il database SQL di Azure e le esperienze di analisi di flusso.

![Flusso di analisi di flusso](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>Vantaggi principali

- Cambio di contesto minimo: è possibile iniziare da un database SQL nel portale e iniziare a inserire dati in tempo reale in una tabella senza passare a un altro servizio. 
- Numero ridotto di passaggi: il contesto del database e della tabella viene usato per pre-configurare un processo di analisi di flusso.
- Maggiore facilità di utilizzo con i dati di anteprima: visualizzare l'anteprima dei dati in arrivo dall'origine eventi (hub eventi/hub degli eventi) nel contesto della tabella selezionata 


## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo articolo, sono necessarie le risorse seguenti:

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/). 
- Un database SQL. Per informazioni dettagliate, vedere [creare un database singolo nel database SQL di Azure](sql-database-single-database-get-started.md).
- Una regola del firewall che consente al computer di connettersi al server SQL di Azure. Per informazioni dettagliate, vedere [creare una regola del firewall a livello di server](sql-database-server-level-firewall-rule.md).


## <a name="configure-stream-analytics-integration"></a>Configurare l'integrazione di analisi di flusso

1. Accedere al portale di Azure. 
2. Passare al database SQL in cui si desidera inserire i dati di streaming. Selezionare **analisi di flusso (anteprima)** . 

    ![Analisi di flusso](media/sql-database-stream-analytics/stream-analytics.png)

3. Per iniziare a inserire i dati in streaming in questo database SQL, selezionare **Crea** e assegnare un nome al processo di streaming, quindi selezionare **Avanti: input**. 

    ![Crea processo di analisi di flusso](media/sql-database-stream-analytics/create-job.png)

4. Immettere i dettagli dell'origine eventi e quindi selezionare **Avanti: output**.

   - **Tipo di input**: Hub eventi/hub tutto
   - **Alias di input**: immettere un nome per identificare l'origine eventi 
   - **Sottoscrizione**: uguale alla sottoscrizione del database SQL 
   - **Spazio dei nomi dell'hub eventi**: nome dello spazio dei nomi 
   - **Nome dell'hub eventi**: nome dell'hub eventi nello spazio dei nomi selezionato 
   - **Nome criterio Hub eventi** (impostazione predefinita: Crea nuovo): assegnare un nome ai criteri 
   - **Gruppo di consumer dell'hub eventi** (predefinito per crearne uno nuovo): assegnare un nome al gruppo di consumer  
     - Si consiglia di creare un gruppo di consumer e un criterio per ogni nuovo processo di analisi di flusso di Azure creato da qui. I gruppi di consumer consentono solo cinque lettori simultanei, pertanto la fornitura di un gruppo di consumer dedicato per ogni processo eviterà eventuali errori che potrebbero derivare dal superamento di tale limite. Un criterio dedicato consente di ruotare la chiave o revocare le autorizzazioni senza influisca sulle altre risorse.

    ![Crea processo di analisi di flusso](media/sql-database-stream-analytics/create-job-output.png)

5. Consente di selezionare la tabella in cui si desidera inserire i dati in streaming. Al termine, selezionare **Crea**.
   - **Nome utente**, **password**: immettere le credenziali per l'autenticazione di SQL Server. Selezionare **Convalida**.
   - **Tabella**: selezionare **Crea nuovo** o **Usa esistente**. In questo flusso selezionare **Crea**. Quando si avvia il processo di analisi di flusso, verrà creata una nuova tabella.

    ![Crea processo di analisi di flusso](media/sql-database-stream-analytics/create.png)

6. Viene visualizzata una pagina di query con i dettagli seguenti:

   - **Input** (origine eventi di input) dal quale si inseriranno i dati  
   - **Output** (tabella di output) in cui vengono archiviati i dati trasformati 
   - Esempio di [query SAQL](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) con l'istruzione SELECT. 
   - **Input Preview**: Mostra lo snapshot dei dati in arrivo più recenti dall'origine degli eventi di input.  
     - Il tipo di serializzazione nei dati viene rilevato automaticamente (JSON/CSV). È possibile modificarla manualmente anche in JSON/CSV/AVRO. 
     - È possibile visualizzare in anteprima i dati in ingresso nel formato tabella o in formato non elaborato. 
     - Se i dati visualizzati non sono aggiornati, selezionare **Aggiorna** per visualizzare gli eventi più recenti. 
     - Selezionare **Seleziona intervallo di tempo** per testare la query in base a un intervallo di tempo specifico per gli eventi in ingresso. 
     - Selezionare **carica input di esempio** per testare la query caricando un file JSON/CSV di esempio. Per altre informazioni sul test di una query SAQL, vedere [testare un processo di analisi di flusso di Azure con dati di esempio](../stream-analytics/stream-analytics-test-query.md). 

    ![query di test](media/sql-database-stream-analytics/test-query.png)


   - **Risultati del test**: selezionare **query di test** ed è possibile visualizzare i risultati della query di streaming 

    ![risultati dei test](media/sql-database-stream-analytics/test-results.png)

   - **Schema risultati test**: Mostra lo schema dei risultati della query di streaming dopo il test. Verificare che lo schema dei risultati del test corrisponda allo schema di output. 

    ![schema dei risultati del test](media/sql-database-stream-analytics/test-results-schema.png)


   - **Schema di output**: contiene lo schema della tabella selezionata nel passaggio 5 (nuovo o esistente).
     - Crea nuovo: se è stata selezionata questa opzione nel passaggio 5, lo schema non sarà ancora visibile fino a quando non si avvia il processo di streaming. Quando si crea una nuova tabella, selezionare l'indice della tabella appropriato. Per ulteriori informazioni sull'indicizzazione di tabelle, vedere [Descrizione di indici cluster e non cluster](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/).
    - Usa esistente: se è stata selezionata questa opzione nel passaggio 5, verrà visualizzato lo schema della tabella selezionata. 
 
7. Al termine della creazione & test della query, selezionare **Salva query**. Selezionare **Avvia processo di analisi di flusso** per iniziare a inserire i dati trasformati nella tabella SQL. Dopo aver completato i campi seguenti, **avviare** il processo. 
   - **Ora di inizio dell'output**: definisce l'ora del primo output del processo.  
     - Ora: il processo verrà avviato ora ed elaborerà i nuovi dati in arrivo.
     - Personalizzata: il processo verrà avviato adesso, ma elaborerà i dati da un momento specifico, che può essere in passato o in futuro. Per altre informazioni, vedere [come avviare un processo di analisi di flusso di Azure](../stream-analytics/start-job.md).
   - **Unità di streaming**: i prezzi di analisi di flusso di Azure vengono calcolati in base al numero di unità di streaming necessarie per elaborare i dati nel servizio. Per altre informazioni, vedere [prezzi di analisi di flusso di Azure](https://azure.microsoft.com/pricing/details/stream-analytics/). 
   - **Gestione degli errori dei dati di output**:  
     - Nuovo tentativo: quando si verifica un errore, analisi di flusso di Azure tenta di scrivere l'evento all'infinito fino a quando la scrittura ha esito positivo. Non è impostato alcun timeout per i tentativi. Alla fine, l'elaborazione di tutti gli eventi successivi viene bloccata dall'evento in fase di esecuzione. Questa opzione è il criterio di gestione degli errori di output predefinito. 
     - Drop: analisi di flusso di Azure eliminerà qualsiasi evento di output che genera un errore di conversione dei dati. Gli eventi rilasciati non possono essere recuperati per essere rielaborati in seguito. Tutti gli errori temporanei (ad esempio, gli errori di rete) vengono ritentati indipendentemente dalla configurazione dei criteri di gestione degli errori di output. 
   - **Impostazioni di output del database SQL**: opzione per l'ereditarietà dello schema di partizionamento del passaggio precedente della query, per consentire la topologia completamente parallela con più writer alla tabella. Per altre informazioni, consultare [Output di Analisi di flusso di Azure in Database SQL di Azure](../stream-analytics/stream-analytics-sql-output-perf.md).
   - Numero **massimo batch**: il limite superiore consigliato per il numero di record inviati con ogni transazione di inserimento bulk.  
    Per altre informazioni sulla gestione degli errori di output, vedere [output Error Policies in Azure Stream Analytics](../stream-analytics/stream-analytics-output-error-policy.md).  

    ![Avvia processo](media/sql-database-stream-analytics/start-job.png)

8. Una volta avviato il processo, il processo in esecuzione verrà visualizzato nell'elenco ed è possibile eseguire le azioni seguenti: 
   - **Avviare/arrestare il processo**: se il processo è in esecuzione, è possibile arrestare il processo. Se il processo viene arrestato, è possibile avviare il processo. 
   - **Modifica processo**: è possibile modificare la query. Se si vuole eseguire altre modifiche al processo, aggiungere altri input/output, quindi aprire il processo in analisi di flusso. L'opzione di modifica è disabilitata quando il processo è in esecuzione. 
   - **Anteprima tabella di output**: è possibile visualizzare l'anteprima della tabella nell'editor di query SQL. 
   - **Apri in analisi di flusso**: aprire il processo in servizio di analisi di flusso per visualizzare il monitoraggio e i dettagli di debug del processo. 


    ![processi di analisi di flusso](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>Passaggi successivi

- [Documentazione di Analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/)
- [Modelli di soluzioni di analisi di flusso di Azure](../stream-analytics/stream-analytics-solution-patterns.md)
