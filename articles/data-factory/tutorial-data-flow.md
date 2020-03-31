---
title: Trasformare i dati usando un flusso di dati di mappingTransform data using a mapping data flow
description: Questa esercitazione fornisce istruzioni dettagliate per l'uso di Azure Data Factory per trasformare i dati con il mapping del flusso di dati
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: e6ca8007a96cc63b51b4f79b69029cbf0799e71c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979190"
---
# <a name="transform-data-using-mapping-data-flows"></a>Trasformare i dati usando i flussi di dati di mappingTransform data using mapping data flows

Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

In questa esercitazione si userà l'interfaccia utente di Azure Data Factory per creare una pipeline che copia e trasforma i dati da un'origine Generazione 2 di Azure Data Lake Storage (ADLS) a un sink DI ADLS Gen2 usando il flusso di dati di mapping. Il modello di configurazione in questa esercitazione può essere espanso quando si trasformano i dati usando il mapping del flusso di datiThe configuration pattern in this tutorial can be expanded on when transforming data using mapping data flow

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare una pipeline con un'attività Flusso di dati.
> * Creare un flusso di dati di mapping con quattro trasformazioni.
> * Eseguire test della pipeline.
> * Monitorare un'attività del flusso di datiMonitor a Data Flow activity

## <a name="prerequisites"></a>Prerequisiti
* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure.** L'archiviazione ADLS viene utilizzata come archivi dati di *origine* e *sink.* Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md) per informazioni su come crearne uno.

Il file che stiamo trasformando in questo tutorial è MoviesDB.csv, che può essere trovato [qui](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Per recuperare il file da GitHub, copiare il contenuto in un editor di testo di propria scelta da salvare localmente come file CSV. Per caricare il file nell'account di archiviazione, vedere Caricare BLOB con il portale di Azure.To upload the file to your storage account, see [Upload blobs with the Azure Portal.](../storage/blobs/storage-quickstart-blobs-portal.md) Gli esempi faranno riferimento a un contenitore denominato "sample-data".

## <a name="create-a-data-factory"></a>Creare una data factory

In questo passaggio si crea una data factory e si apre l'esperienza utente di Data Factory per creare una pipeline nella data factory.

1. Aprire **Microsoft Edge** o **Google Chrome**. Attualmente, l'interfaccia utente di Data Factory è supportata solo nei browser Web Microsoft Edge e Google Chrome.
2. Nel menu a sinistra selezionare **Crea una risorsa** > **Analytics** > **Data Factory:**

   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**.

   Il nome della data factory di Azure deve essere *univoco a livello globale.* Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory. Ad esempio, nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere [Azure Data factory - Regole di denominazione](naming-rules.md).

     ![Nuova data factory](./media/doc-common-process/name-not-available-error.png)
4. Selezionare la **sottoscrizione** di Azure in cui creare la data factory.
5. In **Gruppo di risorse** eseguire una di queste operazioni:

    a. Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa.

    b. Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse. 
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/overview.md). 
6. In **Versione** selezionare **V2**.
7. In **Località** selezionare una località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (ad esempio, Archiviazione di Azure e il database SQL) e i calcoli (ad esempio, Azure HDInsight) usati dalla data factory possono trovarsi in altre aree.
8. Selezionare **Crea**.
9. Al termine della creazione, la relativa notifica verrà visualizzata nel centro notifiche. Selezionare **Vai alla risorsa** per passare alla pagina della data factory.
10. Selezionare **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Creare una pipeline con un'attività Flusso di datiCreate a pipeline with a Data Flow activity

In questo passaggio verrà creata una pipeline contenente un'attività Flusso di dati.

1. Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline).

   ![Creare una pipeline](./media/doc-common-process/get-started-page.png)

1. Nella scheda **Generale** della pipeline immettere **TransformMovies** per **Nome** della pipeline.
1. Nella barra superiore della fabbrica, far scorrere il dispositivo di scorrimento del **debug Flusso di dati.** La modalità di debug consente il test interattivo della logica di trasformazione in un cluster Spark attivo. I cluster di flusso di dati richiedono 5-7 minuti per il riscaldamento e si consiglia agli utenti di attivare prima il debug se prevedono lo sviluppo di flussi di dati. Per ulteriori informazioni, vedere [Modalità debug](concepts-data-flow-debug-mode.md).

    ![Attività flusso di datiData Flow Activity](media/tutorial-data-flow/dataflow1.png)
1. Nel riquadro **Attività,** espandere la fisarmonica **Sposta e trasforma.** Trascinare l'attività **Flusso di dati** dal riquadro all'area di disegno della pipeline.

    ![Attività flusso di datiData Flow Activity](media/tutorial-data-flow/activity1.png)
1. Nel popup **Aggiunta flusso** di dati selezionare Crea nuovo flusso di **dati** e quindi assegnare al flusso di dati il nome **TransformMovies**. Fare clic su Fine al termine.

    ![Attività flusso di datiData Flow Activity](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Creare la logica di trasformazione nell'area di disegno del flusso di datiBuild transformation logic in the data flow canvas

Dopo aver creato il flusso di dati, si verrà inviati automaticamente all'area di disegno del flusso di dati. In questo passaggio verrà creato un flusso di dati che accetta moviesDB.csv nell'archiviazione ADLS e aggrega la valutazione media delle commedie dal 1910 al 2000. Questo file verrà quindi riscritto nell'archivio ADLS.

1. Nell'area di disegno del flusso di dati aggiungere un'origine facendo clic sulla casella **Aggiungi origine.**

    ![Area di disegno flusso di datiData Flow Canvas](media/tutorial-data-flow/dataflow2.png)
1. Denominare l'origine **MoviesDB**. Fare clic su **Nuovo** per creare un nuovo set di dati di origine.

    ![Area di disegno flusso di datiData Flow Canvas](media/tutorial-data-flow/dataflow3.png)
1. Scegliere **Azure Data Lake Storage Gen2**. Fare clic su Continue.

    ![Set di dati](media/tutorial-data-flow/dataset1.png)
1. Scegliere **DelimitedText**. Fare clic su Continue.

    ![Set di dati](media/tutorial-data-flow/dataset2.png)
1. Assegnare al dataset il nome del set di dati **MoviesDB**. Nell'elenco a discesa del servizio collegato scegliere **Nuovo**.

    ![Set di dati](media/tutorial-data-flow/dataset3.png)
1. Nella schermata di creazione del servizio collegato assegnare al servizio collegato il nome ADLS gen2 ADLSGen2 il nome del servizio collegato **ADLSGen2** e specificare il metodo di autenticazione. Immettere quindi le credenziali di connessione. In questa esercitazione viene usata la chiave Account per connettersi all'account di archiviazione. È possibile fare clic su **Test connessione** per verificare che le credenziali siano state immesse correttamente. Al termine, fare clic su Crea.

    ![Servizio collegato](media/tutorial-data-flow/ls1.png)
1. Una volta che si torna alla schermata di creazione del set di dati, immettere la posizione del file nel campo **Percorso file.** In questa esercitazione il file moviesDB.csv si trova nel contenitore sample-data. Poiché il file contiene intestazioni, **selezionare Prima riga come intestazione**. Selezionare **Da connessione/archivio** per importare lo schema di intestazione direttamente dal file nell'archivio. Fare clic su OK al termine dell'operazione.

    ![Set di dati](media/tutorial-data-flow/dataset4.png)
1. Se il cluster di debug è stato avviato, passare alla scheda **Anteprima dati** della trasformazione di origine e fare clic su **Aggiorna** per ottenere uno snapshot dei dati. È possibile utilizzare l'anteprima dei dati per verificare che la trasformazione sia configurata correttamente.

    ![Area di disegno flusso di datiData Flow Canvas](media/tutorial-data-flow/dataflow4.png)
1. Accanto al nodo di origine nell'area di disegno del flusso di dati fare clic sull'icona più per aggiungere una nuova trasformazione. La prima trasformazione che si sta aggiungendo è un **filtro**.

    ![Area di disegno flusso di datiData Flow Canvas](media/tutorial-data-flow/dataflow5.png)
1. Assegnare alla trasformazione del filtro il nome **FilterYears**. Fare clic sulla casella di espressione accanto a **Filtra in** base per aprire il generatore di espressioni. Qui è possibile specificare la condizione di filtro.

    ![Filtro](media/tutorial-data-flow/filter1.png)
1. Il generatore di espressioni del flusso di dati consente di compilare in modo interattivo espressioni da usare in varie trasformazioni. Le espressioni possono includere funzioni predefinite, colonne dallo schema di input e parametri definiti dall'utente. Per ulteriori informazioni su come compilare espressioni, vedere Generatore di [espressioni flusso](concepts-data-flow-expression-builder.md)di dati .

    In questa esercitazione, si desidera filtrare i film di commedia di genere che è uscito tra gli anni 1910 e 2000. Poiché anno è attualmente una stringa, è necessario ```toInteger()``` convertirlo in un numero intero utilizzando la funzione. Utilizzare gli operatori maggiore o uguale a (>) e minore o uguale a (<) per confrontare con i valori letterali dell'anno 1910 e 200-. Unire queste espressioni con l'operatore e (&&). L'espressione viene fuori come:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Per trovare quali film sono commedie, ```rlike()``` è possibile utilizzare la funzione per trovare modello 'Commedia' nei generi di colonna. Unione l'espressione rlike con il confronto anno per ottenere:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Se è attivo un cluster di debug, è possibile verificare la logica facendo clic su **Aggiorna** per visualizzare l'output dell'espressione rispetto agli input usati. C'è più di una risposta corretta su come è possibile eseguire questa logica utilizzando il linguaggio delle espressioni del flusso di dati.

    ![Filtro](media/tutorial-data-flow/filter2.png)

    Al termine dell'espressione, fare clic su **Salva e chiudi.**

1. Recuperare **un'anteprima dei dati** per verificare che il filtro funzioni correttamente.

    ![Filtro](media/tutorial-data-flow/filter3.png)
1. La trasformazione successiva che verrà aggiunta è una trasformazione **Aggregazione** in **Modificatore schema**.

    ![Aggregate](media/tutorial-data-flow/agg1.png)
1. Assegnare alla trasformazione di aggregazione il nome **AggregateComedyRatings**. Nella scheda **Raggruppa per,** selezionare **anno** dall'elenco a discesa per raggruppare le aggregazioni in base all'anno in cui il filmato è uscito.

    ![Aggregate](media/tutorial-data-flow/agg2.png)
1. Passare alla scheda **Aggregazioni.** Nella casella di testo sinistra assegnare alla colonna di aggregazione il nome **AverageComedyRating**. Fare clic sulla casella di espressione destra per immettere l'espressione di aggregazione tramite il generatore di espressioni.

    ![Aggregate](media/tutorial-data-flow/agg3.png)
1. Per ottenere la **Rating**media della ```avg()``` colonna Rating , utilizzare la funzione di aggregazione. Poiché **Rating** è ```avg()``` una stringa e accetta un input numerico, ```toInteger()``` dobbiamo convertire il valore in un numero tramite la funzione. Questa è l'espressione è simile alla seguente:This is expression looks like:

    ```avg(toInteger(Rating))```

    Al termine, fare clic su **Salva e chiudi.**

    ![Aggregate](media/tutorial-data-flow/agg4.png)
1. Passare alla scheda **Anteprima dati** per visualizzare l'output della trasformazione. Si noti che sono presenti solo due colonne, **year** e **AverageComedyRating**.

    ![Aggregate](media/tutorial-data-flow/agg3.png)
1. Successivamente, si desidera aggiungere una trasformazione **Sink** in **Destinazione**.

    ![Sink](media/tutorial-data-flow/sink1.png)
1. Assegna un nome al **lavandino Sink**. Fare clic su **Nuovo** per creare il set di dati sink.

    ![Sink](media/tutorial-data-flow/sink2.png)
1. Scegliere **Azure Data Lake Storage Gen2**. Fare clic su Continue.

    ![Set di dati](media/tutorial-data-flow/dataset1.png)
1. Scegliere **DelimitedText**. Fare clic su Continue.

    ![Set di dati](media/tutorial-data-flow/dataset2.png)
1. Denominare il dataset sink **MoviesSink**. Per il servizio collegato, scegliere il servizio collegato ADLS gen2 creato nel passaggio 6.For linked service, choose the ADLS gen2 linked service you created in step 6. Immettere una cartella di output in cui scrivere i dati. In questa esercitazione stiamo scrivendo nella cartella 'output' nel contenitore 'sample-data'. La cartella non deve esistere in anticipo e può essere creata dinamicamente. Impostare **Prima riga come intestazione** come true e **selezionare Nessuno** per Importa **schema**. Fare clic su Fine.

    ![Sink](media/tutorial-data-flow/sink3.png)

A questo punto è stata completata la creazione del flusso di dati. È possibile eseguirlo nella pipeline.

## <a name="running-and-monitoring-the-data-flow"></a>Esecuzione e monitoraggio del flusso di datiRunning and monitoring the Data Flow

È possibile eseguire il debug di una pipeline prima di pubblicarla. In questo passaggio verrà attivata un'esecuzione di debug della pipeline del flusso di dati. Mentre l'anteprima dei dati non scrive dati, un'esecuzione di debug scriverà i dati nella destinazione del sink.

1. Passare all'area di disegno della tubazione. Fare clic su **Debug** per attivare un'esecuzione di debug.

    ![Pipeline](media/tutorial-data-flow/pipeline1.png)
1. Il debug della pipeline delle attività flusso di dati usa il cluster di debug attivo ma richiede comunque almeno un minuto per l'inizializzazione. È possibile tenere traccia dello stato di avanzamento tramite la scheda **Output.** Una volta eseguita correttamente, fare clic sull'icona degli occhiali per aprire il riquadro di monitoraggio.

    ![Pipeline](media/tutorial-data-flow/pipeline2.png)
1. Nel riquadro di monitoraggio è possibile visualizzare il numero di righe e il tempo trascorso in ogni passaggio di trasformazione.

    ![Monitoraggio](media/tutorial-data-flow/pipeline3.png)
1. Fare clic su una trasformazione per ottenere informazioni dettagliate sulle colonne e sul partizionamento dei dati.

    ![Monitoraggio](media/tutorial-data-flow/pipeline4.png)

Se questa esercitazione è stata seguita correttamente, è necessario aver scritto 83 righe e 2 colonne nella cartella del sink. È possibile verificare che i dati siano corretti controllando l'archiviazione BLOB.

## <a name="next-steps"></a>Passaggi successivi

La pipeline in questa esercitazione esegue un flusso di dati che aggrega la classificazione media delle commedie da 1910 a 2000 e scrive i dati in ADLS. Si è appreso come:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare una pipeline con un'attività Flusso di dati.
> * Creare un flusso di dati di mapping con quattro trasformazioni.
> * Eseguire test della pipeline.
> * Monitorare un'attività del flusso di datiMonitor a Data Flow activity

Ulteriori informazioni sul [linguaggio](data-flow-expression-functions.md)delle espressioni del flusso di dati .
