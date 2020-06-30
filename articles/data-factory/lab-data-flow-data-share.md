---
title: Integrazione dei dati con Azure Data Factory e Condivisione dati di Azure
description: Copiare, trasformare e condividere i dati usando Azure Data Factory e Condivisione dati di Azure
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: dac018db1737b0395f78955d16dd753c6ac2f359
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85252662"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Integrazione dei dati con Azure Data Factory e Condivisione dati di Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I clienti che si imbarcano in progetti di data warehouse moderno e di analisi hanno bisogno non solo di più dati ma anche di una maggiore visibilità sugli stessi. Questo workshop illustra come semplificare l'integrazione e la gestione dei dati in Azure con i miglioramenti apportati ad Azure Data Factory e a Condivisione dati di Azure. 

Dalla possibilità di eseguire procedure ETL/ELT senza codice alla creazione di una visualizzazione completa dei dati, i miglioramenti di Azure Data Factory consentiranno agli ingegneri dei dati di introdurre una maggiore quantità di dati, e quindi più valore, nell'azienda. Condivisione dati di Azure renderà possibile la condivisione Business to Business in un modo regolamentato.

In questo workshop si userà Azure Data Factory (ADF) per inserire i dati dal database SQL di Azure in Azure Data Lake Storage Gen2 (ADLS Gen2). Una volta inseriti nel data lake, i dati verranno trasformati tramite flussi di dati per mapping, il servizio di trasformazione nativo della data factory, e acquisiti tramite sink in Azure Synapse Analytics (in precedenza SQL DW). Quindi, la tabella con i dati trasformati, insieme ad alcuni dati aggiuntivi, verrà condivisa tramite Condivisione dati di Azure. 

I dati usati in questo lab sono relativi ai taxi di New York. Per importarli nel database del database SQL di Azure, scaricare il [file bacpac taxi-data](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac).

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

* **Database SQL di Azure**: se non è disponibile, vedere come [creare un account di DB SQL](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)

* **Account di archiviazione di Azure Data Lake Storage Gen2** : se non è disponibile, vedere come [creare un account di archiviazione di ADLS Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

* **Azure Synapse Analytics (in precedenza SQL DW)** : se non è disponibile, vedere come [creare un'istanza di Azure Synapse Analytics](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal).

* **Azure Data Factory**: se non è stata creata una data factory, vedere come [crearla](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).

* **Condivisione dati di Azure**: se non è stata creata una condivisione dati, vedere come [crearla](https://docs.microsoft.com/azure/data-share/share-your-data#create-a-data-share-account).

## <a name="set-up-your-azure-data-factory-environment"></a>Configurare l'ambiente di Azure Data Factory

Questa sezione illustra come accedere all'esperienza utente di Azure Data Factory dal portale di Azure. Tramite l'esperienza utente di Azure Data Factory, sarà possibile configurare tre servizi collegati per ogni archivio dati da usare: DB SQL di Azure, ADLS Gen2 e Azure Synapse Analytics.

Nei servizi collegati di Azure Data Factory definire le informazioni per la connessione a risorse esterne. Azure Data Factory attualmente supporta 85 connettori.

### <a name="open-the-azure-data-factory-ux"></a>Aprire l'esperienza utente di Azure Data Factory

1. Aprire il [portale di Azure](https://portal.azure.com) in Microsoft Edge o Google Chrome.
1. Usando la barra di ricerca nella parte superiore della pagina, cercare 'data factory'

    ![Portale](media/lab-data-flow-data-share/portal1.png)
1. Fare clic sulla risorsa data factory per aprire il relativo pannello.

    ![Portale](media/lab-data-flow-data-share/portal2.png)
1. Fare clic su **Crea e monitora** per aprire l'esperienza utente di ADF. In alternativa, accedere all'indirizzo adf.azure.com.

    ![Portale](media/lab-data-flow-data-share/portal3.png)
1. Si verrà reindirizzati alla home page dell'esperienza utente di ADF. Questa pagina contiene argomenti di avvio rapido, video di istruzioni e collegamenti a esercitazioni sui concetti di data factory. Per avviare la creazione, fare clic sull'icona della matita sulla barra laterale sinistra.

    ![Portale](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Creare un servizio collegato Database SQL di Azure

1. La pagina di creazione consente di creare risorse di data factory come pipeline, set di dati, flussi di dati, trigger e servizi collegati. Per creare un servizio collegato, fare clic sul pulsante **Connessioni** nell'angolo in basso a destra.

    ![Portale](media/lab-data-flow-data-share/configure2.png)
1. Nella scheda Connessioni fare clic su **Nuovo** per aggiungere un nuovo servizio collegato.

    ![Portale](media/lab-data-flow-data-share/configure3.png)
1. Il primo servizio collegato da configurare è un database SQL di Azure. È possibile usare la barra di ricerca per filtrare l'elenco di archivi dati. Fare clic sul riquadro **Database SQL di Azure** e quindi su Continua.

    ![Portale](media/lab-data-flow-data-share/configure4.png)
1. Nel riquadro di configurazione del database SQL immettere 'SQLDB' come nome del servizio collegato. Immettere le proprie credenziali per consentire alla data factory di connettersi al database. Se si usa l'autenticazione SQL, immettere il nome del server, il database, il nome utente e la password. Per verificare se le informazioni per la connessione sono corrette, fare clic su **Test connessione**. Al termine, fare clic su **Crea**.

    ![Portale](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Creare un servizio collegato Azure Synapse Analytics

1. Ripetere la stessa procedura per aggiungere un servizio collegato Azure Synapse Analytics. Nella scheda Connessioni fare clic su **Nuovo**. Selezionare il riquadro **Azure Synapse Analytics (in precedenza SQL DW)** e fare clic su Continua.

    ![Portale](media/lab-data-flow-data-share/configure6.png)
1. Nel riquadro di configurazione del servizio collegato immettere 'SQLDW' come nome. Immettere le proprie credenziali per consentire alla data factory di connettersi al database. Se si usa l'autenticazione SQL, immettere il nome del server, il database, il nome utente e la password. Per verificare se le informazioni per la connessione sono corrette, fare clic su **Test connessione**. Al termine, fare clic su **Crea**.

    ![Portale](media/lab-data-flow-data-share/configure7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Creare un servizio collegato Azure Data Lake Storage Gen2

1. L'ultimo servizio collegato necessario per questo lab è Azure Data Lake Storage Gen2.  Nella scheda Connessioni fare clic su **Nuovo**. Selezionare il riquadro **Azure Data Lake Storage Gen2** e fare clic su Continua.

    ![Portale](media/lab-data-flow-data-share/configure8.png)
1. Nel riquadro di configurazione del servizio collegato immettere 'ADLSGen2' come nome. Se si usa l'autenticazione tramite chiave dell'account, selezionare l'account di archiviazione di ADLS Gen2 nel menu a discesa **Nome account di archiviazione**. Per verificare se le informazioni per la connessione sono corrette, fare clic su **Test connessione**. Al termine, fare clic su **Crea**.

    ![Portale](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Attivare la modalità di debug dei flussi di dati

Nella sezione *Trasformare i dati con il flusso di dati per mapping* verranno creati flussi di dati per mapping. Prima di creare flussi di dati per mapping, la procedura consigliata consiste nell'attivare la modalità di debug, che consente di testare la logica di trasformazione in pochi secondi su un cluster Spark attivo.

Per attivare il debug, fare clic sul dispositivo di scorrimento **Data flow debug** (Debug flusso di dati) sulla barra superiore della factory. Nella finestra di dialogo di conferma fare clic su OK. L'avvio del cluster richiede circa 5-7 minuti. Durante l'inizializzazione, procedere a *inserire i dati del database SQL di Azure in ADLS Gen2 con l'attività di copia*.

![Portale](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>Inserire i dati usando l'attività di copia

In questa sezione verrà creata una pipeline con un'attività di copia che inserisce una tabella di un database SQL di Azure in un account di archiviazione di ADLS Gen2. Verrà illustrato come aggiungere una pipeline, configurare un set di dati ed eseguire il debug della pipeline tramite l'esperienza utente di ADF. Il modello di configurazione di questa esercitazione si applica alla copia da un archivio dati relazionale a un archivio dati basato su file.

In Azure Data Factory una pipeline è un raggruppamento logico di attività che insieme eseguono un'azione. L'attività definisce un'operazione da eseguire sui dati. Il set di dati punta ai dati da usare in un servizio collegato.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Creare una pipeline con un'attività di copia

1. Nel riquadro di risorse della factory fare clic sull'icona del segno più per aprire il menu Nuova risorsa. Selezionare **Pipeline**.

    ![Portale](media/lab-data-flow-data-share/copy1.png)
1. Nella scheda **Generale** del canvas assegnare alla pipeline un nome descrittivo, ad esempio 'IngestAndTransformTaxiData'.

    ![Portale](media/lab-data-flow-data-share/copy2.png)
1. Nel riquadro attività del canvas della pipeline aprire il menu **Move and Transform** (Sposta e trasforma) e trascinare l'attività **Copy data** (Copia dati) nel canvas. Assegnare all'attività di copia un nome descrittivo, ad esempio 'IngestIntoADLS'.

    ![Portale](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Configurare il set di dati di origine del database SQL di Azure

1. Fare clic sulla scheda **Origine** dell'attività di copia. Per creare un nuovo set di dati, fare clic su **Nuovo**. L'origine sarà la tabella 'dbo.TripData' situata nel servizio collegato 'SQLDB' configurato in precedenza.

    ![Portale](media/lab-data-flow-data-share/copy4.png)
1. Cercare **Database SQL di Azure** e fare clic su Continua.

    ![Portale](media/lab-data-flow-data-share/copy5.png)
1. Assegnare al set di dati il nome 'TripData'. Selezionare 'SQLDB' come servizio collegato. Selezionare il nome della tabella 'dbo.TripData' nell'elenco a discesa corrispondente. Importare lo schema **From connection/store** (Da connessione/archivio). Al termine, fare clic su OK.

    ![Portale](media/lab-data-flow-data-share/copy6.png)

Il set di dati di origine è stato creato. Assicurarsi che nel campo Usa query delle impostazioni dell'origine sia selezionato il valore predefinito **Tabella**.

### <a name="configure-adls-gen-2-sink-dataset"></a>Configurare il set di dati sink di ADLS Gen 2

1. Fare clic sulla scheda **Sink** dell'attività di copia. Per creare un nuovo set di dati, fare clic su **Nuovo**.

    ![Portale](media/lab-data-flow-data-share/copy7.png)
1. Cercare **Azure Data Lake Storage Gen2** e fare clic su Continua.

    ![Portale](media/lab-data-flow-data-share/copy8.png)
1. Nel riquadro Formato selezionare **DelimitedText** perché si scriverà un file CSV. Fare clic su Continua.

    ![Portale](media/lab-data-flow-data-share/copy9.png)
1. Assegnare al set di dati sink il nome 'TripDataCSV'. Selezionare 'ADLSGen2' come servizio collegato. Specificare dove si vuole scrivere il file CSV. Ad esempio, è possibile scrivere i dati nel file `trip-data.csv` nel contenitore `staging-container`. Impostare **First row as header** (Prima riga come intestazione) su True se si vuole che i dati di output abbiano intestazioni. Poiché nella destinazione non esistono ancora file, impostare **Importa schema** su **Nessuno**. Al termine, fare clic su OK.

    ![Portale](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>Testare l'attività di copia con l'esecuzione del debug della pipeline

1. Per verificare il corretto funzionamento dell'attività di copia, fare clic su **Debug** nella parte superiore del canvas della pipeline per avviare l'esecuzione del debug. L'esecuzione del debug consente di testare la pipeline end-to-end o fino a un punto di interruzione prima di pubblicarla nel servizio data factory.

    ![Portale](media/lab-data-flow-data-share/copy11.png)
1. Per monitorare l'esecuzione del debug, aprire la scheda **Output** del canvas della pipeline. La schermata del monitoraggio viene aggiornata automaticamente ogni 20 secondi oppure manualmente facendo clic sul pulsante di aggiornamento. L'attività di copia include una speciale visualizzazione di monitoraggio accessibile facendo clic sull'icona degli occhiali nella colonna **Azioni**.

    ![Portale](media/lab-data-flow-data-share/copy12.png)
1. La visualizzazione del monitoraggio della copia fornisce i dettagli sull'esecuzione e le caratteristiche delle prestazioni dell'attività. Le informazioni riguardano la quantità di dati, righe e file letti/scritti, oltre alla velocità effettiva. Se tutte le impostazioni sono state configurate correttamente, si dovrebbe verificare che sono state scritte 49.999 righe in un unico file nel sink ADLS.

    ![Portale](media/lab-data-flow-data-share/copy13.png)
1. Prima di passare alla sezione successiva, è consigliabile pubblicare le modifiche nel servizio data factory facendo clic su **Pubblica tutto** sulla barra superiore della factory. Anche se non è descritto in questo lab, Azure Data Factory supporta la piena integrazione con git. L'integrazione con git consente il controllo della versione, il salvataggio iterativo in un repository e la collaborazione su una data factory. Per altre informazioni, vedere [Controllo del codice sorgente in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control#troubleshooting-git-integration).

    ![Portale](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Trasformare i dati con il flusso di dati di mapping

Dopo aver correttamente copiato i dati in Azure Data Lake Storage, è possibile aggregarli in un data warehouse. Verrà usato il flusso di dati per mapping, il servizio di trasformazione di Azure Data Factory progettato graficamente. I flussi di dati per mapping consentono agli utenti di sviluppare logica di trasformazione senza codice e di eseguirli in cluster Spark gestiti dal servizio ADF.

Il flusso di dati creato in questo passaggio unisce tramite inner join il set di dati 'TripDataCSV' creato nella sezione precedente con una tabella 'dbo.TripFares' archiviata in 'SQLDB' in base a quattro colonne chiave. Quindi i dati vengono aggregati in base alla colonna `payment_type` per calcolare la media di determinati campi e vengono scritti in una tabella di Azure Synapse Analytics.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Aggiungere un'attività di flusso di dati alla pipeline

1. Nel riquadro attività del canvas della pipeline aprire il menu **Move and Transform** (Sposta e trasforma) e trascinare l'attività **Flusso di dati** nel canvas.

    ![Portale](media/lab-data-flow-data-share/dataflow1.png)
1. Nel riquadro laterale che si apre selezionare **Create new data flow** (Crea nuovo flusso di dati) e scegliere **Flusso di dati per mapping**. Fare clic su **OK**.

    ![Portale](media/lab-data-flow-data-share/dataflow2.png)
1. Si verrà indirizzati nel canvas del flusso di dati, dove verrà sviluppata la logica di trasformazione. Nella scheda Generale assegnare al flusso di dati il nome 'JoinAndAggregateData'.

    ![Portale](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Configurare l'origine CSV dei dati sulle corse

1. La prima cosa da fare è configurare le due trasformazioni di origine. La prima origine punterà al set di dati DelimitedText 'TripDataCSV'. Per aggiungere una trasformazione di origine, fare clic sulla casella **Aggiungi origine** nel canvas.

    ![Portale](media/lab-data-flow-data-share/dataflow4.png)
1. Assegnare all'origine il nome 'TripDataCSV' e selezionare il set di dati 'TripDataCSV' dall'elenco a discesa. Inizialmente, quando è stato creato questo set di dati, non è stato importato uno schema perché non erano disponibili dati. Poiché ora esiste `trip-data.csv`, fare clic su **Modifica** per passare alla scheda di impostazioni del set di dati.

    ![Portale](media/lab-data-flow-data-share/dataflow5.png)
1. Passare alla scheda **Schema** e fare clic su **Importa schema**. Selezionare **From connection/store** (Da connessione/archivio) per eseguire l'importazione direttamente dall'archivio file. Verranno visualizzate 14 colonne di tipo stringa.

    ![Portale](media/lab-data-flow-data-share/dataflow6.png)
1. Tornare nel flusso di dati 'JoinAndAggregateData'. Se il cluster di debug è stato avviato (come indicato da un cerchio verde accanto al relativo dispositivo di scorrimento), è possibile ottenere uno snapshot dei dati nella scheda **Anteprima dei dati**. Fare clic su **Aggiorna** per recuperare un'anteprima dei dati.

    ![Portale](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> L'anteprima dei dati non scrive dati.

### <a name="configure-your-trip-fares-sql-db-source"></a>Configurare l'origine del database SQL per le tariffe delle corse

1. La seconda origine da aggiungere punterà alla tabella 'dbo.TripFares' del database SQL. Nell'origine 'TripDataCSV' sarà presente un'altra casella **Aggiungi origine**. Selezionarla per aggiungere una nuova trasformazione di origine.

    ![Portale](media/lab-data-flow-data-share/dataflow8.png)
1. Assegnare a questa origine il nome 'TripFaresSQL'. Fare clic su **Nuovo** accanto al campo del set di dati di origine per creare un nuovo set di dati del database SQL.

    ![Portale](media/lab-data-flow-data-share/dataflow9.png)
1. Selezionare il riquadro **Database SQL di Azure** e fare clic su Continua. *Nota: come si può notare, molti connettori della data factory non sono supportati nel flusso di dati per mapping. Per trasformare i dati di una di queste origini, inserirli in un'origine supportata usando l'attività di copia*.

    ![Portale](media/lab-data-flow-data-share/dataflow10.png)
1. Assegnare al set di dati il nome 'TripFares'. Selezionare 'SQLDB' come servizio collegato. Selezionare il nome della tabella 'dbo.TripFares' nell'elenco a discesa corrispondente. Importare lo schema **From connection/store** (Da connessione/archivio). Al termine, fare clic su OK.

    ![Portale](media/lab-data-flow-data-share/dataflow11.png)
1. Per verificare i dati, recuperare un'anteprima nella scheda **Anteprima dei dati**.

    ![Portale](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>Unire TripDataCSV e TripFaresSQL tramite inner join

1. Per aggiungere una nuova trasformazione, fare clic sull'icona del segno più nell'angolo in basso a destra di "TripDataCSV". In **Multiple inputs/outputs** (Più input/output) selezionare **Join**.

    ![Portale](media/lab-data-flow-data-share/join1.png)
1. Assegnare alla trasformazione di join il nome 'InnerJoinWithTripFares'. Selezionare 'TripFaresSQL' nell'elenco a discesa Right stream (Flusso destro). Selezionare **Inner** come tipo di join. Per altre informazioni sui diversi tipi di join nel flusso di dati per mapping, vedere [Tipi di join](https://docs.microsoft.com/azure/data-factory/data-flow-join#join-types).

    Selezionare le colonne per cui trovare una corrispondenza da ogni flusso tramite l'elenco a discesa **Join conditions** (Condizioni di join). Per aggiungere un'altra condizione di join, fare clic sull'icona del segno più accanto a una condizione esistente. Per impostazione predefinita, tutte le condizioni di join vengono combinate con un operatore AND, il che significa che devono essere soddisfatte tutte per restituire una corrispondenza. In questo lab verrà trovata una corrispondenza per le colonne `medallion`, `hack_license`, `vendor_id` e `pickup_datetime`

    ![Portale](media/lab-data-flow-data-share/join2.png)
1. Verificare di aver unito correttamente 25 colonne tra loro con un'anteprima dei dati.

    ![Portale](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Aggregazione per payment_type

1. Dopo aver completato la trasformazione di join, aggiungere una trasformazione di aggregazione facendo clic sull'icona del segno più accanto a 'InnerJoinWithTripFares'. Scegliere **Aggregate** (Aggregazione) in **Schema modifier** (Modificatore di schema).

    ![Portale](media/lab-data-flow-data-share/agg1.png)
1. Assegnare alla trasformazione di aggregazione il nome 'AggregateByPaymentType'. Selezionare `payment_type` come colonna in base a cui raggruppare.

    ![Portale](media/lab-data-flow-data-share/agg2.png)
1. Passare alla scheda **Aggregates** (Aggregazioni). Specificare due aggregazioni:
    * La tariffa media raggruppata per tipo di pagamento
    * La distanza totale delle corse raggruppata per tipo di pagamento

    Creare prima di tutto l'espressione per la tariffa media. Nella casella di testo **Add or select a column** (Aggiungere o selezionare una colonna) immettere 'average_fare'.

    ![Portale](media/lab-data-flow-data-share/agg3.png)
1. Per immettere un'espressione di aggregazione, fare clic sulla casella blu **Enter expression** (Immettere un'espressione). Verrà aperto il generatore di espressioni del flusso di dati, uno strumento che consente di creare visivamente espressioni per flussi di dati usando lo schema di input, funzioni e operazioni predefinite, oltre a parametri definiti dall'utente. Per altre informazioni sulle funzionalità di generatore di espressioni, vedere la relativa [documentazione](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder).

    Per ottenere la tariffa media, usare la funzione di aggregazione `avg()` per aggregare il cast della colonna `total_amount` a un intero con `toInteger()`. Nel linguaggio delle espressioni del flusso di dati, questa operazione viene definita come `avg(toInteger(total_amount))`. Al termine, fare clic su **Save and finish** (Salva e chiudi).

    ![Portale](media/lab-data-flow-data-share/agg4.png)
1. Per aggiungere un'espressione di aggregazione aggiuntiva, fare clic sull'icona del segno più accanto a `average_fare`. Selezionare **Add column** (Aggiungi colonna).

    ![Portale](media/lab-data-flow-data-share/agg5.png)
1. Nella casella di testo **Add or select a column** (Aggiungere o selezionare una colonna) immettere 'total_trip_distance'. Come nel passaggio precedente, aprire il generatore di espressioni per immettere l'espressione.

    Per ottenere la distanza totale delle corse, usare la funzione di aggregazione `sum()` per aggregare il cast della colonna `trip_distance` a un intero con `toInteger()`. Nel linguaggio delle espressioni del flusso di dati, questa operazione viene definita come `sum(toInteger(trip_distance))`. Al termine, fare clic su **Save and finish** (Salva e chiudi).

    ![Portale](media/lab-data-flow-data-share/agg6.png)
1. Testare la logica di trasformazione nella scheda **Anteprima dei dati**. Come si può notare, il numero di righe e colonne è significativamente inferiore rispetto a prima. Solo le tre colonne di raggruppamento e aggregazione definite in questa trasformazione continuano downstream. Poiché nell'esempio sono presenti solo cinque gruppi di tipi di pagamento, vengono restituite solo cinque righe.

    ![Portale](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Configurare il sink di Azure Synapse Analytics

1. Una volta completata la logica di trasformazione, è possibile eseguire il sink dei dati in una tabella di Azure Synapse Analytics. Aggiungere una trasformazione di sink nella sezione **Destination** (Destinazione).

    ![Portale](media/lab-data-flow-data-share/sink1.png)
1. Assegnare un nome al sink 'SQLDWSink'. Fare clic su **Nuovo** accanto al campo del set di dati sink per creare un nuovo set di dati di Azure Synapse Analytics.

    ![Portale](media/lab-data-flow-data-share/sink2.png)

1. Selezionare il riquadro **Azure Synapse Analytics (in precedenza SQL DW)** e fare clic su Continua.

    ![Portale](media/lab-data-flow-data-share/sink3.png)
1. Assegnare al set di dati il nome 'AggregatedTaxiData'. Selezionare 'SQLDW' come servizio collegato. Selezionare **Crea nuova tabella** e assegnare alla nuova tabella il nome dbo.AggregateTaxiData. Al termine, fare clic su OK

    ![Portale](media/lab-data-flow-data-share/sink4.png)
1. Passare alla scheda **Impostazioni** del sink. Poiché si sta creando una nuova tabella, è necessario selezionare **Recreate table** (Ricrea tabella) come azione. Deselezionare **Enable staging** (Abilita staging), che consente di scegliere se inserire i dati riga per riga o in batch.

    ![Portale](media/lab-data-flow-data-share/sink5.png)

Il flusso di dati è stato creato. A questo punto è possibile eseguirlo in un'attività della pipeline.

### <a name="debug-your-pipeline-end-to-end"></a>Eseguire il debug della pipeline end-to-end

1. Tornare nella scheda relativa alla pipeline **IngestAndTransformData**. Notare la casella verde nell'attività di copia 'IngestIntoADLS'. Trascinarla nell'attività del flusso di dati 'JoinAndAggregateData'. Viene creato un oggetto 'on success', che determina l'esecuzione dell'attività del flusso di dati solo se la copia riesce.

    ![Portale](media/lab-data-flow-data-share/pipeline1.png)
1. Come per l'attività di copia, fare clic su **Debug** per avviare un'esecuzione di debug. Per le esecuzioni di debug, l'attività del flusso di dati userà il cluster di debug attivo invece di creare un nuovo cluster. L'esecuzione di questa pipeline richiede poco più di un minuto.

    ![Portale](media/lab-data-flow-data-share/pipeline2.png)
1. Analogamente all'attività di copia, il flusso di dati include una speciale visualizzazione di monitoraggio accessibile tramite l'icona degli occhiali al completamento dell'attività.

    ![Portale](media/lab-data-flow-data-share/pipeline3.png)
1. Nella visualizzazione di monitoraggio è possibile visualizzare un grafico del flusso di dati semplificato insieme ai tempi di esecuzione e alle righe in ogni fase dell'esecuzione. Se l'attività viene eseguita correttamente, si dovrebbe aver aggregato 49.999 righe in cinque righe.

    ![Portale](media/lab-data-flow-data-share/pipeline4.png)
1. È possibile fare clic su una trasformazione per ottenere ulteriori dettagli sulla relativa esecuzione, ad esempio informazioni sul partizionamento e sulle colonne nuove/aggiornate/eliminate.

    ![Portale](media/lab-data-flow-data-share/pipeline5.png)

A questo punto è stata completata la parte di questo lab relativa alla data factory. Pubblicare le risorse da operazionalizzare con i trigger. È stata eseguita correttamente una pipeline che ha inserito i dati del database SQL di Azure in Azure Data Lake Storage usando l'attività di copia e quindi ha aggregato questi dati in Azure Synapse Analytics. È possibile verificare che i dati siano stati scritti correttamente esaminando l'istanza stessa di SQL Server.

## <a name="share-data-using-azure-data-share"></a>Condividere dati con Condivisione dati di Azure

In questa sezione verrà illustrato come configurare una nuova condivisione dati usando il portale di Azure. Verrà creata una nuova condivisione dati che conterrà i set di dati di Azure Data Lake Store Gen2 e Azure SQL Data Warehouse. Verrà quindi configurata una pianificazione degli snapshot che fornirà ai consumer di dati l'opzione per aggiornare automaticamente i dati condivisi. Infine, verranno invitati i destinatari nella condivisione dati. 

Dopo aver creato una condivisione di dati, si assumerà il ruolo di *consumer di dati*. In veste di consumer di dati, si completerà il flusso che consiste nell'accettare un invito alla condivisione dati, configurare la posizione in cui ricevere i dati ed eseguire il mapping dei set di dati in posizioni di archiviazione diverse. Si attiverà quindi uno snapshot, che copierà i dati condivisi nella destinazione specificata. 

### <a name="sharing-data-data-provider-flow"></a>Condivisione dei dati (flusso del provider di dati)

1. Aprire il portale di Azure in Microsoft Edge o Google Chrome.

1. Usando la barra di ricerca nella parte superiore della pagina, cercare **Condivisioni dati**

    ![Portale](media/lab-data-flow-data-share/portal-ads.png)

1. Selezionare l'account di condivisione dati con 'Provider' nel nome. Ad esempio, **DataProvider0102**. 

1. Selezionare **Inizia la condivisione dei dati**

    ![Iniziare la condivisione](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Selezionare **+ Crea** per avviare la configurazione della nuova condivisione dati. 

1. In **Condivisione dati** specificare un nome a scelta. Si tratta del nome della condivisione che verrà visualizzato dal consumer di dati, quindi assicurarsi di specificare un nome descrittivo, ad esempio TaxiData.

1. In **Descrizione** inserire una frase che descriva il contenuto della condivisione dati. La condivisione dati conterrà i dati relativi alle corse dei taxi di tutto il mondo, memorizzati in numerosi archivi, tra cui Azure SQL Data Warehouse e Azure Data Lake Store. 

1. In **Condizioni per l'utilizzo** specificare una serie di condizioni a cui il consumer di dati dovrà conformarsi, ad esempio "Non distribuire questi dati all'esterno dell'organizzazione" oppure "Vedere il contratto legale". 

    ![Dettagli sulla condivisione](media/lab-data-flow-data-share/ads-details.png)

1. Selezionare **Continua**. 

1. Selezionare **Aggiungi set di dati** 

    ![Aggiungere il set di dati](media/lab-data-flow-data-share/add-dataset.png)

1. Selezionare **Azure SQL Data Warehouse** per selezionare una tabella di Azure SQL Data Warehouse in cui sono state inserite le trasformazioni di Azure Data Factory.

    ![Aggiungere il set di dati](media/lab-data-flow-data-share/add-dataset-sql.png)

> [!NOTE]
> Azure SQL Data Warehouse ora si chiama Azure Synapse Analytics

1. Prima di procedere, verrà fornito uno script da eseguire. Lo script fornito crea un utente nel database SQL per consentire all'identità del servizio gestita di Condivisione dati di Azure di eseguire l'autenticazione per suo conto. 

> [!IMPORTANT]
> Prima di eseguire lo script, è necessario impostare se stessi come amministratore di Active Directory per SQL Server. 

1. Aprire una nuova scheda e passare al portale di Azure. Copiare lo script fornito per creare un utente nel database da cui si vogliono condividere i dati. A questo scopo, accedere al database EDW tramite Esplora query (anteprima) usando l'autenticazione di AAD. 

    Sarà necessario modificare lo script in modo che l'utente creato sia racchiuso tra parentesi quadre. Ad esempio:
    
    create user [dataprovider-xxxx] from external login;  exec sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. Tornare in Condivisione dati di Azure in cui sono stati aggiunti i set di dati alla condivisione dati. 

1. Selezionare **EDW** per SQL Data Warehouse e **AggregatedTaxiData** per la tabella. 

1. Selezionare **Aggiungi set di dati**

    È ora disponibile una tabella SQL che fa parte del set di dati. Successivamente, si aggiungeranno altri set di dati da Azure Data Lake Store. 

1. Selezionare **Aggiungi set di dati** e quindi **Azure Data Lake Store Gen2**

    ![Aggiungere il set di dati](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Selezionare **Avanti**

1. Espandere *wwtaxidata*. Espandere *Boston Taxi Data*. Si noti che è possibile condividere fino al livello di file. 

1. Selezionare la cartella *Boston Taxi Data* per aggiungere l'intera cartella alla condivisione dati. 

1. Selezionare **Aggiungi set di dati**

1. Esaminare i set di dati aggiunti. Alla condivisione dati dovrebbero essere state aggiunte una tabella SQL e una cartella ADLSGen2. 

1. Selezionare **Continua**

1. In questa schermata è possibile aggiungere i destinatari alla condivisione dati. I destinatari aggiunti riceveranno gli inviti alla condivisione dati. Ai fini di questo lab, è necessario aggiungere due indirizzi di posta elettronica:

    1. L'indirizzo di posta elettronica della sottoscrizione di Azure in uso. 

        ![Aggiungere destinatari](media/lab-data-flow-data-share/add-recipients.png)

    1. Aggiungere il consumer di dati fittizio denominato *janedoe@fabrikam.com* .

1. In questa schermata è possibile configurare un'impostazione degli snapshot per il consumer di dati. In questo modo, il consumer potrà ricevere aggiornamenti regolari dei dati a un intervallo definito dal provider. 

1. Selezionare **Pianificazione degli snapshot** e configurare un aggiornamento orario dei dati usando l'elenco a discesa *Ricorrenza*.  

1. Selezionare **Crea**.

    A questo punto è disponibile una condivisione dati attiva. Esaminare i contenuti visibili a un provider di dati quando si crea una condivisione dati. 

1. Selezionare la condivisione dati creata, denominata **DataProvider**. È possibile accedervi selezionando **Condivisioni inviate** in **Condivisione dati**. 

1. Fare clic su Pianificazione degli snapshot. Se si preferisce, è possibile disabilitare la pianificazione degli snapshot. 

1. Selezionare quindi la scheda **Set di dati**. È possibile aggiungere altri set di dati a questa condivisione dati dopo la creazione. 

1. Selezionare la scheda **Sottoscrizioni di condivisione**. Non esistono ancora sottoscrizioni di condivisione perché il consumer di dati non ha ancora accettato l'invito.

1. Passare alla scheda **Inviti**. Viene visualizzato un elenco di inviti in sospeso. 

    ![Inviti in sospeso](media/lab-data-flow-data-share/pending-invites.png)

1. Selezionare l'invito inviato a *janedoe@fabrikam.com* . Selezionare Elimina. Se il destinatario non ha ancora accettato l'invito, non sarà più in grado di farlo. 

1. Selezionare la scheda **Cronologia** . Non viene ancora visualizzato nulla perché il consumer di dati non ha ancora accettato l'invito né ha attivato uno snapshot. 

### <a name="receiving-data-data-consumer-flow"></a>Ricezione di dati (flusso del consumer di dati)

Dopo aver esaminato la condivisione dati, è possibile cambiare contesto e assumere il ruolo di consumer di dati. 

A questo punto, nella posta in arrivo si dovrebbe avere un invito di Condivisione dati di Azure inviato da Microsoft Azure. Avviare Outlook Web Access (outlook.com) e accedere con le credenziali fornite per la sottoscrizione di Azure.

Nel messaggio di posta elettronica ricevuto fare clic su "Visualizza invito >". A questo punto, si simulerà l'esperienza del consumer di dati quando accetta l'invito di un provider di dati nella sua condivisione dati. 

![Invito tramite posta elettronica](media/lab-data-flow-data-share/email-invite.png)

Potrebbe essere richiesto di selezionare una sottoscrizione. Assicurarsi di selezionare la sottoscrizione che si usa per questo lab. 

1. Fare clic sull'invito denominato *DataProvider*. 

1. In questa schermata di invito si noteranno diversi dettagli sulla condivisione dati configurata in precedenza come provider di dati. Esaminare i dettagli e accettare le condizioni per l'utilizzo, se disponibili.

1. Selezionare la sottoscrizione e il gruppo di risorse già esistenti per il lab. 

1. Per **Data share account** (Account di condivisione dati), selezionare **DataConsumer**. È anche possibile creare un nuovo account di condivisione dati. 

1. Accanto a **Received share name** (Nome condivisione ricevuta) si noterà che il nome predefinito della condivisione corrisponde a quello specificato dal provider di dati. Assegnare alla condivisione un nome descrittivo per i dati che si stanno per ricevere, ad esempio **TaxiDataShare**.

    ![Invito accettato](media/lab-data-flow-data-share/consumer-accept.png)

1. È possibile scegliere l'opzione **Accept and configure now** (Accetta e configura ora) o **Accept and configure later** (Accetta e configura più tardi). Se si sceglie la prima opzione, specificare un account di archiviazione in cui devono essere copiati tutti i dati. Se si sceglie la seconda opzione, i set di dati della condivisione non saranno mappati e sarà necessario eseguirne il mapping manualmente. Questa opzione verrà scelta in seguito. 

1. Selezionare **Accept and configure later** (Accetta e configura più tardi). 

    Quando si configura questa opzione, viene creata una sottoscrizione di condivisione, ma non è disponibile alcuna posizione per i dati perché non è stato eseguito il mapping di una destinazione. 

    Configurare quindi i mapping dei set di dati per la condivisione dati. 

1. Selezionare la condivisione ricevuta (il nome specificato nel passaggio 5).

    L'opzione **Attiva snapshot** è disattivata, ma la condivisione è attiva. 

1. Selezionare la scheda **Set di dati**. Si noti che ogni set di dati risulta non mappato, il che significa che non è presente alcuna destinazione in cui copiare i dati. 

    ![Set di dati senza mapping](media/lab-data-flow-data-share/unmapped.png)

1. Selezionare la tabella SQL Data Warehouse, quindi selezionare **+ Esegui mapping alla destinazione**.

1. Sul lato destro dello schermo selezionare l'elenco a discesa **Tipo di dati di destinazione**. 

    È possibile eseguire il mapping dei dati SQL a un'ampia gamma di archivi dati. In questo caso, verrà eseguito a un database SQL di Azure.

    ![mapping](media/lab-data-flow-data-share/mapping-options.png)
    
    (Facoltativo) Selezionare **Azure Data Lake Store Gen2** come tipo di dati di destinazione. 
    
    (Facoltativo) Selezionare la sottoscrizione, il gruppo di risorse e l'account di archiviazione in uso. 
    
    (Facoltativo) È possibile scegliere di ricevere i dati nel data lake in formato CSV o parquet. 

1. Quindi, accanto a **Tipo di dati di destinazione**selezionare Database SQL di Azure. 

1. Selezionare la sottoscrizione, il gruppo di risorse e l'account di archiviazione in uso. 

    ![Mapping a SQL](media/lab-data-flow-data-share/map-to-sqldb.png)

1. Prima di continuare, è necessario creare un nuovo utente in SQL Server eseguendo lo script fornito. Copiare prima di tutto lo script fornito negli Appunti. 

1. Aprire una nuova scheda del portale di Azure. Non chiudere la scheda esistente perché sarà necessario tornarvi tra poco. 

1. Nella nuova scheda aperta passare a **Database SQL**.

1. Selezionare il database SQL (dovrebbe esserne presente uno solo nella sottoscrizione). Fare attenzione a non selezionare SQL Data Warehouse. 

1. Selezionare **Editor di query (anteprima)**

1. Usare l'autenticazione di AAD per accedere all'editor di query. 

1. Eseguire la query specificata nella condivisione dati (copiata negli Appunti nel passaggio 14). 

    Con questo comando il servizio Condivisione dati di Azure può usare identità gestite per consentire ai servizi di Azure di eseguire l'autenticazione in SQL Server in modo da potervi copiare i dati. 

1. Tornare nella scheda originale e selezionare **Esegui mapping alla destinazione**.

1. Selezionare quindi la cartella Azure Data Lake Gen2 che fa parte del set di dati ed eseguirne il mapping a un account di archiviazione BLOB di Azure. 

    ![storage](media/lab-data-flow-data-share/storage-map.png)

    Con tutti i set di dati mappati, è ora possibile iniziare a ricevere dati dal provider di dati. 

    ![Mappati](media/lab-data-flow-data-share/all-mapped.png)
    
1. Selezionare **Dettagli**. 

    Si noti che l'opzione **Attiva snapshot** non è più disattivata, perché ora sono disponibili destinazioni della condivisione dati per la copia.

1. Selezionare Attiva snapshot -> Copia completa. 

    ![trigger](media/lab-data-flow-data-share/trigger-full.png)

    Verrà avviata la copia dei dati nel nuovo account di condivisione dati. In uno scenario reale, questi dati provengono da terze parti. 

    Sono necessari circa 3-5 minuti prima che i dati vengano ricevuti. È possibile monitorare lo stato di avanzamento facendo clic sulla scheda **Cronologia**. 

    Durante l'attesa, passare alla condivisione dati originale (DataProvider) e visualizzare lo stato delle schede **Sottoscrizioni di condivisione** e **Cronologia**. Si noti che ora è disponibile una sottoscrizione attiva e, come provider di dati, è anche possibile monitorare quando il consumer di dati ha iniziato a ricevere i dati condivisi. 

1. Tornare nella condivisione dati del consumer di dati. Quando lo stato del trigger indica che l'operazione è riuscita, passare al database SQL e al data lake di destinazione per verificare che i dati siano stati inseriti nei rispettivi archivi. 

Il lab è stato completato.


