---
title: Trasformare i dati usando un flusso di dati di mapping in Azure Data Factory | Microsoft Docs
description: In questa esercitazione vengono fornite istruzioni dettagliate per l'utilizzo di Azure Data Factory per trasformare i dati con il flusso di dati di mapping
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 8c9043db8159e2b7ff6520e9525472048cf73ae1
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031279"
---
# <a name="transform-data-using-mapping-data-flows"></a>Trasformare i dati usando il mapping dei flussi di dati

Se non si ha familiarità con Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

In questa esercitazione si userà l'interfaccia utente di Azure Data Factory per creare una pipeline che copia e trasforma i dati da Azure Data Lake Storage un'origine Gen2 (ADLS) a un sink ADLS Gen2 usando il flusso di dati di mapping. Il modello di configurazione in questa esercitazione può essere espanso quando si trasformano i dati utilizzando il flusso di dati di mapping

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare una pipeline con un'attività flusso di dati.
> * Compilare un flusso di dati di mapping con quattro trasformazioni. 
> * Eseguire test della pipeline.
> * Monitorare un'attività flusso di dati

## <a name="prerequisites"></a>Prerequisiti
* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**. Si usa l'archiviazione ADLS come archivi dati di *origine* e *sink* . Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](../storage/common/storage-quickstart-create-account.md) per informazioni su come crearne uno.

Il file che si sta trasformando in questa esercitazione è MoviesDB. csv, disponibile [qui](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Per recuperare il file da GitHub, copiare il contenuto in un editor di testo di propria scelta per salvarlo localmente come file con estensione CSV. Per caricare il file nell'account di archiviazione, vedere [caricare BLOB con il portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md). Gli esempi faranno riferimento a un contenitore denominato "Sample-Data".

## <a name="create-a-data-factory"></a>Creare una data factory

In questo passaggio si crea una data factory e si apre l'Data Factory UX per creare una pipeline nell'data factory. 

1. Aprire **Microsoft Edge** o **Google Chrome**. Attualmente, Data Factory interfaccia utente è supportata solo nei Web browser Microsoft Edge e Google Chrome.
2. Nel menu a sinistra selezionare **Crea una risorsa** > **Analytics** > **Data Factory**: 
  
   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**. 
 
   Il nome della data factory di Azure deve essere *univoco a livello globale*. Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory. Ad esempio, nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere [Azure Data factory - Regole di denominazione](naming-rules.md).
        
     ![Nuova data factory](./media/doc-common-process/name-not-available-error.png)
4. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
5. In **Gruppo di risorse** eseguire una di queste operazioni:
     
    a. Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa.

    b. Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse. 
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/resource-group-overview.md). 
6. In **Versione** selezionare **V2**.
7. In **Località** selezionare una località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (ad esempio, archiviazione di Azure e il database SQL) e le risorse di calcolo (ad esempio, Azure HDInsight) usati dal data factory possono trovarsi in altre aree.
8. Selezionare **Create**. 
9. Al termine della creazione, la relativa notifica verrà visualizzata nel centro notifiche. Selezionare **Vai alla risorsa** per passare alla pagina della data factory.
10. Selezionare **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Creare una pipeline con un'attività flusso di dati

In questo passaggio verrà creata una pipeline che contiene un'attività flusso di dati.

1. Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline). 

   ![Creare una pipeline](./media/doc-common-process/get-started-page.png)

1. Nella scheda **generale** della pipeline immettere **TransformMovies** per **nome** della pipeline.
1. Nella barra superiore della Factory scorrere il dispositivo di scorrimento **debug del flusso di dati** . La modalità di debug consente il test interattivo della logica di trasformazione in un cluster Spark attivo. I cluster di flussi di dati impiegano 5-7 minuti per il riscaldamento e si consiglia agli utenti di attivare prima il debug se pianificano lo sviluppo del flusso di dati. Per altre informazioni, vedere [modalità di debug](concepts-data-flow-debug-mode.md).

    ![Attività flusso di dati](media/tutorial-data-flow/dataflow1.png)
1. Nel riquadro **attività** espandere l'accordo di **spostamento e trasformazione** . Trascinare e rilasciare l'attività **flusso di dati** dal riquadro nell'area di disegno della pipeline.

    ![Attività flusso di dati](media/tutorial-data-flow/activity1.png)
1. Nella finestra popup **aggiunta flusso di dati** selezionare **Crea nuovo flusso di dati** e quindi assegnare un nome al flusso di dati **TransformMovies**. Al termine, fare clic su fine.

    ![Attività flusso di dati](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Logica di trasformazione compilazione nell'area di disegno del flusso di dati

Dopo aver creato il flusso di dati, verrà inviato automaticamente all'area di disegno del flusso di dati. In questo passaggio verrà compilato un flusso di dati che accetta il moviesDB. CSV nell'archiviazione ADLS e aggrega la valutazione media di Comedie da 1910 a 2000. Il file verrà quindi scritto nuovamente nell'archivio ADLS.

1. Nell'area di disegno del flusso di dati aggiungere un'origine facendo clic sulla casella **Aggiungi origine** .

    ![Area di disegno del flusso di dati](media/tutorial-data-flow/dataflow2.png)
1. Assegnare un nome all'origine **MoviesDB**. Fare clic su **nuovo** per creare un nuovo set di dati di origine.
    
    ![Area di disegno del flusso di dati](media/tutorial-data-flow/dataflow3.png)
1. Scegliere **Azure Data Lake storage Gen2**. Fare clic su Continue.

    ![Set di dati](media/tutorial-data-flow/dataset1.png)
1. Scegliere **DelimitedText**. Fare clic su Continue.

    ![Set di dati](media/tutorial-data-flow/dataset2.png)
1. Denominare il set di dati **MoviesDB**. Nell'elenco a discesa servizio collegato scegliere **nuovo**.

    ![Set di dati](media/tutorial-data-flow/dataset3.png)
1. Nella schermata di creazione del servizio collegato denominare il servizio collegato ADLS Gen2 **ADLSGen2** e specificare il metodo di autenticazione. Immettere quindi le credenziali di connessione. In questa esercitazione viene usata la chiave dell'account per connettersi all'account di archiviazione. È possibile fare clic su **Test connessione** per verificare che le credenziali siano state immesse correttamente. Al termine, fare clic su Crea.

    ![Servizio collegato](media/tutorial-data-flow/ls1.png)
1. Quando si torna alla schermata di creazione del set di dati, immettere dove si trova il file nel campo **percorso file** . In questa esercitazione il file moviesDB. csv si trova in container Sample-Data. Quando il file contiene intestazioni, controllare la **prima riga come intestazione**. Selezionare **da connessione/archivio** per importare lo schema dell'intestazione direttamente dal file nella risorsa di archiviazione. Al termine, fare clic su OK.

    ![Set di dati](media/tutorial-data-flow/dataset4.png)
1. Se il cluster di debug è stato avviato, passare alla scheda **Anteprima dati** della trasformazione origine e fare clic su **Aggiorna** per ottenere uno snapshot dei dati. È possibile utilizzare l'anteprima dati per verificare che la trasformazione sia configurata correttamente.
    
    ![Area di disegno del flusso di dati](media/tutorial-data-flow/dataflow4.png)
1. Accanto al nodo di origine nell'area di disegno del flusso di dati fare clic sull'icona a forma di segno più per aggiungere una nuova trasformazione. La prima trasformazione che si sta aggiungendo è un **filtro**.
    
    ![Area di disegno del flusso di dati](media/tutorial-data-flow/dataflow5.png)
1. Assegnare un nome alla trasformazione filtro **FilterYears**. Fare clic sulla casella espressione accanto a applica **filtro** per aprire Generatore di espressioni. Qui si specificherà la condizione di filtro. 
    
    ![Filtro](media/tutorial-data-flow/filter1.png)
1. Il generatore di espressioni del flusso di dati consente di compilare in modo interattivo espressioni da usare in diverse trasformazioni. Le espressioni possono includere funzioni predefinite, colonne dello schema di input e parametri definiti dall'utente. Per ulteriori informazioni su come compilare espressioni, vedere [Generatore di espressioni del flusso di dati](concepts-data-flow-expression-builder.md).
    
    In questa esercitazione si vogliono filtrare i film di genere Comedy che hanno avuto come risultato tra gli anni 1910 e 2000. Come Year è attualmente una stringa, è necessario convertirla in un Integer usando la funzione ```toInteger()```. Utilizzare gli operatori maggiore di o uguale a (> =) e minore o uguale a (< =) per confrontare i valori letterali anno 1910 e 200-. Unire queste espressioni con l'operatore and (& &). L'espressione viene visualizzata come segue:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Per individuare i film che rappresentano le medie, è possibile usare la funzione ```rlike()``` per trovare il modello "Comedy" nei generi di colonna. Unione dell'espressione rlike con il confronto Year da ottenere:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Se si dispone di un cluster di debug attivo, è possibile verificare la logica facendo clic su **Aggiorna** per visualizzare l'output delle espressioni rispetto agli input utilizzati. Esiste più di una risposta giusta su come eseguire questa logica usando il linguaggio delle espressioni del flusso di dati.
    
    ![Filtro](media/tutorial-data-flow/filter2.png)

    Al termine dell'espressione, fare clic su **Salva e fine** .

1. Recuperare un' **anteprima dei dati** per verificare che il filtro funzioni correttamente.
    
    ![Filtro](media/tutorial-data-flow/filter3.png)
1. La trasformazione successiva che verrà aggiunta è una trasformazione **aggregazione** nel **modificatore dello schema**.
    
    ![Aggregazione](media/tutorial-data-flow/agg1.png)
1. Assegnare un nome alla trasformazione aggregazione **AggregateComedyRatings**. Nella scheda **raggruppa** per selezionare **anno** dall'elenco a discesa per raggruppare le aggregazioni in base all'anno in cui si è rivelato il film.
    
    ![Aggregazione](media/tutorial-data-flow/agg2.png)
1. Passare alla scheda **aggregazioni** . Nella casella di testo a sinistra, denominare la colonna aggregata **AverageComedyRating**. Fare clic sulla casella espressione a destra per immettere l'espressione di aggregazione tramite Generatore di espressioni.
    
    ![Aggregazione](media/tutorial-data-flow/agg3.png)
1. Per ottenere la media della **classificazione**di colonna, utilizzare la funzione di aggregazione ```avg()```. Poiché **rating** è una stringa e ```avg()``` accetta un input numerico, è necessario convertire il valore in un numero tramite la funzione ```toInteger()```. Questa espressione è simile a quanto segue:

    ```avg(toInteger(Rating))```
    
    Al termine, fare clic su **Salva e fine** . 

    ![Aggregazione](media/tutorial-data-flow/agg4.png)
1. Passare alla scheda **Anteprima dati** per visualizzare l'output della trasformazione. Si noti che sono presenti solo due colonne, **year** e **AverageComedyRating**.
    
    ![Aggregazione](media/tutorial-data-flow/agg3.png)
1. Successivamente, si desidera aggiungere una trasformazione **sink** in **destinazione**.
    
    ![Sink](media/tutorial-data-flow/sink1.png)
1. Assegnare un nome al **sink**di sink. Fare clic su **nuovo** per creare il set di dati del sink.
    
    ![Sink](media/tutorial-data-flow/sink2.png)
1. Scegliere **Azure Data Lake storage Gen2**. Fare clic su Continue.

    ![Set di dati](media/tutorial-data-flow/dataset1.png)
1. Scegliere **DelimitedText**. Fare clic su Continue.

    ![Set di dati](media/tutorial-data-flow/dataset2.png)
1. Assegnare al set di dati del sink il nome **MoviesSink**. Per servizio collegato scegliere il servizio collegato ADLS Gen2 creato al passaggio 6. Immettere una cartella di output in cui scrivere i dati. In questa esercitazione si sta scrivendo nella cartella "output" nel contenitore "Sample-Data". La cartella non deve esistere in anticipo e può essere creata dinamicamente. Impostare la **prima riga come intestazione** su true e selezionare **None** per **Importa schema**. Fare clic su Finish.
    
    ![Sink](media/tutorial-data-flow/sink3.png)

A questo punto è stata completata la creazione del flusso di dati. Si è pronti per eseguirlo nella pipeline.

## <a name="running-and-monitoring-the-data-flow"></a>Esecuzione e monitoraggio del flusso di dati

È possibile eseguire il debug di una pipeline prima di pubblicarla. In questo passaggio verrà avviata un'esecuzione del debug della pipeline del flusso di dati. Sebbene l'anteprima dei dati non scriva i dati, un'esecuzione del debug scriverà i dati nella destinazione del sink.

1. Passare all'area di disegno della pipeline. Fare clic su **debug** per attivare un'esecuzione del debug.
    
    ![Pipeline](media/tutorial-data-flow/pipeline1.png)
1. Il debug della pipeline delle attività del flusso di dati usa il cluster di debug attivo, ma occorre ancora almeno un minuto per l'inizializzazione. È possibile tenere traccia dello stato di avanzamento tramite la scheda **output** . Una volta completata l'esecuzione, fare clic sull'icona degli occhiali per aprire il riquadro monitoraggio.
    
    ![Pipeline](media/tutorial-data-flow/pipeline2.png)
1. Nel riquadro monitoraggio è possibile visualizzare il numero di righe e il tempo impiegato in ogni passaggio della trasformazione.
    
    ![Monitoraggio](media/tutorial-data-flow/pipeline3.png)
1. Fare clic su una trasformazione per ottenere informazioni dettagliate sulle colonne e il partizionamento dei dati.
    
    ![Monitoraggio](media/tutorial-data-flow/pipeline4.png)

Se questa esercitazione è stata eseguita correttamente, è necessario avere scritto 83 righe e 2 colonne nella cartella del sink. È possibile verificare che i dati siano corretti controllando l'archivio BLOB.

## <a name="next-steps"></a>Passaggi successivi

La pipeline in questa esercitazione esegue un flusso di dati che aggrega la valutazione media di Comedie da 1910 a 2000 e scrive i dati in ADLS. Si è appreso come:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare una pipeline con un'attività flusso di dati.
> * Compilare un flusso di dati di mapping con quattro trasformazioni. 
> * Eseguire test della pipeline.
> * Monitorare un'attività flusso di dati

Altre informazioni sul [linguaggio delle espressioni del flusso di dati](data-flow-expression-functions.md).