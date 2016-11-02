<properties
    pageTitle="Analisi di flusso: rilevamento di illeciti in tempo reale | Microsoft Azure"
    description="Informazioni su come creare una soluzione per il rilevamento di illeciti in tempo reale con Analisi di flusso. Utilizzare un hub eventi per l'elaborazione di eventi in tempo reale."
    keywords="rilevamento anomalie, rilevamento delle frodi, rilevamento anomalie in tempo reale"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok" />




# <a name="get-started-using-azure-stream-analytics:-real-time-fraud-detection"></a>Introduzione all’uso di Analisi di flusso di Azure: rilevamento di illeciti in tempo reale

Informazioni su come creare una soluzione end-to-end per il rilevamento di illeciti in tempo reale con Analisi di flusso di Azure. Portare gli eventi negli hub eventi di Azure, scrivere le query di Analisi di flusso per l'aggregazione o l'avviso e inviare i risultati a un sink di output per ottenere informazioni sui dati in tempo reale. Viene spiegato il rilevamento anomalie delle telecomunicazioni in tempo reale, ma la tecnica di esempio è ideale anche per altri tipi di rilevamento delle frodi, ad esempio gli scenari di furto di identità o di carta di credito.

Analisi di flusso è un servizio completamente gestito che consente l'elaborazione di eventi complessi con bassa latenza, elevata disponibilità e scalabilità per lo streaming di dati nel cloud. Per altre informazioni, vedere l'articolo relativo all' [introduzione ad Analisi di flusso di Azure](stream-analytics-introduction.md).


## <a name="scenario:-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scenario: telecomunicazioni e rilevamento di illecito relativo alle SIM in tempo reale

Un'azienda di telecomunicazioni dispone di un volume di dati elevato relativamente alle chiamate in ingresso. Per quanto riguarda i propri dati, la società ha le necessità seguenti:

* Ridurre i dati a una quantità gestibile e ottenere informazioni sull'utilizzo da parte dei clienti nel tempo e in diverse aree geografiche.
* Rilevare le frodi riguardanti le SIM (più chiamate dalla stessa identità intorno alla stessa ora, ma in luoghi geograficamente diversi) in tempo reale, in modo tale che la società possa rispondere facilmente con una notifica ai clienti o l'arresto del servizio.

Gli scenari Internet of Things (IoT) canonici hanno una considerevole quantità di dati di telemetria o dati dei sensori. I clienti vogliono aggregare i dati o ricevere avvisi sulle anomalie in tempo reale.

## <a name="prerequisites"></a>Prerequisiti

- Scaricare [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) dall'Area download Microsoft
- Facoltativo: codice sorgente del generatore di eventi da [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator)

## <a name="create-azure-event-hubs-input-and-consumer-group"></a>Creare un input di hub eventi di Azure e un gruppo di consumer

L'applicazione di esempio genererà gli eventi e li invierà a un'istanza degli hub eventi per l'elaborazione in tempo reale. Gli hub eventi del bus di servizio sono il metodo preferito per l’inserimento di eventi per l'analisi dei flussi. Altre informazioni sugli hub eventi sono disponibili nella [documentazione del bus di servizio di Azure](/documentation/services/service-bus/).

Per creare un hub eventi:

1.  Nel [portale di Azure](https://manage.windowsazure.com/) fare clic su **NUOVO** > **SERVIZI APP** > **BUS DI SERVIZIO** > **HUB EVENTI** > **CREAZIONE RAPIDA**. Indicare un nome, un'area e uno spazio dei nomi nuovo o esistente per creare un nuovo hub eventi.  
2.  Come procedura consigliata, ogni processo di Analisi dei flussi deve essere letto da un singolo gruppo di consumer di hub eventi. Il processo di creazione di un gruppo di consumer verrà illustrato in un secondo momento. [Altre informazioni sui gruppi di consumer](https://msdn.microsoft.com/library/azure/dn836025.aspx). Per creare un gruppo di consumer, passare all'hub eventi appena creato, scegliere la scheda **GRUPPI DI CONSUMER**, fare clic su **CREA** nella parte inferiore della pagina, quindi fornire un nome per il gruppo di consumer.
3.  Per concedere l'accesso all'hub eventi, è necessario creare un criterio di accesso condiviso. Fare clic sulla scheda **CONFIGURA** dell'hub eventi.
4.  In **CRITERI DI ACCESSO CONDIVISI** creare un nuovo criterio con autorizzazioni di **gestione**.

    ![Criteri di accesso condivisi in cui è possibile creare un nuovo criterio con autorizzazioni di gestione.](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-shared-access-policies.png)

5.  Fare clic su **SAVE** nella parte inferiore della pagina.
6.  Passare al **Dashboard**, fare clic su **INFORMAZIONI DI CONNESSIONE** nella parte inferiore della pagina e quindi copiare e salvare le informazioni di connessione.

## <a name="configure-and-start-the-event-generator-application"></a>Configurare e avviare l'applicazione di generazione di eventi

È disponibile un'applicazione client per generare i metadati di esempio relativi alle chiamate in ingresso ed eseguirne il push nell'hub eventi. Usare la procedura seguente per configurare questa applicazione.  

1.  Scaricare il [file TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) e decomprimerlo in una directory.

    > [AZURE.NOTE] Windows può bloccare il file con estensione zip scaricato. Fare clic con il pulsante destro del mouse sul file e selezionare **Proprietà**. Se viene visualizzato il messaggio "Il file proviene da un altro computer. Per facilitare la protezione del computer, potrebbe essere bloccato", selezionare la casella **Sblocca** e quindi scegliere Applica sul file con estensione zip.

2.  Sostituire i valori di Microsoft.ServiceBus.ConnectionString ed EventHubName in telcodatagen.exe.config con la stringa di connessione e il nome dell'hub eventi.

    Nella stringa di connessione copiata dal portale di Azure il nome della connessione è posizionato alla fine. Rimuovere ";EntityPath=<value>" dal campo "add key=".

3.  Avviare l’applicazione. L'utilizzo è il seguente:

    telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]

Nell'esempio seguente vengono generati 1.000 eventi con una probabilità del 20% di illecito nel corso di due ore.

    telcodatagen.exe 1000 .2 2

Verranno visualizzati i record inviati all'hub eventi. Di seguito sono definiti alcuni campi chiave che verranno utilizzati in questa applicazione di rilevamento di illecito in tempo reale:

| Record | Definizione |
| ------------- | ------------- |
| CallrecTime | Timestamp dell'ora di inizio della chiamata. |
| SwitchNum | Commutatore telefonico usato per la connessione della chiamata. |
| CallingNum | Numero di telefono del chiamante. |
| CallingIMSI | Codice IMSI (International Mobile Subscriber Identity).  Identificatore univoco del chiamante. |
| CalledNum | Numero di telefono del destinatario della chiamata. |
| CalledIMSI | Codice IMSI (International Mobile Subscriber Identity).  Identificatore univoco del destinatario della chiamata. |


## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.
Ora che si dispone di un flusso di eventi di telecomunicazioni, è possibile impostare un processo di Analisi dei flussi per analizzare questi eventi in tempo reale.

### <a name="provision-a-stream-analytics-job"></a>Eseguire il provisioning di un processo di Analisi dei flussi

1.  Nel portale di Azure fare clic su **NUOVO** > **SERVIZI DATI** > **ANALISI DI FLUSSO** > **CREAZIONE RAPIDA**.
2.  Specificare i valori seguenti, quindi fare clic su **CREA PROCESSO DI ANALISI DEI FLUSSI**:

    * **NOME PROCESSO**: immettere un nome del processo.

    * **AREA**: scegliere l'area in cui si desidera eseguire il processo. È consigliabile inserire il processo e l'hub eventi nella stessa area per garantire prestazioni migliori ed evitare addebiti connessi al trasferimento di dati tra aree diverse.

    * **ACCOUNT DI ARCHIVIAZIONE**: scegliere l'account di archiviazione di Azure da usare per archiviare i dati di monitoraggio per tutti i processi di Analisi di flusso in esecuzione all'interno dell'area. È possibile scegliere un account di archiviazione esistente o crearne uno nuovo.

3.  Fare clic su **ANALISI DEI FLUSSI** nel riquadro sinistro per visualizzare un elenco dei processi di Analisi dei flussi.

    ![Icona di servizio di analisi dei flussi](./media/stream-analytics-real-time-fraud-detection/stream-analytics-service-icon.png)

    Il nuovo processo verrà visualizzato nell'elenco con uno stato **CREATO**. Si noti che il pulsante **AVVIA** nella parte inferiore della pagina è disabilitato. Prima di poter avviare il processo, è necessario configurare l'input, l'output, la query per l'esecuzione.

### <a name="specify-job-input"></a>Specificare l'input del processo
1.  Nel processo di Analisi di flusso fare clic su **INPUT** nella parte superiore della pagina, quindi scegliere **AGGIUNGI INPUT**. La finestra di dialogo visualizzata presenterà alcuni passaggi per l'impostazione dell'input.
2.  Fare clic su **FLUSSO DATI** e quindi sul pulsante a destra.
3.  Fare clic su **HUB EVENTI** e quindi sul pulsante a destra.
4.  Digitare o selezionare i valori seguenti nella terza pagina:

    * **ALIAS DI INPUT**: immettere un nome descrittivo per il processo, ad esempio *CallStream*. Si noti che il nome verrà usato nella query in un secondo momento.

    * **HUB EVENTI**: se l'hub eventi creato si trova nella stessa sottoscrizione del processo di Analisi di flusso, selezionare lo spazio dei nomi in cui si trova l'hub eventi.

        Se l'hub eventi si trova in un'altra sottoscrizione, selezionare **Usa hub eventi da un'altra sottoscrizione**, quindi immettere manualmente i valori per **SPAZIO DEI NOMI DEL BUS DI SERVIZIO**, **NOME HUB EVENTI**, **NOME CRITERIO HUB EVENTI**, **CHIAVE CRITERIO HUB EVENTI** e **CONTEGGIO PARTIZIONI HUB EVENTI**.

    * **NOME HUB EVENTI**: selezionare il nome dell'hub eventi.

    * **NOME CRITERIO HUB EVENTI**: selezionare il criterio dell'hub eventi creato precedentemente in questa esercitazione.

    * **GRUPPO DI CONSUMER DELL'HUB EVENTI**: digitare il nome del gruppo di consumer creato in precedenza in questa esercitazione.

5.  Fare clic sul pulsante a destra.
6.  Specificare i valori seguenti:

    * **FORMATO SERIALIZZATORE EVENTI**: JSON
    * **CODIFICA**: UTF8
7.  Fare clic sul pulsante con il **segno di spunta** per aggiungere l'origine e per verificare che Analisi dei flussi sia in grado di connettersi all'hub eventi.

### <a name="specify-job-query"></a>Specificare una query del processo

Analisi di flusso supporta un semplice modello di query dichiarative che descrive le trasformazioni per l'elaborazione in tempo reale. Per altre informazioni sul linguaggio, vedere le [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/dn834998.aspx). Questa esercitazione consente di creare e testare diverse query sul flusso in tempo reale dei dati chiamata.

#### <a name="optional:-sample-input-data"></a>Facoltativo: dati di input di esempio
Per convalidare la query in base al tipo di dati effettivo del processo, è possibile usare la funzionalità **DATI DI ESEMPIO** per estrarre gli eventi dal flusso di e creare un file JSON degli eventi per il test.  I passaggi che seguono spiegano come eseguire questa operazione. È anche disponibile un esempio di file [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) a scopo di test.

1.  Selezionare l'input dell'hub eventi e fare clic su **DATI DI ESEMPIO** nella parte inferiore della pagina.
2.  Nella finestra di dialogo visualizzata specificare un valore per **ORA DI INIZIO** per iniziare a raccogliere i dati e un valore per **DURATA** per indicare quanti dati aggiuntivi verranno usati.
3.  Fare clic sul pulsante con il **segno di spunta** per avviare il campionamento dei dati dell'input.  Per la produzione del file sono richiesti uno o due minuti.  Al termine del processo, fare clic su **DETTAGLI**, scaricare il file JSON generato e salvarlo.

    ![Scaricare e salvare i dati elaborati in un file JSON](./media/stream-analytics-real-time-fraud-detection/stream-analytics-download-save-json-file.png)

#### <a name="pass-through-query"></a>Query pass-through

Per archiviare ogni evento, è possibile usare una query pass-through per leggere tutti i campi nel payload dell'evento o del messaggio. Per iniziare, eseguire una semplice query pass-through che proietti tutti i campi in un evento.

1.  Fare clic su **QUERY** dalla parte superiore della pagina del processo di analisi dei flussi.
2.  Aggiungere il codice seguente all'editor del codice:

        SELECT * FROM CallStream

    > [AZURE.IMPORTANT] Verificare che il nome dell'origine di input corrisponda al nome dell'input specificato in precedenza.

3.  Fare clic su **Test** nell'editor di query.
4.  Aggiungere un file di test. Usare un file creato nei passaggi precedenti o il file [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/SampleDataFiles/Telco.json).
5.  Fare clic sul pulsante con il **segno di spunta** e vedere i risultati visualizzati sotto la definizione della query.

    ![Risultati della definizione della query](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sim-fraud-output.png)


### <a name="column-projection"></a>Proiezione di colonna

Ora i campi restituiti verranno ridotti a un set più piccolo.

1.  Modificare la query nell'editor di codice nel modo seguente:

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
        FROM CallStream

2.  Fare clic su **Riesegui** nell'editor di query per visualizzare i risultati della query.

    ![Output in un editor di query.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region:-tumbling-window-with-aggregation"></a>Numero di chiamate in ingresso per area: finestra a cascata con aggregazione

Per confrontare il numero di chiamate in ingresso per area verrà usata una [finestra a cascata](https://msdn.microsoft.com/library/azure/dn835055.aspx), in modo da ottenere il conteggio delle chiamate in ingresso raggruppate per **SwitchNum** ogni cinque secondi.

1.  Modificare la query nell'editor di codice nel modo seguente:

        SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
        FROM CallStream TIMESTAMP BY CallRecTime
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    In questa query viene utilizzata la parola chiave **Timestamp By** per specificare un campo di timestamp nel payload da utilizzare nel calcolo temporale. Se il campo non è stato specificato, l'operazione di windowing verrà eseguita usando l'ora in cui ogni evento è arrivato all'hub eventi. Vedere la sezione relativa a [tempo di arrivo e tempo di applicazione nel riferimento per il linguaggio di query di Analisi dei flussi](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Si noti che è possibile accedere a un timestamp per la fine di ogni finestra usando la proprietà **System.Timestamp** .

2.  Fare clic su **Riesegui** nell'editor di query per visualizzare i risultati della query.

    ![Risultati della query per Timestamp by](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>Rilevamento di illeciti relativi alla SIM con self-join

Per identificare l'uso potenzialmente illecito, verranno osservate le chiamate provenienti dallo stesso utente ma in località diverse in meno di 5 secondi.  Per cercare questi casi, verrà utilizzata un’operazione [join](https://msdn.microsoft.com/library/azure/dn835026.aspx) sul flusso di eventi di chiamata.

1.  Modificare la query nell'editor di codice nel modo seguente:

        SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
        CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
        ON CS1.CallingIMSI = CS2.CallingIMSI
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum

2.  Fare clic su **Riesegui** nell'editor di query per visualizzare i risultati della query.

    ![Risultati della query di un join](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>Creare un sink di output

Ora che è stato definito un flusso di eventi, un input dell'hub eventi per acquisire gli eventi e una query per eseguire una trasformazione nel flusso, l'ultimo passaggio consiste nel definire un sink di output per il processo. Gli eventi per il comportamento fraudolento verranno scritti nell'archivio BLOB di Azure.

Se necessario, attenersi alla procedura seguente per creare un contenitore per l'archiviazione BLOB.

1.  Usare un account di archiviazione esistente o crearne uno nuovo facendo clic su **NUOVO > SERVIZI DATI > ARCHIVIAZIONE > CREAZIONE RAPIDA** e seguendo le istruzioni.
2.  Selezionare l'account di archiviazione, fare clic su **CONTENITORI** nella parte superiore della pagina e quindi fare clic su **AGGIUNGI**.
3.  Specificare un **NOME** per il contenitore e impostare il relativo **ACCESSO** su **BLOB pubblico**.

## <a name="specify-job-output"></a>Specificare l'output del processo

1.  Nel processo di Analisi di flusso fare clic su **OUTPUT** nella parte superiore della pagina, quindi scegliere **AGGIUNGI OUTPUT**. La finestra di dialogo visualizzata presenterà alcuni passaggi per l'impostazione dell'output.
2.  Fare clic su **ARCHIVIAZIONE BLOB**, quindi sul pulsante a destra.
3.  Digitare o selezionare i valori seguenti nella terza pagina:

    * **ALIAS DI OUTPUT**: immettere un nome descrittivo per l'output del processo.
    * **SOTTOSCRIZIONE**: se l'archivio BLOB creato si trova nella stessa sottoscrizione del processo di Analisi di flusso, scegliere **Usare l'account di archiviazione dalla sottoscrizione corrente**. Se il dispositivo di archiviazione è in una sottoscrizione diversa, fare clic su **Usare l'account di archiviazione dalla sottoscrizione corrente** e immettere manualmente le informazioni relative ad **ACCOUNT DI ARCHIVIAZIONE**, **CHIAVE ACCOUNT DI ARCHIVIAZIONE** e **CONTENITORE**.
    * **ACCOUNT DI ARCHIVIAZIONE**: selezionare il nome dell’account di archiviazione.
    * **CONTENITORE**: selezionare il nome del contenitore.
    * **PREFISSO NOME FILE**: digitare un prefisso di file da usare per la scrittura dell'output del BLOB.

4.  Fare clic sul pulsante a destra.
5.  Specificare i valori seguenti:

    * **FORMATO SERIALIZZATORE EVENTI**: JSON
    * **CODIFICA**: UTF8

6.  Fare clic sul pulsante con il **segno di spunta** per aggiungere l'origine e per verificare che Analisi di flusso sia in grado di connettersi all'account di archiviazione.

## <a name="start-job-for-real-time-processing"></a>Avviare il processo per l'elaborazione in tempo reale

Poiché l'input, la query e l'output di un processo sono stati specificati, a questo punto è possibile avviare il processo di Analisi di flusso per il rilevamento delle frodi in tempo reale.

1.  Dal **DASHBOARD** del processo fare clic su **AVVIA** nella parte inferiore della pagina.
2.  Nella finestra di dialogo visualizzata selezionare **ORA DI INIZIO PROCESSO**, quindi fare clic sul pulsante con il **segno di spunta** nella parte inferiore della finestra di dialogo. Lo stato del processo diventerà **Avvio in corso** e poco dopo **In esecuzione**.

## <a name="view-fraud-detection-output"></a>Visualizzare l'output di rilevamento di illeciti

Usare uno strumento come [Esplora archivi di Microsoft Azure](http://storageexplorer.com/) o [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) per visualizzare la scrittura degli eventi illeciti nell'output in tempo reale.  

![Rilevamento di illeciti: eventi illeciti visualizzati in tempo reale](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>Supporto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)



<!--HONumber=Oct16_HO2-->


