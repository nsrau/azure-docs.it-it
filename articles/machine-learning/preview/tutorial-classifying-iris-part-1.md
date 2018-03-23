---
title: Esercitazione per la preparazione dei dati per la classificazione con il set di dati Iris nei servizi di Azure Machine Learning (anteprima) | Microsoft Docs
description: Questa esaustiva esercitazione illustra come usare i servizi di Azure Machine Learning (anteprima) end-to-end. Questa prima parte illustra la preparazione dei dati.
services: machine-learning
author: hning86
ms.author: haining, j-martens
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs, gcampanella
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/7/2018
ms.openlocfilehash: caddfff329d0e8f4c4007386b377ea56a51249a5
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="tutorial-classify-iris-part-1---preparing-the-data"></a>Esercitazione: Classificare il set di dati Iris - Parte 1 - Preparazione dei dati

Il servizio di Azure Machine Learning (anteprima) è una soluzione integrata di data science e analisi avanzata end-to-end con cui i data scientist professionisti possono preparare i dati, sviluppare esperimenti e distribuire modelli su scala cloud.

Questa esercitazione è la **prima di una serie in tre parti**. In questa esercitazione vengono illustrate le nozioni di base dei servizi di Azure Machine Learning (anteprima) e viene spiegato come eseguire queste operazioni:

> [!div class="checklist"]
> * Creare un progetto in Azure Machine Learning Workbench
> * Creare un pacchetto di preparazione dei dati
> * Generare codice Python/PySpark per richiamare un pacchetto di preparazione dei dati

Questa esercitazione usa il sempre attuale [set di dati dei fiori Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set). 

## <a name="prerequisites"></a>prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Per completare l'esercitazione, sono necessari:
- Un account Sperimentazione di Azure Machine Learning
- Azure Machine Learning Workbench installato

Se questi prerequisiti non sono già disponibili, seguire la procedura illustrata nell'articolo [Guida introduttiva: Installazione e avvio](quickstart-installation.md) per configurare l'account e installare l'applicazione Azure Machine Learning Workbench. 

## <a name="create-a-new-project-in-workbench"></a>Creare un nuovo progetto in Workbench

Se è stata seguita la procedura illustrata nell'articolo [Guida introduttiva: Installazione e avvio](quickstart-installation.md), il progetto dovrebbe essere già disponibile ed è possibile passare alla sezione successiva.

1. Aprire l'app Azure Machine Learning Workbench ed eseguire l'accesso, se necessario. 
   
   + Per avviare Workbench in Windows, usare il collegamento **Machine Learning Workbench** sul desktop. 
   + In macOS selezionare **Azure ML Workbench** in Launchpad.

1. Selezionare il segno più (+) nel riquadro **PROJECTS** (PROGETTI) e scegliere **New Project** (Nuovo progetto).  

   ![Nuova area di lavoro](media/tutorial-classifying-iris/new_ws.png)

1. Compilare i campi del modulo e selezionare il pulsante **Create** (Crea) per creare un nuovo progetto in Workbench.

   Campo|Valore consigliato per l'esercitazione|DESCRIZIONE
   ---|---|---
   Project name (Nome progetto) | myIris |Immettere un nome univoco che identifica l'account. È possibile usare il proprio nome o il nome di un reparto o un progetto che identifichi l'esperimento. Il nome deve avere una lunghezza compresa tra 2 e 32 caratteri. Può contenere solo caratteri alfanumerici e il trattino (-). 
   Directory del progetto | c:\Temp\ | Specificare la directory in cui viene creato il progetto.
   Descrizione del progetto | _lasciare vuoto_ | Campo facoltativo utile per descrivere i progetti.
   URL repository GIT Visualstudio.com |_lasciare vuoto_ | Campo facoltativo. È possibile associare un progetto a un repository Git in Visual Studio Team Services per il controllo del codice sorgente e la collaborazione. [Informazioni su come eseguire la configurazione](https://docs.microsoft.com/en-us/azure/machine-learning/preview/using-git-ml-project#step-3-set-up-a-machine-learning-project-and-git-repo). 
   Area di lavoro selezionata | IrisGarden (se esistente) | Scegliere un'area di lavoro creata per l'account di Sperimentazione nel portale di Azure. <br/>Se è stata seguita la procedura illustrata nella Guida introduttiva, dovrebbe essere presente un'area di lavoro denominata IrisGarden. In caso contrario, selezionare l'area di lavoro creata durante la creazione dell'account di Sperimentazione o qualsiasi altra area di lavoro da usare.
   Modello di progetto | Classificazione del set di dati Iris | I modelli contengono script e dati che possono essere usati per esplorare il prodotto. Questo modello contiene gli script e i dati necessari per la guida introduttiva e per altre esercitazioni disponibili in questo sito di documentazione. 

   ![Nuovo progetto](media/tutorial-classifying-iris/new_project.png)
 
 Viene creato un nuovo progetto e il dashboard viene aperto con tale progetto visualizzato. A questo punto, è possibile esplorare la home page, le origini dati, i blocchi appunti e i file di codice sorgente del progetto. 

   ![Aprire il progetto](media/tutorial-classifying-iris/project-open.png)
 

## <a name="create-a-data-preparation-package"></a>Creare un pacchetto di preparazione dei dati

È quindi possibile esplorare e iniziare a preparare i dati in Azure Machine Learning Workbench. Ogni trasformazione eseguita in Workbench viene archiviata in un formato JSON in un pacchetto locale di preparazione dei dati (file *.dprep). Questo pacchetto di preparazione dei dati è il contenitore principale per le operazioni di preparazione dei dati in Workbench.

Il pacchetto di preparazione dei dati può essere inviato in un secondo momento a un runtime, ad esempio local-C#/CoreCLR, Scala/Spark o Scala/HDI. 

1. Selezionare l'icona della cartella per aprire la visualizzazione dei file, quindi selezionare **iris.csv** per aprire il file.

   Il file contiene una tabella con 5 colonne e 50 righe. Quattro colonne sono colonne numeriche. La quinta colonna è una colonna di destinazione di tipo stringa. Nessuna delle colonne include nomi di intestazione.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Non includere file di dati nella cartella di progetto, soprattutto se le dimensioni del file sono elevate. Poiché il file di dati **iris.csv** è di dimensioni ridotte, è stato incluso in questo modello a scopo dimostrativo. Per altre informazioni, vedere l'articolo su [come leggere e scrivere file di dati di grandi dimensioni](how-to-read-write-files.md).

2. Nella **visualizzazione dati** selezionare il segno più (**+**) per aggiungere una nuova origine dati. Verrà visualizzata la pagina **Aggiungi origine dati**. 

   ![Visualizzazione dati in Azure Machine Learning Workbench](media/tutorial-classifying-iris/data_view.png)

3. Selezionare **Text Files (File di testo) (\*.csv, \*.json, \*.txt. e così via)** e fare clic su **Next** (Avanti).
   ![Origine dati in Azure Machine Learning Workbench](media/tutorial-classifying-iris/data-source.png)

4. Cercare il file **iris.csv** e fare clic su **Fine**. Verranno usati valori predefiniti per parametri come separatori e tipi di dati.

   >[!IMPORTANT]
   >Per questo esercizio assicurarsi di selezionare il file **iris.csv** dalla directory di progetto corrente. In caso contrario, i passaggi successivi potrebbero avere esito negativo.
 
   ![Selezionare iris](media/tutorial-classifying-iris/select_iris_csv.png)
   
5. Verrà creato un nuovo file denominato **iris-1.dsource**. Il file viene denominato in modo univoco con "-1" perché il progetto di esempio include già un file **iris.dsource** non numerato.  

   Il file verrà aperto e verranno visualizzati i dati. Al set di dati viene aggiunta automaticamente una serie di intestazioni di colonna, da **Column1** a **Column5**. Scorrere verso il basso e notare che l'ultima riga del set di dati è vuota. Ciò è dovuto alla presenta di un'interruzione di riga aggiuntiva nel file CSV.

   ![Visualizzazione dati Iris](media/tutorial-classifying-iris/iris_data_view.png)

1. Selezionare il pulsante **Metriche**. Gli istogrammi verranno generati e visualizzati.

   Per tornare alla visualizzazione dati, selezionare il pulsante **Data** (Dati).
   
   ![Visualizzazione dati Iris](media/tutorial-classifying-iris/iris_data_view_metrics.png)

1. Osservare gli istogrammi. Per ogni colonna è stato calcolato un set completo di statistiche. 

   ![Visualizzazione dati Iris](media/tutorial-classifying-iris/iris_metrics_view.png)

8. Iniziare la creazione di un pacchetto di preparazione dei dati selezionando il pulsante **Prepara**. Verrà visualizzata la finestra di dialogo **Prepara**. 

   Il progetto di esempio contiene un file **iris.dprep** di preparazione dei dati selezionato per impostazione predefinita. 

   ![Visualizzazione dati Iris](media/tutorial-classifying-iris/prepare.png)

1. Creare un nuovo pacchetto di preparazione dei dati selezionando **+ New Data Preparation Package** (Nuovo pacchetto di preparazione dei dati) dal menu a discesa.

   ![Visualizzazione dati Iris](media/tutorial-classifying-iris/prepare_new.png)

1. Immettere un nuovo valore per il nome del pacchetto (usare **iris-1**) e quindi selezionare **OK**.

   Un nuovo pacchetto di preparazione dei dati denominato **iris-1.dprep** verrà creato e aperto nell'editor di preparazione dei dati.

   ![Visualizzazione dati Iris](media/tutorial-classifying-iris/prepare_iris-1.png)

   È ora possibile eseguire alcune operazioni di preparazione dei dati di base. 

1. Selezionare ogni intestazione di colonna per rendere modificabile il testo dell'intestazione. Rinominare quindi ogni colonna, come indicato di seguito: 

   Per le cinque colonne, immettere rispettivamente **Sepal Length**, **Sepal Width**, **Petal Length**, **Petal Width** e **Species**.

   ![Rinominare le colonne](media/tutorial-classifying-iris/rename_column.png)

1. Contare i valori distinti:
   1. Selezionare la colonna **Species**.
   1. Fare clic con il pulsante destro del mouse per selezionare un'opzione. 
   1. Scegliere **Value Counts** (Conteggi dei valori) dal menu a discesa. 

   Il riquadro **Inspectors** (Controlli) viene visualizzato sotto i dati. Viene visualizzato un istogramma con quattro barre. La colonna di destinazione include i quattro valori distinti **Iris-virginica**, **Iris-versicolor**, **Iris-setosa** e un valore **(Null)**.

   ![Selezionare Value Counts (Conteggi dei valori)](media/tutorial-classifying-iris/value_count.png)

   ![Istogramma dei conteggi dei valori](media/tutorial-classifying-iris/filter_out.png)

1. Per escludere tramite filtro i valori Null, selezionare la barra "(null)" e quindi selezionare il segno meno (**-**). 

   La riga (null) viene quindi visualizzata in grigio per indicare che è stata esclusa tramite filtro. 

   ![Escludere tramite filtro i valori null](media/tutorial-classifying-iris/filter_out2.png)

1. Notare i singoli passaggi per la preparazione dei dati indicati in modo dettagliato nel riquadro **STEPS** (PASSAGGI). Durante le operazioni di ridenominazione delle colonne e di filtro delle righe con valore Null, ogni azione è stata registrata come passaggio di preparazione dei dati. È possibile modificare singoli passaggi per cambiarne le impostazioni, riordinare i passaggi e rimuoverli.

   ![Passaggi](media/tutorial-classifying-iris/steps.png)

1. Chiudere l'editor di preparazione dei dati. Selezionare l'icona **x** nella scheda **iris-1** con l'icona del grafico per chiudere la scheda. Il lavoro viene salvato automaticamente nel file **iris-1.dprep** visualizzato sotto l'intestazione **Data Preparations** (Preparazioni dati).

   ![Chiudi](media/tutorial-classifying-iris/close.png)

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Generare codice Python/PySpark per richiamare un pacchetto di preparazione dei dati

 L'output di un pacchetto di preparazione dei dati può essere esaminato direttamente in Python o in un notebook di Jupyter. I pacchetti possono essere eseguiti in più runtime tra cui Python, Spark che include Docker, e HDInsight locali. 

1. Individuare il file **iris-1.dprep** nella scheda Data Preparations (Preparazioni dati).

1. Fare clic con il pulsante destro del mouse sul file **iris-1.dprep**, quindi scegliere **Generate Data Access Code File** (Genera file di codice per l'accesso ai dati) dal menu contestuale. 

   ![Generare il codice](media/tutorial-classifying-iris/generate_code.png)

   Un nuovo file denominato **iris-1.py** viene aperto con le righe di codice seguenti per richiamare la logica creata come pacchetto di preparazione dei dati.

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

   `df` rappresenta un tipo di frame di dati diverso a seconda del contesto in cui questo codice viene eseguito:
   + In caso di esecuzione in un runtime di Python, viene usato un [frame di dati pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html).
   + In caso di esecuzione in un contesto Spark, viene usato un [frame di dati Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html). 
   
   Per altre informazioni sulla preparazione dei dati in Azure Machine Learning Workbench, vedere la guida [Introduzione alla preparazione dati](data-prep-getting-started.md).

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato Azure Machine Learning Workbench per:
> [!div class="checklist"]
> * Creare un nuovo progetto
> * Creare un pacchetto di preparazione dei dati
> * Generare codice Python/PySpark per richiamare un pacchetto di preparazione dei dati

È ora possibile passare alla parte successiva della serie di esercitazioni, in cui viene illustrato come creare un modello di Azure Machine Learning:
> [!div class="nextstepaction"]
> [Esercitazione 2 - Creare modelli](tutorial-classifying-iris-part-2.md)
