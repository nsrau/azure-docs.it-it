---
title: Trasformare i dati con un flusso di dati di mapping VNet gestito Azure Data Factory
description: In questa esercitazione vengono fornite istruzioni dettagliate per l'utilizzo di Azure Data Factory per trasformare i dati con il flusso di dati di mapping
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/19/2019
ms.openlocfilehash: 5515f6c4dfbc5d3c0e391373e763597d7fdc89ed
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531754"
---
# <a name="transform-data-securely-using-mapping-data-flows"></a>Trasformare i dati in modo sicuro usando i flussi di dati di mapping

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).

In questa esercitazione si userà l'interfaccia utente di Azure Data Factory per creare una pipeline che copia e trasforma i dati **da Azure Data Lake storage un'origine Gen2 (ADLS) a un sink ADLS Gen2 (sia per consentire l'accesso solo alle reti selezionate)** utilizzando il flusso di dati di mapping in [Azure Data Factory rete virtuale gestita](managed-virtual-network-private-endpoint.md). Il modello di configurazione in questa esercitazione può essere espanso quando si trasformano i dati utilizzando il flusso di dati di mapping.

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
>
> * Creare una data factory.
> * Creare una pipeline con un'attività flusso di dati.
> * Compilare un flusso di dati di mapping con quattro trasformazioni.
> * Eseguire test della pipeline.
> * Monitorare un'attività flusso di dati

## <a name="prerequisites"></a>Prerequisiti
* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**. Si usa l'archiviazione ADLS come archivi dati di *origine* e *sink* . Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) per informazioni su come crearne uno. **Verificare che l'account di archiviazione consenta l'accesso solo da' reti selezionate '.** 

Il file che si sta trasformando in questa esercitazione è MoviesDB.csv, disponibile [qui](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Per recuperare il file da GitHub, copiare il contenuto in un editor di testo di propria scelta per salvarlo localmente come file con estensione CSV. Per caricare il file nell'account di archiviazione, vedere [caricare BLOB con il portale di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Gli esempi faranno riferimento a un contenitore denominato "Sample-Data".

## <a name="create-a-data-factory"></a>Creare una data factory

In questo passaggio si crea una data factory e si apre l'Data Factory UX per creare una pipeline nell'data factory.

1. Aprire **Microsoft Edge** o **Google Chrome**. Attualmente, Data Factory interfaccia utente è supportata solo nei Web browser Microsoft Edge e Google Chrome.
2. Nel menu a sinistra selezionare **Crea una risorsa** > **Analisi** > **Data factory**.
3. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**.

   Il nome della data factory di Azure deve essere *univoco a livello globale*. Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory. Ad esempio, nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere [Azure Data factory - Regole di denominazione](naming-rules.md).

4. Selezionare la **sottoscrizione** di Azure in cui creare la data factory.
5. In **Gruppo di risorse** eseguire una di queste operazioni:

    a. Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa.

    b. Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse. 
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/overview.md). 
6. In **Versione** selezionare **V2**.
7. In **Località** selezionare una località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (ad esempio, archiviazione di Azure e il database SQL) e le risorse di calcolo (ad esempio, Azure HDInsight) usati dal data factory possono trovarsi in altre aree.

8. Selezionare **Crea**.

9. Al termine della creazione, la relativa notifica verrà visualizzata nel centro notifiche. Selezionare **Vai alla risorsa** per passare alla pagina della data factory.
10. Selezionare **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata.

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>Creare una Azure Integration Runtime nella rete virtuale gestita di ADF
In questo passaggio viene creato un Azure Integration Runtime e viene abilitata la rete virtuale gestita.

1. Nel portale di ADF passare a **Gestisci Hub** e fare clic su **nuovo** per creare una nuova Azure Integration Runtime.
   ![Crea nuovo Azure Integration Runtime](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. Scegliere di creare un Integration Runtime di **Azure** .
   ![Nuovo Azure Integration Runtime](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. Abilitare la **rete virtuale**.
   ![Nuovo Azure Integration Runtime](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. Selezionare **Crea**.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Creare una pipeline con un'attività flusso di dati

In questo passaggio verrà creata una pipeline che contiene un'attività flusso di dati.

1. Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline).

   ![Creare una pipeline](./media/doc-common-process/get-started-page.png)

1. Nel riquadro **Proprietà** della pipeline immettere **TransformMovies** per **nome** della pipeline.
1. Nella barra superiore della Factory scorrere il dispositivo di scorrimento **debug del flusso di dati** . La modalità di debug consente il test interattivo della logica di trasformazione in un cluster Spark attivo. I cluster di flussi di dati impiegano 5-7 minuti per il riscaldamento e si consiglia agli utenti di attivare prima il debug se pianificano lo sviluppo del flusso di dati. Per altre informazioni, vedere [Modalità di debug](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-debug-mode).

    ![Debug del flusso di dati](media/tutorial-data-flow-private/dataflow-debug.png)
1. Nel riquadro **attività** espandere l'accordo di **spostamento e trasformazione** . Trascinare e rilasciare l'attività **flusso di dati** dal riquadro nell'area di disegno della pipeline.

1. Nella finestra popup **aggiunta flusso di dati** selezionare **Crea nuovo flusso di dati** e quindi selezionare flusso di **dati mapping**. Fare clic su **OK** al termine dell'operazione.

    ![Flusso di dati per mapping](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Denominare il flusso di dati **TransformMovies** nel riquadro proprietà.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Logica di trasformazione compilazione nell'area di disegno del flusso di dati

Dopo aver creato il flusso di dati, verrà inviato automaticamente all'area di disegno del flusso di dati. In questo passaggio verrà compilato un flusso di dati che accetta il moviesDB.csv nell'archiviazione ADLS e aggrega la valutazione media di Comedie da 1910 a 2000. Il file verrà quindi scritto nuovamente nell'archivio ADLS.

### <a name="add-the-source-transformation"></a>Aggiungere la trasformazione origine

In questo passaggio si configurano Azure Data Lake Storage Gen2 come origine.

1. Nell'area di disegno del flusso di dati aggiungere un'origine facendo clic sulla casella **Aggiungi origine** .

1. Assegnare un nome all'origine **MoviesDB**. Fare clic su **nuovo** per creare un nuovo set di dati di origine.

1. Scegliere **Azure Data Lake storage Gen2**. Fare clic su Continue.

1. Scegliere **DelimitedText**. Fare clic su Continue.

1. Denominare il set di dati **MoviesDB**. Nell'elenco a discesa servizio collegato scegliere **nuovo**.

1. Nella schermata di creazione del servizio collegato denominare il servizio collegato ADLS Gen2 **ADLSGen2** e specificare il metodo di autenticazione. Immettere quindi le credenziali di connessione. In questa esercitazione viene usata la chiave dell'account per connettersi all'account di archiviazione. 

1. Assicurarsi di abilitare la **creazione interattiva**. L'abilitazione potrebbe richiedere circa 1 minuto.

    ![Creazione interattiva](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Selezionare **Test connessione**. l'operazione non riesce perché l'account di archiviazione non consente l'accesso al suo interno senza la creazione e l'approvazione di un endpoint privato. Nel messaggio di errore viene visualizzato un collegamento per creare un **endpoint privato** che è possibile seguire per creare un endpoint privato gestito. *In alternativa, è possibile passare direttamente alla scheda Gestisci e seguire le istruzioni riportate in [questa sezione](#create-a-managed-private-endpoint) per creare un endpoint privato gestito*

1. Tenere aperta la finestra di dialogo e quindi passare all'account di archiviazione selezionato in precedenza.

1. Per approvare il collegamento privato, seguire le istruzioni riportate in [questa sezione](#approval-of-a-private-link-in-storage-account) .

1. Tornare alla finestra di dialogo. **Testare** di nuovo la connessione e selezionare **Crea** per distribuire il servizio collegato.

1. Quando si torna alla schermata di creazione del set di dati, immettere dove si trova il file nel campo **percorso file** . In questa esercitazione il file moviesDB.csv si trova in container Sample-Data. Quando il file contiene intestazioni, controllare la **prima riga come intestazione**. Selezionare **da connessione/archivio** per importare lo schema dell'intestazione direttamente dal file nella risorsa di archiviazione. Fare clic su OK al termine dell'operazione.

    ![Percorso di origine](media/tutorial-data-flow-private/source-file-path.png)

1. Se il cluster di debug è stato avviato, passare alla scheda **Anteprima dati** della trasformazione origine e fare clic su **Aggiorna** per ottenere uno snapshot dei dati. È possibile utilizzare l'anteprima dati per verificare che la trasformazione sia configurata correttamente.

    ![Anteprima dati](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Creare un endpoint privato gestito

Se non è stato fatto clic sul collegamento ipertestuale quando si testa la connessione precedente, seguire il percorso seguente. A questo punto è necessario creare un endpoint privato gestito che verrà connesso al servizio collegato creato in precedenza.

1. Passare alla scheda Gestisci.
> [!NOTE]
> La scheda Gestisci potrebbe non essere disponibile per tutte le istanze di data factory. Se non viene visualizzato, è comunque possibile accedere a endpoint privati tramite la scheda "**autore**"-->'**connessioni**'-->'**endpoint privato**'
1. Passare alla sezione endpoint privati gestiti.
1. Selezionare **+ nuovo** in endpoint privati gestiti.

    ![Nuovo endpoint privato gestito](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Selezionare il riquadro Azure Data Lake Storage Gen2 dall'elenco e selezionare **continua**.
1. Immettere il nome dell'account di archiviazione creato in precedenza.
1. Selezionare **Crea**.
1. Si noterà che dopo aver atteso alcuni secondi il collegamento privato creato necessita di un'approvazione.
1. Selezionare l'endpoint privato creato in precedenza. È possibile visualizzare un collegamento ipertestuale che consentirà di approvare l'endpoint privato a livello di account di archiviazione.

    ![Gestisci endpoint privato](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>Approvazione di un collegamento privato nell'account di archiviazione

1. Nell'account di archiviazione passare alla sezione **connessioni endpoint privato** in impostazioni.

1. Selezionare l'endpoint privato creato in precedenza e selezionare **approva**.

    ![Approvare un endpoint privato](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Aggiungere una descrizione e fare clic su **Sì**.
1. Tornare alla sezione **endpoint privati gestiti** della scheda **gestisci** in Azure Data Factory.
1. L'approvazione per l'endpoint privato dovrebbe richiedere circa 1 minuto.

### <a name="add-the-filter-transformation"></a>Aggiungere la trasformazione filtro

1. Accanto al nodo di origine nell'area di disegno del flusso di dati fare clic sull'icona a forma di segno più per aggiungere una nuova trasformazione. La prima trasformazione che si sta aggiungendo è un **filtro**.

    ![Aggiungi filtro](media/tutorial-data-flow-private/add-filter.png)
1. Assegnare un nome alla trasformazione filtro **FilterYears**. Fare clic sulla casella espressione accanto a applica **filtro** per aprire Generatore di espressioni. Qui si specificherà la condizione di filtro.

    ![Filtra anni](media/tutorial-data-flow-private/filter-years.png)
1. Il generatore di espressioni del flusso di dati consente di compilare in modo interattivo espressioni da usare in diverse trasformazioni. Le espressioni possono includere funzioni predefinite, colonne dello schema di input e parametri definiti dall'utente. Per ulteriori informazioni su come compilare espressioni, vedere [Generatore di espressioni del flusso di dati](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder).

    * In questa esercitazione si vogliono filtrare i film di genere Comedy che hanno avuto come risultato tra gli anni 1910 e 2000. Come Year è attualmente una stringa, è necessario convertirla in un Integer usando la ```toInteger()``` funzione. Utilizzare gli operatori maggiore di o uguale a (>=) e minore o uguale a (<=) per confrontare i valori letterali anno 1910 e 200-. Unire queste espressioni con l'operatore and (&&). L'espressione viene visualizzata come segue:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Per individuare i film che rappresentano le medie, è possibile usare la ```rlike()``` funzione per trovare il modello "Comedy" nei generi di colonna. Unione dell'espressione rlike con il confronto Year da ottenere:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Se si dispone di un cluster di debug attivo, è possibile verificare la logica facendo clic su **Aggiorna** per visualizzare l'output delle espressioni rispetto agli input utilizzati. Esiste più di una risposta giusta su come eseguire questa logica usando il linguaggio delle espressioni del flusso di dati.

        ![Espressione di filtro](media/tutorial-data-flow-private/filter-expression.png)

    * Al termine dell'espressione, fare clic su **Salva e fine** .

1. Recuperare un' **anteprima dei dati** per verificare che il filtro funzioni correttamente.

    ![Filtra Anteprima dati](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Aggiungere la trasformazione aggregazione

1. La trasformazione successiva che verrà aggiunta è una trasformazione **aggregazione** nel **modificatore dello schema**.

    ![Aggiungi aggregazione](media/tutorial-data-flow-private/add-aggregate.png)
1. Assegnare un nome alla trasformazione aggregazione **AggregateComedyRatings**. Nella scheda **raggruppa** per selezionare **anno** dall'elenco a discesa per raggruppare le aggregazioni in base all'anno in cui si è rivelato il film.

    ![Gruppo aggregato](media/tutorial-data-flow-private/group-by-year.png)
1. Passare alla scheda **aggregazioni** . Nella casella di testo a sinistra, denominare la colonna aggregata **AverageComedyRating**. Fare clic sulla casella espressione a destra per immettere l'espressione di aggregazione tramite Generatore di espressioni.

    ![Nome colonna aggregata](media/tutorial-data-flow-private/name-column.png)
1. Per ottenere la media della **classificazione**di colonna, utilizzare la ```avg()``` funzione di aggregazione. Poiché **rating** è una stringa e ```avg()``` accetta un input numerico, è necessario convertire il valore in un numero tramite la ```toInteger()``` funzione. Questa espressione è simile a quanto segue:

    ```avg(toInteger(Rating))```

    Al termine, fare clic su **Salva e fine** .

    ![Salva aggregazione](media/tutorial-data-flow-private/save-aggregate.png)
1. Passare alla scheda **Anteprima dati** per visualizzare l'output della trasformazione. Si noti che sono presenti solo due colonne, **year** e **AverageComedyRating**.

### <a name="add-the-sink-transformation"></a>Aggiungere la trasformazione sink

1. Successivamente, si desidera aggiungere una trasformazione **sink** in **destinazione**.

    ![Aggiungi sink](media/tutorial-data-flow-private/add-sink.png)
1. Assegnare un nome al **sink**di sink. Fare clic su **nuovo** per creare il set di dati del sink.

    ![Crea sink](media/tutorial-data-flow-private/create-sink.png)
1. Nella pagina nuovo set di dati scegliere **Azure Data Lake storage Gen2**. Fare clic su Continue.

1. Nella pagina Seleziona formato scegliere **DelimitedText**. Fare clic su Continue.

1. Assegnare al set di dati del sink il nome **MoviesSink**. Per servizio collegato scegliere lo stesso servizio collegato ADLSGen2 creato per la trasformazione di origine. Immettere una cartella di output in cui scrivere i dati. In questa esercitazione si sta scrivendo nella cartella "output" nel contenitore "Sample-Data". La cartella non deve esistere in anticipo e può essere creata dinamicamente. Impostare la **prima riga come intestazione** su true e selezionare **None** per **Importa schema**. Fare clic su OK.

    ![Percorso sink](media/tutorial-data-flow-private/sink-file-path.png)

A questo punto è stata completata la creazione del flusso di dati. Si è pronti per eseguirlo nella pipeline.

## <a name="running-and-monitoring-the-data-flow"></a>Esecuzione e monitoraggio del flusso di dati

È possibile eseguire il debug di una pipeline prima di pubblicarla. In questo passaggio verrà avviata un'esecuzione del debug della pipeline del flusso di dati. Sebbene l'anteprima dei dati non scriva i dati, un'esecuzione del debug scriverà i dati nella destinazione del sink.

1. Passare all'area di disegno della pipeline. Fare clic su **debug** per attivare un'esecuzione del debug.

1. Il debug della pipeline delle attività del flusso di dati usa il cluster di debug attivo, ma occorre ancora almeno un minuto per l'inizializzazione. È possibile tenere traccia dello stato di avanzamento tramite la scheda **output** . Una volta completata l'esecuzione, fare clic sull'icona degli occhiali per i dettagli dell'esecuzione.

1. Nella pagina dei dettagli è possibile visualizzare il numero di righe e il tempo impiegato in ogni passaggio della trasformazione.

    ![Esecuzione del monitoraggio](media/tutorial-data-flow-private/run-details.png)
1. Fare clic su una trasformazione per ottenere informazioni dettagliate sulle colonne e il partizionamento dei dati.

Se questa esercitazione è stata eseguita correttamente, è necessario avere scritto 83 righe e 2 colonne nella cartella del sink. È possibile verificare che i dati siano corretti controllando l'archivio BLOB.

## <a name="summary"></a>Summary

In questa esercitazione è stata usata l'interfaccia utente di Azure Data Factory per creare una pipeline che copia e trasforma i dati **da Azure Data Lake storage un'origine Gen2 (ADLS) a un sink ADLS Gen2 (sia per consentire l'accesso solo alle reti selezionate)** usando il flusso di dati di mapping in [Azure Data Factory rete virtuale gestita](managed-virtual-network-private-endpoint.md).
