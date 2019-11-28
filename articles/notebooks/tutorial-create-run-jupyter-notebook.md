---
title: 'Esercitazione: creare ed eseguire un notebook di Jupyter in Azure'
description: Come creare ed eseguire un notebook di Jupyter in Azure Notebooks che illustra il processo di regressione lineare in data science.
ms.topic: tutorial
ms.date: 01/11/2019
ms.openlocfilehash: 2c151cb0de2855856e92d9de07ad7dabfda2f55b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277420"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Esercitazione: creare ed eseguire un notebook di Jupyter con Python

Questa esercitazione descrive come usare Azure Notebooks per creare un notebook di Jupyter completo che illustra la regressione lineare semplice. Nel corso di questa esercitazione sarà possibile acquisire familiarità con l'interfaccia utente del notebook di Jupyter, che include la creazione di diverse celle, l'esecuzione delle celle e la visualizzazione del notebook come una presentazione.

Il notebook completato è disponibile in [GitHub - Esempi di Azure Notebooks](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Questa esercitazione, tuttavia, inizia con un nuovo progetto e un notebook vuoto, in modo da illustrarne la creazione in modo dettagliato.

## <a name="create-the-project"></a>Creare il progetto

1. Passare ad [Azure Notebooks](https://notebooks.azure.com) ed eseguire l'accesso. Per informazioni dettagliate, vedere [Avvio rapido: Accedere ad Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Dalla pagina del profilo pubblico selezionare **My Projects** (Progetti personali) nella parte superiore:

    ![Collegamento My Projects (Progetti personali) nella parte superiore della finestra del browser](media/quickstarts/my-projects-link.png)

1. Nella pagina **My Projects** (Progetti personali) selezionare **+ New Project** (+ Nuovo progetto) (tasto di scelta rapida: n); se la finestra del browser è di dimensioni ridotte, il pulsante potrebbe essere visualizzato solo come **+** :

    ![Comando New Project (Nuovo progetto) nella pagina My Projects (Progetti personali)](media/quickstarts/new-project-command.png)

1. Nella finestra popup **Create New Project** (Crea nuovo progetto) visualizzata immettere o impostare i dettagli seguenti e quindi selezionare **Create** (Crea):

    - **Project name** (Nome progetto): Esempio di regressione lineare - Chirps Cricket
    - **Project ID** (ID progetto): esempio di regressione lineare
    - **Public project** (Progetto pubblico): (disattivata)
    - **Create a README.md** (Crea un file README.md): (disattivata)

1. Dopo qualche istante, Azure Notebooks passa al nuovo progetto.

## <a name="create-the-data-file"></a>Creare il file di dati

Il modello di regressione lineare creato nel notebook recupera i dati da un file nel progetto denominato *cricket_chirps.csv*. È possibile creare questo file copiandolo da [GitHub - Esempi di Azure Notebooks](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps) oppure immettendo direttamente i dati. Le sezioni seguenti descrivono entrambi gli approcci.

### <a name="upload-the-data-file"></a>Caricare il file di dati

1. Nel dashboard del progetto in Azure Notebooks selezionare **Upload** (Carica)  > **From URL** (Da URL)
1. Nella finestra popup immettere l'URL seguente in **File URL** (URL del file) e *cricket_chirps.csv* in **File Name** (Nome file), quindi selezionare **Done** (Fine).

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. Il file *cricket_chirps.csv* sarà visualizzato nell'elenco dei file del progetto:

    ![Nuovo file CSV visualizzato nell'elenco dei file del progetto](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Creare un file da zero

1. Nel dashboard del progetto in Azure Notebooks selezionare **+ New** (+ Nuovo)  > **Blank File** (File vuoto)
1. Verrà visualizzato un campo nell'elenco dei file del progetto. Immettere *cricket_chirps.csv* e premere INVIO.
1. Fare clic con il pulsante destro del mouse su *cricket_chirps.csv* e selezionare **Edit File** (Modifica file).
1. Nell'editor visualizzato immettere i dati seguenti:

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. Selezionare **Save File** (Salva file) per salvare il file e tornare al dashboard del progetto.

## <a name="install-project-level-packages"></a>Installare i pacchetti a livello di progetto

All'interno di un notebook è sempre possibile usare comandi come `!pip install` in una cella di codice per installare i pacchetti necessari. Tuttavia, tali comandi vengono eseguiti ogni volta che si eseguono le celle di codice del notebook, quindi possono richiedere molto tempo. Per questo motivo, è possibile installare i pacchetti a livello di progetto usando un file `requirements.txt`.

1. Usare la procedura descritta in [Creare un file da zero](#create-a-file-from-scratch) per creare un file denominato `requirements.txt` con il contenuto seguente:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    È anche possibile caricare un file `requirements.txt` dal computer locale se si preferisce, come descritto in [Caricare il file di dati](#upload-the-data-file).

1. Nel dashboard del progetto selezionare **Project Settings** (Impostazioni progetto).
1. Nella finestra popup visualizzata selezionare la scheda **Environment** (Ambiente) e quindi selezionare **+ Add** (+ Aggiungi).
1. Nel primo controllo a discesa (operazione) in **Environment Setup Steps** (Passaggi di configurazione dell'ambiente) scegliere **Requirements.txt**.
1. Nel secondo controllo a discesa (nome file) scegliere *requirements.txt* (il file che è stato creato).
1. Nel terzo controllo a discesa (versione di Python) scegliere **Python Version 3.6** (Python versione 3.6).
1. Selezionare **Salva**.

![Scheda Project Settings - Environment (Impostazioni progetto - Ambiente) in cui è specificato un file requirements.txt](media/tutorial/tutorial-requirements-txt.png)

Dopo aver eseguito questo passaggio di configurazione, qualsiasi notebook eseguito nel progetto verrà eseguito in un ambiente in cui sono installati i pacchetti.

## <a name="create-and-run-a-notebook"></a>Creare ed eseguire un notebook

Dopo aver preparato il file di dati e impostato l'ambiente del progetto, è ora possibile creare e aprire il notebook.

1. Nel dashboard del progetto selezionare **+ New** (+ Nuovo)  > **Notebook**.
1. Nella finestra popup immettere *Esempio di regressione lineare - Cricket Chirps.ipynb* per **Item Name** (Nome elemento), scegliere **Python 3.6** per il linguaggio, quindi selezionare **New** (Nuovo).
1. Una volta che il nuovo notebook viene visualizzato nell'elenco dei file, selezionarlo per avviare il notebook. Verrà aperta automaticamente una nuova scheda del browser.
1. Poiché è presente un file *requirements.txt* nelle impostazioni dell'ambiente, viene visualizzato il messaggio "Waiting for your container to finish being prepared" (In attesa del completamento della preparazione del contenitore). È possibile selezionare **OK** per chiudere il messaggio e continuare a lavorare nel notebook. Tuttavia, non sarà possibile eseguire le celle di codice finché l'ambiente non è completamente configurato.
1. Il notebook viene aperto nell'interfaccia di Jupyter con una singola cella di codice vuota come valore predefinito.

    [![Visualizzazione iniziale di un nuovo notebook in Azure Notebooks](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Esplorare l'interfaccia del notebook

Con il notebook in esecuzione, è possibile aggiungere celle di codice e di Markdown, eseguire tali celle e gestire il funzionamento del notebook. In primo luogo, tuttavia, è consigliabile dedicare alcuni minuti per acquisire familiarità con l'interfaccia. Per la documentazione completa, selezionare il comando di menu **Help** (Guida)  > **Notebook Help** (Guida del notebook).

Nella parte superiore della finestra sono visualizzati gli elementi seguenti:

(A) Il nome del notebook, che è possibile modificare facendo clic su di esso.
(B) I pulsanti per passare al progetto che contiene il notebook e al dashboard dei progetti, che aprono nuove schede nel browser.
(C) Un menu con i comandi per l'uso del notebook.
(D) Una barra degli strumenti con i collegamenti per le operazioni comuni.
(E) Il canvas di modifica contenente le celle.
(F) Indicatore che segnala se il notebook è attendibile. Il valore predefinito è **Not Trusted** (Non attendibile).
(G) Il kernel usato per eseguire il notebook, insieme a un indicatore dell'attività.

[![Aree principali dell'interfaccia utente di Jupyter](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter fornisce una presentazione integrata dei principali elementi dell'interfaccia utente. Avviare la presentazione selezionando il comando **Help** (Guida)  > **User Interface Tour** (Presentazione dell'interfaccia utente) e facendo clic nelle finestre popup.

I gruppi di comandi di menu sono i seguenti:

| Menu | DESCRIZIONE |
| --- | --- |
| File | Comandi per gestire il file del notebook, inclusi i comandi per creare e copiare i notebook, visualizzare un'anteprima di stampa e scaricare il notebook in diversi formati. |
| Modificare | Normali comandi per tagliare, copiare e incollare celle, trovare e sostituire valori, gestire gli allegati delle celle e inserire immagini.  |
| Visualizza | Comandi per controllare la visibilità delle diverse parti dell'interfaccia utente di Jupyter. |
| Inserimento | Comandi per inserire una nuova cella sopra o sotto la cella corrente. Questi comandi vengono usati di frequente durante la creazione di un notebook. |
| Cell (Cella) | I vari comandi **Run** (Esegui) consentono di eseguire una o più celle in diverse combinazioni. I comandi **Cell Type** (Tipo di cella) consentono di modificare il tipo di una cella tra **Code** (Codice), **Markdown** e **Raw NBConvert** (testo normale). I comandi **Current Outputs** (Output correnti) e **All Outputs** (Tutti gli output) consentono di controllare la visualizzazione dell'output del codice eseguito e includono un comando per cancellare tutto l'output. |
| Kernel | Comandi per gestire la modalità di esecuzione del codice nel kernel, insieme a **Change kernel** (Cambia kernel) per modificare il linguaggio o la versione di Python usati per eseguire il notebook. |
| Dati | Comandi per caricare e scaricare i file dal progetto o dalla sessione. Vedere [Lavorare con i file di dati di progetto](work-with-project-data-files.md) |
| Widgets (Widget) | Comandi per gestire i [widget di Jupyter](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), che offrono funzionalità aggiuntive per la visualizzazione, il mapping e il tracciato.|
| Guida | Comandi che consentono di accedere alla Guida e alla documentazione per l'interfaccia di Jupyter. |

La maggior parte dei comandi sulla barra degli strumenti dispone di comandi di menu equivalenti. L'unica eccezione è rappresentata da **Enter/Edit RISE Slideshow** (Immetti/modifica presentazione RISE), come descritto in [Condividere e presentare i notebook](present-jupyter-notebooks-slideshow.md).

Molti di questi comandi vengono usati per popolare il notebook nelle sezioni che seguono.

## <a name="create-a-markdown-cell"></a>Creare una cella di Markdown

1. Fare clic sulla prima cella vuota visualizzata nel canvas del notebook. Per impostazione predefinita, una cella è di tipo **Code** (Codice), ovvero è progettata per contenere codice eseguibile per il kernel selezionato (Python, R o F#). Il tipo corrente è indicato nell'elenco a discesa del tipo sulla barra degli strumenti:

    ![Elenco a discesa per il tipo di cella sulla barra degli strumenti](media/tutorial/tutorial-cell-type-drop-down.png)

1. Modificare il tipo di cella in **Markdown** usando l'elenco a discesa sulla barra degli strumenti. In alternativa, usare il comando di menu **Cell** (Cella)  > **Cell Type** (Tipo di cella)  > **Markdown**:

    ![Comando di menu per il tipo di cella](media/tutorial/tutorial-cell-type-menu.png)

1. Fare clic nella cella per iniziare la modifica, quindi immettere il seguente codice Markdown:

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. Per eseguire il rendering del codice Markdown in formato HTML per il browser, selezionare il comando **Run** (Esegui) sulla barra degli strumenti oppure usare il comando **Cell** (Cella)  > **Run Cells** (Esegui celle). Il codice Markdown per la formattazione e i collegamenti vengono visualizzati come previsto in un browser.

1. Quando si esegue l'ultima cella del notebook, Jupyter crea automaticamente una nuova cella sotto quella che è stata eseguita. Inserire ulteriore codice Markdown in questa cella, ripetendo i passaggi descritti in questa sezione con il seguente codice Markdown:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Per modificare di nuovo il codice Markdown, fare doppio clic nella cella di cui è stato eseguito il rendering. Per eseguire nuovamente il rendering HTML dopo aver apportato le modifiche, eseguire la cella.

## <a name="create-a-code-cell-with-commands"></a>Creare una cella di codice con i comandi

Come descritto per la cella Markdown precedente, è possibile includere comandi direttamente nel notebook. È possibile usare i comandi per installare i pacchetti, eseguire curl o wget per recuperare dati o effettuare qualsiasi altra operazione. I notebook di Jupyter vengono eseguiti in modo efficace all'interno di una macchina virtuale Linux, pertanto è disponibile l'intero set di comandi di Linux.

1. Immettere i comandi seguenti nella cella di codice visualizzata dopo che è stato usato **Run** (Esegui) nella cella Markdown precedente. Se non viene visualizzata una nuova cella, crearne una con **Insert** (Inserisci)  > **Insert Cell Below** (Inserisci cella sotto) oppure usare il pulsante **+** sulla barra degli strumenti.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Prima di eseguire la cella, creare una nuova cella con il pulsante **+** sulla barra degli strumenti, impostarla su Markdown e immettere la descrizione seguente:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Selezionare il comando **Cell** (Cella)  > **Run All** (Esegui tutto) per eseguire tutte le celle del notebook. Si noti che viene effettuato il rendering delle celle di Markdown in formato HTML e che il comando viene eseguito nel kernel. Osservare inoltre l'indicatore del kernel, come descritto nel Markdown stesso:

    ![Indicatore di occupato per il kernel del notebook](media/tutorial/tutorial-kernel-busy.png)

1. Anche l'esecuzione di tutti i comandi `pip install` richiede un certo tempo. Inoltre, poiché questi pacchetti sono già stati installati nell'ambiente del progetto (e poiché sono inclusi anche in Azure Notebooks per impostazione predefinita), vengono visualizzati numerosi messaggi "Requirement already satisfied" (Requisito già soddisfatto). Tutto questo output può essere fonte di distrazione, pertanto selezionare la cella (con un solo clic) e quindi usare il comando **Cell** (Cella)  > **Cell Outputs** (Output cella)  > **Toggle** (Attiva/disattiva) per nascondere l'output. È anche possibile usare il comando **Clear** (Cancella) nello stesso sottomenu per rimuovere completamente l'output.

    Il comando **Toggle** (Attiva/disattiva) nasconde solo l'output più recente della cella. Se si esegue nuovamente la cella, l'output viene nuovamente visualizzato.

1. Poiché i pacchetti sono installati nell'ambiente del progetto, impostare come commento i comandi `! pip install` usando `#`. In questo modo, i comandi possono rimanere nel notebook come materiale di riferimento, ma non richiederanno tempo per l'esecuzione e non produrranno output non necessari. In questo caso, mantenendo nel notebook i comandi impostati come commenti, è anche possibile indicare le dipendenze del notebook.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Creare le celle rimanenti

Per popolare il resto del notebook, creare una serie di celle di Markdown e di codice. Per ogni cella elencata di seguito, creare innanzitutto la nuova cella, quindi impostare il tipo e incollare il contenuto.

Anche se è possibile attendere di eseguire il notebook dopo aver creato ogni cella, è interessante eseguire ogni cella che si sta creando. Non tutte le celle mostrano un output. Se non vengono visualizzati errori, la cella è stata eseguita normalmente.

Ogni cella di codice dipende dal codice che è stato eseguito nelle celle precedenti. Se non si esegue una delle celle, le celle successive possono generare errori. Se si dimentica di eseguire una cella, provare a usare **Cell** (Cella)  > **Run All Above** (Esegui tutte le precedenti) prima di eseguire la cella corrente.

Se si ottengono risultati imprevisti (come è probabile), verificare che ogni cella sia impostata su "Code" (Codice) o "Markdown" in base alle esigenze. Ad esempio, in genere si verifica un errore "Invalid syntax" (Sintassi non valida) dopo aver immesso Markdown in una cella di codice.

1. Cella di Markdown:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Cella di codice. Quando viene eseguita, visualizza il contenuto della tabella come output. È possibile eliminare l'output impostando come commento l'istruzione `print`.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Potrebbero essere visualizzati avvisi da questo codice con il messaggio "numpy.dtype size changed" (Dimensioni di numpy.dtype modificate). È possibile ignorare gli avvisi.

1. Cella di Markdown:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Cella di codice. Quando viene eseguita, questa cella è priva di output.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Cella di Markdown:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Cella di codice. Quando viene eseguita, questa cella visualizza l'output `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`.

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Cella di Markdown:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Cella di codice. Quando viene eseguita, questa cella visualizza risultati come `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`.

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Cella di Markdown:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Cella di Markdown:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Cella di codice. Quando viene eseguita, questa cella produce un tracciato grafico. Se non viene visualizzato il tracciato la prima volta, e viene invece indicato "Figure size 640x480 with 1 Axes" (Figura di dimensioni 640x480 con 1 asse), eseguire nuovamente la cella.

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![Tracciato di output dal codice di matplotlib](media/tutorial/tutorial-plot-output.png)

1. Cella di Markdown:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Cancellare gli output ed eseguire di nuovo tutte le celle

Dopo aver seguito i passaggi nella sezione precedente per popolare l'intero notebook, è stata creata una parte del codice in esecuzione nel contesto di un'esercitazione completa sulla regressione lineare. Questa combinazione diretta di codice e testo è uno dei principali vantaggi dei notebook.

Provare a eseguire nuovamente l'intero notebook:

1. Cancellare tutti i dati della sessione del kernel e tutto l'output delle celle selezionando **Kernel** > **Restart & Clear Output** (Riavvia e cancella output). È sempre consigliabile eseguire questo comando dopo aver completato un notebook, per assicurarsi di non aver creato dipendenze impreviste tra le celle di codice.

1. Eseguire di nuovo il notebook usando **Cell** (Cella)  > **Run All** (Esegui tutto). Si noti che viene inserito l'indicatore del kernel durante l'esecuzione del codice.

    Se il codice viene eseguito troppo a lungo o si blocca, è possibile arrestare il kernel con il comando **Kernel** > **Interrupt** (Interrompi).

1. Scorrere il notebook per esaminare i risultati. Se anche in questo caso non viene visualizzato il tracciato, eseguire nuovamente tale cella.

## <a name="save-halt-and-close-the-notebook"></a>Salvare, arrestare e chiudere il notebook

Durante la modifica di un notebook, è possibile salvarne lo stato corrente con il comando **File** > **Save and Checkpoint** (Salva e checkpoint) o il pulsante per il salvataggio sulla barra degli strumenti. Un "checkpoint" crea uno snapshot che è possibile ripristinare in qualsiasi momento durante la sessione. I checkpoint consentono di apportare una serie di modifiche sperimentali. Se tali modifiche non funzionano, è possibile ripristinare facilmente un checkpoint usando il comando **File** > **Revert to Checkpoint** (Ripristina checkpoint). Un approccio alternativo consiste nel creare celle aggiuntive e impostare come commento il codice che non si vuole eseguire. Entrambe le soluzioni sono valide.

È anche possibile usare in qualsiasi momento il comando **File** > **Make a Copy** (Crea una copia) per creare una copia dello stato corrente del notebook in un nuovo file nel progetto. La copia viene aperta automaticamente in una nuova scheda del browser.

Al termine delle attività con un notebook, usare il comando **File** > **Close and halt** (Chiudi e interrompi), che consente di chiudere il notebook e arrestare il kernel in cui è stato eseguito. Azure Notebooks chiude automaticamente la scheda del browser.

## <a name="debug-notebooks-using-visual-studio-code"></a>Eseguire il debug dei notebook con Visual Studio Code

Se le celle di codice nel notebook non funzionano come previsto, possono essere presenti bug nel codice o altri difetti. Tuttavia, a parte l'uso delle istruzioni `print` per visualizzare il valore delle variabili, un ambiente di Jupyter non offre funzionalità di debug.

Fortunatamente, è possibile scaricare il file del notebook *.ipynb*, quindi aprirlo in Visual Studio Code con l'estensione di Python. L'estensione importa direttamente un notebook come un singolo file di codice, mantenendo le celle di Markdown nei commenti. Una volta importato il notebook, è quindi possibile eseguire il debugger di Visual Studio Code per esaminare il codice, impostare punti di interruzione, esaminare lo stato e così via. Dopo aver apportato le correzioni al codice, esportare il file *.ipynb* da Visual Studio Code e caricarlo nuovamente in Azure Notebooks.

Per altre informazioni, vedere [Debug a Jupyter notebook (Eseguire il debug di un notebook Jupyter)](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) nella documentazione di Visual Studio Code.

Vedere anche [Visual Studio Code - Jupyter support (Visual Studio Code - supporto di Jupyter)](https://code.visualstudio.com/docs/python/jupyter-support) per funzionalità aggiuntive di Visual Studio Code per i notebook di Jupyter.

## <a name="next-steps"></a>Passaggi successivi

- [Explore sample notebooks (Esplorare i notebook di esempio)](azure-notebooks-samples.md)

Procedure dettagliate:

- [Creare e clonare progetti](create-clone-jupyter-notebooks.md)
- [Configure and manage projects (Configurare e gestire i progetti)](configure-manage-azure-notebooks-projects.md)
- [Install packages from within a notebook (Installare pacchetti da un notebook)](install-packages-jupyter-notebook.md)
- [Eseguire una presentazione](present-jupyter-notebooks-slideshow.md)
- [Usare i file di dati](work-with-project-data-files.md)
- [Accedere alle risorse dati](access-data-resources-jupyter-notebooks.md)
- [Usare Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
