---
title: "Introduzione all’uso di Analisi di flusso di Azure: rilevamento di illeciti in tempo reale | Documentazione Microsoft"
description: Informazioni su come creare una soluzione per il rilevamento di illeciti in tempo reale con Analisi di flusso. Utilizzare un hub eventi per l&quot;elaborazione di eventi in tempo reale.
keywords: rilevamento anomalie, rilevamento delle frodi, rilevamento anomalie in tempo reale
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: d1ed7bd8-9296-4ff8-ade3-d2765541d377
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 14c708405ea73bb7bc81863bdcf51a056447a296
ms.lasthandoff: 11/17/2016


---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Introduzione all’uso di Analisi di flusso di Azure: rilevamento di illeciti in tempo reale
Informazioni su come creare una soluzione end-to-end per il rilevamento di illeciti in tempo reale con Analisi di flusso di Azure. Portare gli eventi nell’hub eventi di Azure, scrivere le query di Analisi di flusso per l'aggregazione o l’avviso e inviare i risultati a un sink di output per ottenere informazioni sui dati in tempo reale. Viene descritto il rilevamento anomalie delle telecomunicazioni in tempo reale, ma la tecnica di esempio è ideale anche per altri tipi di rilevamento delle frodi, ad esempio gli scenari di furto di identità o di carta di credito.

Analisi di flusso è un servizio completamente gestito che consente l'elaborazione di eventi complessi con bassa latenza, elevata disponibilità e scalabilità per lo streaming di dati nel cloud. Per altre informazioni, vedere l'articolo relativo all' [introduzione ad Analisi di flusso di Azure](stream-analytics-introduction.md).

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scenario: telecomunicazioni e rilevamento di illecito relativo alle SIM in tempo reale
Un'azienda di telecomunicazioni dispone di un volume di dati elevato relativamente alle chiamate in ingresso. Per quanto riguarda i propri dati, la società ha le necessità seguenti:

* Ridurre questi dati a una quantità gestibile e ottenere informazioni sull'utilizzo da parte dei clienti nel tempo e nelle diverse aree geografiche.
* Rilevare le frodi riguardanti le SIM (più chiamate provenienti dalla stessa identità intorno alla stessa ora, ma in luoghi geograficamente diversi) in tempo reale, in modo da poter rispondere facilmente con una notifica ai clienti o l'arresto del servizio.

In scenari di tipo "Internet delle cose" (IoT, Internet of Things) standard viene generata una considerevole quantità di dati di telemetria o sensore e i clienti desiderano effettuarne l'aggregazione o generare avvisi relativi alle anomalie in tempo reale.

## <a name="prerequisites"></a>Prerequisiti
* Scaricare [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) dall'Area download Microsoft 
* Facoltativo: codice sorgente del generatore di eventi da [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TelcoGenerator)

## <a name="create-an-azure-event-hubs-input-and-consumer-group"></a>Creare un input di hub eventi di Azure e un gruppo di consumer
L'applicazione di esempio genererà gli eventi e li invierà a un'istanza dell’hub eventi per l’elaborazione in tempo reale. Gli hub eventi del bus di servizio sono il metodo preferito di inserimento di eventi per Analisi di flusso. Per altre informazioni sugli hub eventi, vedere [Documentazione sul bus di servizio](/documentation/services/service-bus/).

Per creare un hub eventi:

1. Nel [portale di Azure](https://manage.windowsazure.com/) fare clic su **Nuovo** > **Servizi app** > **Bus di servizio** > **Hub eventi** > **Creazione rapida**. Fornire un nome, un’area e uno spazio dei nomi nuovo o esistente per creare un nuovo hub eventi.  
2. Come procedura consigliata, ogni processo di Analisi dei flussi deve essere letto da un singolo gruppo di consumer di hub eventi. Verrà illustrata la procedura di creazione di un gruppo di consumer e sarà possibile [apprendere ulteriori informazioni sui gruppi di consumer](https://msdn.microsoft.com/library/azure/dn836025.aspx). Per creare un gruppo di consumer, individuare l'hub eventi appena creato, scegliere la scheda **Gruppi consumer**, fare clic su **Crea** nella parte inferiore della pagina, quindi fornire un nome per il gruppo di consumer.
3. Per concedere l'accesso all'Hub di eventi, è necessario creare un criterio di accesso condiviso.  Scegliere la scheda **Configura** dell'hub eventi.
4. In **Criteri di accesso condivisi** creare un nuovo criterio con autorizzazioni di **gestione**.
   
   ![Criteri di accesso condivisi in cui è possibile creare un nuovo criterio con autorizzazioni di gestione.](./media/stream-analytics-get-started/stream-ananlytics-shared-access-policies.png)
5. Fare clic su **Save** nella parte inferiore della pagina.
6. Passare al **Dashboard**, fare clic su **Informazioni di connessione** nella parte inferiore della pagina, quindi salvare le informazioni di connessione.

## <a name="configure-and-start-event-generator-application"></a>Configurare e avviare l'applicazione di generazione di eventi
Microsoft ha fornito un'applicazione client per generare i metadati di esempio relativi alle chiamate in ingresso ed eseguirne il push nell’hub eventi. Attenersi alla procedura seguente per configurare questa applicazione.  

1. Scaricare il [file TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip). Decomprimerlo in una directory.
   
   **Nota**: Windows può bloccare il file con estensione zip scaricato. Fare clic con il pulsante destro del mouse sul file e selezionare Proprietà. Se viene visualizzato il messaggio "Il file proviene da un altro computer. Per facilitare la protezione del computer, potrebbe essere bloccato.", selezionare quindi la casella "Sblocca" e scegliere Applica sul file con estensione zip.
2. Sostituire i valori di Microsoft.ServiceBus.ConnectionString e di EventHubName in **telcodatagen.exe.config** con la stringa di connessione e il nome dell'hub eventi.
   
   **Nota**: la stringa di connessione copiata dal portale di Azure posiziona il nome della connessione alla fine. Rimuovere ";EntityPath=<value>" dal campo "add key=".
3. Avviare l’applicazione. L'utilizzo è il seguente:
   
   telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]

Nell'esempio seguente vengono generati 1000 eventi con una probabilità del 20% di frode nel corso di 2 ore.

    telcodatagen.exe 1000 .2 2

Verranno visualizzati i record inviati all'hub eventi. Di seguito sono definiti alcuni campi chiave che verranno utilizzati in questa applicazione di rilevamento di illecito in tempo reale:

| Record | Definizione |
| --- | --- |
| CallrecTime |Timestamp dell'ora di inizio della chiamata. |
| SwitchNum |Commutatore telefonico usato per la connessione della chiamata. |
| CallingNum |Numero di telefono del chiamante. |
| CallingIMSI |Codice IMSI (International Mobile Subscriber Identity).  Identificatore univoco del chiamante. |
| CalledNum |Numero di telefono del destinatario della chiamata. |
| CalledIMSI |Codice IMSI (International Mobile Subscriber Identity).  Identificatore univoco del destinatario della chiamata. |

## <a name="create-stream-analytics-job"></a>Creare un processo di Analisi di flusso
Ora che si dispone di un flusso di eventi di telecomunicazioni, è possibile impostare un processo di Analisi di flusso per analizzare questi eventi in tempo reale.

### <a name="provision-a-stream-analytics-job"></a>Eseguire il provisioning di un processo di Analisi dei flussi
1. Nel portale di Azure fare clic su **Nuovo > Servizi dati > Analisi di flusso > Creazione rapida**.
2. Specificare i valori seguenti, quindi fare clic su **Crea processo di Analisi dei flussi**:
   
   * **Nome processo**: immettere un nome per il processo.
   * **Area**: scegliere l'area in cui si desidera eseguire il processo. È consigliabile inserire il processo e l'hub eventi nella stessa area per garantire prestazioni migliori ed evitare addebiti connessi al trasferimento di dati tra aree diverse.
   * **Account di archiviazione**: scegliere l'account di archiviazione di Azure da usare per archiviare i dati di monitoraggio per tutti i processi di Analisi di flusso in esecuzione all'interno dell'area scelta. È possibile scegliere un account di archiviazione esistente o crearne uno nuovo.
3. Fare clic su **Analisi dei flussi** nel riquadro sinistro per visualizzare un elenco dei processi di Analisi dei flussi.
   
   ![Icona di servizio di analisi dei flussi](./media/stream-analytics-get-started/stream-analytics-service-icon.png)
4. Il nuovo processo verrà visualizzato nell'elenco con uno stato **Creato**. Si noti che il pulsante **Avvia** nella parte inferiore della pagina è disabilitato. Prima di poter avviare il processo, è necessario configurare l'input, l'output, la query per l'esecuzione.

### <a name="specify-job-input"></a>Specificare l'input del processo
1. Nel processo di Analisi di flusso fare clic su **Input** nella parte superiore della pagina, quindi scegliere **Aggiungi input**. La finestra di dialogo visualizzata presenterà una serie di passaggi per l'impostazione dell'input.
2. Selezionare **Flusso dati**, quindi fare clic sul pulsante a destra.
3. Selezionare **Hub eventi**, quindi fare clic sul pulsante a destra.
4. Digitare o selezionare i valori seguenti nella terza pagina:
   
   * **Alias di input**:immettere un nome descrittivo per il processo di input, ad esempio *CallStream*. Si noti che il nome verrà usato nella query in un secondo momento.
   * **Hub eventi**: se l'hub eventi creato si trova nella stessa sottoscrizione del processo di Analisi di flusso, selezionare lo spazio dei nomi in cui si trova l'hub eventi.
   
   Se l'hub eventi si trova in un'altra sottoscrizione, selezionare **Usa hub eventi da un'altra sottoscrizione**, quindi immettere manualmente i valori per **Spazio dei nomi del bus di servizio**, **Nome hub eventi**, **Nome criterio hub eventi**, **Chiave criterio hub eventi** e **Conteggio partizioni hub eventi**.
   
   * **Nome hub eventi**: selezionare il nome dell'hub eventi.
   * **Nome del criterio hub eventi**: selezionare il criterio dell'hub eventi creato precedentemente in questa esercitazione.
   * **Gruppo di consumer dell'hub eventi**: digitare il gruppo di consumer creato in precedenza in questa esercitazione.
5. Fare clic sul pulsante a destra.
6. Specificare i valori seguenti:
   
   * **Formato serializzatore eventi**: JSON
   * **Codifica**: UTF8
7. Fare clic sul pulsante con il segno di spunta per aggiungere l'origine e per verificare che Analisi dei flussi possa connettersi all'hub eventi.

### <a name="specify-job-query"></a>Specificare una query del processo
Analisi di flusso supporta un semplice modello di query dichiarative per descrivere le trasformazioni per l’elaborazione in tempo reale. Per altre informazioni sul linguaggio, vedere le [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/dn834998.aspx). Questa esercitazione consente di creare e testare diverse query sul flusso in tempo reale dei dati chiamata.

#### <a name="optional-sample-input-data"></a>Facoltativo: dati di input di esempio
Per convalidare la query in base al tipo di dati effettivo del processo, è possibile utilizzare la funzionalità **Dati di esempio** per estrarre gli eventi dal flusso e creare un file JSON di eventi per il test.  La procedura seguente illustra come eseguire questa operazione e viene anche fornito un file [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) di esempio a scopo di test.

1. Selezionare l'input dell'hub eventi e fare clic su **Dati di esempio** nella parte inferiore della pagina.
2. Nella finestra di dialogo visualizzata specificare un valore per **Ora di inizio** da cui iniziare a raccogliere dati e un valore per **Durata** per la quantità di dati aggiuntivi da utilizzare.
3. Fare clic sul segno di spunta per avviare i dati di campionamento dell'input.  Per la produzione del file sono richiesti uno o due minuti.  Dopo il completamento del processo, fare clic su **Dettagli** e scaricare e salvare il file JSON generato.
   
   ![Scaricare e salvare i dati elaborati in un file JSON](./media/stream-analytics-get-started/stream-analytics-download-save-json-file.png)

#### <a name="passthrough-query"></a>Query passthrough
Se si desidera archiviare ogni evento, è possibile utilizzare una query passthrough per leggere tutti i campi nel payload dell'evento o del messaggio. Per iniziare, eseguire una semplice query passthrough che proietti tutti i campi in un evento.

1. Fare clic su **Query** dalla parte superiore della pagina del processo di Analisi dei flussi.
2. Aggiungere il codice seguente all'editor del codice:
   
     SELECT * FROM CallStream
   
   > Assicurarsi che il nome dell'origine di input corrisponda al nome dell'input specificato precedentemente.
   > 
   > 
3. Fare clic su **Test** nell'editor di query.
4. Fornire un file di test, che può essere sia un file creato tramite la procedura precedente che [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json).
5. Fare clic sul segno di spunta e vedere i risultati visualizzati sotto la definizione della query.
   
   ![Risultati della definizione della query](./media/stream-analytics-get-started/stream-analytics-sim-fraud-output.png)

### <a name="column-projection"></a>Proiezione di colonna
Ora i campi restituiti verranno ridotti a un set più piccolo.

1. Modificare la query nell'editor di codice nel modo seguente:
   
     SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum   FROM CallStream
2. Fare clic su **Riesegui** nell'editor di query per visualizzare i risultati della query.
   
   ![Output in un editor di query.](./media/stream-analytics-get-started/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Numero di chiamate in ingresso per area: finestra a cascata con aggregazione
Per confrontare la quantità di chiamate in ingresso per area verrà utilizzata una [finestra a cascata](https://msdn.microsoft.com/library/azure/dn835055.aspx) , in modo da ottenere il numero di chiamate in ingresso raggruppate per SwitchNum ogni 5 secondi.

1. Modificare la query nell'editor di codice nel modo seguente:
   
     SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount   FROM CallStream TIMESTAMP BY CallRecTime   GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
   
   In questa query viene utilizzata la parola chiave **Timestamp By** per specificare un campo di timestamp nel payload da utilizzare nel calcolo temporale. Se il campo non è stato specificato, verrà eseguita l'operazione di windowing utilizzando l’orario in cui ogni evento è arrivato all’hub eventi. Vedere la sezione relativa a [tempo di arrivo e tempo di applicazione nel riferimento per il linguaggio di query di Analisi dei flussi](https://msdn.microsoft.com/library/azure/dn834998.aspx).
   
   Si noti che è possibile accedere a un timestamp per la fine di ogni finestra usando la proprietà **System.Timestamp** .
2. Fare clic su **Riesegui** nell'editor di query per visualizzare i risultati della query.
   
   ![Risultati della query per Timestamp by](./media/stream-analytics-get-started/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>Rilevamento di illeciti relativi alla SIM con self-join
Per identificare l'utilizzo potenzialmente illecito osserveremo le chiamate provenienti dallo stesso utente ma in località diverse in meno di 5 secondi.  Per cercare questi casi, verrà utilizzata un’operazione [join](https://msdn.microsoft.com/library/azure/dn835026.aspx) sul flusso di eventi di chiamata.

1. Modificare la query nell'editor di codice nel modo seguente:
   
     SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,   CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2   FROM CallStream CS1 TIMESTAMP BY CallRecTime   JOIN CallStream CS2 TIMESTAMP BY CallRecTime   ON CS1.CallingIMSI = CS2.CallingIMSI   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5   WHERE CS1.SwitchNum != CS2.SwitchNum
2. Fare clic su **Riesegui** nell'editor di query per visualizzare i risultati della query.
   
   ![Risultati della query di un join](./media/stream-analytics-get-started/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>Creare un sink di output
Ora che è stato definito un flusso di eventi, un input dell’hub eventi per acquisire gli eventi e una query per eseguire una trasformazione nel flusso, l'ultimo passaggio consiste nel definire un sink di output per il processo.  Gli eventi per il comportamento fraudolento verranno scritti nell'archivio BLOB.

Attenersi alla procedura seguente per creare un contenitore per l'archivio BLOB, se non è già disponibile:

1. Usare un account di archiviazione esistente o crearne uno nuovo facendo clic su **Nuovo > Servizi dati > Archiviazione > Creazione rapida** e seguendo le istruzioni.
2. Selezionare l'account di archiviazione, fare clic su **CONTENITORI** nella parte superiore della pagina e quindi fare clic su **AGGIUNGI**.
3. Specificare un **Nome** per il contenitore e impostare il relativo **Accesso** su BLOB pubblico.

## <a name="specify-job-output"></a>Specificare l'output del processo
1. Nel processo di Analisi dei flussi fare clic su **Output** nella parte superiore della pagina, quindi scegliere **Aggiungi output**. La finestra di dialogo visualizzata presenterà una serie di passaggi per l'impostazione dell'output.
2. Selezionare **ARCHIVIAZIONE BLOB**, quindi fare clic sul pulsante a destra.
3. Digitare o selezionare i valori seguenti nella terza pagina:
   
   * **ALIAS DI OUTPUT**: immettere un nome descrittivo per l'output del processo.
   * **SOTTOSCRIZIONE**: se l'archiviazione BLOB creata si trova nella stessa sottoscrizione del processo di analisi di flusso, selezionare **Usare l'account di archiviazione dalla sottoscrizione corrente**. Se il dispositivo di archiviazione si trova in una sottoscrizione diversa, selezionare l’opzione per **usare l’account di archiviazione usato da un'altra sottoscrizione** e immettere manualmente le informazioni relative all’**account di archiviazione**, alla **chiave dell'account di archiviazione** e al **contenitore**.
   * **ACCOUNT DI ARCHIVIAZIONE**: selezionare il nome dell’account di archiviazione.
   * **CONTENITORE**: selezionare il nome del contenitore.
   * **PREFISSO DEL NOME FILE**: digitare un prefisso di file da utilizzare nella scrittura dell’output del BLOB
4. Fare clic sul pulsante a destra.
5. Specificare i valori seguenti:
   
   * **FORMATO SERIALIZZATORE EVENTI**: JSON
   * **CODIFICA**: UTF8
6. Fare clic sul pulsante con il segno di spunta per aggiungere l'origine e per verificare che Analisi di flusso possa connettersi all’account di archiviazione.

## <a name="start-job-for-real-time-processing"></a>Avviare il processo per l'elaborazione in tempo reale
Poiché l'input, la query e l'output di un processo sono stati specificati, a questo punto è possibile avviare il processo di Analisi di flusso.

1. Dal **DASHBOARD** del processo fare clic su **AVVIA** nella parte inferiore della pagina.
2. Nella finestra di dialogo visualizzata selezionare **ORA DI INIZIO DEL PROCESSO** , quindi fare clic sul pulsante con il segno di spunta nella parte inferiore della finestra di dialogo. A breve lo stato del processo passerà da **Avvio** a **In esecuzione**.

## <a name="view-fraud-detection-output"></a>Visualizzare l'output di rilevamento di illeciti
Usare uno strumento come [Esplora archivi di Microsoft Azure](https://azurestorageexplorer.codeplex.com/) o [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) per visualizzare la scrittura degli eventi illeciti nell'output in tempo reale.  

![Rilevamento di illeciti: eventi illeciti visualizzati in tempo reale](./media/stream-analytics-get-started/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>Supporto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


