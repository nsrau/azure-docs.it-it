<properties 
	pageTitle="Accedere a set di dati mediante la libreria client Python di Azure Machine Learning | Microsoft Azure" 
	description="Installare e usare la libreria client Python per accedere e gestire i dati di Azure Machine Learning in modo protetto da un ambiente Python locale." 
	services="machine-learning" 
	documentationCenter="python" 
	authors="bradsev" 
	manager="jhubbard" 
	editor="cgronlun"/>  

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="huvalo;bradsev" />  


#Accedere a set di dati con Python mediante la libreria client Python di Azure Machine Learning 

L'anteprima della libreria client Python di Microsoft Azure Machine Learning consente l'accesso sicuro a set di dati di Azure Machine Learning da un ambiente Python locale, nonché la creazione e la gestione di set di dati in un'area di lavoro.

Questo argomento fornisce istruzioni su come:

* Installare la libreria client Python di Machine Learning
* Accedere e caricare set di dati, fornendo istruzioni su come ottenere l'autorizzazione per accedere a set di dati di Azure Machine Learning dall'ambiente Python locale
*  Accedere ai set di dati intermedi di un esperimento
*  Usare la libreria client Python per enumerare set di dati, accedere a metadati, leggere il contenuto di un set di dati, creare nuovi set di dati e aggiornare set di dati esistenti.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

 
##<a name="prerequisites"></a>Prerequisiti

La libreria client Python è stata testata negli ambienti seguenti:

 - Windows, Mac e Linux
 - Python 2.7, 3.3 e 3.4

Presenta una dipendenza dai pacchetti seguenti:

 - requests
 - python-dateutil
 - pandas

È consigliabile usare una distribuzione di Python fornita di serie, ad esempio [Anaconda](http://continuum.io/downloads#all) o [Canopy](https://store.enthought.com/downloads/), insieme a IPython e ai tre pacchetti sopra elencati già installati. Sebbene IPython non sia obbligatorio, costituisce un ambiente ottimale per la manipolazione e la visualizzazione dei dati in modo interattivo.


###<a name="installation"></a>Come installare la libreria client Python di Azure Machine Learning

Per completare le attività descritte in questo argomento, è necessario che sia installata la libreria client Python di Azure Machine Learning. È disponibile nella pagina [Python Package Index](https://pypi.python.org/pypi/azureml). Per installarla nel proprio ambiente Python, eseguire il comando seguente dall'ambiente Python locale:

    pip install azureml

In alternativa, è possibile scaricarla e installarla dalle origini disponibili in [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Se nel proprio computer è installato Git, è possibile usare pip per installarla direttamente dal repository Git.

	pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


##<a name="datasetAccess"></a>Usare frammenti di codice di Studio per accedere a set di dati

La libreria client Python consente l'accesso a livello di codice a set di dati esistenti in esperimenti in esecuzione.

Dall'interfaccia Web di Studio è possibile generare frammenti di codice che includono tutte le informazioni necessarie per scaricare e deserializzare set di dati come oggetti Pandas DataFrame nel proprio computer locale.

### <a name="security"></a>Sicurezza per l'accesso ai dati

I frammenti di codice forniti da Studio per essere usati con la libreria client Python includono l'ID dell'area di lavoro e il token di autorizzazione, che offrono l'accesso completo all'area di lavoro e pertanto devono essere protetti, come una password.

Per motivi di sicurezza, le funzionalità dei frammenti di codice sono disponibili solo per gli utenti il cui ruolo nell'area di lavoro è impostato su **Owner**. Il ruolo viene visualizzato in Azure Machine Learning Studio nella pagina **USERS** della sezione **Settings**.

![Sicurezza][security]  

Se il proprio ruolo non è impostato su **Owner**, è possibile chiedere di essere nuovamente invitati con il ruolo di proprietario o chiedere il frammento di codice al proprietario dell'area di lavoro.

Per ottenere il token di autorizzazione, è possibile eseguire una di queste operazioni:



- Chiedere un token a un proprietario. I proprietari possono accedere ai propri token di autorizzazione dalla pagina Settings dell'area di lavoro personale in Studio. Selezionare **Settings** (Impostazioni) dal riquadro sinistro e fare clic su **AUTHORIZATION TOKENS** (Token di autorizzazione) per visualizzare i token primari e secondari. Sebbene per il frammento di codice sia possibile usare sia i token di autorizzazione primari sia quelli secondari, è consigliabile che i proprietari condividano solo i token di autorizzazione secondari.

![](./media/machine-learning-python-data-access/ml-python-access-settings-tokens.png)

- Chiedere di essere promossi al ruolo di proprietario. A questo scopo, è necessario prima essere rimossi dall'area di lavoro da un proprietario corrente dell'area di lavoro, quindi essere nuovamente invitati con il ruolo di proprietario.

Dopo aver ottenuto l'ID dell'area di lavoro e il token di autorizzazione, gli sviluppatori possono usare il frammento di codice per accedere all'area di lavoro indipendentemente dal proprio ruolo.

I token di autorizzazione vengono gestiti nella pagina **AUTHORIZATION TOKENS** della sezione **SETTINGS**. È possibile rigenerarli, ma questa procedura revoca l'accesso al token precedente.

### <a name="accessingDatasets"></a>Accedere a set di dati da un'applicazione Python locale

1. In Machine Learning Studio fare clic su **DATASETS** (Set di dati) sulla barra di spostamento a sinistra.

2. Selezionare il set di dati a cui si desidera accedere. È possibile selezionare qualsiasi set di dati dall'elenco **MY DATASETS** o **SAMPLES**.

3. Sulla barra degli strumenti inferiore fare clic su **Generate Data Access Code** (Genera codice di accesso ai dati). Se i dati si presentano in un formato non compatibile con la raccolta client di Python, questo pulsante non è attivo.

	![Set di dati][datasets]  

4. Selezionare il frammento di codice dalla finestra che viene visualizzata e copiarlo negli Appunti.

	![Codice di accesso][dataset-access-code]  

5. Incollare il codice nel blocco appunti dell'applicazione Python locale.

	![Blocco appunti][ipython-dataset]  

## <a name="accessingIntermediateDatasets"></a>Accedere a set di dati intermedi da esperimenti di Machine Learning

Dopo aver eseguito un esperimento in Machine Learning Studio, è possibile accedere ai set di dati intermedi dai nodi di output dei moduli. I set di dati intermedi sono costituiti da dati creati e usati per i passaggi intermedi quando è in esecuzione uno strumento di modello.

È possibile accedere ai set di dati intermedi sono se si trovano in un formato compatibile con la libreria client Python.

Sono supportati i formati seguenti (le costanti per questi formati sono disponibili nella classe `azureml.DataTypeIds`):

 - PlainText
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader

È possibile determinare il formato passando il puntatore del mouse su un nodo di output del modulo. Il formato viene visualizzato in una descrizione comando insieme al nome del nodo.

Alcuni moduli, ad esempio [Split][split], eseguono l'output in un formato denominato `Dataset`, che non è supportato dalla libreria client Python.

![Formato del set di dati][dataset-format]  

Per ottenere un output in un formato supportato, è necessario usare un modulo di conversione, ad esempio [Convert to CSV][convert-to-csv].

![Formato GenericCSV][csv-format]  

I passaggi seguenti illustrano un esempio in cui si crea e si esegue un esperimento e si accede al set di dati intermedio.

1. Creare un nuovo esperimento.

2. Inserire un modulo **Adult Census Income Binary Classification dataset**.

3. Inserire un modulo [Split][split] e connetterne l'input all'output del modulo del set di dati.

4. Inserire un modulo [Convert to CSV][convert-to-csv] e connetterne l'input a uno degli output del modulo [Split][split].

5. Salvare l'esperimento, eseguirlo e attendere il completamento dell'esecuzione.

6. Fare clic sul nodo di output del modulo [Convert to CSV][convert-to-csv].

7. Viene visualizzato un menu di scelta rapida.
8.  Selezionare **Generate Data Access Code** (Genera codice di accesso ai dati).

	![Menu di scelta rapida][experiment]  

8. Selezionare il frammento di codice dalla finestra che viene visualizzata e copiarlo negli Appunti.

	![Codice di accesso][intermediate-dataset-access-code]  

9. Incollare il codice nel blocco appunti.

	![Blocco appunti][ipython-intermediate-dataset]  

10. È possibile visualizzare i dati usando matplotlib. In questo caso, viene creato un istogramma per la colonna dell'età.

	![Istogramma][ipython-histogram]  


##<a name="clientApis"></a>Usare la libreria client Python di Machine Learning per accedere, leggere, creare e gestire set di dati

### Area di lavoro

L'area di lavoro è il punto di ingresso della libreria client Python. Per creare un'istanza è necessario specificare nella classe `Workspace` l'ID della propria area di lavoro e il token di autorizzazione:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### Enumerare set di dati

Per enumerare tutti i set di dati presenti in un'area di lavoro:

    for ds in ws.datasets:
        print(ds.name)

Per enumerare solo i set di dati creati dall'utente:

    for ds in ws.user_datasets:
        print(ds.name)

Per enumerare solo i set di dati di esempio:

    for ds in ws.example_datasets:
        print(ds.name)

È possibile accedere a un set di dati per nome (si applica la distinzione maiuscole/minuscole):

    ds = ws.datasets['my dataset name']

In alternativa, è possibile accedere tramite indice:

    ds = ws.datasets[0]


### Metadata

I set di dati sono composti non solo dal contenuto ma anche da metadati (i set di dati intermedi rappresentano un'eccezione a questa regola poiché non contengono metadati).

Alcuni valori di metadati vengono assegnati dall'utente in fase di creazione:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Altri valori vengono assegnati da Azure Machine Learning:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Vedere la classe `SourceDataset` per altre informazioni sui metadati disponibili.


### Leggere il contenuto

I frammenti di codice forniti da Machine Learning Studio scaricano e deserializzano automaticamente il set di dati in un oggetto Pandas DataFrame. Questa operazione viene eseguita con il metodo `to_dataframe`:

    frame = ds.to_dataframe()

Se lo si preferisce, è possibile scaricare i dati non elaborati ed eseguire la deserializzazione manualmente. Questa è l'unica alternativa per i formati come "ARFF" che la libreria client Python non è attualmente in grado di deserializzare.

Per leggere il contenuto come file di testo:

    text_data = ds.read_as_text()

Per leggere il contenuto come file binario:

    binary_data = ds.read_as_binary()

È anche possibile aprire un flusso nel contenuto:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### Creare un nuovo set di dati

La libreria client Python consente di caricare set di dati dal programma Python, per poterli usare nell'area di lavoro.

Se i dati sono contenuti in un oggetto DataFrame Pandas, usare il codice seguente:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Se invece i dati sono già serializzati, è possibile usare:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

La libreria client Python è in grado di serializzare un oggetto DataFrame Pandas nei formati seguenti (le costanti per questi formati sono disponibili nella classe `azureml.DataTypeIds`):

 - PlainText
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader


### Aggiornare un set di dati esistente

Se si tenta di caricare un nuovo set di dati con un nome corrispondente a un set di dati esistente, si potrebbe ottiene un errore di conflitto.

Per aggiornare un set di dati esistente, è necessario prima ottenere un riferimento a tale set di dati:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Quindi, è necessario usare `update_from_dataframe` per serializzare e sostituire il contenuto del set di dati in Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Se si desidera serializzare i dati in un formato diverso, specificare un valore per il parametro `data_type_id` facoltativo.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

È possibile anche impostare una nuova descrizione specificando un valore per il parametro `description`.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

È possibile anche impostare un nuovo nome specificando un valore per il parametro `name`. D'ora in poi sarà possibile recuperare il set di dati usando solo il nuovo nome. Il codice seguente consente di aggiornare i dati, il nome e la descrizione.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

I parametri `data_type_id`, `name` e `description` sono facoltativi e, per impostazione predefinita, vengono ripristinati ai valori precedenti. Il parametro `dataframe` è sempre obbligatorio.

Se i dati sono già serializzati, usare `update_from_raw_data` anziché `update_from_dataframe`. Il funzionamento è analogo: è sufficiente passare `raw_data` invece di `dataframe`.



<!-- Images -->  
[security]: ./media/machine-learning-python-data-access/security.png
[dataset-format]: ./media/machine-learning-python-data-access/dataset-format.png
[csv-format]: ./media/machine-learning-python-data-access/csv-format.png
[datasets]: ./media/machine-learning-python-data-access/datasets.png
[dataset-access-code]: ./media/machine-learning-python-data-access/dataset-access-code.png
[ipython-dataset]: ./media/machine-learning-python-data-access/ipython-dataset.png
[experiment]: ./media/machine-learning-python-data-access/experiment.png
[intermediate-dataset-access-code]: ./media/machine-learning-python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]: ./media/machine-learning-python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]: ./media/machine-learning-python-data-access/ipython-histogram.png


<!-- Module References -->  
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 

<!---HONumber=AcomDC_0914_2016-->