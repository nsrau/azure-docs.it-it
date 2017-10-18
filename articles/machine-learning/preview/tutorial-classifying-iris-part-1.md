---
title: Esercitazione per la preparazione dei dati per la classificazione con il set di dati Iris nei servizi Machine Learning (anteprima) | Microsoft Docs
description: "Questa esaustiva esercitazione illustra come usare i servizi di Azure Machine Learning (anteprima) end-to-end. Questa è la Parte 1, che illustra la preparazione dei dati."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/28/2017
ms.openlocfilehash: 975a86c1f9d9692f6eadd232177f33cbbbeeff2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="classifying-iris-part-1-prepare-data"></a>Classificazione di dati Iris - Parte 1: Preparare i dati
I servizi di Azure Machine Learning (anteprima) sono una soluzione integrata di analisi scientifica dei dati e analisi avanzata end-to-end con cui i data scientist professionisti possono preparare i dati, sviluppare esperimenti e distribuire modelli su scala cloud.

Questa esercitazione è la prima di una serie in tre parti. In questa esercitazione sono disponibili informazioni dettagliate sulle nozioni di base dei servizi di Azure Machine Learning (anteprima). In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Creare un progetto in Azure Machine Learning Workbench
> * Creare un pacchetto di preparazione dei dati
> * Generare codice Python/PySpark per richiamare il pacchetto di preparazione dei dati

Questa esercitazione usa il sempre attuale [set di dati dei fiori Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) per presentare l'argomento in modo semplice. Gli screenshot sono specifici di Windows, ma l'esperienza in macOS è praticamente identica.

## <a name="prerequisites"></a>Prerequisiti
- Creare un account di Sperimentazione di Azure Machine Learning
- Installare Azure Machine Learning Workbench

Per installare l'applicazione Azure Machine Learning Workbench, seguire la [guida introduttiva per l'installazione e la creazione](quickstart-installation.md). L'installazione include anche l'interfaccia della riga di comando.

## <a name="create-a-new-project-in-azure-ml-workbench"></a>Creare un nuovo progetto in Azure Machine Learning Workbench
1. Avviare l'app Azure Machine Learning Workbench ed eseguire l'accesso, se necessario. Nel riquadro **PROGETTI** fare clic sull'icona **+** per creare un **Nuovo progetto**.

   ![Nuova area di lavoro](media/tutorial-classifying-iris/new_ws.png)

2. Specificare i dettagli per **Crea nuovo progetto**. 

   ![Nuovo progetto](media/tutorial-classifying-iris/new_project.png)

   - Nel campo **Nome progetto** specificare un nome per il progetto. Usare ad esempio il valore **myIris**.
   - Scegliere la **Directory del progetto** in cui viene creato il progetto. Usare ad esempio il valore **C:\Temp**. 
   - Immettere una **Descrizione del progetto**. 
   - Il campo **Repository Git** è facoltativo e può essere lasciato vuoto. È possibile specificare un repository Git vuoto esistente, ovvero un repository privo di ramo master, in Visual Studio Team Services. Questo approccio consente di abilitare in seguito scenari di roaming e di condivisione. Per altre informazioni, vedere l'articolo [Using Git repo](using-git-ml-project.md) (Uso di un repository Git). 
   - Scegliere un'**Area di lavoro**. Questa esercitazione, ad esempio, usa **IrisGarden**. 
   - Selezionare il modello **Classifying Iris** dall'elenco di modelli per il progetto. 

3. Fare clic sul pulsante **Crea** per creare il progetto. Il progetto viene creato e viene aperto automaticamente.

## <a name="create-a-data-preparation-package"></a>Creare un pacchetto di preparazione dei dati
1. Aprire il file **iris.csv** dalla **visualizzazione file**. Il file è una semplice tabella con 5 colonne e 150 righe. Include quattro colonne con funzionalità numeriche e una colonna di destinazione di tipo stringa. Non include intestazioni di colonna.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Non è consigliabile includere file di dati nella cartella di progetto, soprattutto se le dimensioni del file sono elevate. Il file **iris.csv** è incluso in questo modello per finalità dimostrative perché di dimensioni molto ridotte. Per altre informazioni, vedere l'articolo [How to read and write large data files](how-to-read-write-files.md) (Come leggere e scrivere file di dati di grandi dimensioni).

2. Nella **Visualizzazione dati** fare clic sull'icona **+** per aggiungere una nuova origine dati. Viene avviata la procedura guidata **Aggiungi origine dati**. 

   ![Visualizzazione dati](media/tutorial-classifying-iris/data_view.png)

3. Completare la preparazione guidata dei dati. 
   - Nella prima schermata selezionare l'opzione **File(s)/Directory** (File/Directory) e quindi fare clic su **Avanti**.
   - Nella seconda schermata scegliere il file locale **iris.csv**, ad esempio "C:\Temp\myIris\iris.csv".
   - Nella terza schermata **File Details** (Dettagli file) accettare i valori predefiniti.
   - Nella quarta schermata **Tipi di dati** modificare **TIPO DI DATI** da _Stringa_ a _Numerico_ per le colonne da _Column1_ a _Column4_, perché contengono valori numerici. 
   - Nella quinta e nella sesta schermata accettare le impostazioni predefinite.
   - Fare clic sul pulsante **Fine**.

   ![Selezionare iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Per questo esercizio assicurarsi di selezionare il file **iris.csv** dall'interno della directory di progetto corrente. In caso contrario, i passaggi successivi potrebbero avere esito negativo. 

4. Verrà creato un nuovo file **iris-1.dsource**. Il file viene denominato in modo univoco con un trattino e il numero 1 perché il progetto di esempio include già un file **iris.dsource** non numerato.  

   Il file viene aperto e vengono visualizzati i dati. Al set di dati viene aggiunta automaticamente una serie di intestazioni di colonna, da **Column1** a **Column5**. Scorrere verso il basso e notare che l'ultima riga del set di dati è vuota. Ciò è dovuto alla presenza di un'interruzione di riga aggiuntiva nel file con estensione csv.

   ![Visualizzazione dati iris](media/tutorial-classifying-iris/iris_data_view.png)

5. Fare ora clic sul pulsante **Metriche**. Osservare gli istogrammi e un set completo di statistiche calcolate automaticamente per ogni colonna. È anche possibile fare clic sul pulsante **Dati** per visualizzare di nuovo i dati. 

   ![Visualizzazione dati iris](media/tutorial-classifying-iris/iris_metrics_view.png)

6. Fare clic sul pulsante **Prepara**. Viene visualizzata la finestra di dialogo **Prepara**. 

   Il progetto di esempio include già un file **iris.dprep**. Per impostazione predefinita viene quindi richiesto di creare un nuovo flusso di dati nel pacchetto di preparazione dei dati **iris.dprep** esistente. 

   Modificare il valore dell'elenco a discesa in **+New Data Preparation Package** (Nuovo pacchetto di preparazione dei dati), quindi immettere un nuovo valore "iris-1" e infine fare clic su **OK**.

   ![Visualizzazione dati Iris](media/tutorial-classifying-iris/new_dprep.png)

   Un nuovo pacchetto di preparazione dei dati denominato **iris-1.dprep** verrà creato e aperto nell'editor di preparazione dei dati.

7. È ora possibile eseguire alcune operazioni di preparazione dei dati di base. Rinominare le colonne facendo clic su ogni intestazione di colonna e rendendo modificabile il testo dell'intestazione. 

   Per le cinque colonne, immettere rispettivamente **Sepal Length**, **Sepal Width**, **Petal Length**, **Petal Width** e **Species**.

   ![Rinominare le colonne](media/tutorial-classifying-iris/rename_column.png)

8. Per conteggiare valori distinti, selezionare la colonna **Species** e fare clic con il pulsante destro del mouse. Scegliere **Value Counts** (Conteggi dei valori). 

   ![Fare clic su Value Counts (Conteggi dei valori)](media/tutorial-classifying-iris/value_count.png)

   Verrà così visualizzato il riquadro **Inspectors** (Controlli), contenente un istogramma con quattro barre. Si noti che la colonna di destinazione contiene i tre valori distinti **Iris_virginica**, **Iris_versicolor** e **Iris-setosa** e un valore **(Null)**.

9. Filtrare i valori Null selezionando la barra del grafico che rappresenta Null. È presente una riga con un valore **(Null)**. Per eliminare questa riga, fare clic sul pulsante di filtro **-**.

   ![Istogramma dei conteggi dei valori](media/tutorial-classifying-iris/filter_out.png)

10. Si notino i singoli passaggi riportati in dettaglio nel riquadro **PASSAGGI**. Durante le operazioni per rinominare le colonne e filtrare le righe con valore Null, ogni azione è stata registrata come passaggio di preparazione dei dati. È possibile modificare singoli passaggi per modificare le impostazioni, riordinare i passaggi e anche rimuoverli.

   ![Passi](media/tutorial-classifying-iris/steps.png)

11. Chiudere quindi l'editor di preparazione dei dati facendo clic su **X** nella scheda denominata **iris-1** con l'icona a forma di grafico. Il lavoro viene salvato automaticamente nel file **iris-1.dprep** visualizzato sotto l'intestazione **Data Preparations** (Preparazioni dati).

## <a name="generate-pythonpyspark-code-to-invoke-data-prep-package"></a>Generare codice Python/PySpark per richiamare il pacchetto di preparazione dei dati

1. Fare clic con il pulsante destro del mouse sul file **iris-1.dprep** per visualizzare il menu di scelta rapida, quindi scegliere **Generate Data Access Code File** (Genera file di codice per l'accesso ai dati). 

   ![Generare codice](media/tutorial-classifying-iris/generate_code.png)

2. Verrà aperto un nuovo file denominato **iris-1.py** con le righe di codice seguenti:

   ```python
   # This code snippet will load the referenced package and return a DataFrame.
   # If the code is run in a PySpark environment, the code will return a
   # Spark DataFrame. If not, the code will return a Pandas DataFrame.

   from azureml.dataprep.package import run
   df = run('iris.dprep', dataflow_idx=0)
   ```

   Questo frammento di codice richiama la logica creata come pacchetto di preparazione dei dati. `df` rappresenta diverse tipologie di frame di dati a seconda del contesto in cui questo codice viene eseguito. In caso di esecuzione in un runtime Python viene usato un [frame di dati pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html), mentre in caso di esecuzione in un contesto Spark viene usato un [frame di dati Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html). 

   Per altre informazioni sulla preparazione dei dati in Azure Machine Learning Workbench, vedere la guida [Getting Started with Data Preparation](data-prep-getting-started.md) (Introduzione alla preparazione dei dati).

## <a name="next-steps"></a>Passaggi successivi
In questa prima parte della serie di esercitazioni in tre parti Azure Machine Learning Workbench è stato usato per:
> [!div class="checklist"]
> * Creare un nuovo progetto 
> * Creare un pacchetto di preparazione dei dati
> * Generare codice Python/PySpark per richiamare il pacchetto di preparazione dei dati

È ora possibile passare alla parte successiva della serie per creare un modello di Machine Learning.
> [!div class="nextstepaction"]
> [Creare un modello](tutorial-classifying-iris-part-2.md)
