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
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e88b6bf74b7492353e5d5d004bde12fa4787e405
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="classifying-iris-part-1-prepare-data"></a>Classificazione di dati Iris - Parte 1: Preparare i dati
I servizi di Azure Machine Learning (anteprima) sono una soluzione integrata di analisi scientifica dei dati e analisi avanzata end-to-end con cui i data scientist professionisti possono preparare i dati, sviluppare esperimenti e distribuire modelli su scala cloud.

Questa esercitazione è la prima di una serie in tre parti. In questa esercitazione sono disponibili informazioni dettagliate sulle nozioni di base dei servizi di Azure Machine Learning (anteprima). In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Creare un progetto in Azure Machine Learning Workbench
> * Creare un pacchetto di preparazione dei dati
> * Generare codice Python/PySpark per richiamare il pacchetto di preparazione dei dati

Questa esercitazione usa il sempre attuale [set di dati dei fiori Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) per presentare l'argomento in modo semplice. Gli screenshot sono specifici di Windows, ma l'esperienza in macOS è praticamente identica.

## <a name="launch-azure-machine-learning-workbench"></a>Avviare Azure Machine Learning Workbench
Seguire le istruzioni disponibili in [Guida introduttiva all'installazione e alla creazione](quickstart-installation.md) per installare l'applicazione Azure Machine Learning Workbench, che include anche l'interfaccia della riga di comando. Avviare l'app Azure Machine Learning Workbench ed eseguire l'accesso, se necessario.

## <a name="create-a-new-project"></a>Creare un nuovo progetto
1. Nel riquadro **PROGETTI** fare clic sull'icona **+** per creare un **Nuovo progetto**.

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
1. Aprire il file `iris.csv` dalla **Visualizzazione file**. Il file è una semplice tabella con 5 colonne e 150 righe. Include quattro colonne con funzionalità numeriche e una colonna di destinazione di tipo stringa. Non include intestazioni di colonna.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   >Nota: non è consigliabile includere file di dati nella cartella del progetto, in particolare se le dimensioni del file sono elevate. Il file `iris.csv` viene incluso nel modello per finalità dimostrative perché le dimensioni del file sono molto ridotte. Per altre informazioni, vedere l'articolo [How to read and write large data files](how-to-read-write-files.md) (Come leggere e scrivere file di dati di grandi dimensioni).

2. Nella **Visualizzazione dati** fare clic sull'icona **+** per aggiungere una nuova origine dati. Viene avviata la procedura guidata **Aggiungi origine dati**. 

   ![Visualizzazione dati](media/tutorial-classifying-iris/data_view.png)

3. Selezionare l'opzione **File(s)/Directory** (File/Directory) e scegliere il file locale `iris.csv`. Accettare le impostazioni predefinite per ogni schermata e infine fare clic su **Fine**. 

   ![Selezionare iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Assicurarsi di selezionare il file `iris.csv` dall'interno della directory del progetto corrente per questo esercizio. In caso contrario, è possibile che i passaggi successivi abbiano esito negativo. 

4. Viene creato un nuovo file `iris-1.dsource`. Il file viene denominato in modo univoco con un trattino e il numero 1, perché il progetto include già un file `iris.dsource` non numerato.  Il file viene aperto e vengono visualizzati i dati. Al set di dati viene aggiunta automaticamente una serie di intestazioni di colonna, da `Column1` a `Column5`. Scorrere verso il basso e notare che l'ultima riga del set di dati è vuota. Ciò è dovuto alla presenza di un'interruzione di riga aggiuntiva nel file con estensione csv.

   ![Visualizzazione dati iris](media/tutorial-classifying-iris/iris_data_view.png)

5. Fare ora clic sul pulsante **Metriche**. Osservare gli istogrammi e un set completo di statistiche calcolate automaticamente per ogni colonna. È anche possibile fare clic sul pulsante **Dati** per visualizzare di nuovo i dati. 

   ![Visualizzazione dati iris](media/tutorial-classifying-iris/iris_metrics_view.png)

6. Fare clic sul pulsante **Prepara** accanto al pulsante **Metriche** oppure al pulsante **Dati** se si usa la visualizzazione delle metriche. Viene visualizzata la finestra di dialogo **Prepara**. Il progetto di esempio include già un file `iris.dprep`. Per impostazione predefinita, viene quindi richiesta la creazione di un nuovo flusso di dati nel pacchetto di preparazione dei dati **iris.dprep** esistente. Modificare il valore dell'elenco a discesa in **+New Data Preparation Package** (Nuovo pacchetto di preparazione dei dati), quindi immettere un nuovo valore "iris-1" e infine fare clic su **OK**.

   ![Visualizzazione dati iris](media/tutorial-classifying-iris/new_dprep.png)

Un nuovo pacchetto di preparazione dei dati denominato `iris-1.dprep` viene creato e viene aperto nell'editor di preparazione dei dati.

È ora possibile eseguire alcune semplici operazioni di preparazione dei dati. Rinominare le colonne facendo clic su ogni intestazione di colonna e rendendo modificabile il testo dell'intestazione. Immettere `Sepal Length`, `Sepal Width`, `Petal Length`, `Petal Width` e `Species` per le cinque colonne, rispettivamente.

![Rinominare le colonne](media/tutorial-classifying-iris/rename_column.png)

Selezionare la colonna `Species` e farvi clic con il pulsante destro del mouse. Scegliere **Value Counts** (Conteggi dei valori). 

![Conteggio dei valori](media/tutorial-classifying-iris/value_count.png)

Questa azione crea un istogramma con quattro barre. Si noti che la colonna di destinazione include tre valori distinti, `Iris_virginica`, `Iris_versicolor`, `Iris-setosa`. È inoltre presente una riga con un valore `(null)`. Eliminare tale riga selezionando la barra che rappresenta il valore Null e quindi facendo clic sul pulsante di filtro **-** per rimuoverla. 

![Conteggio dei valori](media/tutorial-classifying-iris/filter_out.png)

Durante le operazioni per rinominare le colonne ed escludere tramite filtro la riga con valore Null, ogni azione eseguita viene registrata come passaggio di preparazione dei dati nel riquadro **PASSAGGI**. È possibile modificare i passaggi (per ottimizzarne le impostazioni), riordinarli o addirittura rimuoverli.

![steps](media/tutorial-classifying-iris/steps.png)

## <a name="generate-pythonpyspark-code-to-invoke-data-prep-package"></a>Generare codice Python/PySpark per richiamare il pacchetto di preparazione dei dati

Chiudere l'editor di preparazione dei dati. Le modifiche vengono salvate automaticamente. Fare clic con il pulsante destro del mouse sul file **iris-1.dprep** per visualizzare il menu di scelta rapida, quindi scegliere **Generate Data Access Code File** (Genera file di codice per l'accesso ai dati). 

![Generare codice](media/tutorial-classifying-iris/generate_code.png)

Viene creato un file **iris-1.py** con le due righe di codice seguente prepopolate (oltre ad alcuni commenti):

```python
# This code snippet will load the referenced package and return a DataFrame.
# If the code is run in a PySpark environment, the code will return a
# Spark DataFrame. If not, the code will return a Pandas DataFrame.

from azureml.dataprep.package import run
df = run('iris.dprep', dataflow_idx=0)
```
Questo frammento di codice richiama la logica creata come pacchetto di preparazione dei dati. In base al contesto in cui viene eseguito il codice, `df` può essere [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html), in caso di esecuzione nel runtime di Python, o [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html), in caso di esecuzione in un contesto Spark. Per altre informazioni sulla preparazione dei dati in Azure Machine Learning Workbench, vedere la guida [Getting Started with Data Preparation](data-prep-getting-started.md) (Introduzione alla preparazione dei dati).

## <a name="next-steps"></a>Passaggi successivi
In questa prima parte della serie di esercitazioni in tre parti Azure Machine Learning Workbench è stato usato per:
> [!div class="checklist"]
> * Creare un nuovo progetto 
> * Creare un pacchetto di preparazione dei dati
> * Generare codice Python/PySpark per richiamare il pacchetto di preparazione dei dati

È ora possibile passare alla parte successiva della serie per creare un modello di Machine Learning.
> [!div class="nextstepaction"]
> [Creare un modello](tutorial-classifying-iris-part-2.md)

