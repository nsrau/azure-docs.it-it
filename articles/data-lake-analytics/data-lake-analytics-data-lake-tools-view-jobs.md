---
title: Usare Job Browser e Job View (Visualizzazione processo) per i processi di Azure Data Lake Analytics | Microsoft Docs
description: 'Informazioni su come usare Job Browser e Job View (Visualizzazione processo) per i processi di Azure Data Lake Analytics. '
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/02/2017
ms.author: jgao
ms.openlocfilehash: 8f1729f84a4fde2a56427a41b356d6263818519e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics-jobs"></a>Usare Job Browser e Job View (Visualizzazione processo) per i processi di Azure Data Lake Analytics
Il servizio Azure Data Lake Analytics archivia i processi inviati in un [archivio query](#query-store). Questo articolo contiene informazioni su come usare Job Browser e Job View (Visualizzazione processo) in Azure Data Lake Tools per Visual Studio per trovare informazioni sulla cronologia di processi. 

Per impostazione predefinita, il servizio Data Lake Analytics archivia i processi per 30 giorni. È possibile configurare il periodo di scadenza nel portale di Azure configurando un criterio di scadenza personalizzato. Non sarà possibile accedere alle informazioni sui processi dopo la scadenza. 

## <a name="prerequisites"></a>Prerequisiti
Vedere [Prerequisiti di Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Aprire Job Browser
Accedere a Browser processi tramite **Esplora server > Azure > Data Lake Analytics > Processi** in Visual Studio.  Usando Browser processi è possibile accedere all'archivio query di un account Data Lake Analytics. In Browser processi a sinistra viene visualizzato Query Store, che mostra le informazioni di base sul processo, e a destra Vista processi, che mostra le informazioni dettagliate sul processo.

## <a name="job-view"></a>Job View (Visualizzazione processo)
Contiene informazioni dettagliate su un processo. Per aprire un processo, è possibile fare doppio clic su di esso in Job Browser o aprirlo dal menu Data Lake facendo clic su Job View (Visualizzazione processo). Verrà visualizzata una finestra di dialogo in cui è inserito l'URL del processo.

![Job Browser di Data Lake Tools per Visual Studio](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

La finestra di dialogo Job View (Visualizzazione processo) contiene:

* Riepilogo dei processi
  
    Aggiornare la visualizzazione del processo per mostrare le informazioni più recenti relative ai processi in esecuzione.
  
  * Stato del processo (grafico):
    
      Descrive le fasi del processo:
    
      ![Stato delle fasi del processo di Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Preparing (Preparazione): caricare lo script nel cloud, compilandolo e ottimizzandolo tramite il servizio di compilazione.
    * Queued (In coda): i processi vengono inseriti in coda quando sono in attesa di risorse sufficienti o quando viene superato il numero massimo di processi simultanei per il limite dell'account. L'impostazione di priorità determina la sequenza di processi in coda: più basso è il numero, maggiore è la priorità.
    * Running (In esecuzione): il processo è in esecuzione nell'account Data Lake Analytics.
    * Finalizing (Finalizzazione): il processo è in fase di completamento, ad esempio, nella fase di finalizzazione del file.
      
      Il processo può avere esito negativo in ogni fase. Ad esempio, possono verificarsi errori di compilazione nella fase di preparazione, errori di timeout nella fase di inserimento in coda, errori di esecuzione nella fase di esecuzione e così via.
  * Basic Information
    
      Le informazioni di base sui processi vengono visualizzate nella parte inferiore del pannello di riepilogo del processo.
    
      ![Stato delle fasi del processo di Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Job Result (Risultato processo): indica l'esito positivo o negativo. In ogni fase il processo potrebbe non riuscire.
    * Total Duration (Durata totale): il tempo reale tra l'ora di invio e l'ora di fine.
    * Total Compute Time (Tempo di calcolo totale): la somma del tempo di esecuzione di ogni vertice; questo valore può essere considerato come il tempo di esecuzione del processo in un solo vertice. Per altre informazioni sul vertice, fare riferimento all'opzione Total Vertices (Totale vertici).
    * Submit Time/Start Time/End Time: (Ora di invio/Ora di inizio/Ora di fine): ora in cui il servizio Data Lake Analytics riceve l'invio di un processo/avvia l'esecuzione del processo/termina il processo con esito positivo o negativo.
    * Compilation/Queued/Running (Compilazione/In coda/In esecuzione): il tempo reale trascorso durante la fase di preparazione/inserimento in coda/esecuzione.
    * Account: l'account Data Lake Analytics usato per l'esecuzione del processo.
    * Author (Autore): l'utente che ha inviato il processo; può essere l'account di una persona reale o un account di sistema.
    * Priority (Priorità): la priorità del processo. Più è basso il numero, maggiore sarà la priorità. Interessa solo la sequenza di processi in coda. L'impostazione di una priorità più elevata non ha la precedenza sui processi in esecuzione.
    * Parallelismo: il numero massimo richiesto di unità Azure Data Lake Analytics Unit (ADLAU) simultanee, note anche come vertici. Attualmente, un vertice è uguale a una VM con due memorie centrali virtuali e 6 GB di RAM, anche se questo valore potrebbe essere modificato negli aggiornamenti futuri di Data Lake Analytics.
    * Bytes Left (Byte restanti): byte da elaborare fino al completamento del processo.
    * Bytes read/written (Byte letti/scritti): byte che sono stati letti/scritti dopo l'avvio del processo.
    * Total vertices (Totale vertici): il processo è suddiviso in elementi di lavoro e ogni elemento è chiamato vertice. Questo valore descrive il numero di elementi di lavoro che costituiscono il processo. Un vertice può essere considerato come un'unità di processo di base, nota anche come Azure Data Lake Analytics Unit (ADLAU); i vertici possono essere eseguiti in parallelismo. 
    * Completed/Running/Failed (Completati/In esecuzione/Non riusciti): indica il numero di vertici completati, in esecuzione o non riusciti. I vertici possono avere esito negativo a causa di errori di sistema o del codice utente, ma il sistema tenta automaticamente di eseguire i vertici più volte. Se l'esito è negativo dopo questi tentativi, tutto il processo avrà esito negativo.
* Grafico del processo
  
    Uno script SQL U rappresenta la logica di trasformazione dei dati di input in dati di output. Lo script viene compilato e ottimizzato in un piano di esecuzione fisico in fase di preparazione. Il grafico del processo mostra il piano di esecuzione fisico.  Il diagramma seguente illustra il processo:
  
    ![Stato delle fasi del processo di Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Un processo è suddiviso in più elementi di lavoro. Ogni elemento di lavoro è chiamato vertice. I vertici vengono raggruppati come Supervertice (noto anche come fase) e visualizzati sotto forma di grafico del processo. Le etichette di colore verde nel grafico del processo indicano le fasi.
  
    Ogni vertice raggruppato in una fase esegue lo stesso tipo di lavoro con blocchi diversi degli stessi dati. Ad esempio, se si dispone di un file con 1 TB di dati e centinaia di vertici leggono i dati nel file, ogni vertice sta leggendo un blocco. Questi vertici vengono raggruppati nella stessa fase ed eseguono lo stesso lavoro su blocchi diversi dello stesso file di input.
  
  * <a name="state-information"></a>Informazioni sulla fase
    
      In una specifica fase, l'etichetta riporta alcuni numeri.
    
      ![Fase del grafico del processo di Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 Extract (Estrazione SV1): il nome di una fase, indicato da un numero e dal metodo dell'operazione.
    * 84 vertices (84 vertici): il conteggio totale di vertici in questa fase. La cifra indica il numero di elementi di lavoro in cui il processo è suddiviso in questa fase.
    * 12.90 s/vertex (12,90 s/vertice): il tempo medio di esecuzione di vertici per questa fase. Questo valore viene calcolato da SUM (tempo di esecuzione di ogni vertice)/(conteggio totale vertici). Se si assegnassero tutti i vertici eseguiti in parallelismo, l'intera fase verrebbe completata in 12,90 s. Se quindi tutto il lavoro in questa fase fosse eseguito in serie, il valore sarebbe numero vertici * tempo AVG.
    * 850,895 rows written (850,895 righe scritte): il conteggio totale delle righe scritte in questa fase.
    * R/W (L/S): la quantità di dati letti/scritti in questa fase, espressa in byte.
    * I colori usati nella fase indicano lo stato dei vertici.
      
      * Il verde indica che il vertice è riuscito.
      * L'arancione indica che sono stati eseguiti nuovi tentativi per il vertice. Il vertice non ha avuto esito positivo, ma il sistema ha eseguito automaticamente un nuovo tentativo riuscito e la fase è stata completata correttamente. Se i nuovi tentativi non riescono, il colore diventa rosso e l'intero processo risulta non riuscito.
      * Il rosso indica l'esito negativo, ovvero il sistema ha eseguito più tentativi non riusciti per un determinato vertice. Questo scenario causa l'esito negativo dell'intero processo.
      * Il blu indica che un determinato vertice è in esecuzione.
      * Il bianco indica che il vertice è in attesa. Il vertice può essere in attesa di pianificazione dopo che un'unità ADLAU è diventata disponibile o in attesa di input poiché i relativi dati di input potrebbero non essere pronti.
      
      Posizionare il cursore del mouse vicino a uno stato per ottenere altri dettagli sulla fase:
      
      ![Dettagli della fase del grafico del processo di Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Vertices (Vertici): descrive i dettagli relativi ai vertici, ad esempio il numero totale di vertici, il numero di vertici completati, non riusciti, ancora in esecuzione o in attesa e così via.
  * Data read cross/intra pod (Lettura dati tra pod/nel pod): i file e i dati vengono archiviati in più pod nel file system distribuito. Il valore qui descrive la quantità di dati letti nello stesso pod o tra più pod.
  * Total Compute Time (Tempo di calcolo totale): la somma del tempo di esecuzione di ogni vertice nella fase; questo valore può essere considerato come il tempo di esecuzione se tutto il lavoro nella fase fosse eseguito in un solo vertice.
  * Data and rows written/read (Dati e righe scritti/letti): la quantità di dati o righe letti/scritti o che devono essere letti.
  * Vertex read failures (Errori di lettura vertici): descrive il numero di vertici con esito negativo durante la lettura dei dati.
  * Vertex duplicate discards (Eliminazioni vertici duplicati): se un vertice viene eseguito troppo lentamente, il sistema può pianificare più vertici per eseguire lo stesso elemento di lavoro. I vertici duplicati verranno rimossi dopo il completamento corretto di uno dei vertici. Registra il numero di vertici eliminati come duplicati nella fase.
  * Vertex revocations (Revoche vertici): il vertice è stato completato, ma verrà eseguito di nuovo in un secondo momento per qualche motivo. Ad esempio, se il vertice di downstream perde i dati di input intermedi, verrà chiesto di eseguire di nuovo il vertice di upstream.
  * Vertex schedule executions (Esecuzioni pianificazione vertici): il tempo totale per cui i vertici sono stati pianificati.
  * Min/Average/Max Vertex data read (Dati vertici min/medi/max letti): la quantità minima/media/massima di dati letti di ogni vertice.
  * Duration (Durata): il tempo reale di una fase; è necessario caricare il profilo per visualizzare questo valore.
  * Riproduzione del processo
    
      Data Lake Analytics esegue i processi e archivia le informazioni di esecuzione sui vertici dei processi, ad esempio quando i vertici sono stati avviati o arrestati, hanno avuto esito negativo, i nuovi tentativi eseguiti e così via. Tutte le informazioni vengono registrate automaticamente nell'archivio query e archiviate nel relativo profilo del processo. È possibile scaricare il profilo del processo tramite Carica profilo nella visualizzazione processo; è possibile esaminare la riproduzione del processo dopo avere scaricato il profilo del processo.
    
      Il processo di riproduzione è una visualizzazione di sintesi delle operazioni eseguite nel cluster. Consente di controllare lo stato di avanzamento del processo e rilevare visivamente eventuali colli di bottiglia o anomalie delle prestazioni in un periodo molto breve, in genere inferiore a 30 secondi.
  * Visualizzazione della mappa termica del processo 
    
      È possibile selezionare la mappa termica del processo tramite l'elenco a discesa di visualizzazione nel grafico del processo. 
    
      ![Visualizzazione della mappa termica del grafico del processo di Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Mostra la mappa termica di I/O, del tempo e della velocità effettiva di un processo, consentendo di individuare il punto in cui il processo impiega la maggior parte del tempo o se si tratta di un processo associato a operazioni di I/O e così via.
    
      ![Esempio di mappa termica del grafico del processo di Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Progress (Stato): lo stato di esecuzione del processo: vedere i dettagli in [informazioni sulla fase](#stage-information).
    * Data read/written (Dati letti/scritti): la mappa termica dei dati totali letti/scritti in ogni fase.
    * Compute time (Tempo di calcolo): la mappa termica di SUM (tempo di esecuzione di ogni vertice); può essere considerato come il tempo necessario se tutte le operazioni nella fase fossero eseguite solo con 1 vertice.
    * Average execution time per node (Tempo medio di esecuzione per ogni nodo): la mappa termica di SUM (tempo di esecuzione di ogni vertice)/(numero di vertici). Se quindi si assegnassero tutti i vertici eseguiti in parallelismo, l'intera fase verrebbe completata in questo intervallo di tempo.
    * Input/Output throughput (Velocità effettiva di input/output): la mappa termica della velocità effettiva di input/output di ogni fase; consente di verificare se il processo è associato a operazioni di I/O.
* Operazioni sui metadati
  
    È possibile eseguire alcune operazioni sui metadati nello script U-SQL, ad esempio creare un database, eliminare una tabella e così via. Queste operazioni vengono visualizzate in Metadata Operation (Operazione sui metadati) dopo la compilazione. Qui è possibile trovare asserzioni, creare e cancellare entità.
  
    ![Operazioni sui metadati di visualizzazione processo di Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Cronologia dello stato
  
    Nel riepilogo del processo viene visualizzata la cronologia dello stato, ma qui sono disponibili maggiori dettagli. È possibile trovare informazioni dettagliate, ad esempio quando il processo è stato preparato, inserito in coda, avviato in esecuzione e terminato. È anche possibile trovare il numero di volte per cui il processo è stato compilato (CcsAttempts: 1), quando il processo è stato inviato effettivamente al cluster (Detail: Dispatching job to cluster - Dettaglio: Invio del processo al cluster) e così via.
  
    ![Cronologia dello stato di visualizzazione processo di Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnostica
  
    Lo strumento diagnostica automaticamente l'esecuzione del processo e invierà avvisi in caso di errori o problemi di prestazioni nei processi. Si noti che è necessario scaricare il profilo per ottenere informazioni complete qui. 
  
    ![Diagnostica di visualizzazione processo di Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Warnings (Avvisi): viene visualizzato un avviso del compilatore. È possibile fare clic sul collegamento relativo a "x problemi" per maggiori dettagli dopo la visualizzazione dell'avviso.
  * Vertex run too long (Esecuzione prolungata vertice): se si verifica il timeout di uno dei vertici, ad esempio dopo 5 ore, i problemi vengono indicati qui.
  * Resource usage (Uso risorse): se il parallelismo assegnato è maggiore o minore rispetto al valore necessario, i problemi vengono indicati qui. È anche possibile fare clic su questa opzione per visualizzare altri dettagli e scenari di simulazione per migliorare l'allocazione delle risorse; per altre informazioni, vedere queste indicazioni.
  * Memory check (Controllo memoria): se uno dei vertici usa più di 5 GB di memoria, i problemi vengono visualizzati qui. Il sistema potrebbe arrestare l'esecuzione del processo se viene usata una quantità di memoria superiore al limite di sistema.

## <a name="job-detail"></a>Dettagli processo
Mostra informazioni dettagliate sul processo, inclusi gli script, le risorse e la visualizzazione esecuzione vertici.

![Azure Data Lake Analytics di Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Script
  
    Lo script SQL U del processo viene archiviato nell'archivio query. È possibile visualizzare lo script U-SQL originale ed inviarlo di nuovo, se necessario.
* Risorse
  
    Consente di trovare gli output di compilazione del processo archiviati nell'archivio query. Ad esempio, è possibile trovare "algebra.xml" che viene usato per mostrare il grafico del processo, l'assembly registrato e così via.
* Vertex execution view (Visualizzazione esecuzioni vertici)
  
    Mostra i dettagli di esecuzione dei vertici. Il profilo del processo archivia il log di esecuzione di ogni vertice, ad esempio i dati totali letti/scritti, il runtime, lo stato e così via. Tramite questa visualizzazione è possibile ottenere altre informazioni su come è stato eseguito un processo. Per altre informazioni vedere [Usare la visualizzazione esecuzioni di vertici in Azure Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Passaggi successivi
* Per registrare informazioni di diagnostica, vedere [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Per visualizzare una query più complessa, vedere [Analizzare i log del sito Web mediante Analisi Data Lake di Azure](data-lake-analytics-analyze-weblogs.md).
* Per usare la visualizzazione esecuzioni vertici, vedere [Usare la visualizzazione esecuzioni vertici in Azure Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

