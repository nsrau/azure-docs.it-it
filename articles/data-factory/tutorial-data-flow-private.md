---
title: Trasformare i dati con un flusso di dati di mapping di rete virtuale gestito Azure Data Factory
description: In questa esercitazione vengono fornite istruzioni dettagliate per l'utilizzo di Azure Data Factory per trasformare i dati con il mapping di flussi di dati.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/19/2019
ms.openlocfilehash: d752b747a0156bcef587f81ee421c55a6de81e17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079473"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>Trasformare i dati in modo sicuro utilizzando il flusso di dati di mapping

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).

In questa esercitazione si userà l'interfaccia utente di Data Factory per creare una pipeline che copia e trasforma i dati *da un'origine Azure Data Lake storage Gen2 a un sink data Lake storage Gen2 (sia per consentire l'accesso solo alle reti selezionate)* utilizzando il flusso di dati di mapping in [Data Factory rete virtuale gestita](managed-virtual-network-private-endpoint.md). È possibile espandere il modello di configurazione in questa esercitazione quando si trasformano i dati utilizzando il flusso di dati di mapping.

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
>
> * Creare una data factory.
> * Creare una pipeline con un'attività flusso di dati.
> * Compilare un flusso di dati di mapping con quattro trasformazioni.
> * Eseguire test della pipeline.
> * Monitorare un'attività del flusso di dati.

## <a name="prerequisites"></a>Prerequisiti
* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**. Usare Data Lake Storage come archivi dati di *origine* e *sink* . Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) per informazioni su come crearne uno. *Assicurarsi che l'account di archiviazione consenta l'accesso solo da reti selezionate.* 

Il file che verrà trasformato in questa esercitazione è moviesDB.csv, disponibile nel [sito di contenuto GitHub](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Per recuperare il file da GitHub, copiare il contenuto in un editor di testo di propria scelta per salvarlo localmente come file con estensione CSV. Per caricare il file nell'account di archiviazione, vedere [caricare BLOB con il portale di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Gli esempi faranno riferimento a un contenitore denominato **Sample-Data**.

## <a name="create-a-data-factory"></a>Creare una data factory

In questo passaggio si crea una data factory e si apre l'interfaccia utente di Data Factory per creare una pipeline nella data factory.

1. Aprire Microsoft Edge o Google Chrome. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.
1. Nel menu a sinistra selezionare **Crea una risorsa** > **Analisi** > **Data factory**.
1. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**.

   Il nome della data factory deve essere *univoco a livello globale*. Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory (ad esempio, nomeADFTutorialDataFactory). Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere [Azure Data factory - Regole di denominazione](naming-rules.md).

1. Selezionare la **sottoscrizione** di Azure in cui creare la data factory.
1. In **Gruppo di risorse** eseguire una di queste operazioni:

    * Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa.
    * Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse. 
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/overview.md). 
1. In **Versione** selezionare **V2**.
1. In **Località** selezionare una località per la data factory. Nell'elenco a discesa vengono visualizzate solo le località supportate. Gli archivi dati (ad esempio, archiviazione di Azure e il database SQL di Azure) e le risorse di calcolo (ad esempio, Azure HDInsight) usati dal data factory possono trovarsi in altre aree.

1. Selezionare **Crea**.
1. Al termine della creazione, la relativa notifica verrà visualizzata nel centro notifiche. Selezionare **Vai alla risorsa** per passare alla pagina **Data Factory**.
1. Selezionare **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata.

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Creare un Azure IR in Data Factory rete virtuale gestita
In questo passaggio si crea una Azure IR e si Abilita Data Factory rete virtuale gestita.

1. Nel portale di Data Factory passare a **Gestisci**e selezionare **nuovo** per creare una nuova Azure IR.

   ![Screenshot che mostra la creazione di una nuova Azure IR.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Selezionare l'opzione **Azure** IR.

   ![Screenshot che mostra un nuovo Azure IR.](./media/tutorial-copy-data-portal-private/azure-ir.png)

1. In **Configurazione della rete virtuale (anteprima)** selezionare **Abilita**.

   ![Screenshot che mostra l'abilitazione di un nuovo Azure IR.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)

1. Selezionare **Crea**.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Creare una pipeline con un'attività flusso di dati

In questo passaggio verrà creata una pipeline che contiene un'attività flusso di dati.

1. Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline).

   ![Screenshot che mostra la creazione di una pipeline.](./media/doc-common-process/get-started-page.png)

1. Nel riquadro proprietà della pipeline immettere **TransformMovies** per nome pipeline.
1. Nella barra superiore della Factory scorrere il dispositivo di scorrimento **debug del flusso di dati** . La modalità di debug consente il test interattivo della logica di trasformazione in un cluster Spark attivo. I cluster di flussi di dati sono necessari da cinque a sette minuti per il riscaldamento. Attivare prima il **debug del flusso di dati** se si prevede di eseguire lo sviluppo del flusso di dati. Per altre informazioni, vedere [modalità di debug](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-debug-mode).

    ![Screenshot che mostra il dispositivo di scorrimento debug del flusso di dati.](media/tutorial-data-flow-private/dataflow-debug.png)
1. Nel riquadro **attività** espandere **Sposta e trasforma**. Trascinare l'attività **flusso di dati** dal riquadro nell'area di disegno della pipeline.

1. Nella finestra popup **aggiunta flusso di dati** selezionare **Crea nuovo flusso di dati** e quindi selezionare flusso di **dati mapping**. Al termine, fare clic su **OK** .

    ![Screenshot che mostra il flusso di dati del mapping.](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Denominare il flusso di dati **TransformMovies** nel riquadro proprietà.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Logica di trasformazione compilazione nell'area di disegno del flusso di dati

Dopo aver creato il flusso di dati, verrà inviato automaticamente all'area di disegno del flusso di dati. In questo passaggio verrà compilato un flusso di dati che accetta il file di moviesDB.csv in Data Lake Storage e aggrega la valutazione media di Comedie da 1910 a 2000. Il file verrà quindi scritto nuovamente in Data Lake Storage.

### <a name="add-the-source-transformation"></a>Aggiungere la trasformazione origine

In questo passaggio si configurano Data Lake Storage Gen2 come origine.

1. Nell'area di disegno del flusso di dati aggiungere un'origine selezionando la casella **Aggiungi origine** .

1. Assegnare un nome all'origine **MoviesDB**. Selezionare **nuovo** per creare un nuovo set di dati di origine.

1. Selezionare **Azure Data Lake storage Gen2**, quindi selezionare **continua**.

1. Selezionare **DelimitedText**e quindi fare clic su **continua**.

1. Denominare il set di dati **MoviesDB**. Nell'elenco a discesa servizio collegato selezionare **nuovo**.

1. Nella schermata di creazione del servizio collegato assegnare un nome al servizio collegato Data Lake Storage Gen2 **ADLSGen2** e specificare il metodo di autenticazione. Immettere quindi le credenziali di connessione. In questa esercitazione viene usata la **chiave dell'account** per connettersi all'account di archiviazione. 

1. Assicurarsi di abilitare **Creazione interattiva**. L'abilitazione potrebbe richiedere un minuto.

    ![Screenshot che mostra la creazione interattiva.](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Selezionare **Test connessione**. Dovrebbe avere esito negativo perché l'account di archiviazione non consente l'accesso al suo interno senza la creazione e l'approvazione di un endpoint privato. Nel messaggio di errore verrà visualizzato un collegamento che è possibile seguire per creare un endpoint privato gestito. In alternativa, è possibile passare direttamente alla scheda **Gestisci** e seguire le istruzioni riportate in [questa sezione](#create-a-managed-private-endpoint) per creare un endpoint privato gestito.

1. Tenere aperta la finestra di dialogo e quindi passare all'account di archiviazione.

1. Seguire le istruzioni riportate in [questa sezione](#approval-of-a-private-link-in-a-storage-account) per approvare il collegamento privato.

1. Tornare nella finestra di dialogo. Selezionare di nuovo **Test connessione** e selezionare **Crea** per distribuire il servizio collegato.

1. Nella schermata Creazione set di dati immettere dove si trova il file nel campo **percorso file** . In questa esercitazione il file moviesDB.csv si trova nel contenitore **Sample-Data**. Poiché il file contiene intestazioni, selezionare la casella **di controllo prima riga come intestazione** . Selezionare **da connessione/archivio** per importare lo schema dell'intestazione direttamente dal file nella risorsa di archiviazione. Al termine, fare clic su **OK** .

    ![Screenshot che mostra il percorso di origine.](media/tutorial-data-flow-private/source-file-path.png)

1. Se il cluster di debug è stato avviato, passare alla scheda **Anteprima dati** della trasformazione origine e selezionare **Aggiorna** per ottenere uno snapshot dei dati. È possibile utilizzare l'anteprima dati per verificare che la trasformazione sia configurata correttamente.

    ![Screenshot che mostra la scheda Anteprima dati.](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Creare un endpoint privato gestito

Se non è stato usato il collegamento ipertestuale quando è stato testato la connessione precedente, seguire il percorso. A questo punto è necessario creare un endpoint privato gestito che verrà connesso al servizio collegato creato.

1. Passare alla scheda **Gestisci**.

   > [!NOTE]
   > La scheda **Gestisci** potrebbe non essere disponibile per tutte le istanze di Data Factory. Se non viene visualizzata, è possibile accedere agli endpoint privati selezionando **Autore** > **Connessioni**  > **Endpoint privato**.

1. Passare alla sezione **Managed private endpoints** (Endpoint privati gestiti).
1. Selezionare **+ Nuovo** in **Managed private endpoints** (Endpoint privati gestiti).

    ![Screenshot che mostra il pulsante Nuovo in Managed private endpoints (Endpoint privati gestiti).](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Selezionare il riquadro **Azure Data Lake storage Gen2** dall'elenco e selezionare **continua**.
1. Immettere il nome dell'account di archiviazione creato.
1. Selezionare **Crea**.
1. Dopo alcuni secondi si noterà che il collegamento privato creato necessita dell'approvazione.
1. Selezionare l'endpoint privato creato. Verrà visualizzato un collegamento ipertestuale, seguendo il quale sarà possibile approvare l'endpoint privato a livello di account di archiviazione.

    ![Screenshot che mostra il riquadro Gestisci endpoint privato.](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Approvazione di un collegamento privato in un account di archiviazione

1. Nell'account di archiviazione passare a **Connessioni a endpoint privato** nella sezione **Impostazioni**.

1. Selezionare la casella di controllo dall'endpoint privato creato e selezionare **approva**.

    ![Screenshot che mostra il pulsante approva endpoint privato.](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Aggiungere una descrizione e selezionare **sì**.
1. Tornare nella sezione **Managed private endpoints** (Gestisci endpoint privati) della scheda **Gestisci** in Data Factory.
1. Dopo circa un minuto dovrebbe essere visualizzata l'approvazione per l'endpoint privato.

### <a name="add-the-filter-transformation"></a>Aggiungere la trasformazione filtro

1. Accanto al nodo di origine nell'area di disegno del flusso di dati, selezionare l'icona a forma di segno più per aggiungere una nuova trasformazione. La prima trasformazione che verrà aggiunta è un **filtro**.

    ![Screenshot che mostra l'aggiunta di un filtro.](media/tutorial-data-flow-private/add-filter.png)
1. Assegnare un nome alla trasformazione filtro **FilterYears**. Selezionare la casella espressione accanto a **filtro** per aprire il generatore di espressioni. Qui si specificherà la condizione di filtro.

    ![Screenshot che mostra FilterYears.](media/tutorial-data-flow-private/filter-years.png)
1. Il generatore di espressioni del flusso di dati consente di compilare in modo interattivo espressioni da usare in diverse trasformazioni. Le espressioni possono includere funzioni predefinite, colonne dello schema di input e parametri definiti dall'utente. Per ulteriori informazioni su come compilare espressioni, vedere [Generatore di espressioni del flusso di dati](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder).

    * In questa esercitazione si vogliono filtrare i film nel genere di commedia che si è rivelato tra gli anni 1910 e 2000. Poiché l'anno è attualmente una stringa, è necessario convertirla in un Integer utilizzando la ```toInteger()``` funzione. Usare gli operatori maggiore o uguale a (>=) e minore o uguale a (<=) per confrontare i valori letterali anno 1910 e 2000. Unire queste espressioni con l'operatore and (&&). L'espressione viene visualizzata come segue:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Per individuare i film che rappresentano le medie, è possibile usare la ```rlike()``` funzione per trovare il modello "Comedy" nei generi di colonna. Unione dell'espressione rlike con il confronto Year da ottenere:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Se si dispone di un cluster di debug attivo, è possibile verificare la logica selezionando **Aggiorna** per visualizzare l'output dell'espressione rispetto agli input utilizzati. Esiste più di una risposta giusta su come eseguire questa logica usando il linguaggio delle espressioni del flusso di dati.

        ![Screenshot che mostra l'espressione di filtro.](media/tutorial-data-flow-private/filter-expression.png)

    * Al termine dell'espressione, selezionare **Salva e fine** .

1. Recuperare un' **anteprima dei dati** per verificare che il filtro funzioni correttamente.

    ![Screenshot che mostra l'anteprima dei dati filtrati.](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Aggiungere la trasformazione aggregazione

1. La trasformazione successiva che verrà aggiunta è una trasformazione **aggregazione** nel **modificatore dello schema**.

    ![Screenshot che mostra l'aggiunta dell'aggregazione.](media/tutorial-data-flow-private/add-aggregate.png)
1. Assegnare un nome alla trasformazione aggregazione **AggregateComedyRating**. Nella scheda **raggruppa** per selezionare **anno** dalla casella di riepilogo a discesa per raggruppare le aggregazioni in base all'anno in cui è uscito il film.

    ![Screenshot che mostra il gruppo aggregato.](media/tutorial-data-flow-private/group-by-year.png)
1. Passare alla scheda **aggregazioni** . Nella casella di testo a sinistra, denominare la colonna aggregata **AverageComedyRating**. Selezionare la casella espressione a destra per inserire l'espressione di aggregazione tramite Generatore di espressioni.

    ![Screenshot che mostra il nome della colonna aggregata.](media/tutorial-data-flow-private/name-column.png)
1. Per ottenere la media della **classificazione**di colonna, utilizzare la ```avg()``` funzione di aggregazione. Poiché **rating** è una stringa e ```avg()``` accetta un input numerico, è necessario convertire il valore in un numero tramite la ```toInteger()``` funzione. Questa espressione ha un aspetto simile al seguente:

    ```avg(toInteger(Rating))```

1. Al termine, selezionare **Salva e Chiudi** .

    ![Screenshot che mostra il salvataggio dell'aggregazione.](media/tutorial-data-flow-private/save-aggregate.png)
1. Passare alla scheda **Anteprima dati** per visualizzare l'output della trasformazione. Si noti che sono presenti solo due colonne, **year** e **AverageComedyRating**.

### <a name="add-the-sink-transformation"></a>Aggiungere la trasformazione sink

1. Successivamente, si desidera aggiungere una trasformazione **sink** in **destinazione**.

    ![Screenshot che mostra l'aggiunta di un sink.](media/tutorial-data-flow-private/add-sink.png)
1. Assegnare un nome al **sink**di sink. Selezionare **nuovo** per creare il set di dati del sink.

    ![Screenshot che mostra la creazione di un sink.](media/tutorial-data-flow-private/create-sink.png)
1. Nella pagina **nuovo set di dati** selezionare **Azure Data Lake storage Gen2** e quindi fare clic su **continua**.

1. Nella pagina **Seleziona formato** selezionare **DelimitedText** e quindi fare clic su **continua**.

1. Assegnare al set di dati del sink il nome **MoviesSink**. Per servizio collegato scegliere lo stesso servizio collegato **ADLSGen2** creato per la trasformazione di origine. Immettere una cartella di output in cui scrivere i dati. In questa esercitazione si sta scrivendo nell' **output** della cartella nel contenitore **Sample-Data**. La cartella non deve esistere in anticipo e può essere creata dinamicamente. Selezionare la **prima riga come intestazione** casella di controllo e selezionare **nessuno** per **Importa schema**. Selezionare **OK**.

    ![Screenshot che mostra il percorso del sink.](media/tutorial-data-flow-private/sink-file-path.png)

A questo punto è stata completata la creazione del flusso di dati. Si è pronti per eseguirlo nella pipeline.

## <a name="run-and-monitor-the-data-flow"></a>Eseguire e monitorare il flusso di dati

È possibile eseguire il debug di una pipeline prima di pubblicarla. In questo passaggio viene avviata un'esecuzione del debug della pipeline del flusso di dati. Mentre l'anteprima dei dati non scrive dati, un'esecuzione del debug scriverà i dati nella destinazione del sink.

1. Passare all'area di disegno della pipeline. Selezionare **debug** per attivare un'esecuzione del debug.

1. Il debug delle pipeline delle attività del flusso di dati usa il cluster di debug attivo, ma richiede almeno un minuto per l'inizializzazione. È possibile tenere traccia dello stato di avanzamento tramite la scheda **output** . Una volta completata l'esecuzione, selezionare l'icona degli occhiali per i dettagli dell'esecuzione.

1. Nella pagina dettagli è possibile visualizzare il numero di righe e il tempo impiegato per ogni passaggio di trasformazione.

    ![Screenshot che mostra un'esecuzione del monitoraggio.](media/tutorial-data-flow-private/run-details.png)
1. Selezionare una trasformazione per ottenere informazioni dettagliate sulle colonne e il partizionamento dei dati.

Se questa esercitazione è stata eseguita correttamente, è necessario avere scritto 83 righe e 2 colonne nella cartella del sink. È possibile verificare che i dati siano corretti controllando l'archivio BLOB.

## <a name="summary"></a>Riepilogo

In questa esercitazione è stata usata l'interfaccia utente di Data Factory per creare una pipeline che consente di copiare e trasformare i dati da un'origine Data Lake Storage Gen2 a un sink Data Lake Storage Gen2 (per consentire l'accesso solo alle reti selezionate) usando il flusso di dati di mapping in [Data Factory rete virtuale gestita](managed-virtual-network-private-endpoint.md).
