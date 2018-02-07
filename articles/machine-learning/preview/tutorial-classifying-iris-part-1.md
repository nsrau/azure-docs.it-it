---
title: Esercitazione per la preparazione dei dati per la classificazione con il set di dati Iris nei servizi di Azure Machine Learning (anteprima) | Microsoft Docs
description: Questa esaustiva esercitazione illustra come usare i servizi di Azure Machine Learning (anteprima) end-to-end. Questa prima parte illustra la preparazione dei dati.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 09/28/2017
ms.openlocfilehash: 4e558518a5a1fb7b4cd0a58fe2453fd4c083b46a
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="classify-iris-part-1-prepare-the-data"></a>Classificare i dati Iris - Parte 1: Preparare i dati
I servizi di Azure Machine Learning (anteprima) sono una soluzione integrata di data science e analisi avanzata end-to-end con cui i data scientist professionisti possono preparare i dati, sviluppare esperimenti e distribuire modelli su scala cloud.

Questa esercitazione è la prima di una serie in tre parti. In questa esercitazione vengono illustrate le nozioni di base dei servizi di Azure Machine Learning (anteprima). Si apprenderà come:
> [!div class="checklist"]
> * Creare un progetto in Azure Machine Learning Workbench.
> * Creare un pacchetto di preparazione dei dati.
> * Generare codice Python/PySpark per richiamare un pacchetto di preparazione dei dati.

Questa esercitazione usa il sempre attuale [set di dati dei fiori Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set). Gli screenshot sono specifici di Windows, ma l'esperienza in Mac OS è quasi identica.

## <a name="prerequisites"></a>prerequisiti
- Creare un account Sperimentazione di Azure Machine Learning.
- Installare Azure Machine Learning Workbench.

Per installare l'applicazione Azure Machine Learning Workbench, è possibile seguire le istruzioni riportate nella [guida introduttiva per l'installazione e la creazione](quickstart-installation.md). L'installazione include anche l'interfaccia della riga di comando di Azure, lo strumento da riga di comando multipiattaforma di Azure.

## <a name="create-a-new-project-in-azure-machine-learning-workbench"></a>Creare un nuovo progetto in Azure Machine Learning Workbench
1. Aprire l'app Azure Machine Learning Workbench ed eseguire l'accesso, se necessario. Nel riquadro **PROGETTI** selezionare il segno più (**+**) per creare un **nuovo progetto**.

   ![Nuova area di lavoro](media/tutorial-classifying-iris/new_ws.png)

2. Specificare i dettagli in **Create New Project** (Crea nuovo progetto): 

   ![Nuovo progetto](media/tutorial-classifying-iris/new_project.png)

   - Nella casella **Nome progetto** specificare un nome per il progetto. Usare ad esempio il valore **myIris**.
   - Selezionare la **directory di progetto** in cui creare il progetto. Ad esempio, usare il valore `C:\Temp\`. 
   - Immettere una descrizione nel campo facoltativo **Project description** (Descrizione progetto). 
   - Anche il campo **Git Repository** (Repository Git) è facoltativo e può essere lasciato vuoto. È possibile specificare un repository Git vuoto (ossia privo di ramo principale) esistente in Visual Studio Team Services. Se si usa un repository Git già esistente, si possono abilitare scenari di roaming e condivisione in un secondo momento. Per altre informazioni, vedere l'articolo su come [usare un repository Git](using-git-ml-project.md). 
   - Selezionare un'**area di lavoro**. Questa esercitazione, ad esempio, usa **IrisGarden**. 
   - Selezionare il modello **Classifying Iris** dall'elenco di modelli per il progetto. 

3. Selezionare il pulsante **Crea**. Il progetto viene creato e viene aperto automaticamente.

## <a name="create-a-data-preparation-package"></a>Creare un pacchetto di preparazione dei dati
1. Aprire il file **iris.csv** dalla **visualizzazione file**. Il file è una tabella con 5 colonne e 150 righe. Include quattro colonne con funzionalità numeriche e una colonna di destinazione di tipo stringa. Non include intestazioni di colonna.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Non includere file di dati nella cartella di progetto, soprattutto se le dimensioni del file sono elevate. Il file **iris.csv** è incluso in questo modello per finalità dimostrative perché di dimensioni molto ridotte. Per altre informazioni, vedere l'articolo su [come leggere e scrivere file di dati di grandi dimensioni](how-to-read-write-files.md).

2. Nella **visualizzazione dati** selezionare il segno più (**+**) per aggiungere una nuova origine dati. Verrà visualizzata la pagina **Aggiungi origine dati**. 

   ![Visualizzazione dati](media/tutorial-classifying-iris/data_view.png)

3. Selezionare **File di testo (*.csv, .json, .txt.,... )** e fare clic su **Avanti**.
   ![Origine dati](media/tutorial-classifying-iris/data-source.png)
   

4. Cercare il file **iris.csv** e fare clic su **Avanti**.  
 
   ![Selezionare iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Per questo esercizio assicurarsi di selezionare il file **iris.csv** dalla directory di progetto corrente. In caso contrario, i passaggi successivi potrebbero avere esito negativo.
   
5. Lasciare i valori predefiniti e fare clic su **Fine**.

6. Verrà creato un nuovo file denominato **iris-1.dsource**. Il file viene denominato in modo univoco con "-1" perché il progetto di esempio include già un file **iris.dsource** non numerato.  

   Il file verrà aperto e verranno visualizzati i dati. Al set di dati viene aggiunta automaticamente una serie di intestazioni di colonna, da **Column1** a **Column5**. Scorrere verso il basso e notare che l'ultima riga del set di dati è vuota. Ciò è dovuto alla presenta di un'interruzione di riga aggiuntiva nel file CSV.

   ![Visualizzazione dati Iris](media/tutorial-classifying-iris/iris_data_view.png)

7. Selezionare il pulsante **Metriche**. Osservare gli istogrammi. Per ogni colonna è stato calcolato un set completo di statistiche. È anche possibile selezionare il pulsante **Dati** per visualizzare di nuovo i dati. 

   ![Visualizzazione dati Iris](media/tutorial-classifying-iris/iris_metrics_view.png)

8. Selezionare il pulsante **Prepara**. Verrà visualizzata la finestra di dialogo **Prepara**. 

   Il progetto di esempio include già un file **iris.dprep**. Per impostazione predefinita verrà quindi richiesto di creare un nuovo flusso di dati nel pacchetto di preparazione dei dati **iris.dprep** già esistente. 

   Selezionare **+ New Data Preparation Package** (Nuovo pacchetto di preparazione dei dati) nel menu a discesa, immettere **iris-1** come nuovo valore per il nome del pacchetto e quindi selezionare **OK**.

   ![Visualizzazione dati Iris](media/tutorial-classifying-iris/new_dprep.png)

   Un nuovo pacchetto di preparazione dei dati denominato **iris-1.dprep** verrà creato e aperto nell'editor di preparazione dei dati.

9. È ora possibile eseguire alcune operazioni di preparazione dei dati di base. Rinominare le colonne. Selezionare ogni intestazione di colonna per rendere modificabile il testo dell'intestazione. 

   Per le cinque colonne, immettere rispettivamente **Sepal Length**, **Sepal Width**, **Petal Length**, **Petal Width** e **Species**.

   ![Rinominare le colonne](media/tutorial-classifying-iris/rename_column.png)

10. Per conteggiare valori distinti, selezionare la colonna **Species** e quindi fare clic con il pulsante destro del mouse. Scegliere **Value Counts** (Conteggi dei valori) dal menu a discesa. 

   ![Selezionare Value Counts (Conteggi dei valori)](media/tutorial-classifying-iris/value_count.png)

   Verrà così visualizzato il riquadro **Inspectors** (Controlli), contenente un istogramma con quattro barre. La colonna di destinazione include i tre valori distinti **Iris_virginica**, **Iris_versicolor** e **Iris-setosa** e un valore **(Null)**.

11. Per filtrare i valori Null, selezionare la barra del grafico che rappresenta Null. È presente una riga con un valore **(Null)**. Per rimuovere questa riga, selezionare il segno meno (**-**).

   ![Istogramma dei conteggi dei valori](media/tutorial-classifying-iris/filter_out.png)

12. Si notino i singoli passaggi riportati in dettaglio nel riquadro **PASSAGGI**. Durante le operazioni di ridenominazione delle colonne e di filtro delle righe con valore Null, ogni azione è stata registrata come passaggio di preparazione dei dati. È possibile modificare singoli passaggi per modificare le impostazioni, riordinare i passaggi e rimuoverli.

   ![Passaggi](media/tutorial-classifying-iris/steps.png)

13. Chiudere l'editor di preparazione dei dati. Selezionare **Chiudi** (x) nella scheda **iris-1** con l'icona a forma di grafico. Il lavoro viene salvato automaticamente nel file **iris-1.dprep** visualizzato sotto l'intestazione **Data Preparations** (Preparazioni dati).

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Generare codice Python/PySpark per richiamare un pacchetto di preparazione dei dati

1. Fare clic con il pulsante destro del mouse sul file **iris-1.dprep** per visualizzare il menu di scelta rapida e quindi scegliere **Generate Data Access Code File** (Genera file di codice per l'accesso ai dati). 

   ![Generare il codice](media/tutorial-classifying-iris/generate_code.png)

2. Verrà aperto un nuovo file denominato **iris-1.py** con le righe di codice seguenti:

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   Questo frammento di codice richiama la logica creata come pacchetto di preparazione dei dati. `df` rappresenta le varie tipologie di frame di dati a seconda del contesto in cui questo codice viene eseguito. In caso di esecuzione in un runtime Python viene usato un [frame di dati pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html), mentre in caso di esecuzione in un contesto Spark viene usato un [frame di dati Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html). 

   Per altre informazioni sulla preparazione dei dati in Azure Machine Learning Workbench, vedere la guida [Introduzione alla preparazione dati](data-prep-getting-started.md).

## <a name="next-steps"></a>Passaggi successivi
In questa prima parte della serie di esercitazioni in tre parti, Azure Machine Learning Workbench è stato usato per:
> [!div class="checklist"]
> * Creare un nuovo progetto. 
> * Creare un pacchetto di preparazione dei dati.
> * Generare codice Python/PySpark per richiamare un pacchetto di preparazione dei dati.

È ora possibile passare alla parte successiva della serie, in cui viene illustrato come creare un modello di Azure Machine Learning:
> [!div class="nextstepaction"]
> [Creare un modello](tutorial-classifying-iris-part-2.md)
