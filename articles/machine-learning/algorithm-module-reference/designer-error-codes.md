---
title: Risolvere gli errori del modulo
titleSuffix: Azure Machine Learning
description: Risolvere i problemi relativi alle eccezioni del modulo nella finestra di progettazione di Azure Machine Learning usando i codici di errore
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/16/2020
ms.openlocfilehash: bfb70aaa092cc62fbff87e9e3e327ee7364f8701
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83833814"
---
# <a name="exceptions-and-error-codes-for-the-designer-preview"></a>Eccezioni e codici di errore per la finestra di progettazione (anteprima)

Questo articolo descrive i messaggi di errore e i codici di eccezione nella finestra di progettazione di Azure Machine Learning (anteprima) per risolvere i problemi relativi alle pipeline di Machine Learning.

È possibile trovare il messaggio di errore nella finestra di progettazione, seguendo la procedura seguente:  

- Selezionare il modulo non riuscito, passare alla scheda **Outputs+logs** (Output e log), è possibile trovare il log dettagliato nel file **70_driver_log.txt** della categoria **azureml-logs**.

- È possibile controllare l'errore dettagliato del modulo nel file error_info.json della categoria **module_statistics**.

Sotto sono riportati i codici di errore dei moduli nella finestra di progettazione.

## <a name="error-0001"></a>Errore 0001  
 L'eccezione si verifica se non è possibile trovare una o più colonne specificate del set di dati.  

 Questo errore viene visualizzato se si esegue una selezione di colonne per un modulo, ma le colonne selezionate non esistono nel set di dati di input. L'errore può verificarsi se il nome di una colonna è stato digitato manualmente o se il selettore di colonne ha specificato una colonna suggerita che non esisteva nel set di dati quando è stata eseguita la pipeline.  

**Risoluzione:** Rivedere il modulo che genera l'eccezione e verificare che il nome della colonna è corretto e che tutte le colonne a cui si fa riferimento esistano.  

|Messaggi di eccezione|
|------------------------|
|One or more specified columns were not found. (Non è stato possibile trovare una o più colonne.)|
|Column with name or index "{column_id}" not found. (Non è stato possibile trovare la colonna con il nome o l'indice "{column_id}".)|
|Column with name or index "{column_id}" does not exist in "{arg_name_missing_column}". (La colonna con il nome o l'indice "{column_id}" non esiste in "{arg_name_missing_column}".)|
|Column with name or index "{column_id}" does not exist in "{arg_name_missing_column}", but exists in "{arg_name_has_column}". (La colonna con il nome o l'indice "{column_id}" non esiste in "{arg_name_missing_column}", ma esiste in "{arg_name_has_column}".)|
|Columns with name or index "{column_names}" not found. (Non sono state trovate le colonne con il nome o l'indice "{column_names}".)|
|Columns with name or index "{column_names}" does not exist in "{arg_name_missing_column}". (Le colonne con il nome o l'indice "{column_names}" non esistono in "{arg_name_missing_column}".)|
|Columns with name or index "{column_names}" does not exist in "{arg_name_missing_column}", but exists in "{arg_name_has_column}". (Le colonne con il nome o l'indice "{column_names}" non esistono in "{arg_name_missing_column}", ma esistono in "{arg_name_has_column}".)|


## <a name="error-0002"></a>Errore 0002  
 L'eccezione si verifica se non è stato possibile analizzare o convertire uno o più parametri dal tipo specificato nel tipo richiesto dal metodo di destinazione.  

 Questo errore si verifica in Azure Machine Learning quando si specifica un parametro come input, il tipo di valore è diverso dal tipo previsto e non è possibile eseguire la conversione implicita.  

**Risoluzione:** Verificare i requisiti del modulo e determinare quale tipo di valore è richiesto: stringa, numero intero, valore doppio e così via.  

|Messaggi di eccezione|
|------------------------|
|Failed to parse parameter. (Non è stato possibile analizzare il parametro).|
|Failed to parse "{arg_name_or_column}" parameter. (Non è stato possibile analizzare il parametro "{arg_name_or_column}".)|
|Failed to convert "{arg_name_or_column}" parameter to "{to_type}". (Non è stato possibile convertire il parametro "{arg_name_or_column}" in "{to_type}".)|
|Failed to convert "{arg_name_or_column}" parameter from "{from_type}" to "{to_type}". (Non è stato possibile convertire il parametro "{arg_name_or_column}" da "{from_type}" in "{to_type}".)|
|Failed to convert "{arg_name_or_column}" parameter value "{arg_value}" from "{from_type}" to "{to_type}". (Non è stato possibile convertire il valore "{arg_value}" del parametro "{arg_name_or_column}" da "{from_type}" in "{to_type}".)|
|Failed to convert value "{arg_value}" in column "{arg_name_or_column}" from "{from_type}" to "{to_type}" with usage of the format "{fmt}" provided. (Non è stato possibile convertire il valore "{arg_value}" nella colonna "{arg_name_or_column}" da "{from_type}" in "{to_type}" con l'uso del formato "{fmt}" specificato.)|


## <a name="error-0003"></a>Errore 0003  
 L'eccezione si verifica se uno o più input sono null o vuoti.  

 Questo errore verrà visualizzato in Azure Machine Learning se ci sono input o parametri di un modulo null o vuoti.  Questo errore può verificarsi, ad esempio, quando non è stato digitato alcun valore per un parametro. Può verificarsi anche se si sceglie un set di dati senza valori o un set di dati vuoto.  

**Risoluzione:**

+ Aprire il modulo che ha generato l'eccezione e verificare che tutti gli input siano stati specificati. Verificare che siano specificati tutti gli input obbligatori. 
+ Assicurarsi che i dati caricati dall'archiviazione di Azure siano accessibili e che il nome o la chiave dell'account non siano stati modificati.  
+ Controllare che nei dati di input i valori non siano mancanti o null.
+ Se si usa una query in un'origine dati, verificare che i dati vengano restituiti nel formato previsto. 
+ Verificare la presenza di errori di digitazione o altre modifiche nella specifica dei dati.
  
|Messaggi di eccezione|
|------------------------|
|One or more of inputs are null or empty. (Uno o più input sono null o vuoti.)|
|Input "{name}" is null or empty.(L'input "{name}" è null o vuoto.)|


## <a name="error-0004"></a>Errore 0004  
 L'eccezione si verifica se un parametro è inferiore o uguale a un valore specifico.  

 Questo errore verrà visualizzato in Azure Machine Learning se il parametro nel messaggio è inferiore a un valore limite necessario affinché il modulo elabori i dati.  

**Risoluzione:** Rivedere il modulo che genera l'eccezione e modificare il parametro in modo che sia maggiore del valore specificato.  

|Messaggi di eccezione|
|------------------------|
|Parameter should be greater than boundary value. (Il parametro deve essere maggiore del valore limite.)|
|Parameter "{arg_name}" value should be greater than {lower_boundary}. (Il valore del parametro "{arg_name}" deve essere maggiore di {lower_boundary}.)|
|Parameter "{arg_name}" has value "{actual_value}" which should be greater than {lower_boundary}. (Il parametro "{arg_name}" ha un valore "{actual_value}" che deve essere maggiore di {lower_boundary}.)|


## <a name="error-0005"></a>Errore 0005  
 L'eccezione si verifica se il parametro è inferiore a un valore specifico.  

 Questo errore verrà visualizzato in Azure Machine Learning se il parametro nel messaggio è inferiore o uguale a un valore limite necessario affinché il modulo elabori i dati.  

**Risoluzione:** Rivedere il modulo che genera l'eccezione e modificare il parametro in modo che sia maggiore o uguale al valore specificato.  

|Messaggi di eccezione|
|------------------------|
|Parameter should be greater than or equal to boundary value. (Il valore del parametro deve essere maggiore o uguale al valore limite.)|
|Parameter "{arg_name}" value should be greater than or equal to {lower_boundary}. (Il valore del parametro "{arg_name}" deve essere maggiore o uguale a {lower_boundary}.)|
|Parameter "{arg_name}" has value "{value}" which should be greater than or equal to {lower_boundary}. (Il parametro "{arg_name}" ha un valore "{value}" che deve essere maggiore o uguale a {lower_boundary}.)|


## <a name="error-0006"></a>Errore 0006  
 L'eccezione si verifica se il parametro è maggiore o uguale al valore specificato.  

 Questo errore verrà visualizzato in Azure Machine Learning se il parametro nel messaggio è maggiore o uguale a un valore limite necessario affinché il modulo elabori i dati.  

**Risoluzione:** Rivedere il modulo che genera l'eccezione e modificare il parametro in modo che sia inferiore al valore specificato.  

|Messaggi di eccezione|
|------------------------|
|Parameters mismatch. (Parametri non corrispondenti.) One of the parameters should be less than another. (Il valore di uno dei parametri deve essere minore del valore dell'altro.)|
|Parameter "{arg_name}" value should be less than parameter "{upper_boundary_parameter_name}" value. (Il valore del parametro "{arg_name}" deve essere minore del parametro "{upper_boundary_parameter_name}".)|
|Parameter "{arg_name}" has value "{value}" which should be less than {upper_boundary_parameter_name (Il parametro "{arg_name}" ha un valore "{value}" che deve essere minore di {upper_boundary_parameter_name}.)|


## <a name="error-0007"></a>Errore 0007  
 L'eccezione si verifica se il parametro è maggiore di un valore specifico.  

 Questo errore verrà visualizzato in Azure Machine Learning se, nelle proprietà del modulo, è stato specificato un valore maggiore di quello consentito. È possibile, ad esempio, specificare un valore che non è compreso nell'intervallo di date supportate. In alternativa, è possibile indicare che vengono usate cinque colonne quando sono disponibili solo tre colonne. 

 Questo errore può essere visualizzato anche se si specificano due set di dati che devono corrispondere. Se, ad esempio, si rinominano le colonne e si specificano le colonne in base all'indice, il numero di nomi specificati deve corrispondere al numero di indici di colonna. Un altro esempio potrebbe essere un'operazione matematica che usa due colonne, in cui le colonne devono avere lo stesso numero di righe. 

**Risoluzione:**

 + Aprire il modulo in questione ed esaminare le impostazioni delle proprietà numeriche.
 + Verificare che i valori dei parametri rientrino nell'intervallo di valori supportato per quella proprietà.
 + Se il modulo riceve più input, verificare che le dimensioni degli input siano uguali.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Controllare che il set di dati o l'origine dati siano stati modificati. Talvolta un valore che funzionava con una versione precedente dei dati non sarà adeguato dopo che il numero di colonne, i tipi di dati della colonna o le dimensioni dei dati sono stati modificati.  

|Messaggi di eccezione|
|------------------------|
|Parameters mismatch. (Parametri non corrispondenti.) One of the parameters should be less than or equal to another. (Il valore di uno dei parametri deve essere maggiore o uguale al valore dell'altro.)|
|Parameter "{arg_name}" value should be less than or equal to parameter "{upper_boundary_parameter_name}" value. (Il valore del parametro "{arg_name}" deve essere minore o uguale al valore del parametro "{upper_boundary_parameter_name}".)|
|Parameter "{arg_name}" has value "{actual_value}" which should be less than or equal to {upper_boundary}. (Il parametro "{arg_name}" ha un valore "{actual_value}" che deve essere minore o uguale a {upper_boundary}.)|
|Parameter "{arg_name}" value {actual_value} should be less than or equal to parameter "{upper_boundary_parameter_name}" value {upper_boundary}. (Il valore {actual_value} del parametro "{arg_name}" deve essere minore o uguale al valore del parametro "{upper_boundary_parameter_name}".)|
|Parameter "{arg_name}" value {actual_value} should be less than or equal to {upper_boundary_meaning} value {upper_boundary}. (Il valore {actual_value} del parametro "{arg_name}" deve essere minore o uguale al valore {upper_boundary} del parametro "{upper_boundary_meaning}".)|


## <a name="error-0008"></a>Errore 0008  
 L'eccezione si verifica se il parametro non rientra nell'intervallo.  

 Questo errore verrà visualizzato in Azure Machine Learning se il parametro nel messaggio non è compreso nei limiti necessari affinché il modulo elabori i dati.  

 Questo errore viene visualizzato, ad esempio, se si tenta di usare [Aggiungi righe](add-rows.md) per combinare due set di dati che hanno un numero diverso di colonne.  

**Risoluzione:** Rivedere il modulo che genera l'eccezione e modificare il parametro in modo che sia compreso nell'intervallo specificato.  

|Messaggi di eccezione|
|------------------------|
|Parameter value is not in the specified range. (Il valore del parametro non è compreso nell'intervallo specificato.)|
|Parameter "{arg_name}" value is not in range. (Il valore del parametro "{arg_name}" non è compreso nell'intervallo.)|
|Parameter "{arg_name}" value should be in the range of [{lower_boundary}, {upper_boundary}]. (Il valore del parametro "{arg_name}" deve essere compreso nell'intervallo [{lower_boundary}, {upper_boundary}].)|
|Parameter "{arg_name}" value is not in range. (Il valore del parametro "{arg_name}" non è compreso nell'intervallo.) {reason}|


## <a name="error-0009"></a>Errore 0009  
 L'eccezione si verifica quando il nome dell'account di Archiviazione di Azure o il nome del contenitore non è stato specificato correttamente.  

Questo errore si verifica nella finestra di progettazione di Azure Machine Learning quando si specificano i parametri per un account di archiviazione di Azure, ma non è possibile risolvere il nome o la password. Gli errori di password o relativi ai nomi degli account possono verificarsi per diversi motivi:

 + Il tipo di account non è corretto. Alcuni nuovi tipi di account non sono supportati per l'uso con la finestra di progettazione di Machine Learning. Per informazioni dettagliate, vedere [Importazione dei dati](import-data.md).
 + Il nome dell'account inserito non è corretto
 + L'account non esiste più
 + La password per l'account di archiviazione è errata o è stata modificata
 + Il nome del contenitore non è stato specificato oppure il contenitore non esiste
 + Non è stato specificato il percorso del file completo (percorso del BLOB)
   

**Risoluzione:**

Questi problemi si verificano spesso quando si immette manualmente il nome dell'account, la password o il percorso del contenitore. Si consiglia di usare la nuova procedura guidata per il modulo [Importazione dei dati](import-data.md), che consente di cercare e controllare i nomi.

Controllare anche se l'account, il contenitore o il BLOB sono stati eliminati. Usare un'altra utilità di archiviazione di Azure per verificare che il nome e la password dell'account siano stati immessi correttamente e che il contenitore esista. 

Alcuni tipi di account più recenti non sono supportati da Azure Machine Learning. Ad esempio, non è possibile usare i nuovi tipi di archiviazione "ad accesso frequente" o "offline sicura" per Machine Learning. Sia gli account di archiviazione classica che gli account di archiviazione creati per "utilizzo generico" funzionano correttamente.

Se è stato specificato il percorso completo di un BLOB, verificare che il percorso sia specificato come **container/blobname** e che sia il contenitore che il BLOB esistano nell'account.  

 Il percorso non deve contenere una barra iniziale. Ad esempio **/container/blob** non è corretto e deve essere immesso come **container/blob**.  


|Messaggi di eccezione|
|------------------------|
|The Azure storage account name or container name is incorrect. (Il nome dell'account o del contenitore di archiviazione di Azure non è corretto.)|
|The Azure storage account name "{account_name}" or container name "{container_name}" is incorrect; a container name of the format container/blob was expected (Il nome "{account_name}" dell'account o il nome "{container_name}" del contenitore di archiviazione di Azure non è corretto. È previsto un nome di contenitore nel formato contenitore/BLOB.)|


## <a name="error-0010"></a>Errore 0010  
 L'eccezione si verifica se i nomi di colonna nei set di dati di input non corrispondono, mentre dovrebbero essere corrispondenti.  

 Questo errore verrà visualizzato in Azure Machine Learning se l'indice di colonna nel messaggio ha nomi di colonna diversi nei due set di dati di input.  

**Risoluzione:** Usare [Modifica metadati](edit-metadata.md) o modificare il set di dati originale in modo da avere lo stesso nome di colonna per l'indice di colonna specificato.  

|Messaggi di eccezione|
|------------------------|
|Columns with corresponding index in input datasets have different names. (Le colonne con indice corrispondente nei set di dati di input hanno nomi diversi.)|
|Column names are not the same for column {col_index} (zero-based) of input datasets ({dataset1} and {dataset2} respectively). (I nomi di colonna non sono gli stessi per la colonna {col_index} (in base zero) dei set di dati di input ({dataset1} e {dataset2} rispettivamente).)|


## <a name="error-0011"></a>Errore 0011  
 L'eccezione si verifica se l'argomento passato per il set di colonne non è applicabile ad alcuna colonna del set di dati.  

 Questo errore verrà visualizzato in Azure Machine Learning se la selezione della colonna specificata non corrisponde a nessuna delle colonne nel set di dati specificato.  

 È possibile ottenere questo errore anche se non è stata selezionata una colonna ed è necessaria almeno una colonna affinché il modulo funzioni.  

**Risoluzione:** Modificare la selezione delle colonne nel modulo in modo che venga applicata alle colonne nel set di dati.  

 Se per il modulo è necessario selezionare una colonna specifica, ad esempio una colonna etichetta, verificare che sia selezionata la colonna corretta.  

 Se vengono selezionate colonne errate, rimuoverle ed eseguire di nuovo la pipeline.  

|Messaggi di eccezione|
|------------------------|
|Specified column set does not apply to any of dataset columns. (Il set di colonne specificato non è applicabile ad alcuna colonna del set di dati.)|
|Specified column set "{column_set}" does not apply to any of dataset columns. (Il set di colonne specificato "{column_set}" non è applicabile ad alcuna colonna del set di dati.)|


## <a name="error-0012"></a>Errore 0012  
 L'eccezione si verifica se l'istanza della classe non può essere creata con il set di argomenti passato.  

**Risoluzione:** L'utente non può intervenire e l'errore verrà deprecato in una versione futura.  

|Messaggi di eccezione|
|------------------------|
|Untrained model, please train model first. (Modello non sottoposto a training. Eseguire prima il training del modello.)|
|Untrained model ({arg_name}), use trained model. (Modello ({arg_name}) non sottoposto a training, usare un modello con training.)|


## <a name="error-0013"></a>Errore 0013  
 L'eccezione si verifica se il tipo di apprendimento passato al modulo non è valido.  

 Questo errore si verifica ogni volta che un modello con training non è compatibile con il modulo di punteggio connesso. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**Risoluzione:**

Determinare il tipo di apprendimento prodotto dal modulo di training e il modulo di punteggio adatto all'apprendimento. 

Se è stato eseguito il training del modello usando uno dei moduli di training specializzati, connettere il modello con training solo al modulo di punteggio specializzato corrispondente. 


|Tipo di modello|Modulo di training| Modulo di punteggio|
|----|----|----|
|un classificatore qualsiasi|[Eseguire il training del modello](train-model.md) |[Score Model](score-model.md) (Punteggio modello)|
|un modello di regressione qualsiasi|[Eseguire il training del modello](train-model.md) |[Score Model](score-model.md) (Punteggio modello)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->

|Messaggi di eccezione|
|------------------------|
|Learner of invalid type is passed. (È stato passato un tipo di apprendimento non valido.)|
|Learner "{arg_name}" has invalid type. (Il tipo di apprendimento "{arg_name}" non è valido.)|
|Learner "{arg_name}" has invalid type "{learner_type}". (Il tipo "{learner_type}" dell'apprendimento "{arg_name}" non è valido.)|
|Learner of invalid type is passed. Exception message: {exception_message} (È stato passato un tipo di apprendimento non valido. Messaggio di eccezione: {exception_message})|


## <a name="error-0014"></a>Errore 0014  
 L'eccezione si verifica se il numero di valori univoci di colonna è superiore a quella consentita.  

 Questo errore si verifica quando una colonna contiene troppi valori univoci.  Ad esempio, è possibile visualizzare questo errore se si specifica che una colonna deve essere gestita come dati di categoria, ma nella colonna sono presenti troppi valori univoci per consentire il completamento dell'elaborazione. Questo errore può essere visualizzato anche in caso di mancata corrispondenza tra il numero di valori univoci in due input.   

**Risoluzione:**

Aprire il modulo che ha generato l'errore e identificare le colonne usate come input. Per alcuni moduli, è possibile fare clic con il pulsante destro del mouse sull'input del set di dati e selezionare **Visualizza** per vedere le statistiche sulle singole colonne, insieme al numero di valori univoci e alla loro distribuzione.

Per le colonne che si desidera usare per il raggruppamento o la categorizzazione, adottare le misure necessarie per ridurre il numero di valori univoci nelle colonne. È possibile ridurlo in modi diversi, a seconda del tipo di dati della colonna. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Se non è possibile trovare una soluzione che corrisponda al proprio scenario, inviare un feedback su questo argomento che includa il nome del modulo che ha generato l'errore, il tipo di dati e la cardinalità della colonna. Le informazioni verranno usate per indicare procedure di risoluzione più mirate per gli scenari comuni.   

|Messaggi di eccezione|
|------------------------|
|Amount of column unique values is greater than allowed. (La quantità di valori univoci della colonna è superiore al limite consentito.)|
|Number of unique values in column: "{column_name}" is greater than allowed. (Il numero di valori univoci nella colonna "{column_name}" è maggiore di quello consentito.)|
|Number of unique values in column: "{column_name}" exceeds tuple count of {limitation}. (Il numero di valori univoci nella colonna "{column_name}" supera il numero di tupla di {limitation}.)|


## <a name="error-0015"></a>Errore 0015  
 L'eccezione si verifica se la connessione al database non è riuscita.  

 Questo errore viene visualizzato se si immette un nome, una password, un server di database o un nome di database dell'account SQL errato oppure se non è possibile stabilire una connessione con il database a causa di problemi con il database o con il server.  

**Risoluzione:** Verificare che il nome dell'account, la password, il server di database e il database siano stati immessi correttamente e che l'account specificato disponga del livello di autorizzazioni corretto. Verificare che il database sia attualmente accessibile.  

|Messaggi di eccezione|
|------------------------|
|Error making database connection. (Errore durante la connessione al database.)|
|Error making database connection: {connection_str}. (Errore durante la connessione al database: {connection_str}.)|


## <a name="error-0016"></a>Errore 0016  
 L'eccezione si verifica se i tipi di colonna dei set di dati di input passati al modulo dovrebbero essere compatibili ma non lo sono.  

 Questo errore verrà visualizzato in Azure Machine Learning se i tipi di colonna passati in due o più set di dati non sono compatibili tra loro.  

**Risoluzione:** Usare [Modifica metadati](edit-metadata.md) oppure modificare il set di dati di input originale<!--, or use [Convert to Dataset](convert-to-dataset.md)--> per assicurarsi che i tipi delle colonne siano compatibili.  

|Messaggi di eccezione|
|------------------------|
|Columns with corresponding index in input datasets do have incompatible types. (Le colonne con indice corrispondente nei set di dati di input hanno tipi incompatibili.)|
|Columns '{first_col_names}' are incompatible between train and test data. (Le colonne "{first_col_names}" non sono compatibili tra i dati di training e di test.)|
|Columns '{first_col_names}' and '{second_col_names}' are incompatible. (Le colonne "{first_col_names}" e "{second_col_names}" non sono compatibili.)|
|Column element types are not compatible for column '{first_col_names}' (zero-based) of input datasets ({first_dataset_names} and {second_dataset_names} respectively). (I tipi di elemento della colonna non sono compatibili per la colonna "{first_col_names}" (in base zero) dei set di dati di input (rispettivamente {first_dataset_names} e {second_dataset_names})).|


## <a name="error-0017"></a>Errore 0017  
 L'eccezione si verifica se una colonna selezionata usa un tipo di dati che non supportato dal modulo corrente.  

 Ad esempio, è possibile che venga visualizzato questo errore in Azure Machine Learning se la selezione della colonna include una colonna con un tipo di dati che non può essere elaborato dal modulo, ad esempio una colonna di stringhe per un'operazione matematica o una colonna punteggio in cui è richiesta una colonna per la funzionalità di categoria.  

**Risoluzione:**
 1. Identificare la colonna che genera il problema.
 2. Esaminare i requisiti del modulo.
 3. Modificare la colonna per renderla conforme ai requisiti. Potrebbe essere necessario usare diversi moduli tra quelli seguenti per apportare modifiche, a seconda della colonna e della conversione che si sta tentando di eseguire:
    + Usare [Modifica metadati](edit-metadata.md) per modificare il tipo di dati delle colonne o per modificare l'utilizzo delle colonne da funzionalità a numerica, da categoria a non di categoria e così via.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Come ultima risorsa, potrebbe essere necessario modificare il set di dati di input originale.

> [!TIP]
> Se non è possibile trovare una soluzione che corrisponda al proprio scenario, inviare un feedback su questo argomento che includa il nome del modulo che ha generato l'errore, il tipo di dati e la cardinalità della colonna. Le informazioni verranno usate per indicare procedure di risoluzione più mirate per gli scenari comuni. 

|Messaggi di eccezione|
|------------------------|
|Cannot process column of current type. The type is not supported by the module. (Non è possibile elaborare la colonna del tipo corrente. Il tipo non è supportato dal modulo.)|
|Cannot process column of type {col_type}. The type is not supported by the module. (Non è possibile elaborare la colonna di tipo {col_type}. Il tipo non è supportato dal modulo.)|
|Cannot process column "{col_name}" of type {col_type}. The type is not supported by the module. (Non è possibile elaborare la colonna "{col_name}" di tipo {col_type}. Il tipo non è supportato dal modulo.)|
|Cannot process column "{col_name}" of type {col_type}. The type is not supported by the module. Parameter name: {arg_name}. (Non è possibile elaborare la colonna "{col_name}" di tipo {col_type}. Il tipo non è supportato dal modulo. Nome del parametro: {arg_name}.)|


## <a name="error-0018"></a>Errore 0018  
 L'eccezione si verifica se il set di dati di input non è valido.  

**Risoluzione:** Questo errore può essere visualizzato in molti contesti di Azure Machine Learning, quindi non esiste una sola risoluzione. In generale, l'errore indica che i dati inseriti come input per un modulo hanno un numero di colonne errato oppure che il tipo di dati non corrisponde ai requisiti del modulo. Ad esempio:  

-   Il modulo richiede una colonna per le etichette, ma nessuna colonna è contrassegnata come tale oppure non è ancora stata selezionata una colonna per le etichette.  
  
-   È necessario che i dati siano di categoria, ma sono numerici.  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   Il formato dei dati non è corretto.  
  
-   I dati importati contengono caratteri non validi, valori non validi o valori non compresi nell'intervallo.  
-   La colonna è vuota oppure troppi valori sono mancanti.  

 Per determinare i requisiti e la modalità di utilizzo dei dati, vedere l'argomento della Guida per il modulo che userà il set di dati come input.  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|Messaggi di eccezione|
|------------------------|
|Dataset is not valid. (Il set di dati non è valido.)|
|{dataset1} contiene dati non validi.|
|{dataset1} and {dataset2} should be consistent columnwise. ({dataSet1} e {dataset2} devono essere coerenti nella colonna.)|
|{dataset1} contiene dati non validi, {reason}.|
|{dataset1} contiene {invalid_data_category}. {troubleshoot_hint}|
|{dataset1} non è valido, {reason}. {troubleshoot_hint}|


## <a name="error-0019"></a>Errore 0019  
 L'eccezione si verifica se è previsto che la colonna contenga valori ordinati, ma tali valori non sono presenti.  

 Questo errore verrà visualizzato in Azure Machine Learning se i valori della colonna specificati non sono ordinati.  

**Risoluzione:** Ordinare i valori della colonna modificando manualmente il set di dati di input e rieseguire il modulo.  

|Messaggi di eccezione|
|------------------------|
|Values in column are not sorted. (I valori della colonna non sono ordinati.)|
|Values in column "{col_index}" are not sorted. (I valori della colonna "{col_index}" non sono ordinati.)|
|Values in column "{col_index}" of dataset "{dataset}" are not sorted. (I valori della colonna "{col_index}" del set di dati "{dataset}" non sono ordinati.)|
|Values in argument "{arg_name}" are not sorted in "{sorting_order}" order. (I valori dell'argomento "{arg_name}" non sono ordinati nell'ordine in "{sorting_order}".)|


## <a name="error-0020"></a>Errore 0020  
 L'eccezione si verifica se il numero di colonne in alcuni set di dati passati al modulo è troppo piccolo.  

 Questo errore verrà visualizzato in Azure Machine Learning se non è stato selezionato un numero di colonne sufficienti per un modulo.  

**Risoluzione:** Rivedere il modulo e assicurarsi che il selettore di colonne abbia selezionato il numero corretto di colonne.  

|Messaggi di eccezione|
|------------------------|
|Number of columns in input dataset is less than allowed minimum. (Il numero di colonne nel set di dati di input è inferiore al minimo consentito.)|
|Number of columns in input dataset "{arg_name}" is less than allowed minimum. (Il numero di colonne nel set di dati di input "{arg_name}" è inferiore al minimo consentito.)|
|Number of columns in input dataset is less than allowed minimum of {required_columns_count} column(s). (Il numero di colonne nel set di dati di input è inferiore al minimo consentito di {required_columns_count} colonna/e).|
|Number of columns in input dataset "{arg_name}" is less than allowed minimum of {required_columns_count} column(s). (Il numero di colonne nel set di dati di input "{arg_name}" è inferiore al minimo consentito di {required_columns_count} colonna/e).|


## <a name="error-0021"></a>Errore 0021  
 L'eccezione si verifica se il numero di righe in alcuni set di dati passati al modulo è troppo piccolo.  

 Questo errore si verifica in Azure Machine Learning quando nel set di dati non ci sono righe sufficienti per eseguire l'operazione specificata. Ad esempio, è possibile che venga visualizzato se il set di dati di input è vuoto o se si sta tentando di eseguire un'operazione che richiede un numero minimo di righe per essere valida. Tali operazioni possono includere, ma non solo, il raggruppamento o la classificazione in base a metodi statistici, determinati tipi di binning e apprendimento con conteggi.  

**Risoluzione:**

 + Aprire il modulo che ha restituito l'errore e controllare le proprietà del modulo e del set di dati di input. 
 + Verificare che il set di dati di input non sia vuoto e che ci siano righe di dati sufficienti per soddisfare i requisiti descritti nella guida del modulo.  
 + Se i dati vengono caricati da un'origine esterna, assicurarsi che l'origine dati sia disponibile e che non siano presenti errori o modifiche nella definizione dei dati che potrebbero generare un minor numero di righe per il processo di importazione.
 + Se si sta eseguendo un'operazione sull'upstream dei dati del modulo che potrebbero influire sul tipo di dati o sul numero di valori, ad esempio un'operazione di pulizia, suddivisione o join, controllare gli output di tali operazioni per determinare il numero di righe restituite.  

|Messaggi di eccezione|
|------------------------|
|Number of rows in input dataset is less than allowed minimum. (Il numero di righe nel set di dati di input è inferiore al minimo consentito.)|
|Number of rows in input dataset is less than allowed minimum of {required_rows_count} row(s). (Il numero di righe nel set di dati di input è inferiore al minimo consentito di {required_rows_count} riga/righe.)|
|Number of rows in input dataset is less than allowed minimum of {required_rows_count} row(s). (Il numero di righe nel set di dati di input è inferiore al minimo consentito di {required_rows_count} riga/righe.) {reason}|
|Number of rows in input dataset "{arg_name}" is less than allowed minimum of {required_rows_count} row(s). (Il numero di righe nel set di dati di input "{arg_name}" è inferiore al minimo consentito di {required_rows_count} riga/righe.)|
|Number of rows in input dataset "{arg_name}" is {actual_rows_count}, less than allowed minimum of {required_rows_count} row(s). (Il numero di righe nel set di dati di input "{arg_name}" è {actual_rows_count}, inferiore al minimo consentito di {required_rows_count} riga/righe.)|
|Number of "{row_type}" rows in input dataset "{arg_name}" is {actual_rows_count}, less than allowed minimum of {required_rows_count} row(s). (Il numero di righe "{row_type}" nel set di dati di input "{arg_name}" è {actual_rows_count}, inferiore al minimo consentito di {required_rows_count} riga/righe.)|


## <a name="error-0022"></a>Errore 0022  
 L'eccezione si verifica se il numero di colonne selezionate nel set di dati di input non corrisponde al numero previsto.  

 Questo errore in Azure Machine Learning può verificarsi quando il modulo o l'operazione di downstream richiede un numero specifico di colonne o di input e sono state inserite troppe colonne o troppi input. Ad esempio:  

-   È possibile specificare una singola colonna etichetta o colonna chiave e selezionare accidentalmente più colonne.  
  
-   Si stanno rinominando le colonne, ma sono stati specificati troppi o pochi nomi rispetto alle colonne.  
  
-   Il numero di colonne nell'origine o nella destinazione è stato modificato o non corrisponde al numero di colonne usate dal modulo.  
  
-   È stato inserito un elenco delimitato da virgole di valori per gli input, ma il numero di valori non corrisponde oppure gli input multipli non sono supportati.  

**Risoluzione:** Rivedere il modulo e controllare la selezione delle colonne per assicurarsi che sia selezionato il numero corretto di colonne. Verificare gli output dei moduli di upstream e i requisiti delle operazioni di downstream.  

 Se è stata usata una delle opzioni di selezione delle colonne che può selezionare più colonne (indici di colonne, tutte funzionalità, tutte numeriche e così via), verificare il numero esatto di colonne restituite dalla selezione.  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 Verificare che il numero o il tipo di colonne di upstream non sia cambiato.  

 Se si usa un set di dati suggerito per eseguire il training di un modello, ricordare che la funzione di suggerimento prevede un numero limitato di colonne, corrispondente alle coppie utente-elemento o alla classificazione utente-elemento. Rimuovere le colonne aggiuntive prima di eseguire il training del modello o suddividere i set di dati suggeriti. Per altre informazioni, vedere [Dividere dati](split-data.md).  

|Messaggi di eccezione|
|------------------------|
|Number of selected columns in input dataset does not equal to the expected number. (Il numero di colonne selezionate nel set di dati di input non corrisponde al numero previsto.)|
|Number of selected columns in input dataset does not equal to {expected_col_count}. (Il numero di colonne selezionate nel set di dati di input non corrisponde a {expected_col_count}.)|
|Column selection pattern "{selection_pattern_friendly_name}" provides number of selected columns in input dataset not equal to {expected_col_count}. (Il criterio di selezione delle colonne "{selection_pattern_friendly_name}" specifica il numero di colonne selezionate nel set di dati di input diverso da {expected_col_count}.)|
|Column selection pattern "{selection_pattern_friendly_name}" is expected to provide {expected_col_count} column(s) selected in input dataset, but {selected_col_count} column(s) is/are actually provided. (Il criterio di selezione delle colonne "{selection_pattern_friendly_name}" deve specificare {expected_col_count} colonna/e selezionate nel set di dati di input, ma sono effettivamente specificate {selected_col_count} colonna/e.)|


## <a name="error-0023"></a>Errore 0023  

L'eccezione si verifica se la colonna di destinazione del set di dati di input non è valida per il modulo di training corrente.  

Questo errore si verifica in Azure Machine Learning se la colonna di destinazione (come selezionata nei parametri del modulo) non è del tipo di dati valido, è priva di valori o non era di categoria come previsto.  

**Risoluzione:** Rivedere l'input del modulo per esaminare il contenuto della colonna etichetta/destinazione. Verificare che i valori non siano mancanti. Se il modulo prevede che la colonna di destinazione sia di categoria, assicurarsi che siano presenti più valori distinct nella colonna di destinazione.  

|Messaggi di eccezione|
|------------------------|
|Input dataset has unsupported target column. (Il set di dati di input dispone di una colonna di destinazione non supportata.)|
|Input dataset has unsupported target column "{column_index}". (Il set di dati di input ha la colonna di destinazione "{column_index}" non supportata.)|
|Input dataset has unsupported target column "{column_index}" for learner of type {learner_type}. (Il set di dati di input ha la colonna di destinazione "{column_index}" non supportata per l'apprendimento del tipo {learner_type}.).|


## <a name="error-0024"></a>Errore 0024  
L'eccezione si verifica se il set di dati non contiene una colonna etichetta.  

 Questo errore si verifica in Azure Machine Learning quando il modulo richiede una colonna etichetta e il set di dati non ha tale colonna. Per la valutazione di un set di dati con punteggio, ad esempio, è in genere necessaria una colonna etichetta per calcolare le metriche di accuratezza.  

È anche possibile che nel set di dati sia presente una colonna etichetta, ma che non sia rilevata correttamente da Azure Machine Learning.

**Risoluzione:**

+ Aprire il modulo che ha generato l'errore e verificare la presenza di una colonna etichetta. Il nome o il tipo di dati della colonna non è rilevante, purché la colonna contenga un solo risultato (o una variabile dipendente) che si sta tentando di stimare. Se non si è certi della colonna con l'etichetta, cercare un nome generico, ad esempio *Classe* o *Destinazione*. 
+  Se il set di dati non include una colonna etichetta, è possibile che tale colonna sia stata rimossa upstream in modo esplicito o accidentale. È anche possibile che il set di dati non sia l'output di un modulo di punteggio upstream.
+ Per contrassegnare in modo esplicito la colonna come colonna etichetta, aggiungere il modulo [Modifica metadati](edit-metadata.md) e connettere il set di dati. Selezionare solo la colonna etichetta e selezionare **Etichetta** dall'elenco a discesa **Campi**. 
+ Se si sceglie la colonna sbagliata come etichetta, è possibile selezionare **Cancella etichetta** in **Campi** per correggere i metadati nella colonna. 
  
|Messaggi di eccezione|
|------------------------|
|There is no label column in dataset. (Nel set di dati non c'è una colonna etichetta.)|
|There is no label column in "{dataset_name}". (In "{dataset_name}" non c'è una colonna etichetta.)|


## <a name="error-0025"></a>Errore 0025  
 L'eccezione si verifica se il set di dati non contiene una colonna punteggio.  

 Questo errore si verifica in Azure Machine Learning se l'input per il modello di valutazione non contiene colonne punteggio valide. Ad esempio, l'utente tenta di valutare un set di dati prima di assegnare un punteggio con un modello con training corretto oppure la colonna punteggio è stata eliminata upstream in modo esplicito. Questa eccezione si verifica anche se le colonne punteggio nei due set di dati non sono compatibili. Ad esempio, si potrebbe provare a confrontare l'accuratezza di un regressore lineare con un classificatore binario.  

**Risoluzione:** Rivedere l'input per il modello di valutazione e verificare se contiene una o più colonne punteggio. Se non le contiene, al set di dati non è stato assegnato il punteggio oppure le colonne punteggio sono state eliminate in un modulo upstream.  

|Messaggi di eccezione|
|------------------------|
|There is no score column in dataset. (Non sono presenti colonne punteggio nel set di dati.)|
|There is no score column in "{dataset_name}". (Non sono presenti colonne di punteggio in "{dataset_name}".)|
|There is no score column in "{dataset_name}" that is produced by a "{learner_type}". Score the dataset using the correct type of learner. (Non sono presenti colonne punteggio in "{dataset_name}" prodotte da "{learner_type}". Assegnare un punteggio al set di dati usando il tipo di apprendimento corretto.)|


## <a name="error-0026"></a>Errore 0026  
 L'eccezione si verifica se non sono consentite colonne con lo stesso nome.  

 Questo errore si verifica in Azure Machine Learning se più colonne hanno lo stesso nome. Un motivo per cui si riceve questo errore è se il set di dati non ha una riga di intestazione e i nomi della colonna vengono assegnati in modo automatico: Col0, Col1 e così via.  

**Risoluzione:** Se le colonne hanno lo stesso nome, inserire un modulo [Modifica metadati](edit-metadata.md) tra il set di dati di input e il modulo. Usare il selettore di colonne in [Modifica metadati](edit-metadata.md) per selezionare le colonne da rinominare, digitando i nuovi nomi nella casella di testo **New column names** (Nuovi nomi di colonna).  

|Messaggi di eccezione|
|------------------------|
|Equal column names are specified in arguments. Equal column names are not allowed by module. (I nomi di colonna uguali sono specificati negli argomenti. I nomi di colonna uguali non sono consentiti dal modulo.)|
|Equal column names in arguments "{arg_name_1}" and "{arg_name_2}" are not allowed. Please specify different names. (I nomi di colonna uguali negli argomenti "{arg_name_1}" e "{arg_name_2}" non sono consentiti. Specificare nomi diversi.)|


## <a name="error-0027"></a>Errore 0027  
 L'eccezione si verifica quando due oggetti devono avere le stesse dimensioni ma non le hanno.  

 È un errore comune in Azure Machine Learning e le cause posso essere diverse.  

**Risoluzione:** Non esiste una risoluzione specifica. Tuttavia, è possibile verificare le condizioni seguenti:  

-   Se si rinominano le colonne, assicurarsi che ogni elenco (le colonne di input e l'elenco dei nuovi nomi) abbia lo stesso numero di elementi.  
  
-   Se si crea un join o si concatenano due set di dati, assicurarsi che abbiano lo stesso schema.  
  
-   Se si crea un join di due set di dati che hanno più colonne, verificare che le colonne chiave abbiano lo stesso tipo di dati e selezionare l'opzione **Consenti duplicati e mantieni l'ordine delle colonne nella selezione**.  

|Messaggi di eccezione|
|------------------------|
|The size of passed objects is inconsistent. (Le dimensioni degli oggetti passati non sono coerenti.)|
|The size of "{friendly_name1}" is inconsistent with size of "{friendly_name2}". (Le dimensioni di "{friendly_name1}" non sono coerenti con le dimensioni di "{friendly_name2}".)|


## <a name="error-0028"></a>Errore 0028  
 L'eccezione si verifica nel caso in cui il set di colonne contenga nomi di colonna duplicati e questo non è consentito.  

 Questo errore si verifica in Azure Machine Learning quando i nomi delle colonne sono duplicati, ovvero non sono univoci.  

**Risoluzione:** Se alcune colonne hanno lo stesso nome, aggiungere un'istanza di [Modifica metadati](edit-metadata.md) tra il set di dati di input e il modulo che genera l'errore. Usare il selettore di colonne in [Modifica metadati](edit-metadata.md) per selezionare le colonne da rinominare, quindi digitare i nomi delle nuove colonne nella casella di testo **New column names** (Nuovi nomi di colonna). Se si rinominano più colonne, verificare che i valori digitati in **New column names** (Nuovi nomi di colonna) siano univoci.  

|Messaggi di eccezione|
|------------------------|
|Column set contains duplicated column name(s). (Il set di colonne contiene nomi di colonna duplicati.)|
|The name "{duplicated_name}" is duplicated. (Il nome "{duplicated_name}" è duplicato.)|
|The name "{duplicated_name}" is duplicated in "{arg_name}". (Il nome "{duplicated_name}" è duplicato in "{arg_name}.)|
|The name "{duplicated_name}" is duplicated. (Il nome "{duplicated_name}" è duplicato.) Details: {details} (Il nome "{duplicated_name}" è duplicato. Dettagli: {Details})|


## <a name="error-0029"></a>Errore 0029  
 L'eccezione si verifica quando viene passato un URI non valido.  

 Questo errore si verifica in Azure Machine Learning nel caso in cui venga passato un URI non valido.  Questo errore viene visualizzato se si verifica una delle condizioni seguenti:  

-   L'URI pubblico o SAS specificato in Archiviazione BLOB di Azure per la lettura o la scrittura contiene un errore.  
  
-   L'intervallo di tempo per la firma di accesso condiviso è scaduto.  
  
-   L'URL Web tramite l'origine HTTP rappresenta un file o un URI di loopback.  
  
-   L'URL Web tramite HTTP contiene un URL formattato in modo errato.  
  
-   L'URL non può essere risolto dall'origine remota.  

**Risoluzione:** Rivedere il modulo e verificare il formato dell'URI. Se l'origine dati è un URL Web tramite HTTP, verificare che l'origine desiderata non sia un file o un URI di loopback (localhost).  

|Messaggi di eccezione|
|------------------------|
|Invalid Uri is passed. (Viene passato un URI non valido.)|
|The Uri "{invalid_url}" is invalid. (L'URI "{invalid_url}" non è valido.)|


## <a name="error-0030"></a>Errore 0030  
 L'eccezione si verifica se non è possibile scaricare un file.  

 Questa eccezione si verifica in Azure Machine Learning quando non è possibile scaricare un file. Questa eccezione viene generata quando la lettura di un'origine HTTP ha esito negativo dopo tre 3 tentativi.  

**Risoluzione:** Verificare che l'URI dell'origine HTTP sia corretto e che il sito sia attualmente accessibile tramite Internet.  

|Messaggi di eccezione|
|------------------------|
|Unable to download a file. (Non è possibile scaricare un file.)|
|Errore durante il download del file: {file_url}.|


## <a name="error-0031"></a>Errore 0031  
 L'eccezione si verifica se il numero di colonne nel set di colonne è inferiore al necessario.  

 Questo errore si verifica in Azure Machine Learning se il numero di colonne selezionato è inferiore al necessario.  Questo errore viene visualizzato se non viene selezionato il numero minimo necessario di colonne.  

**Risoluzione:** Aggiungere altre colonne alla selezione di colonne usando il **selettore di colonne**.  

|Messaggi di eccezione|
|------------------------|
|Number of columns in column set is less than required. (Il numero di colonne nel set di colonne è inferiore a quello richiesto.)|
|At least {required_columns_count} column(s) should be specified for input argument "{arg_name}". (È necessario specificare almeno {required_columns_count} colonna/e per l'argomento di input "{arg_name}".)|
|At least {required_columns_count} column(s) should be specified for input argument "{arg_name}". (È necessario specificare almeno {required_columns_count} colonna/e per l'argomento di input "{arg_name}".) The actual number of specified columns is {input_columns_count}. (È necessario specificare almeno {required_columns_count} colonna/e per l'argomento di input "{arg_name}". Il numero effettivo delle colonne specificate è {input_columns_count}.)|


## <a name="error-0032"></a>Errore 0032  
 L'eccezione si verifica se l'argomento non è un numero.  

 Questo errore viene visualizzato in Azure Machine Learning se l'argomento è doppio o NaN.  

**Risoluzione:** Modificare l'argomento specificato affinché usi un valore valido.  

|Messaggi di eccezione|
|------------------------|
|Argument is not a number. (L'argomento non è un numero.)|
|"{arg_name}" is not a number. ("{arg_name}" non è un numero.)|


## <a name="error-0033"></a>Errore 0033  
 L'eccezione si verifica se l'argomento è Infinity.  

 Questo errore si verifica in Azure Machine Learning se l'argomento è infinito. Questo errore viene visualizzato se l'argomento è `double.NegativeInfinity` o `double.PositiveInfinity`.  

**Risoluzione:** Modificare l'argomento specificato affinché abbia un valore valido.  

|Messaggi di eccezione|
|------------------------|
|Argument must be finite. (L'argomento deve essere finito.)|
|"{arg_name}" is not finite. ("{arg_name}" non è finito.)|
|Column "{column_name}" contains infinite values. (La colonna "{column_name}" contiene valori infiniti.)|


## <a name="error-0034"></a>Errore 0034  
 L'eccezione si verifica se esiste più di una classificazione per una determinata coppia utente-elemento.  

 Questo errore si verifica in Azure Machine Learning nei suggerimenti se una coppia utente-elemento ha più di un valore di classificazione.  

**Risoluzione:** Verificare che la coppia utente-elemento abbia un solo valore di classificazione.  

|Messaggi di eccezione|
|------------------------|
|More than one rating exists for the value(s) in dataset. (Per i valori nel set di dati esistono più classificazioni.)|
|More than one rating for user {user} and item {item} in rating prediction data table. (Nella tabella dati contenente le stime delle classificazioni sono presenti più classificazioni per l'utente {user} e l'elemento {item}.)|
|More than one rating for user {user} and item {item} in {dataset}. (In {dataset} sono presenti più classificazioni per l'utente {user} e l'elemento {item}.)|


## <a name="error-0035"></a>Errore 0035  
 L'eccezione si verifica se non sono state specificate funzioni per un determinato utente o elemento.  

 Questo errore si verifica in Azure Machine Learning se si sta provando a usare un modello di suggerimenti per il punteggio ma non è possibile trovare un vettore di funzionalità.  

**Risoluzione:**

Quando si usano le funzionalità degli elementi o dell'utente, è necessario che la funzione di suggerimento Matchbox soddisfi determinati requisiti.  Questo errore indica che manca un vettore della funzionalità per un utente o un elemento inserito come input. Verificare che nei dati sia disponibile un vettore della funzionalità per ogni utente o elemento.  

 Se, ad esempio, è stato eseguito il training di un modello di suggerimento usando funzionalità quali l'età, la posizione o il reddito dell'utente, ma ora si desidera creare i punteggi per i nuovi utenti che non sono stati visualizzati durante il training, è necessario specificare un set di funzionalità equivalente, ovvero valori di età, posizione e reddito, per i nuovi utenti allo scopo di fare stime appropriate. 

 Se non si dispone delle funzionalità per questi utenti, provare a usare l'ingegneria delle funzionalità per generare funzionalità appropriate.  Se, ad esempio, non sono presenti singoli valori di età o reddito dell'utente, è possibile generare valori approssimativi da usare per un gruppo di utenti. 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > La risoluzione non è applicabile al caso in questione? È possibile inviare un feedback su questo articolo e inserire informazioni sullo scenario, inclusi il modulo e il numero di righe nella colonna. Queste informazioni verranno usate per indicare procedure più dettagliate per la risoluzione di problemi futuri.

|Messaggi di eccezione|
|------------------------|
|No features were provided for a required user or item. (Nessuna funzione specificata per un utente o un elemento richiesto.)|
|Features for {required_feature_name} required but not provided. (Le funzionalità per {required_feature_name} sono obbligatorie ma non sono state specificate.)|


## <a name="error-0036"></a>Errore 0036  
 L'eccezione si verifica se sono stati specificati più vettori della funzionalità per un determinato utente o elemento.  

 Questo errore si verifica in Azure Machine Learning se un vettore della funzionalità viene definito più di una volta.  

**Risoluzione:** Verificare che il vettore della funzionalità non venga definito più di una volta.  

|Messaggi di eccezione|
|------------------------|
|Duplicate feature definition for a user or item. (Definizione di funzione duplicata per un utente o un elemento.)|


## <a name="error-0037"></a>Errore 0037  
 L'eccezione si verifica se sono specificate più colonne etichetta e ne è consentita una sola.  

 Questo errore si verifica in Azure Machine Learning se è stata selezionata più di una colonna come nuova colonna etichetta. Per la maggior parte degli algoritmi di apprendimento con supervisione è necessario contrassegnare una sola colonna come destinazione o etichetta.  

**Risoluzione:** Assicurarsi di selezionare una sola colonna come nuova colonna etichetta.  

|Messaggi di eccezione|
|------------------------|
|Multiple label columns are specified. (Sono state specificate più colonne etichetta.)|
|Multiple label columns are specified in "{dataset_name}". (Sono state specificate più colonne etichetta in "{dataset_name}.)|


## <a name="error-0039"></a>Errore 0039  
 L'eccezione si verifica se l'operazione non è riuscita.  

 Questo errore si verifica in Azure Machine Learning quando non è possibile completare un'operazione interna.  

**Risoluzione:** Questo errore è causato da diverse condizioni e non esiste un rimedio specifico.  
 La tabella seguente contiene messaggi generici per questo errore, seguiti da una descrizione specifica della condizione. 

 Se non sono disponibili dettagli, usare la [pagina di domande e risposte di Microsoft per inviare un feedback](https://docs.microsoft.com/answers/topics/azure-machine-learning-studio-classic.html) e inserire informazioni sui moduli che hanno generato l'errore e le relative condizioni.

|Messaggi di eccezione|
|------------------------|
|Operazione non riuscita.|
|Error while completing operation: "{failed_operation}". (Si è verificato un errore durante il completamento dell'operazione: "{failed_operation}".)|
|Error while completing operation: "{failed_operation}". (Si è verificato un errore durante il completamento dell'operazione: "{failed_operation}".) Reason: "{reason}". (Si è verificato un errore durante il completamento dell'operazione: "{failed_operation}". Motivo: "{reason}".)|


## <a name="error-0042"></a>Errore 0042  
 L'eccezione si verifica quando non è possibile convertire la colonna in un altro tipo.  

 Questo errore si verifica in Azure Machine Learning quando non è possibile convertire la colonna nel tipo specificato.  Questo errore viene visualizzato se un modulo richiede un tipo di dati specifico, ad esempio datetime, testo, un numero a virgola mobile o un numero intero, ma non è possibile convertire una colonna esistente nel tipo richiesto.  

Ad esempio, è possibile selezionare una colonna e provare a convertirla in un tipo di dati numerico da usare in un'operazione matematica: questo errore viene generato se la colonna contiene dati non validi. 

Un altro motivo per cui si ottiene questo errore è se si tenta di usare una colonna contenente numeri a virgola mobile o molti valori univoci come colonna di categoria. 

**Risoluzione:**

+ Aprire la pagina della Guida per il modulo che ha generato l'errore e verificare i requisiti del tipo di dati.
+ Esaminare i tipi di dati delle colonne nel set di dati di input.
+ Analizzare i dati provenienti dalle così dette origini dati senza schema.
+ Nel set di dati verificare i valori mancanti o la presenza di caratteri speciali che potrebbero bloccare la conversione al tipo di dati desiderato. 
    + I tipi di dati numerici devono essere coerenti: ad esempio, verificare la presenza di numeri a virgola mobile in una colonna di numeri interi.
    + Cercare stringhe di testo o valori NA in una colonna numerica. 
    + I valori booleani possono essere convertiti in una rappresentazione adeguata a seconda del tipo di dati richiesto.
    + Esaminare le colonne di testo per individuare caratteri non Unicode, caratteri di tabulazione o i caratteri di controllo
    + I dati datetime devono essere coerenti per evitare errori di modellazione, ma la pulizia può essere complessa a causa dei diversi formati. Considerare l'uso dei moduli <!--the [Execute R Script](execute-r-script.md) or -->[Eseguire script Python](execute-python-script.md) per eseguire la pulizia.  
+ Se necessario, modificare i valori nel set di dati di input in modo che la colonna possa essere correttamente convertita. La modifica può includere operazioni di binning, troncamento o arrotondamento, eliminazione di outlier o individuazione di valori mancanti. Vedere gli articoli seguenti per scenari comuni di trasformazione dei dati in Machine Learning:
    + [Clean Missing Data](clean-missing-data.md) (Pulisci dati mancanti)
    + [Normalize Data](normalize-data.md) (Normalizza dati)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> La risoluzione non è chiara o non è applicabile al caso in questione? È possibile inviare un feedback su questo articolo e inserire informazioni sullo scenario, inclusi il modulo e il tipo di dati della colonna. Queste informazioni verranno usate per indicare procedure più dettagliate per la risoluzione di problemi futuri.  

|Messaggi di eccezione|
|------------------------|
|Not allowed conversion. (Conversione non consentita.)|
|Could not convert column of type {type1} to column of type {type2}. (Non è stato possibile convertire la colonna di tipo {type1} nella colonna di tipo {type2}.)|
|Could not convert column "{col_name1}" of type {type1} to column of type {type2}. (Non è stato possibile convertire la colonna "{col_name1}" di tipo {type1} nella colonna di tipo {type2}.)|
|Could not convert column "{col_name1}" of type {type1} to column "{col_name2}" of type {type2}. (Non è stato possibile convertire la colonna "{col_name1}" di tipo {type1} nella colonna "{col_name2}" di tipo {type2}.)|


## <a name="error-0044"></a>Errore 0044  
 L'eccezione si verifica quando non è possibile ricavare il tipo di elemento della colonna dai valori esistenti.  

 Questo errore si verifica in Azure Machine Learning quando non è possibile dedurre il tipo di una o più colonna in un set di dati. Questo problema si verifica in genere durante la concatenazione di due o più set di dati con tipi di elementi diversi. Questo errore verrà generato se Azure Machine Learning non è in grado di determinare un tipo comune che possa rappresentare tutti i valori di una o più colonne senza perdita di informazioni.  

**Risoluzione:** Assicurarsi che tutti i valori di una determinata colonna in entrambi i set di dati combinati siano dello stesso tipo (valori numerici, booleani, di categoria, stringhe, date e così via) o possano essere assegnati forzatamente allo stesso tipo.  

|Messaggi di eccezione|
|------------------------|
|Cannot derive element type of the column. (Non è possibile ricavare il tipo di elemento della colonna.)|
|Cannot derive element type for column "{column_name}" -- all the elements are null references. (Non è possibile ricavare il tipo di elemento per la colonna "{column_name}": tutti gli elementi sono riferimenti Null.)|
|Cannot derive element type for column "{column_name}" of dataset "{dataset_name}" -- all the elements are null references. (Non è possibile ricavare il tipo di elemento per la colonna "{column_name}" del set di dati "{dataset_name}": tutti gli elementi sono riferimenti Null.)|


## <a name="error-0045"></a>Errore 0045  
 L'eccezione si verifica quando non è possibile creare una colonna a causa di tipi di elemento misti nell'origine.  

 Questo errore si genera in Azure Machine Learning quando i tipi di elemento di due set di dati combinati sono diversi.  

**Risoluzione:** Assicurarsi che tutti i valori di una determinata colonna in entrambi i set di dati combinati siano dello stesso tipo (valori numerici, booleani, di categoria, stringhe, date e così via).  

|Messaggi di eccezione|
|------------------------|
|Cannot create column with mixed element types. (Non è possibile creare una colonna con tipi di elemento misti.)|
|Cannot create column with id "{column_id}" of mixed element types:<br />Type of data[{row_1}, {column_id}] is "{type_1}". <br />Type of data[{row_2}, {column_id}] is "{type_2}". (Non è possibile creare una colonna con ID "{column_id}" di tipi di elemento misti:
Il tipo di dati di [{row_1}, {column_id}] è "{type_1}".
Il tipo di dati di [{row_2}, {column_id}] is "{type_2}".)|
|Cannot create column with id "{column_id}" of mixed element types:<br />Type in chunk {chunk_id_1} is "{type_1}". <br />Type in chunk {chunk_id_2} is "{type_2}" with chunk size: {chunk_size}. (Non è possibile creare una colonna con ID "{column_id}" di tipi di elementi misti:
Il tipo nel blocco {chunk_id_1} è "{type_1}".
Il tipo nel blocco {chunk_id_1} è "{type_1}".)|


## <a name="error-0046"></a>Errore 0046  
 L'eccezione si verifica quando non è possibile creare la directory nel percorso specificato.  

 Questo errore si verifica in Azure Machine Learning quando non è possibile creare una directory nel percorso specificato. Questo errore viene visualizzato se una parte del percorso della directory di output per una query Hive non è corretta o è inaccessibile.  

**Risoluzione:** Rivedere il modulo e verificare che il percorso della directory sia formattato in modo corretto e che sia accessibile con le credenziali correnti.  

|Messaggi di eccezione|
|------------------------|
|Specificare un percorso di directory di output valido.|
|Directory: {path} cannot be created. Please specify valid path. (Non è possibile creare la directory {path}. Specificare un percorso valido.)|


## <a name="error-0047"></a>Errore 0047  
 L'eccezione si verifica se il numero di colonne funzionalità in alcuni set di dati passati al modulo è troppo piccolo.  

 Questo errore si verifica in Azure Machine Learning se il set di dati di input per il training non contiene il numero minimo di colonne richieste dall'algoritmo. In genere il set di dati è vuoto oppure contiene solo colonne di training.  

**Risoluzione:** Rivedere il set di dati di input per assicurarsi che esistano una o più colonne aggiuntive oltre alla colonna etichetta.  

|Messaggi di eccezione|
|------------------------|
|Number of feature columns in input dataset is less than allowed minimum. (Il numero di colonne funzionalità nel set di dati di input è inferiore al minimo consentito.)|
|Number of feature columns in input dataset is less than allowed minimum of {required_columns_count} column(s). (Il numero di colonne funzionalità nel set di dati di input è inferiore al minimo consentito di {required_columns_count} colonna/e).|
|Number of feature columns in input dataset "{arg_name}" is less than allowed minimum of {required_columns_count} column(s). (Il numero di colonne funzionalità nel set di dati di input "{arg_name}" è inferiore al minimo consentito di {required_columns_count} colonna/e).|


## <a name="error-0048"></a>Errore 0048  
 L'eccezione si verifica se non è possibile aprire un file.  

 Questo errore si verifica in Azure Machine Learning quando non è possibile aprire un file per le operazioni di lettura o scrittura. Questo errore potrebbe essere visualizzato per i motivi seguenti:  

-   Il contenitore o il file (BLOB) non esiste  
  
-   Il livello di accesso del file o del contenitore non consente di accedere al file  
  
-   Il file è troppo grande per essere letto oppure il formato non è corretto  

**Risoluzione:** Rivedere il modulo e il file che si sta tentando di leggere.  

 Verificare che i nomi del contenitore e del file siano corretti.  

 Usare il portale di Azure classico o uno strumento di Archiviazione di Azure per verificare di avere le autorizzazioni per accedere al file.  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|Messaggi di eccezione|
|------------------------|
|Impossibile aprire un file.|
|Errore durante l'apertura del file: {file_name}.|
|Errore durante l'apertura del file: {file_name}. Exception message: {exception_message} (Messaggio di eccezione: {exception_message})|


## <a name="error-0049"></a>Errore 0049  
 L'eccezione si verifica se non è possibile analizzare un file.  

 Questo errore si verifica in Azure Machine Learning quando non è possibile analizzare un file. Questo errore viene visualizzato se il formato di file selezionato nel modulo [Importazione dei dati](import-data.md) non corrisponde al formato effettivo del file oppure se il file contiene un carattere non riconoscibile.  

**Risoluzione:** Rivedere il modulo e correggere la selezione del formato del file se non corrisponde al formato del file. Se possibile, controllare il file per verificare che non contenga caratteri non validi.  

|Messaggi di eccezione|
|------------------------|
|Unable to parse a file. (Impossibile analizzare un file.)|
|Error while parsing the {file_format} file. (Si è verificato un errore durante l'analisi del file {file_format}.)|
|Error while parsing the {file_format} file: {file_name}. (Si è verificato un errore durante l'analisi del file {file_format}: {file_name}.)|
|Error while parsing the {file_format} file. Reason: {failure_reason}. (Si è verificato un errore durante l'analisi del file {file_format}. Motivo: {failure_reason}.)|
|Error while parsing the {file_format} file: {file_name}. Reason: {failure_reason}. (Si è verificato un errore durante l'analisi del file {file_format}: {file_name}. Motivo: {failure_reason}.)|


## <a name="error-0052"></a>Errore 0052  
 L'eccezione si verifica se la chiave dell'account di archiviazione di Azure è stata specificata in modo errato.  

 Questo errore si verifica in Azure Machine Learning se la chiave usata per accedere all'account di archiviazione di Azure non è corretta. Ad esempio, è possibile che questo errore venga visualizzato se la chiave di archiviazione di Azure è stata troncata quando è stata copiata e incollata o se è stata usata una chiave errata.  

 Per ulteriori informazioni su come ottenere le chiavi di un account di archiviazione di Azure, vedere la pagina relativa alla [Vedere, copiare e rigenerare chiavi di accesso di archiviazione](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  

**Risoluzione:** Rivedere il modulo e verificare che la chiave di archiviazione di Azure sia corretta per l'account; se necessario, copiare di nuovo la chiave dal portale di Azure classico.  

|Messaggi di eccezione|
|------------------------|
|The Azure storage account key is incorrect. (La chiave dell'account di account di archiviazione di Azure non è corretta.)|


## <a name="error-0053"></a>Errore 0053  
 L'eccezione si verifica in assenza di elementi o funzioni utente per i suggerimenti di Matchbox.  

 Questo errore si genera in Azure Machine Learning quando non è possibile trovare un vettore di funzionalità.  

**Risoluzione:** Verificare che nel set di dati di input sia presente il vettore di funzionalità.  

|Messaggi di eccezione|
|------------------------|
|User features or/and items are required but not provided. (Le funzionalità e/o gli elementi dell'utente sono obbligatori ma non sono stati specificati.)|


## <a name="error-0056"></a>Errore 0056  
 L'eccezione si verifica se le colonne selezionate per un'operazione violano i requisiti.  

 Questo errore si verifica in Azure Machine Learning quando si scelgono le colonne per un'operazione che richiede che la colonna sia di un tipo di dati specifico. 

 Questo errore può verificarsi anche se il tipo di dati della colonna è corretto, ma il modulo in uso richiede che anche la colonna sia contrassegnata come una colonna funzionalità, etichetta o di categoria.  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**Risoluzione:**

1.  Esaminare il tipo di dati delle colonne attualmente selezionate. 

2. Verificare se le colonne selezionate sono di categoria, etichetta o funzionalità.  
  
3.  Esaminare l'argomento della Guida per il modulo in cui è stata effettuata la selezione delle colonne, per determinare se ci sono requisiti specifici per il tipo di dati o l'utilizzo delle colonne.  
  
3.  Usare [Modifica metadati](edit-metadata.md) per modificare il tipo di colonna per la durata di questa operazione. Assicurarsi di ripristinare il valore originale del tipo di colonna, usando un'altra istanza di [Modifica metadati](edit-metadata.md), se necessario per le operazioni di downstream.  

|Messaggi di eccezione|
|------------------------|
|One or more selected columns were not in an allowed category. (Una o più colonne selezionate non sono incluse in una categoria consentita.)|
|Column with name "{col_name}" is not in an allowed category. (La colonna denominata "{col_name}" non è inclusa in una categoria consentita.)|


## <a name="error-0057"></a>Errore 0057  
 L'eccezione si verifica quando si tenta di creare un file o un BLOB che esiste già.  

 Questa eccezione si verifica quando si usa il modulo [Esportazione dei dati](export-data.md) o un altro modulo per salvare i risultati di una pipeline in Azure Machine Learning nell'Archiviazione BLOB di Azure, ma si tenta di creare un file o un BLOB che esiste già.   

**Risoluzione:**

 Questo errore viene visualizzato solo se in precedenza è stata impostata la proprietà **Azure blob storage write mode** (Modalità di scrittura dell'Archiviazione BLOB di Azure) per **Errore**. Da progettazione, questo modulo genera un errore se si tenta di scrivere un set di dati in un BLOB già esistente.

 - Aprire le proprietà del modulo e modificare la proprietà **Azure blob storage write mode** (Modalità di scrittura dell'Archiviazione BLOB di Azure) in **Sovrascrivi**.
 - In alternativa, è possibile digitare il nome di un BLOB o un file di destinazione diverso e assicurarsi di specificare un BLOB che non esista già.  

|Messaggi di eccezione|
|------------------------|
|File or Blob already exists. (Il file o il BLOB esiste già.)|
|File or Blob "{file_path}" already exists. (Il file o il BLOB "{file_path}" esiste già.)|


## <a name="error-0058"></a>Errore 0058  
 Questo errore si verifica in Azure Machine Learning se il set di dati non contiene la colonna etichetta prevista.  

 Questa eccezione può verificarsi anche quando la colonna etichetta specificata non corrisponde ai dati o al tipo di dati previsto dall'apprendimento oppure ha valori errati. Ad esempio, questa eccezione si genera quando si usa una colonna etichetta con valori reali durante il training di un classificatore binario.  

**Risoluzione:** La risoluzione dipende dall'apprendimento o dal training usato e dai tipi di dati delle colonne nel set di dati. Innanzitutto, verificare i requisiti dell'algoritmo di Machine Learning o del modulo di training.  

 Rivedere il set di dati di input. Verificare che la colonna che si prevede venga trattata come etichetta abbia il tipo di dati corretto per il modello che si sta creando.  

 Controllare i valori mancanti negli input e, se necessario, eliminarli o sostituirli.  

 Se necessario, aggiungere il modulo [Modifica metadati](edit-metadata.md) e assicurarsi che la colonna etichetta sia contrassegnata come etichetta.  

|Messaggi di eccezione|
|------------------------|
|The label column values and scored label column values are not comparable. (I valori della colonna etichetta e i valori delle colonne etichetta con punteggio non sono confrontabili.)|
|The label column is not as expected in "{dataset_name}". (La colonna etichetta non risulta come previsto in "{dataset_name}".)|
|The label column is not as expected in "{dataset_name}", {reason}. (La colonna etichetta non risulta come previsto in "{dataset_name}", {reason}.)|
|The label column "{column_name}" is not expected in "{dataset_name}". (La colonna etichetta "{column_name}" non risulta come previsto in "{dataset_name}".)|
|The label column "{column_name}" is not expected in "{dataset_name}", {reason}. (La colonna etichetta "{column_name}" non risulta come previsto in "{dataset_name}", {reason}.)|


## <a name="error-0059"></a>Errore 0059  
 L'eccezione si verifica se non è possibile analizzare un indice di colonna specificato in un selettore di colonna.  

 Questo errore si verifica in Azure Machine Learning se non è possibile analizzare un indice di colonna specificato quando si usa il selettore di colonna.  Questo errore viene visualizzato quando l'indice di colonna è in un formato non valido che non può essere analizzato.  

**Risoluzione:** Modificare l'indice di colonna per usare un valore di indice valido.  

|Messaggi di eccezione|
|------------------------|
|One or more specified column indexes or index ranges could not be parsed. (Non è stato possibile analizzare uno o più indici di colonna o intervalli di indice specificati.)|
|Column index or range "{column_index_or_range}" could not be parsed. (Non è stato possibile analizzare l'indice o l'intervallo di colonne "{column_index_or_range}".)|


## <a name="error-0060"></a>Errore 0060  
 L'eccezione si verifica quando viene specificato un intervallo di colonne fuori intervallo in un selettore di colonna.  

 Questo errore si verifica in Azure Machine Learning quando nel selettore di colonna viene specificato un intervallo di colonne non compreso nell'intervallo. Questo errore viene visualizzato se l'intervallo di colonne nel selettore di colonna non corrisponde alle colonne nel set di dati.  

**Risoluzione:** Modificare l'intervallo di colonne nel selettore di colonna affinché corrisponda alle colonne nel set di dati.  

|Messaggi di eccezione|
|------------------------|
|Invalid or out of range column index range specified. (L'intervallo specificato per l'indice di colonna non è valido o non è compreso nei limiti consentiti.)|
|Column range "{column_range}" is invalid or out of range. (L'intervallo di colonne "{column_range}" non è valido o non è compreso nei limiti consentiti.)|


## <a name="error-0061"></a>Errore 0061  
 L'eccezione si verifica quando si tenta di aggiungere una riga a un oggetto DataTable che contiene un numero di colonne diverso rispetto alla tabella.  

 Questo errore si verifica in Azure Machine Learning quando si tenta di aggiungere una riga a un set di dati con un numero diverso di colonne rispetto al set di dati.  Questo errore viene visualizzato se la riga che viene aggiunta al set di dati contiene un numero diverso di colonne del set di dati di input.  Non è possibile aggiungere la riga al set di dati se il numero di colonne è diverso.  

**Risoluzione:** Modificare il set di dati di input in modo che abbia lo stesso numero di colonne della riga aggiunta oppure modificare la riga aggiunta in modo che abbia lo stesso numero di colonne del set di dati.  

|Messaggi di eccezione|
|------------------------|
|All tables must have the same number of columns. (Tutte le tabelle devono avere lo stesso numero di colonne.)|
|Columns in chunk "{chunk_id_1}" is different with chunk "{chunk_id_2}" with chunk size: {chunk_size}. (Le colonne nel blocco "{chunk_id_1}" sono diverse dal blocco "{chunk_id_2}" con dimensione del blocco: {chunk_size}.)|
|Column count in file "{filename_1}" (count={column_count_1}) is different with file "{filename_2}" (count={column_count_2}). (Il numero di colonne nel file "{filename_1}" (count={column_count_1}) è diverso dal file "{filename_2}" (count={column_count_2}).|


## <a name="error-0062"></a>Errore 0062  
 L'eccezione si verifica quando si tenta di confrontare due modelli con tipi di apprendimento diversi.  

 Questo errore si genera in Azure Machine Learning quando non è possibile confrontare le metriche di valutazione per due diversi set di dati con punteggio. In questo caso, non è possibile confrontare l'efficacia dei modelli usati per produrre i due set di dati con punteggio.  

**Risoluzione:** Verificare che i risultati con punteggio siano prodotti dallo stesso tipo di modello di Machine Learning (classificazione binaria, regressione, classificazione multiclasse, suggerimenti, clustering, rilevamento anomalie e così via). Tutti i modelli confrontati devono avere lo stesso tipo di apprendimento.  

|Messaggi di eccezione|
|------------------------|
|All models must have the same learner type. (Tutti i modelli devono avere lo stesso tipo di apprendimento.)|
|Got incompatible learner type: "{actual_learner_type}". (È stato ottenuto un tipo di apprendimento incompatibile: "{actual_learner_type}".) Expected learner types are: "{expected_learner_type_list}". (I tipi di apprendimento previsti sono: "{expected_learner_type_list}".)|


## <a name="error-0064"></a>Errore 0064  
 L'eccezione si verifica se la chiave di archiviazione o il nome dell'account di archiviazione di Azure è stato specificato in modo errato.  

 Questo errore si verifica in Azure Machine Learning se la chiave di archiviazione o il nome dell'account di archiviazione di Azure specificato non è corretto. Questo errore viene visualizzato se si immette un nome o una password errati per l'account di archiviazione. Questo problema può verificarsi se si immette manualmente il nome o la password dell'account. Può verificarsi anche se l'account è stato eliminato.  

**Risoluzione:** Verificare che il nome e la password dell'account siano stati immessi correttamente e che l'account esista.  

|Messaggi di eccezione|
|------------------------|
|The Azure storage account name or storage key is incorrect. (Il nome dell'account di archiviazione di Azure o la chiave di archiviazione non sono corretti.)|
|The Azure storage account name "{account_name}" or storage key for the account name is incorrect. (Il nome dell'account di archiviazione di Azure "{account_name}" o la chiave di archiviazione per il nome dell'account non sono corretti.)|


## <a name="error-0065"></a>Errore 0065  
 L'eccezione si verifica se il nome del BLOB di Azure è stato specificato in modo errato.  

 Questo errore si verifica in Azure Machine Learning se il nome del BLOB di Azure specificato non è corretto.  L'errore viene visualizzato l'errore se:  

-   Non è possibile trovare il BLOB nel contenitore specificato.  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   È stato specificato solo il contenitore come origine in una richiesta [Importazione dei dati](import-data.md) quando il formato era Excel o CSV con codifica; la concatenazione dei contenuti di tutti i BLOB all'interno di un contenitore non è consentita con questi formati.  
  
-   Un URI SAS non contiene il nome di un BLOB valido.  

**Risoluzione:** Rivedere il modulo che genera l'eccezione. Verificare che il BLOB specificato esista nel contenitore dell'account di archiviazione e che le autorizzazioni consentano di visualizzare il BLOB. Verificare che l'input sia nel formato **nome del contenitore/nome del file** se si ha un file Excel o CSV con formati di codifica. Verificare che un URI SAS contenga il nome di un BLOB valido.  

|Messaggi di eccezione|
|------------------------|
|The Azure storage blob name is incorrect. (Il nome del BLOB di archiviazione di Azure non è corretto.)|
|The Azure storage blob name "{blob_name}" is incorrect. (Il nome del BLOB di archiviazione di Azure "{blob_name}" non è corretto.)|
|The Azure storage blob name with prefix "{blob_name_prefix}" does not exist. (Il nome del BLOB di archiviazione di Azure con prefisso "{blob_name_prefix}" non esiste.)|
|Failed to find any Azure storage blobs under container "{container_name}". (Non sono stati trovati BLOB di archiviazione di Azure nel contenitore "{container_name}".)|
|Failed to find any Azure storage blobs with wildcard path "{blob_wildcard_path}". (Non sono stati trovati BLOB di archiviazione di Azure con percorso jolly "{blob_wildcard_path}".)|


## <a name="error-0066"></a>Errore 0066  
 L'eccezione si verifica se non è possibile caricare una risorsa in un BLOB di Azure.  

 Questo errore si verifica in Azure Machine Learning se non è possibile caricare una risorsa in un BLOB di Azure.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Entrambi vengono salvati nello stesso account di archiviazione di Azure come account che contiene il file di input.  

**Risoluzione:** Esaminare il modulo. Verificare che il nome dell'account Azure, la chiave di archiviazione e il contenitore siano corretti e che l'account abbia l'autorizzazione per scrivere nel contenitore.  

|Messaggi di eccezione|
|------------------------|
|The resource could not be uploaded to Azure storage. (Non è stato possibile caricare la risorsa nell'area di archiviazione di Azure.)|
|The file "{source_path}" could not be uploaded to Azure storage as "{dest_path}". (Non è stato possibile caricare il file "{source_path}" in archiviazione di Azure come "{dest_path}".)|


## <a name="error-0067"></a>Errore 0067  
 L'eccezione si verifica se un set di dati contiene un numero diverso di colonne rispetto al previsto.  

 Questo errore si verifica in Azure Machine Learning se un set di dati contiene un numero diverso di colonne rispetto al previsto.  Viene visualizzato quando il numero di colonne nel set di dati è diverso dal numero di colonne previsto dal modulo durante l'esecuzione.  

**Risoluzione:** Modificare il set di dati di input o i parametri.  

|Messaggi di eccezione|
|------------------------|
|Unexpected number of columns in the datatable. (Numero di colonne non previsto nella tabella dati.)|
|Unexpected number of columns in the dataset "{dataset_name}". (Numero di colonne non previsto nel set di dati "{dataset_name}".)|
|Expected "{expected_column_count}" column(s) but found "{actual_column_count}" column(s) instead. (Sono previste "{expected_column_count}" colonne, ma sono state trovate "{actual_column_count}" colonne.)|
|In input dataset "{dataset_name}", expected "{expected_column_count}" column(s) but found "{actual_column_count}" column(s) instead. (Nel set di dati di input "{dataset_name}", sono previste "{expected_column_count}" colonne, ma sono state trovate "{actual_column_count}" colonne.)|


## <a name="error-0068"></a>Errore 0068  
 L'eccezione si verifica se lo script Hive specificato non è corretto.  

 Questo errore si verifica in Azure Machine Learning in presenza di errori di sintassi in uno script Hive QL oppure se l'interprete di Hive rileva un errore durante l'esecuzione della query o dello script.  

**Risoluzione:**

Il messaggio di errore di Hive viene in genere segnalato nel log degli errori, in modo che sia possibile intervenire in base all'errore specifico. 

+ Aprire il modulo ed esaminare la query per individuare eventuali errori.  
+ Verificare che la query funzioni correttamente al di fuori di Azure Machine Learning effettuando l'accesso alla console Hive del cluster Hadoop ed eseguendo la query.  
+ Provare a inserire commenti nello script Hive in un'altra riga, anziché combinare istruzioni e commenti eseguibili in un'unica riga.  

### <a name="resources"></a>Risorse

Vedere gli articoli seguenti per informazioni sull'esecuzione di query di Hive per Machine Learning:

+ [Creare tabelle Hive e caricare i dati dall'archiviazione BLOB di Azure](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Esplorare i dati nelle tabelle con le query di Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Creare funzionalità per i dati in un cluster Hadoop con query Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Scheda di riferimento rapido di Hive per utenti SQL (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Messaggi di eccezione|
|------------------------|
|Hive script is incorrect. (Lo script Hive non è corretto.)|


## <a name="error-0069"></a>Errore 0069  
 L'eccezione si verifica se lo script SQL specificato non è corretto.  

 Questo errore si verifica in Azure Machine Learning se lo script SQL specificato presenta problemi di sintassi o se le colonne o la tabella specificate nello script non sono valide. 

 Questo errore viene visualizzato se il motore SQL rileva un errore durante l'esecuzione della query o dello script. Il messaggio di errore di SQL viene in genere segnalato nel log degli errori, in modo che sia possibile intervenire in base all'errore specifico.  

**Risoluzione:** Rivedere il modulo ed esaminare la query SQL per individuare eventuali errori.  

 Verificare che la query funzioni correttamente all'esterno di Azure ML accedendo direttamente al server di database ed eseguendo la query.  

 Se è presente un messaggio generato da SQL segnalato dall'eccezione del modulo, intervenire in base all'errore segnalato. I messaggi di errore, ad esempio, includono a volte istruzioni specifiche sul probabile errore:
+ *No such column or missing database* (Questa colonna non è presente oppure manca il database), indica che è possibile che sia stato digitato un nome di colonna errato. Se si è certi che il nome della colonna è corretto, provare a usare le parentesi quadre o le virgolette per racchiudere l'identificatore di colonna.
+ *SQL logic error near (Errore logico SQL vicino a)\<SQL keyword\>* , indica che potrebbe essere presente un errore di sintassi prima della parola chiave specificata

  
|Messaggi di eccezione|
|------------------------|
|SQL script is incorrect. (Script SQL errato.)|
|SQL query "{sql_query}" is not correct. (La query SQL "{sql_query}" non è corretta.)|
|SQL query "{sql_query}" is not correct. Exception message: {exception}. (La query SQL "{sql_query}" non è corretta. Messaggio di eccezione: {exception}.)|


## <a name="error-0070"></a>Errore 0070  
 L'eccezione si verifica durante il tentativo di accesso a una tabella di Azure inesistente.  

 Questo errore si verifica in Azure Machine Learning quando si tenta di accedere a una tabella di Azure inesistente. Questo errore viene visualizzato se si specifica una tabella in Archiviazione di Azure, che non esiste durante la lettura o la scrittura in Archiviazione tabelle di Azure. Ciò può verificarsi se il nome della tabella desiderata viene digitato in maniera errata o se il nome di destinazione e il tipo di archiviazione non corrispondono. Ad esempio, lo scopo era leggere una tabella, ma è stato immesso il nome di un BLOB.  

**Risoluzione:** Rivedere il modulo per verificare che il nome della tabella sia corretto.  

|Messaggi di eccezione|
|------------------------|
|Azure table does not exist. (La tabella di Azure non esiste.)|
|Azure table "{table_name}" does not exist. (La tabella di Azure "{table_name}" non esiste.)|


## <a name="error-0072"></a>Errore 0072  
 L'eccezione si verifica in caso di timeout della connessione.  

 Questo errore si verifica in Azure Machine Learning quando si verifica il timeout di una connessione. Questo errore viene visualizzato se al momento si verificano problemi di connettività con l'origine o la destinazione dati, quali connettività a Internet lenta, oppure se il set di dati è di grandi dimensioni e/o la query SQL da leggere nei dati esegue un'elaborazione complessa.  

**Risoluzione:** Determinare se al momento ci sono problemi con le connessioni lente ad Archiviazione di Azure o a Internet.  

|Messaggi di eccezione|
|------------------------|
|Connection timeout occured. (Timeout connessione.)|


## <a name="error-0073"></a>Errore 0073  
 L'eccezione si verifica in caso di errore durante la conversione di una colonna in un altro tipo.  

 Questo errore si verifica in Azure Machine Learning quando non è possibile convertire la colonna in un altro tipo.  Questo errore viene visualizzato se un modulo richiede un determinato tipo e non è possibile convertire la colonna nel nuovo tipo.  

**Risoluzione:** Modificare il set di dati di input in modo che la colonna possa essere convertita in base all'eccezione interna.  

|Messaggi di eccezione|
|------------------------|
|Non è stato possibile convertire la colonna.|
|Non è stato possibile convertire la colonna in {target_type}.|


## <a name="error-0075"></a>Errore 0075  
L'eccezione si verifica quando viene usata una funzione di binning non valida durante la quantizzazione di un set di dati.  

Questo errore si verifica in Azure Machine Learning quando si tenta di creare contenitori per i dati usando un metodo non supportato o quando le combinazioni di parametri non sono valide.  

**Risoluzione:**

La gestione degli errori per questo evento è stata introdotta in una versione precedente di Azure Machine Learning che consentiva una maggiore personalizzazione dei metodi di binning. Attualmente tutti i metodi di binning si basano su una selezione da un elenco a discesa, quindi tecnicamente non dovrebbe più essere possibile ottenere questo errore.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Microsoft Q&A question page for Azure Machine Learning](https://docs.microsoft.com/answers/topics/azure-machine-learning-studio-classic.html), providing the data types, parameter settings, and the exact error message.  -->

|Messaggi di eccezione|
|------------------------|
|Invalid binning function used. (È stata usata una funzione di binning non valida.)|


## <a name="error-0077"></a>Errore 0077  
 L'eccezione si verifica quando viene passata una modalità scrittura di file BLOB sconosciuta.  

 Questo errore si verifica in Azure Machine Learning se un argomento non valido viene passato nelle specifiche per una destinazione o un'origine di file BLOB.  

**Risoluzione:** In quasi tutti i moduli che importano o esportano dati da e verso l'Archiviazione BLOB di Azure, i valori dei parametri che controllano la modalità scrittura vengono assegnati usando un elenco a discesa. Pertanto, non è possibile passare un valore non valido e questo errore non dovrebbe essere visualizzato. Questo errore verrà deprecato in una versione successiva.  

|Messaggi di eccezione|
|------------------------|
|Unsupported blob write mode. (Modalità scrittura del BLOB non supportata.)|
|Unsupported blob write mode: {blob_write_mode}. (Modalità scrittura del BLOB non supportata: {blob_write_mode}.)|


## <a name="error-0078"></a>Errore 0078  
 L'eccezione si verifica quando l'opzione HTTP per [Importazione dei dati](import-data.md) riceve un codice di stato 3xx che indica il reindirizzamento.  

 Questo errore si verifica in Azure Machine Learning quando l'opzione HTTP per [Importazione dei dati](import-data.md) riceve un codice di stato 3xx (301, 302, 304 e così via) che indica il reindirizzamento. Questo errore viene visualizzato se si tenta di eseguire la connessione a un'origine HTTP che reindirizza il browser a un'altra pagina. Per motivi di sicurezza, il reindirizzamento dei siti Web non è consentito come origini dati per Azure Machine Learning.  

**Risoluzione:** Se il sito Web è attendibile, immettere direttamente l'URL reindirizzato.  

|Messaggi di eccezione|
|------------------------|
|Http redirection not allowed. (Reindirizzamento HTTP non consentito.)|


## <a name="error-0079"></a>Errore 0079  
 L'eccezione si verifica se il nome del contenitore di Archiviazione di Azure è stato specificato in modo errato.  

 Questo errore si verifica in Azure Machine Learning se il nome del contenitore di Archiviazione di Azure specificato non è corretto. Questo errore viene visualizzato se non sono stati specificati il nome del contenitore e il nome del BLOB (file) usando l'opzione di **percorso del BLOB che inizia con il contenitore** durante la scrittura in Archiviazione BLOB di Azure.  

**Risoluzione:** Rivedere il modulo [Esportazione dei dati](export-data.md) e verificare che il percorso specificato per il BLOB contenga sia il contenitore che il nome file, nel formato **contenitore/nome file**.  

|Messaggi di eccezione|
|------------------------|
|The Azure storage container name is incorrect. (Il nome del contenitore di Archiviazione di Azure è errato.)|
|The Azure storage container name "{container_name}" is incorrect; a container name of the format container/blob was expected. (Il nome "{container_name}" dell'account del contenitore di Archiviazione di Azure non è corretto. È previsto un nome di contenitore nel formato contenitore/BLOB.)|


## <a name="error-0080"></a>Errore 0080  
 L'eccezione si verifica quando una colonna priva di valori non è consentita dal modulo.  

 Questo errore si genera in Azure Machine Learning quando una o più colonne usate dal modulo è priva di valori. Se, ad esempio, un modulo calcola le statistiche di aggregazione per ogni colonna, non può funzionare su una colonna che non contiene dati. In questi casi, l'esecuzione del modulo viene interrotta con questa eccezione.  

**Risoluzione:** Rivedere il set di dati di input e rimuovere le colonne prive di valori.  

|Messaggi di eccezione|
|------------------------|
|Columns with all values missing are not allowed. (Non sono consentite colonne prive di tutti i valori.)|
|Column {col_index_or_name} has all values missing. (La colonna {col_index_or_name} è priva di valori.)|


## <a name="error-0081"></a>Errore 0081  
 L'eccezione si verifica nel modulo PCA se il numero di dimensioni a cui eseguire la riduzione è uguale al numero di colonne di funzionalità nel set di dati di input, che contiene almeno una colonna di funzionalità di tipo sparse.  

 Questo errore si genera in Azure Machine Learning se vengono soddisfatte le condizioni seguenti: (a) il set di dati di input presenta almeno una colonna di tipo sparse e (b) il numero finale di dimensioni richiesto è uguale al numero di dimensioni di input.  

**Risoluzione:** Provare a ridurre il numero di dimensioni nell'output in modo che sia inferiore al numero di dimensioni nell'input. Questo succede in genere nelle applicazioni di PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|Messaggi di eccezione|
|------------------------|
|For dataset containing sparse feature columns number of dimensions to reduce to should be less than number of feature columns. (Per il set di dati contenente colonne di funzioni di tipo sparse, il numero di dimensioni in base a cui eseguire la riduzione deve essere inferiore al numero di colonne di funzioni.)|


## <a name="error-0082"></a>Errore 0082  
 L'eccezione si verifica quando un modello non può essere deserializzato.  

 Questo errore si verifica in Azure Machine Learning quando un modello o una trasformazione di Machine Learning salvati non può essere caricata da una versione più recente del runtime di Azure Machine Learning a causa di una modifica che causa un'interruzione.  

**Risoluzione:** La pipeline di training che ha prodotto il modello o la trasformazione deve essere eseguita nuovamente e il modello o la trasformazione deve essere salvati di nuovo.  

|Messaggi di eccezione|
|------------------------|
|Model could not be deserialized because it is likely serialized with an older serialization format. Please retrain and resave the model. (Non è stato possibile deserializzare il modello perché probabilmente è stato serializzato in un formato precedente. Ripetere il training e il salvataggio del modello.)|


## <a name="error-0083"></a>Errore 0083  
 L'eccezione si verifica se il set di dati usato per il training non può essere usato per il tipo concreto di apprendimento.  

 Questo errore si genera in Azure Machine Learning quando il set di dati non è compatibile con l'apprendimento sottoposto a training. Il set di dati, ad esempio, potrebbe contenere almeno un valore mancante in ogni riga e, di conseguenza, l'intero set di dati verrebbe ignorato durante il training. In altri casi, alcuni algoritmi di Machine Learning, quali il rilevamento anomalie, non prevedono la presenza di etichette e possono generare questa eccezione se nel set di dati sono presenti etichette.  

**Risoluzione:** Consultare la documentazione relativa all'apprendimento usato per verificare i requisiti per il set di dati di input. Esaminare le colonne per verificare che siano presenti tutte le colonne necessarie.  

|Messaggi di eccezione|
|------------------------|
|Dataset used for training is invalid. (Il set di dati usato per il training non è valido.)|
|{data_name} contains invalid data for training. ({data_name} contiene dati non validi per il training.)|
|{data_name} contains invalid data for training. Learner type: {learner_type}. ({data_name} contiene dati non validi per il training. Tipo di apprendimento: {learner_type}.)|
|{data_name} contains invalid data for training. Learner type: {learner_type}. Reason: {reason}. ({data_name} contiene dati non validi per il training. Tipo di apprendimento: {learner_type}. Motivo: {reason}.)|
|Failed to apply "{action_name}" action on training data {data_name}. Reason: {reason}. (Non è stato possibile applicare l'azione "{action_name}" sui dati di training {data_name}. Motivo: {reason}.)|


## <a name="error-0084"></a>Errore 0084  
 L'eccezione si verifica durante la valutazione dei punteggi prodotti da uno script R. Questa funzione non è attualmente supportata.  

 Questo errore si verifica in Azure Machine Learning se si tenta di usare uno dei moduli per la valutazione di un modello con output da uno script R contenente punteggi.  

**Risoluzione:**

|Messaggi di eccezione|
|------------------------|
|Evaluating scores produced by Custom Model is currently unsupported. (La valutazione dei punteggi prodotti dal modello personalizzato non è attualmente supportata.)|


## <a name="error-0085"></a>Errore 0085  
 L'eccezione si verifica quando la valutazione dello script genera un errore.  

 Questo errore si verifica in Azure Machine Learning quando si esegue uno script personalizzato che contiene errori di sintassi.  

**Risoluzione:** Esaminare il codice in un editor esterno e verificare la presenza di errori.  

|Messaggi di eccezione|
|------------------------|
|Error during evaluation of script. (Errore durante la valutazione dello script.)|
|The following error occurred during script evaluation, please view the output log for more information: (L'errore seguente si è verificato durante la valutazione dello script. Per altre informazioni, vedere il log di output:)<br />---------- Inizio del messaggio di errore dell'interprete {script_language} ----------<br />{message}<br />---------- Fine del messaggio di errore dell'interprete {script_language}  ----------|


## <a name="error-0090"></a>Errore 0090  
 L'eccezione si verifica quando la creazione della tabella Hive non riesce.  

 Questo errore si verifica in Azure Machine Learning quando si usa [Esportazione dei dati](export-data.md) o un'altra opzione per salvare i dati in un cluster di HDInsight e non è possibile creare la tabella Hive specificata.  

**Risoluzione:** Verificare il nome dell'account di archiviazione di Azure associato al cluster e verificare che venga usato lo stesso account nelle proprietà del modulo.  

|Messaggi di eccezione|
|------------------------|
|The Hive table could not be created. For a HDInsight cluster, please ensure the Azure storage account name associated with cluster is the same as what is passed in through the module parameter. (Non è stato possibile creare la tabella Hive. Per il cluster di HDInsight, verificare che il nome dell'account di archiviazione di Azure associato al cluster sia lo stesso di quello passato tramite il parametro del modulo.)|
|The Hive table "{table_name}" could not be created. For a HDInsight cluster, please ensure the Azure storage account name associated with cluster is the same as what is passed in through the module parameter. (Non è stato possibile creare la tabella Hive "{table_name}". Per il cluster di HDInsight, verificare che il nome dell'account di archiviazione di Azure associato al cluster sia lo stesso di quello passato tramite il parametro del modulo.)|
|The Hive table "{table_name}" could not be created. For a HDInsight cluster, please ensure the Azure storage account name associated with cluster is "{cluster_name}". (Non è stato possibile creare la tabella Hive "{table_name}". Per il cluster di HDInsight, verificare che il nome dell'account di archiviazione di Azure associato al cluster sia "{cluster_name}".)|


## <a name="error-0102"></a>Errore 0102  
 Viene generato quando non è possibile estrarre un file ZIP.  

 Questo errore si verifica in Azure Machine Learning quando si importa un pacchetto compresso con estensione ZIP, ma il pacchetto non è un file ZIP oppure il file non usa un formato ZIP supportato.  

**Risoluzione:** Verificare che il file selezionato sia un file con estensione ZIP valido e che sia stato compresso usando uno degli algoritmi di compressione supportati.  

 Se si verifica questo errore quando si importano i set di dati in formato compresso, verificare che tutti i file contenuti usino uno dei formati di file supportati e siano in formato Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 Provare a leggere i file desiderati in una nuova cartella compressa e provare ad aggiungere di nuovo il modulo personalizzato.  

|Messaggi di eccezione|
|------------------------|
|Given ZIP file is not in the correct format. (Il formato del file ZIP specificato non è corretto.)|


## <a name="error-0105"></a>Errore 0105  
 Questo errore viene visualizzato quando un file di definizione del modulo contiene un tipo di parametro non supportato  
  
 Questo errore si genera in Azure Machine Learning quando si crea una definizione XML del modulo personalizzato e il tipo di un parametro o di un argomento nella definizione non corrisponde a un tipo supportato.  
  
**Risoluzione:** Verificare che la proprietà tipo di un elemento **Arg** nel file di definizione XML del modulo personalizzato sia di un tipo supportato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tipo di parametro non supportato.|  
|Unsupported parameter type '{0}' specified. (È stato specificato un tipo di parametro non supportato '{0}'.)|  


## <a name="error-0107"></a>Errore 0107  
 Viene generato quando un file di definizione del modulo definisce un tipo di output non supportato  
  
 Questo errore si genera in Azure Machine Learning quando il tipo di una porta di output in una definizione XML del modulo personalizzato non corrisponde a un tipo supportato.  
  
**Risoluzione:** Verificare che la proprietà tipo di un elemento di output nel file di definizione XML del modulo personalizzato sia di un tipo supportato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Unsupported output type. (Tipo di output non supportato.)|  
|Unsupported output type '{output_type}' specified. (È stato specificato un tipo di output "{output_type}" non supportato.)|  


## <a name="error-0125"></a>Errore 0125  
 Generato quando lo schema per più set di dati non corrisponde.  

**Risoluzione:**

|Messaggi di eccezione|
|------------------------|
|Dataset schema does not match. (Lo schema del set di dati non corrisponde.)|


## <a name="error-0127"></a>Errore 0127  
 Le dimensioni in pixel dell'immagine superano il limite consentito  

 Questo errore si verifica se si leggono immagini di un set di dati dell'immagine per la classificazione e le immagini sono più grandi di quelle che il modello può gestire.  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|Messaggi di eccezione|
|------------------------|
|Image pixel size exceeds allowed limit. (Le dimensioni in pixel dell'immagine superano il limite consentito.)|
|Image pixel size in the file '{file_path}' exceeds allowed limit: '{size_limit}'. (Le dimensioni in pixel dell'immagine nel file "{file_path}" superano il limite consentito: "{size_limit}".)|


## <a name="error-0128"></a>Errore 0128  
 Il numero di probabilità condizionali per le colonne di categoria supera il limite.  

**Risoluzione:**

|Messaggi di eccezione|
|------------------------|
|Number of conditional probabilities for categorical columns exceeds limit. (Il numero di probabilità condizionali per le colonne di categoria supera il limite.)|
|Number of conditional probabilities for categorical columns exceeds limit. Columns '{column_name_or_index_1}' and '{column_name_or_index_2}' are the problematic pair. (Il numero di probabilità condizionali per le colonne di categoria supera il limite. Le colonne "{column_name_or_index_1}" e "{column_name_or_index_2}" sono la coppia problematica.)|


## <a name="error-0129"></a>Errore 0129  
 Il numero di colonne nel set di dati supera il limite consentito.  

**Risoluzione:**

|Messaggi di eccezione|
|------------------------|
|Number of columns in the dataset exceeds allowed limit. (Il numero di colonne nel set di dati supera il limite consentito.)|
|Number of columns in the dataset in '{dataset_name}' exceeds allowed. (Il numero di colonne nel set di dati "{dataset_name}" supera il limite consentito.)|
|Number of columns in the dataset in '{dataset_name}' exceeds allowed limit of '{component_name}'. (Il numero di colonne nel set di dati "{dataset_name}" supera il limite consentito di "{component_name}".)|
|Number of columns in the dataset in '{dataset_name}' exceeds allowed '{limit_columns_count}' limit of '{component_name}'. (Il numero di colonne nel set di dati "{dataset_name}" supera il limite "{limit_columns_count}" consentito di "{component_name}".)|


## <a name="error-0134"></a>Errore 0134
L'eccezione si verifica quando la colonna etichetta è mancante o ha un numero insufficiente di righe con etichetta.  

Questo errore si verifica quando il modulo richiede una colonna di etichetta, ma non ne è stata inclusa una nella selezione delle colonne oppure nella colonna etichetta mancano troppi valori.

Questo errore può verificarsi anche quando un'operazione precedente modifica il set di dati in modo che le righe non sufficienti siano disponibili per un'operazione downstream. Si supponga, ad esempio, di usare un'espressione nel modulo **Crea partizioni ed esegui campionamenti** per dividere un set di dati in base ai valori. Se non vengono trovate corrispondenze per l'espressione, uno dei set di dati risultante dalla partizione sarà vuoto.

Risoluzione: 

 Se si include una colonna etichetta nella selezione delle colonne ma questa non viene riconosciuta, usare il modulo [Modifica metadati](edit-metadata.md) per contrassegnarla come colonna etichetta.

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  A questo punto, è possibile usare il modulo [Pulisci dati mancanti](clean-missing-data.md) per rimuovere le righe con valori mancanti nella colonna etichetta. 

 Controllare i set di dati di input per assicurarsi che contengano dati validi e un numero sufficiente di righe che soddisfi i requisiti dell'operazione. Molti algoritmi genereranno un messaggio di errore se richiedono un numero minimo di righe di dati, ma i dati contengono solo poche righe o solo un'intestazione.

|Messaggi di eccezione|
|------------------------|
|Exception occurs when label column is missing or has insufficient number of labeled rows. (L'eccezione si verifica quando la colonna etichetta è mancante o ha un numero insufficiente di righe con etichetta.)|
|Exception occurs when label column is missing or has less than {required_rows_count} labeled rows. (L'eccezione si verifica quando la colonna etichetta è mancante o ha un numero di righe con etichetta inferiore a {required_rows_count}.)|
|Exception occurs when label column in dataset {dataset_name} is missing or has less than {required_rows_count} labeled rows. (L'eccezione si verifica quando la colonna etichetta nel set di dati {dataset_name} è mancante o ha un numero di righe con etichetta inferiore a {required_rows_count}.)|


## <a name="error-0138"></a>Errore 0138  
 La memoria non è sufficiente, non è possibile completare l'esecuzione del modulo. Il sottocampionando del set di dati può aiutare a risolvere il problema.  

 Questo errore si verifica quando il modulo in esecuzione richiede una quantità di memoria superiore a quella disponibile nel contenitore di Azure. Questa situazione può verificarsi se si usa un set di dati di grandi dimensioni e l'operazione corrente non può essere contenuta nella memoria.  

**Risoluzione:** Se si sta tentando di leggere un set di dati di grandi dimensioni e non è possibile completare l'operazione, il sottocampionando del set di dati potrebbe essere utile.  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|Messaggi di eccezione|
|------------------------|
|Memory has been exhausted, unable to complete running of module. (La memoria non è sufficiente, non è possibile completare l'esecuzione del modulo.)|
|Memory has been exhausted, unable to complete running of module. Details: {details} (La memoria non è sufficiente, non è possibile completare l'esecuzione del modulo. Dettagli: {Details})|


## <a name="error-0141"></a>Errore 0141  
 L'eccezione si verifica se il numero di colonne numeriche selezionate e di valori univoci nelle colonne di categoria e stringa è troppo piccolo.  

 Questo errore si verifica in Azure Machine Learning quando nella colonna selezionata non ci sono valori univoci sufficienti per eseguire l'operazione.  

**Risoluzione:** Alcune operazioni eseguono operazioni statistiche sulle colonne funzionalità e di categoria. Se non c'è un numero sufficiente di valori, l'operazione potrebbe non riuscire o restituire un risultato non valido. Controllare il set di dati per determinare il numero di valori presenti nelle colonne funzionalità ed etichetta e se l'operazione che si sta tentando di eseguire è statisticamente valida.  

 Se il set di dati di origine è valido, è anche possibile controllare se alcune manipolazione dei dati upstream o operazioni sui metadati hanno modificato i dati e rimosso alcuni valori.  

 Se le operazioni upstream includono la suddivisione, il campionamento o il ricampionamento, verificare che gli output contengano il numero previsto di righe e valori.  

|Messaggi di eccezione|
|------------------------|
|The number of the selected numerical columns and unique values in the categorical and string columns is too small. (Il numero di colonne numeriche selezionate e di valori univoci nelle colonne di categoria e stringa è troppo piccolo.)|
|The total number of the selected numerical columns and unique values in the categorical and string columns (currently {actual_num}) should be at least {lower_boundary}. (Il numero totale di colonne numeriche selezionate e valori univoci nelle colonne di categoria e stringa (attualmente {actual_num}) deve essere almeno {lower_boundary}.)|


## <a name="error-0154"></a>Errore 0154  
 L'eccezione si verifica quando l'utente tenta di unire i dati nelle colonne chiave con un tipo di colonna non compatibile.

|Messaggi di eccezione|
|------------------------|
|Key column element types are not compatible. (I tipi di elemento della colonna chiave non sono compatibili.)|
|Key column element types are not compatible.(left: {keys_left}; right: {keys_right}) (I tipi di elemento della colonna chiave non sono compatibili. (Sinistra: {keys_left}; destra: {keys_right}))|


## <a name="error-0155"></a>Errore 0155  
 L'eccezione si verifica quando i nomi della colonna del set di dati non sono stringa.

|Messaggi di eccezione|
|------------------------|
|The dataframe column name must be string type. Column names are not string. (I nomi della colonna dataframe devono essere di tipo stringa. I nomi della colonna non sono stringhe.)|
|The dataframe column name must be string type. Column names {column_names} are not string. (I nomi della colonna dataframe devono essere di tipo stringa. I nomi della colonna {column_names} non sono stringhe.)|


## <a name="error-0156"></a>Errore 0156  
 L'eccezione si verifica quando non è possibile leggere i dati del database SQL di Azure.

|Messaggi di eccezione|
|------------------------|
|Failed to read data from Azure SQL Database. (Non è stato possibile leggere i dati dal database SQL di Azure.)|
|Failed to read data from Azure SQL Database: {detailed_message} DB: {database_server_name}:{database_name} Query: {sql_statement} (Non è stato possibile leggere i dati dal database SQL di Azure: {detailed_message} DB: {database_server_name}:{database_name} Query: {sql_statement})|


## <a name="error-0157"></a>Errore 0157  
 Archivio dati non trovato.

|Messaggi di eccezione|
|------------------------|
|Datastore information is invalid (Informazioni dell'archivio dati non valide.)|
|Datastore information is invalid. Failed to get AzureML datastore '{datastore_name}' in workspace '{workspace_name}'. (Informazioni dell'archivio dati non valide. Non è stato possibile ottenere l'archivio dati AzureML " {datastore_name}" nell'area di lavoro "{workspace_name}".)|


## <a name="error-0158"></a>Errore 0158
 Generato quando una directory di trasformazione non è valida.

|Messaggi di eccezione|
|------------------------------------------------------------|
|Given TransformationDirectory is invalid. (TransformationDirectory specificato non è valido.)|
|TransformationDirectory "{arg_name}" is invalid. Reason: {reason}. Please rerun training experiment which generates the Transform file. If training experiment was deleted, please recreate and save the Transform file. (TransformationDirectory "{arg_name}" non è valido. Motivo: {reason}. Eseguire di nuovo l'esperimento di training che genera il file di trasformazione. Se l'esperimento di training è stato eliminato, ricrearlo e salvare il file di trasformazione.)|
|TransformationDirectory "{arg_name}" is invalid. Reason: {reason}. {troubleshoot_hint} (TransformationDirectory "{arg_name}" non è valido. Motivo: {reason}. {troubleshoot_hint})|


## <a name="error-0159"></a>Errore 0159
 L'eccezione si verifica se la directory del modello passata al modulo non è valida. 

|Messaggi di eccezione|
|------------------------------------------------------------|
|Given ModelDirectory is invalid. (ModelDirectory specificato non è valido.)|
|ModelDirectory "{arg_name}" is invalid. (ModelDirectory "{arg_name}" non è valido.)|
|ModelDirectory "{arg_name}" is invalid. (ModelDirectory "{arg_name}" non è valido.) Reason: {reason}.|
|ModelDirectory "{arg_name}" is invalid. Reason: {reason}. {troubleshoot_hint} (ModelDirectory "{arg_name}" non è valido. Motivo: {reason}. {troubleshoot_hint})|


## <a name="error-1000"></a>Errore 1000  
Eccezione interna della libreria.  

Questo errore viene generato per acquisire errori interni del motore che altrimenti non verrebbero gestiti. La cause di questo errore potrebbero quindi variare a seconda del modulo che ha generato l'errore.  

Per ottenere assistenza, è consigliabile pubblicare il messaggio dettagliato che accompagna l'errore nel forum di Azure Machine Learning, insieme a una descrizione dello scenario, inclusi i dati usati come input. Questo feedback consentirà classificare gli errori in ordine di priorità e di identificare i problemi più importanti da approfondire.  

|Messaggi di eccezione|
|------------------------|
|Library exception. (Eccezione della libreria.)|
|Library exception: {exception}. (Eccezione della libreria: {exception}.)|
|Unknown library exception: {exception}. {customer_support_guidance}. (Eccezione della libreria sconosciuta: {exception}. {customer_support_guidance}.)|

