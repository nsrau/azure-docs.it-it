---
title: Risolvere gli errori del modulo
titleSuffix: Azure Machine Learning service
description: Risolvere i problemi relativi alle eccezioni del modulo in Azure Machine Learning Studio usando codici di errore
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: a1a6817c08223b360c08804e0595f12f2947ea5f
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693081"
---
# <a name="exceptions-and-error-codes-for-algorithm--module-reference"></a>Eccezioni e codici di errore per l'algoritmo & riferimento al modulo

Informazioni sui messaggi di errore e i codici di eccezione che possono verificarsi usando i moduli in Azure Machine Learning Studio. 

Per risolvere il problema, cercare l'errore in questo articolo per leggere le cause più comuni. Esistono due modi per ottenere il testo completo di un messaggio di errore in studio:  
 
- Fare clic sul collegamento, **visualizzare il log di output**nel riquadro destro e scorrere fino alla fine. Il messaggio di errore dettagliato viene visualizzato nelle ultime due righe della finestra.  
  
- Selezionare il modulo in cui si è verificato l'errore, quindi fare clic sulla X rossa. Viene visualizzato solo il testo dell'errore pertinente.  
  
Se il testo del messaggio di errore non è utile, inviare le informazioni sul contesto e eventuali aggiunte o modifiche desiderate. È possibile inviare commenti e suggerimenti sull'argomento di errore oppure visitare il [Forum di Azure Machine Learning Studio](https://aka.ms/aml-forum-studio) e pubblicare una domanda.  


## <a name="error-0001"></a>Errore 0001  
 Si verifica un'eccezione se non è stato possibile trovare una o più colonne specificate del set di dati.  
  
 Questo errore viene visualizzato se viene effettuata una selezione di colonna per un modulo, ma la colonna o le colonne selezionate non esistono nel set di dati di input. Questo errore può verificarsi se è stato digitato manualmente in un nome di colonna o se il selettore di colonna ha fornito una colonna suggerita che non esisteva nel set di dati quando è stata eseguita la pipeline.  
  
**Risoluzione:** Rivedere il modulo generando questa eccezione e verificare che il nome o i nomi di colonna siano corretti e che tutte le colonne a cui si fa riferimento esistano.  
  
|Messaggi di eccezione|  
|------------------------|  
|Impossibile trovare una o più colonne specificate|  
|Impossibile trovare la colonna con nome o indice "{0}"|  
|La colonna con nome o indice "{0}" non esiste in "{1}"|  
 

## <a name="error-0002"></a>Errore 0002  
 Si verifica un'eccezione se non è stato possibile analizzare o convertire uno o più parametri dal tipo specificato in richiesto dal tipo di metodo di destinazione.  
  
 Questo errore si verifica in Azure Machine Learning quando si specifica un parametro come input e il tipo di valore è diverso dal tipo previsto e non è possibile eseguire la conversione implicita.  
  
**Risoluzione:** Verificare i requisiti del modulo e determinare quale tipo di valore è obbligatorio (String, Integer, Double e così via)  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è stato possibile analizzare il parametro|  
|Non è stato possibile analizzare il parametro "{0}"|  
|Non è stato possibile analizzare (convertire) il parametro "{0}" in "{1}"|  
|Non è stato possibile convertire il parametro "{0}" da "{1}" a "{2}"|  
|Non è stato possibile convertire il valore "{1}" del parametro "{0}" da "{2}" a "{3}"|  
|Non è stato possibile convertire il valore "{0}" nella colonna "{1}" da "{2}" a "{3}" con l'utilizzo del formato "{4}" specificato|  
  

## <a name="error-0003"></a>Errore 0003  
 Si verifica un'eccezione se uno o più input sono null o vuoti.  
  
 Questo errore verrà visualizzato in Azure Machine Learning se eventuali input o parametri di un modulo sono null o vuoti.  Questo errore può verificarsi, ad esempio, quando non si digita alcun valore per un parametro. Può verificarsi anche se si sceglie un set di dati con valori mancanti o un set di dati vuoto.  
  
**Risoluzione:**
 
+ Aprire il modulo che ha generato l'eccezione e verificare che siano stati specificati tutti gli input. Verificare che siano specificati tutti gli input obbligatori. 
+ Assicurarsi che i dati caricati dall'archiviazione di Azure siano accessibili e che il nome o la chiave dell'account non sia stata modificata.  
+ Controllare i dati di input per individuare i valori mancanti o null.
+ Se si utilizza una query in un'origine dati, verificare che i dati vengano restituiti nel formato previsto. 
+ Verificare la presenza di errori di digitazione o altre modifiche nella specifica dei dati.
  
|Messaggi di eccezione|  
|------------------------|  
|Uno o più input sono null o vuoti|  
|L'input "{0}" è null o vuoto|  
  

## <a name="error-0004"></a>Errore 0004  
 Si verifica un'eccezione se il parametro è minore o uguale a un valore specifico.  
  
 Questo errore verrà visualizzato in Azure Machine Learning se il parametro nel messaggio è sotto un valore limite necessario per l'elaborazione dei dati da parte del modulo.  
  
**Risoluzione:** Rivedere il modulo generando l'eccezione e modificare il parametro in modo che sia maggiore del valore specificato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il parametro deve essere maggiore del valore limite.|  
|Il valore del parametro "{0}" deve essere maggiore di {1}.|  
|Il valore del parametro "{0}" è "{1}" che deve essere maggiore di {2}|  
  


## <a name="error-0005"></a>Errore 0005  
 Si verifica un'eccezione se il parametro è minore di un valore specifico.  
  
 Questo errore verrà visualizzato in Azure Machine Learning se il parametro nel messaggio è inferiore o uguale a un valore limite necessario per l'elaborazione dei dati da parte del modulo.  
  
**Risoluzione:** Rivedere il modulo generando l'eccezione e modificare il parametro in modo che sia maggiore o uguale al valore specificato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il parametro deve essere maggiore o uguale al valore limite.|  
|Il valore del parametro "{0}" deve essere maggiore o uguale a {1}.|  
|Il parametro "{0}" ha il valore "{1}" che deve essere maggiore o uguale a {2}.|  
  

## <a name="error-0006"></a>Errore 0006  
 Si verifica un'eccezione se il parametro è maggiore o uguale al valore specificato.  
  
 Questo errore verrà visualizzato in Azure Machine Learning se il parametro nel messaggio è maggiore o uguale a un valore limite necessario per l'elaborazione dei dati da parte del modulo.  
  
**Risoluzione:** Rivedere il modulo generando l'eccezione e modificare il parametro in modo che sia minore del valore specificato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Parametri non corrispondenti. Uno dei parametri deve essere minore di un altro.|  
|Il valore del parametro "{0}" deve essere minore del parametro "{1}".|  
|Il valore del parametro "{0}" è "{1}" che deve essere minore di {2}.|  
  

## <a name="error-0007"></a>Errore 0007  
 Si verifica un'eccezione se il parametro è maggiore di un valore specifico.  
  
 Questo errore viene visualizzato in Azure Machine Learning se, nelle proprietà del modulo, è stato specificato un valore maggiore di è consentito. È possibile, ad esempio, specificare un valore che non sia compreso nell'intervallo di date supportate. in alternativa, è possibile indicare che vengono utilizzate cinque colonne quando sono disponibili solo tre colonne. 
 
 Questo errore può essere visualizzato anche se si specificano due set di dati che devono corrispondere in qualche modo. Se, ad esempio, si rinominano le colonne e si specificano le colonne in base all'indice, il numero di nomi fornito deve corrispondere al numero di indici di colonna. Un altro esempio potrebbe essere un'operazione matematica che usa due colonne, in cui le colonne devono avere lo stesso numero di righe. 
  
**Risoluzione:**
 
 + Aprire il modulo in questione ed esaminare le impostazioni delle proprietà numeriche.
 + Verificare che i valori dei parametri rientrino nell'intervallo di valori supportato per la proprietà.
 + Se il modulo accetta più input, verificare che le dimensioni degli input siano uguali.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Controllare se il set di dati o l'origine dati è stato modificato. Talvolta un valore che funzionava con una versione precedente dei dati avrà esito negativo dopo il numero di colonne, i tipi di dati della colonna o le dimensioni dei dati modificati.  
  
|Messaggi di eccezione|  
|------------------------|  
|Parametri non corrispondenti. Uno dei parametri deve essere minore o uguale a un altro.|  
|Il valore del parametro "{0}" deve essere minore o uguale al valore del parametro "{1}".|  
|Il parametro "{0}" ha il valore "{1}" che deve essere minore o uguale a {2}.|  
  

## <a name="error-0008"></a>Errore 0008  
 Si verifica un'eccezione se il parametro non è compreso nell'intervallo.  
  
 Questo errore verrà visualizzato in Azure Machine Learning se il parametro del messaggio non rientra nei limiti necessari per l'elaborazione dei dati da parte del modulo.  
  
 Questo errore viene visualizzato, ad esempio, se si tenta di utilizzare [Aggiungi righe](add-rows.md) per combinare due set di impostazioni con un numero diverso di colonne.  
  
**Risoluzione:** Rivedere il modulo generando l'eccezione e modificare il parametro in modo che sia compreso nell'intervallo specificato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il valore del parametro non è compreso nell'intervallo specificato.|  
|Il valore del parametro "{0}" non è compreso nell'intervallo.|  
|Il valore del parametro "{0}" deve essere compreso nell'intervallo [{1}, {2}].|  
  

## <a name="error-0009"></a>Errore 0009  
 Si verifica un'eccezione quando il nome dell'account di archiviazione di Azure o il nome del contenitore non è specificato correttamente.  
  
Questo errore si verifica in Azure Machine Learning Studio quando si specificano i parametri per un account di archiviazione di Azure, ma non è possibile risolvere il nome o la password. Per diversi motivi è possibile che si verifichino errori di password o nomi di account:
 
 + Il tipo dell'account non è corretto. Alcuni nuovi tipi di account non sono supportati per l'utilizzo con Machine Learning Studio. Per informazioni dettagliate, vedere [importare dati](import-data.md) .
 + Il nome dell'account specificato non è corretto
 + L'account non esiste più
 + La password per l'account di archiviazione è errata o è stata modificata
 + Il nome del contenitore non è stato specificato oppure il contenitore non esiste
 + Il percorso del file non è stato specificato completamente (percorso del BLOB)
   
**Risoluzione:**

Questi problemi si verificano spesso quando si tenta di immettere manualmente il nome dell'account, la password o il percorso del contenitore. Si consiglia di usare la nuova procedura guidata per il modulo [Import Data](import-data.md) , che consente di cercare e controllare i nomi.

Controllare anche se l'account, il contenitore o il BLOB è stato eliminato. Usare un'altra utilità di archiviazione di Azure per verificare che il nome e la password dell'account siano stati immessi correttamente e che il contenitore esista. 

Alcuni tipi di conto più recenti non sono supportati da Azure Machine Learning. Ad esempio, non è possibile usare i nuovi tipi di archiviazione "Hot" o "Cold" per Machine Learning. Sia gli account di archiviazione classici che gli account di archiviazione creati come "utilizzo generico" funzionano correttamente.

Se è stato specificato il percorso completo di un BLOB, verificare che il percorso sia specificato come **contenitore/blobname**e che sia il contenitore che il BLOB esistano nell'account.  
  
 Il percorso non deve contenere una barra iniziali. Ad esempio, **/container/BLOB** non è corretto e deve essere immesso come **contenitore/BLOB**.  

  
|Messaggi di eccezione|  
|------------------------|  
|Il nome dell'account di archiviazione di Azure o il nome del contenitore non è corretto.|  
|Il nome dell'account di archiviazione di Azure "{0}" o il nome del contenitore "{1}" non è corretto; è previsto un nome di contenitore del formato contenitore/BLOB.|  
  

## <a name="error-0010"></a>Errore 0010  
 Si verifica un'eccezione se i nomi di colonna dei set di dati di input non corrispondono.  
  
 Questo errore verrà visualizzato in Azure Machine Learning se l'indice di colonna nel messaggio ha nomi di colonna diversi nei due set di dati di input.  
  
**Risoluzione:** Utilizzare [Modifica metadati](edit-metadata.md) o modificare il set di dati originale con lo stesso nome di colonna per l'indice di colonna specificato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Le colonne con indice corrispondente nei set di dati di input hanno nomi diversi.|  
|I nomi delle colonne non sono gli stessi per le colonne {0} (in base zero) dei set di dati di input (rispettivamente {1} e {2}).|  
  

## <a name="error-0011"></a>Errore 0011  
 Si verifica un'eccezione se l'argomento del set di colonne passato non è applicabile ad alcuna colonna del set di dati.  
  
 Questo errore verrà visualizzato in Azure Machine Learning se la selezione della colonna specificata non corrisponde a nessuna delle colonne del set di dati specificato.  
  
 È anche possibile ottenere questo errore se non è stata selezionata una colonna e almeno una colonna è necessaria per il funzionamento del modulo.  
  
**Risoluzione:** Modificare la selezione delle colonne nel modulo in modo che venga applicata alle colonne nel set di dati.  
  
 Se per il modulo è necessario selezionare una colonna specifica, ad esempio una colonna etichetta, verificare che sia selezionata la colonna destra.  
  
 Se sono selezionate colonne non appropriate, rimuoverle e rieseguire la pipeline.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il set di colonne specificato non è applicabile ad alcuna colonna del set di dati.|  
|Il set di colonne "{0}" specificato non è applicabile ad alcuna colonna del set di dati.|  
  

## <a name="error-0012"></a>Errore 0012  
 Si verifica un'eccezione se non è stato possibile creare l'istanza della classe con il set di argomenti passato.  
  
**Risoluzione:** Questo errore non è interoperabile dall'utente e verrà deprecato in una versione futura.  
  
|Messaggi di eccezione|  
|------------------------|  
|Modello non sottoposto a training, Esegui prima il training del modello.|  
|Modello non sottoposto a training ({0}), usare il modello con training.|  
  

## <a name="error-0013"></a>Errore 0013  
 Si verifica un'eccezione se lo Learner passato al modulo non è un tipo valido.  
  
 Questo errore si verifica quando un modello sottoposto a training non è compatibile con il modulo di Punteggio connesso. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->
  
**Risoluzione:**

Determinare il tipo di discente prodotto dal modulo di training e determinare il modulo di assegnazione dei punteggi appropriato per lo Learner. 

Se è stato eseguito il training del modello utilizzando uno dei moduli di training specializzati, connettere il modello sottoposto a training solo al modulo di assegnazione dei punteggi specializzato corrispondente. 


|Tipo di modello|Modulo di training| Modulo di assegnazione dei punteggi|
|----|----|----|
|qualsiasi classificatore|[Train Model](train-model.md) |[Modello di Punteggio](score-model.md)|
|qualsiasi modello di regressione|[Train Model](train-model.md) |[Modello di Punteggio](score-model.md)|

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
|Viene passato il tipo di apprendimento non valido.|  
|Il tipo di apprendimento "{0}" non è valido.|  


## <a name="error-0014"></a>Errore 0014  
 Si verifica un'eccezione se il numero di valori univoci della colonna è maggiore di quello consentito.  
  
 Questo errore si verifica quando una colonna contiene troppi valori univoci.  Ad esempio, è possibile che venga visualizzato questo errore se si specifica che una colonna deve essere gestita come dati categorici, ma nella colonna sono presenti troppi valori univoci per consentire il completamento dell'elaborazione. Questo errore può essere visualizzato anche in caso di mancata corrispondenza tra il numero di valori univoci in due input.   
  
**Risoluzione:**

Aprire il modulo che ha generato l'errore e identificare le colonne usate come input. Per alcuni moduli, è possibile fare clic con il pulsante destro del mouse sull'input del set di dati e selezionare **Visualizza** per ottenere statistiche sulle singole colonne, incluso il numero di valori univoci e la relativa distribuzione.

Per le colonne che si desidera utilizzare per il raggruppamento o la categorizzazione, adottare le misure necessarie per ridurre il numero di valori univoci nelle colonne. È possibile ridurre in modi diversi, a seconda del tipo di dati della colonna. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Non è possibile trovare una soluzione corrispondente allo scenario? È possibile fornire commenti e suggerimenti su questo argomento che include il nome del modulo che ha generato l'errore e il tipo di dati e la cardinalità della colonna. Le informazioni vengono utilizzate per fornire procedure di risoluzione dei problemi più mirate per gli scenari comuni.   
  
|Messaggi di eccezione|  
|------------------------|  
|Il numero di valori univoci della colonna è maggiore di quello consentito.|  
|Il numero di valori univoci nella colonna: "{0}" supera il numero di tuple di {1}.|  
  

## <a name="error-0015"></a>Errore 0015  
 Si verifica un'eccezione se la connessione al database non è riuscita.  
  
 Questo errore viene visualizzato se si immette un nome di account SQL, una password, un server di database o un nome di database errato o se non è possibile stabilire una connessione con il database a causa di problemi con il database o il server.  
  
**Risoluzione:** Verificare che il nome dell'account, la password, il server di database e il database siano stati immessi correttamente e che l'account specificato disponga del livello di autorizzazioni corretto. Verificare che il database sia attualmente accessibile.  
  
|Messaggi di eccezione|  
|------------------------|  
|Errore durante la connessione al database.|  
|Errore durante la connessione al database: {0}.|  
  


## <a name="error-0016"></a>Errore 0016  
 Si verifica un'eccezione se i tipi di colonna dei set di dati di input passati al modulo non sono compatibili.  
  
 Questo errore verrà visualizzato in Azure Machine Learning se i tipi delle colonne passate in due o più set di impostazioni non sono compatibili tra loro.  
  
**Risoluzione:** Usare [Modifica metadati](edit-metadata.md) o modificare il set di dati di input originale<!--, or use [Convert to Dataset](convert-to-dataset.md)--> per assicurarsi che i tipi delle colonne siano compatibili.  
  
|Messaggi di eccezione|  
|------------------------|  
|Le colonne con indice corrispondente nei set di dati di input hanno tipi incompatibili.|  
|Le colonne {0} e {1} sono incompatibili.|  
|I tipi di elemento Column non sono compatibili per la colonna {0} (in base zero) dei set di dati di input (rispettivamente {1} e {2}).|  
  

## <a name="error-0017"></a>Errore 0017  
 Si verifica un'eccezione se una colonna selezionata utilizza un tipo di dati non supportato dal modulo corrente.  
  
 Ad esempio, è possibile che venga visualizzato questo errore in Azure Machine Learning se la selezione della colonna include una colonna con un tipo di dati che non può essere elaborato dal modulo, ad esempio una colonna stringa per un'operazione matematica o una colonna score in cui una colonna di funzionalità categorica è Obbligatorio.  
  
**Risoluzione:**
 1. Identificare la colonna che rappresenta il problema.
 2. Esaminare i requisiti del modulo.
 3. Modificare la colonna per renderla conforme ai requisiti. Potrebbe essere necessario usare diversi dei moduli seguenti per apportare modifiche, a seconda della colonna e della conversione che si sta tentando di eseguire:
    + Utilizzare [Modifica metadati](edit-metadata.md) per modificare il tipo di dati delle colonne o per modificare l'utilizzo delle colonne da feature a numeric, categorico a non categorico e così via.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Come ultima risorsa, potrebbe essere necessario modificare il set di dati di input originale.

> [!TIP]
> Non è possibile trovare una soluzione corrispondente allo scenario? È possibile fornire commenti e suggerimenti su questo argomento che include il nome del modulo che ha generato l'errore e il tipo di dati e la cardinalità della colonna. Le informazioni vengono utilizzate per fornire procedure di risoluzione dei problemi più mirate per gli scenari comuni. 
  
|Messaggi di eccezione|  
|------------------------|  
|Impossibile elaborare la colonna di tipo corrente. Il tipo non è supportato dal modulo.|  
|Impossibile elaborare la colonna di tipo {0}. Il tipo non è supportato dal modulo.|  
|Impossibile elaborare la colonna "{1}" di tipo {0}. Il tipo non è supportato dal modulo.|  
|Impossibile elaborare la colonna "{1}" di tipo {0}. Il tipo non è supportato dal modulo. Nome parametro: {2}|  
  

## <a name="error-0018"></a>Errore 0018  
 Si verifica un'eccezione se il set di dati di input non è valido.  
  
**Risoluzione:** Questo errore in Azure Machine Learning può essere visualizzato in molti contesti, quindi non esiste una singola risoluzione. In generale, l'errore indica che i dati forniti come input per un modulo hanno un numero errato di colonne oppure che il tipo di dati non corrisponde ai requisiti del modulo. ad esempio:  
  
-   Il modulo richiede una colonna Label, ma nessuna colonna è contrassegnata come etichetta oppure non è ancora stata selezionata una colonna Label.  
  
-   Il modulo richiede che i dati siano categorici, ma i dati sono numerici.  
  
<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->
  
-   Il formato dei dati non è corretto.  
  
-   I dati importati contengono caratteri non validi, valori non validi o valori non compresi nell'intervallo.  
-   La colonna è vuota o contiene troppi valori mancanti.  
  
 Per determinare i requisiti e la modalità di utilizzo dei dati, vedere l'argomento della Guida per il modulo che utilizzerà il set di dati come input.  
  
 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  
  
|Messaggi di eccezione|  
|------------------------|  
|Set di dati non valido.|  
|{0} contiene dati non validi.|  
|{0} e {1} devono essere coerenti con la colonna.|  
  

## <a name="error-0019"></a>Errore 0019  
 Si verifica un'eccezione se è previsto che la colonna contenga valori ordinati, ma non lo è.  
  
 Questo errore verrà visualizzato in Azure Machine Learning se i valori della colonna specificati non sono ordinati.  
  
**Risoluzione:** Ordinare i valori della colonna modificando manualmente il set di dati di input ed eseguire di nuovo il modulo.  
  
|Messaggi di eccezione|  
|------------------------|  
|I valori nella colonna non sono ordinati.|  
|I valori nella colonna "{0}" non sono ordinati.|  
|I valori nella colonna "{0}" del set di dati "{1}" non sono ordinati.|  
  

## <a name="error-0020"></a>Errore 0020  
 Si verifica un'eccezione se il numero di colonne in alcuni set di impostazioni passati al modulo è troppo piccolo.  
  
 Questo errore verrà visualizzato in Azure Machine Learning se non sono state selezionate colonne sufficienti per un modulo.  
  
**Risoluzione:** Rivedere il modulo e assicurarsi che il selettore di colonna includa il numero corretto di colonne selezionate.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il numero di colonne nel set di dati di input è inferiore al minimo consentito.|  
|Il numero di colonne nel set di dati di input è inferiore al minimo consentito di {0} colonna/e.|  
|Il numero di colonne nel set di dati di input "{0}" è inferiore al minimo consentito di {1} colonna/e.|

## <a name="error-0021"></a>Errore 0021  
 Si verifica un'eccezione se il numero di righe in alcuni set di impostazioni passati al modulo è troppo piccolo.  
  
 Questo errore si è verificato in Azure Machine Learning quando nel set di dati non sono presenti righe sufficienti per eseguire l'operazione specificata. Ad esempio, è possibile che venga visualizzato questo errore se il set di dati di input è vuoto o se si sta tentando di eseguire un'operazione che richiede un numero minimo di righe valido. Tali operazioni possono includere, ma non limitate, il raggruppamento o la classificazione in base a metodi statistici, determinati tipi di contenitori e apprendimento con conteggi.  
  
**Risoluzione:**
 
 + Aprire il modulo che ha restituito l'errore e controllare le proprietà del modulo e del set di dati di input. 
 + Verificare che il set di dati di input non sia vuoto e che siano presenti righe di dati sufficienti per soddisfare i requisiti descritti nella guida del modulo.  
 + Se i dati vengono caricati da un'origine esterna, assicurarsi che l'origine dati sia disponibile e che non siano presenti errori o modifiche nella definizione dei dati che potrebbero causare un minor numero di righe da parte del processo di importazione.
 + Se si sta eseguendo un'operazione sui dati upstream del modulo che potrebbero influire sul tipo di dati o sul numero di valori, ad esempio operazioni di pulizia, suddivisione o join, controllare gli output di tali operazioni per determinare il numero di righe restituite.  



## <a name="error-0022"></a>Errore 0022  
 Si verifica un'eccezione se il numero di colonne selezionate nel set di dati di input non corrisponde al numero previsto.  
  
 Questo errore in Azure Machine Learning può verificarsi quando il modulo o l'operazione downstream richiede un numero specifico di colonne o input e sono stati specificati troppi o troppi colonne o input. ad esempio:  
  
-   È possibile specificare una singola colonna di etichetta o colonna chiave e selezionare accidentalmente più colonne.  
  
-   Si sta rinominando le colonne, ma sono stati specificati più o meno nomi rispetto alle colonne.  
  
-   Il numero di colonne nell'origine o nella destinazione è stato modificato o non corrisponde al numero di colonne usate dal modulo.  
  
-   È stato fornito un elenco delimitato da virgole di valori per gli input, ma il numero di valori non corrisponde o non sono supportati più input.  
  
**Risoluzione:** Rivedere il modulo e controllare la selezione della colonna per assicurarsi che sia selezionato il numero corretto di colonne. Verificare gli output dei moduli upstream e i requisiti delle operazioni downstream.  
  
 Se è stata usata una delle opzioni di selezione delle colonne che possono selezionare più colonne (indici di colonna, tutte le funzionalità, tutti numerici e così via), convalidare il numero esatto di colonne restituite dalla selezione.  
  
 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->
  
 Verificare che il numero o il tipo di colonne upstream non sia stato modificato.  
  
 Se si usa un set di dati di raccomandazione per eseguire il training di un modello, tenere presente che il raccomandante prevede un numero limitato di colonne, corrispondenti alle coppie di elementi utente o all'elemento di rango utente. Rimuovere le colonne aggiuntive prima di eseguire il training del modello o suddividere i set di impostazioni di raccomandazione. Per ulteriori informazioni, vedere [Split data](split-data.md).  
  
|Messaggi di eccezione|  
|------------------------|  
|Il numero di colonne selezionate nel set di dati di input non corrisponde al numero previsto.|  
|Il numero di colonne selezionate nel set di dati di input non è uguale a {0}.|  
|Il criterio di selezione delle colonne "{0}" fornisce il numero di colonne selezionate nel set di dati di input diverso da {1}.|  
|È previsto che il modello di selezione delle colonne "{0}" fornisca {1} colonne selezionate nel set di dati di input, ma {2} colonne viene fornito/i.|  



## <a name="error-0023"></a>Errore 0023  
 Si verifica un'eccezione se la colonna di destinazione del set di dati di input non è valida per il modulo Trainer corrente.  
  
 Questo errore si verifica Azure Machine Learning se la colonna di destinazione (come selezionato nei parametri del modulo) non è del tipo di dati valido, contiene tutti i valori mancanti o non è categorico come previsto.  
  
**Risoluzione:** Rivedere l'input del modulo per esaminare il contenuto della colonna etichetta/destinazione. Verificare che non siano presenti tutti i valori mancanti. Se il modulo prevede che la colonna di destinazione sia categorica, assicurarsi che siano presenti più valori distinct nella colonna di destinazione.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il set di dati di input contiene una colonna di destinazione non supportata.|  
|Il set di dati di input contiene la colonna di destinazione "{0}" non supportata.|  
|Per il set di dati di input non è supportata la colonna di destinazione "{0}" per gli studenti di tipo {1}.|  
 

## <a name="error-0024"></a>Errore 0024  
Si verifica un'eccezione se il set di dati non contiene una colonna di etichetta.  

 Questo errore in Azure Machine Learning si verifica quando il modulo richiede una colonna Label e il set di dati non contiene una colonna Label. Per la valutazione di un set di dati con punteggio, ad esempio, è in genere necessario che sia presente una colonna Label per calcolare le metriche di accuratezza.  
 
È inoltre possibile che nel set di dati sia presente una colonna di etichette, ma che non sia stata rilevata correttamente da Azure Machine Learning.
  
**Risoluzione:**

+ Aprire il modulo che ha generato l'errore e determinare se è presente una colonna di etichetta. Il nome o il tipo di dati della colonna non è rilevante, purché la colonna contenga un solo risultato (o variabile dipendente) che si sta tentando di stimare. Se non si è certi della colonna con l'etichetta, cercare un nome generico, ad esempio *classe* o *destinazione*. 
+  Se il set di dati non include una colonna di etichetta, è possibile che la colonna dell'etichetta sia stata rimossa in modo esplicito o accidentale. È anche possibile che il set di dati non sia l'output di un modulo di assegnazione dei punteggi upstream.
+ Per contrassegnare in modo esplicito la colonna come colonna etichetta, aggiungere il modulo [Modifica metadati](edit-metadata.md) e connettere il set di dati. Selezionare solo la colonna etichetta e selezionare **etichetta** dall'elenco a discesa **campi** . 
+ Se si sceglie la colonna errata come etichetta, è possibile selezionare **Cancella etichetta** dai **campi** per correggere i metadati nella colonna. 
  
|Messaggi di eccezione|  
|------------------------|  
|Nessuna colonna Label nel set di dati.|  
|Nessuna colonna di etichetta in "{0}".|  
  

## <a name="error-0025"></a>Errore 0025  
 Si verifica un'eccezione se il set di dati non contiene una colonna del punteggio.  
  
 Questo errore si verifica Azure Machine Learning se l'input per il modello di valutazione non contiene colonne con punteggio valido. Ad esempio, l'utente tenta di valutare un set di dati prima di assegnare un punteggio a un modello con training corretto oppure se la colonna del punteggio è stata eliminata in modo esplicito a Monte. Questa eccezione si verifica anche se le colonne del punteggio nei due set di impostazioni sono incompatibili. Ad esempio, si potrebbe provare a confrontare l'accuratezza di un regressore lineare con quello di un classificatore binario.  
  
**Risoluzione:** Rivedere l'input per il modello Evaluate ed esaminare se contiene una o più colonne score. In caso contrario, non è stato eseguito il punteggio del set di dati oppure le colonne del punteggio sono state eliminate in un modulo upstream.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non sono presenti colonne di punteggio nel set di dati.|  
|Non è presente alcuna colonna score in "{0}".|  
|Non è presente alcuna colonna score in "{0}" prodotta da un "{1}". Assegnare un punteggio al set di dati usando il tipo di apprendimento corretto.|  
  

## <a name="error-0026"></a>Errore 0026  
 Si verifica un'eccezione se non sono consentite colonne con lo stesso nome.  
  
 Questo errore si verifica Azure Machine Learning se più colonne hanno lo stesso nome. Uno dei modi in cui è possibile ricevere questo errore è se al set di dati non è assegnata automaticamente una riga di intestazione e i nomi di colonna: Col0, col1 e così via.  
  
**Risoluzione:** Se le colonne hanno lo stesso nome, inserire un modulo di [modifica dei metadati](edit-metadata.md) tra il set di dati di input e il modulo. Usare il selettore di colonna in [Modifica metadati](edit-metadata.md) per selezionare le colonne da rinominare, digitando i nuovi nomi nella casella di testo **nuovi nomi di colonna** .  
  
|Messaggi di eccezione|  
|------------------------|  
|I nomi di colonna uguali sono specificati negli argomenti. I nomi di colonna uguali non sono consentiti dal modulo.|  
|Non sono consentiti nomi di colonna uguali negli argomenti "{0}" e "{1}". Specificare nomi diversi.|  
  

## <a name="error-0027"></a>Errore 0027  
 Si verifica un'eccezione nel caso in cui due oggetti debbano avere le stesse dimensioni ma non lo sono.  
  
 Si tratta di un errore comune in Azure Machine Learning e può essere causato da molte condizioni.  
  
**Risoluzione:** Non esiste alcuna risoluzione specifica. Tuttavia, è possibile verificare le condizioni seguenti:  
  
-   Se si rinominano le colonne, assicurarsi che ogni elenco (le colonne di input e l'elenco dei nuovi nomi) includa lo stesso numero di elementi.  
  
-   Se si uniscono o si concatenano due set di impostazioni, assicurarsi che abbiano lo stesso schema.  
  
-   Se si uniscono due set di dati che includono più colonne, verificare che le colonne chiave abbiano lo stesso tipo di dati e selezionare l'opzione **Consenti duplicati e Mantieni ordine colonne nella selezione**.  
  
|Messaggi di eccezione|  
|------------------------|  
|Le dimensioni degli oggetti passati sono incoerenti.|  
|Le dimensioni di "{0}" non sono coerenti con le dimensioni di "{1}".|  
  

## <a name="error-0028"></a>Errore 0028  
 Si verifica un'eccezione nel caso in cui il set di colonne contenga nomi di colonna duplicati e non è consentito.  
  
 Questo errore in Azure Machine Learning si verifica quando i nomi delle colonne sono duplicati. ovvero, non è univoco.  
  
**Risoluzione:** Se le colonne hanno lo stesso nome, aggiungere un'istanza di [Modifica metadati](edit-metadata.md) tra il set di dati di input e il modulo che genera l'errore. Usare il selettore di colonna in [Modifica metadati](edit-metadata.md) per selezionare le colonne da rinominare, quindi digitare i nomi delle nuove colonne nella casella di testo **new column names** . Se si rinominano più colonne, assicurarsi che i valori digitati nei **nuovi nomi di colonna** siano univoci.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il set di colonne contiene i nomi di colonna duplicati.|  
|Nome "{0}" duplicato.|  
|Il nome "{0}" è duplicato in "{1}".|  
  

## <a name="error-0029"></a>Errore 0029  
 Si verifica un'eccezione quando viene passato un URI non valido.  
  
 Questo errore in Azure Machine Learning si verifica nel caso in cui venga passato un URI non valido.  Questo errore viene visualizzato se si verifica una delle condizioni seguenti: o.  
  
-   L'URI pubblico o SAS fornito per l'archiviazione BLOB di Azure per la lettura o la scrittura contiene un errore.  
  
-   L'intervallo di tempo per la firma di accesso condiviso è scaduto.  
  
-   L'URL Web tramite l'origine HTTP rappresenta un file o un URI di loopback.  
  
-   L'URL Web tramite HTTP contiene un URL formattato in modo errato.  
  
-   L'URL non può essere risolto dall'origine remota.  
  
**Risoluzione:** Rivedere il modulo e verificare il formato dell'URI. Se l'origine dati è un URL Web tramite HTTP, verificare che l'origine desiderata non sia un file o un URI di loopback (localhost).  
  
|Messaggi di eccezione|  
|------------------------|  
|Viene passato un URI non valido.|  
  

## <a name="error-0030"></a>Errore 0030  
 Si verifica un'eccezione nel caso in cui non sia possibile scaricare un file.  
  
 Questa eccezione in Azure Machine Learning si verifica quando non è possibile scaricare un file. Questa eccezione viene generata quando un tentativo di lettura da un'origine HTTP ha avuto esito negativo dopo tre (3) tentativi.  
  
**Risoluzione:** Verificare che l'URI dell'origine HTTP sia corretto e che il sito sia attualmente accessibile tramite Internet.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è possibile scaricare un file.|  
|Errore durante il download del file: {0}.|  
  

## <a name="error-0031"></a>Errore 0031  
 Si verifica un'eccezione se il numero di colonne nel set di colonne è inferiore al necessario.  
  
 Questo errore si verifica Azure Machine Learning se il numero di colonne selezionato è inferiore al necessario.  Questo errore viene visualizzato se il numero minimo necessario di colonne non è selezionato.  
  
**Risoluzione:** Aggiungere altre colonne alla selezione della colonna usando il **selettore di colonna**.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il numero di colonne nel set di colonne è minore di quello richiesto.|  
|è necessario specificare {0} colonna/e. Il numero effettivo di colonne specificate è {1}.|  

## <a name="error-0032"></a>Errore 0032  
 Si verifica un'eccezione se l'argomento non è un numero.  
  
 Questo errore verrà visualizzato in Azure Machine Learning se l'argomento è un valore Double o NaN.  
  
**Risoluzione:** Modificare l'argomento specificato per utilizzare un valore valido.  
  
|Messaggi di eccezione|  
|------------------------|  
|L'argomento non è un numero.|  
|"{0}" non è un numero.|  
  

## <a name="error-0033"></a>Errore 0033  
 Si verifica un'eccezione se l'argomento è infinito.  
  
 Questo errore si verifica Azure Machine Learning se l'argomento è infinito. Questo errore viene visualizzato se l'argomento è `double.NegativeInfinity` o `double.PositiveInfinity`.  
  
**Risoluzione:** Modificare l'argomento specificato in modo che sia un valore valido.  
  
|Messaggi di eccezione|  
|------------------------|  
|L'argomento deve essere finito.|  
|"{0}" non è finito.|  
  

## <a name="error-0034"></a>Errore 0034  
 Si verifica un'eccezione se esiste più di una classificazione per una coppia di elementi utente specificata.  
  
 Questo errore in Azure Machine Learning si verifica se una coppia di elementi utente ha più di un valore di valutazione.  
  
**Risoluzione:** Verificare che la coppia utente-elemento disponga solo di un valore di valutazione.  
  
|Messaggi di eccezione|  
|------------------------|  
|Sono presenti più classificazioni per i valori nel set di dati.|  
|Più di una classificazione per {0} utente e {1} di elementi nella tabella dati di stima di valutazione.|  
  

## <a name="error-0035"></a>Errore 0035  
 Si verifica un'eccezione se non sono state fornite funzionalità per un determinato utente o elemento.  
  
 Questo errore si verifica Azure Machine Learning si sta provando a usare un modello di raccomandazione per l'assegnazione dei punteggi ma non è possibile trovare un vettore di funzionalità.  
  
**Risoluzione:**

Quando si usano le funzionalità degli elementi o le funzionalità utente, è necessario che il Consiglio di ricorrenza di determinati requisiti venga soddisfatto.  Questo errore indica che manca un vettore di funzionalità per un utente o un elemento fornito come input.  È necessario assicurarsi che un vettore di funzionalità sia disponibile nei dati per ogni utente o elemento.  
  
 Se, ad esempio, è stato eseguito il training di un modello di raccomandazione utilizzando funzionalità quali la durata, la posizione o il reddito dell'utente, ma ora si desidera creare i punteggi per i nuovi utenti che non sono stati visualizzati durante il training, è necessario fornire un set di funzionalità equivalente (ossia, età, località e valori del reddito) per i nuovi utenti per eseguire stime appropriate. 
 
 Se non si dispone di alcuna funzionalità per questi utenti, provare a progettare le funzionalità per generare le funzionalità appropriate.  Se, ad esempio, non sono presenti valori di età o reddito singoli, è possibile generare valori approssimati da usare per un gruppo di utenti. 
 
<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).
 
For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->
  
 > [!TIP]
 > Risoluzione non applicabile al caso? È possibile inviare commenti e suggerimenti su questo articolo e fornire informazioni sullo scenario, inclusi il modulo e il numero di righe nella colonna. Queste informazioni vengono usate per fornire procedure più dettagliate per la risoluzione dei problemi in futuro.
   
|Messaggi di eccezione|  
|------------------------|  
|Non sono state fornite funzionalità per un utente o un elemento obbligatorio.|  
|Funzionalità per {0} richieste ma non fornite.|  
  

## <a name="error-0036"></a>Errore 0036  
 Si verifica un'eccezione se sono stati specificati più vettori di funzionalità per un determinato utente o elemento.  
  
 Questo errore in Azure Machine Learning si verifica se un vettore di funzionalità viene definito più di una volta.  
  
**Risoluzione:** Verificare che il vettore di funzionalità non sia definito più di una volta.  
  
|Messaggi di eccezione|  
|------------------------|  
|Definizione di funzionalità duplicata per un utente o un elemento.|  
|Definizione di funzionalità duplicata per {0}.|  
  

## <a name="error-0037"></a>Errore 0037  
 Si verifica un'eccezione se sono specificate più colonne Label e ne è consentito solo uno.  
  
 Questo errore si verifica Azure Machine Learning se è stata selezionata più di una colonna come nuova colonna etichetta. Per la maggior parte degli algoritmi di apprendimento supervisionato è necessario contrassegnare una singola colonna come destinazione o etichetta.  
  
**Risoluzione:** Assicurarsi di selezionare una singola colonna come nuova colonna etichetta.  
  
|Messaggi di eccezione|  
|------------------------|  
|Sono state specificate più colonne Label.|  
  

## <a name="error-0038"></a>Errore 0038  
 Si verifica un'eccezione se il numero di elementi previsti deve essere un valore esatto, ma non lo è.  
  
 Questo errore si verifica Azure Machine Learning se il numero di elementi previsti deve essere un valore esatto, ma non lo è.  Questo errore viene visualizzato se il numero di elementi non è uguale al valore previsto valido.  
  
**Risoluzione:** Modificare l'input in modo che includa il numero corretto di elementi.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il numero di elementi non è valido.|  
|Il numero di elementi in "{0}" non è valido.|  
|Il numero di elementi in "{0}" non è uguale al numero valido di {1} elementi.|  
  

## <a name="error-0039"></a>Errore 0039  
 Si verifica un'eccezione se un'operazione non è riuscita.  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile completare un'operazione interna.  
  
**Risoluzione:** Questo errore è causato da molte condizioni e non esiste alcun rimedio specifico.  
 La tabella seguente contiene messaggi generici per questo errore, seguiti da una descrizione specifica della condizione. 
 
 Se non sono disponibili dettagli, [inviare commenti e suggerimenti](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) e fornire informazioni sui moduli che hanno generato l'errore e le condizioni correlate.
  
|Messaggi di eccezione|  
|------------------------|  
|Operazione non riuscita.|  
|Errore durante il completamento dell'operazione: {0}.|  
  

## <a name="error-0040"></a>Errore 0040  
 Si verifica un'eccezione quando si chiama un modulo deprecato.  
  
 Questo errore in Azure Machine Learning viene generato quando si chiama un modulo deprecato.  
  
**Risoluzione:** Sostituire il modulo deprecato con uno supportato. Per informazioni sul modulo da usare in alternativa, vedere il log di output del modulo.  
  
|Messaggi di eccezione|  
|------------------------|  
|Accesso al modulo deprecato.|  
|Il modulo "{0}" è deprecato. Usare invece il modulo "{1}".|  
 

## <a name="error-0041"></a>Errore 0041  
 Si verifica un'eccezione quando si chiama un modulo deprecato.  
  
 Questo errore in Azure Machine Learning viene generato quando si chiama un modulo deprecato.  
  
**Risoluzione:** Sostituire il modulo deprecato con un set di quelli supportati. Queste informazioni devono essere visibili nel log di output del modulo.  
  
|Messaggi di eccezione|  
|------------------------|  
|Accesso al modulo deprecato.|  
|Il modulo "{0}" è deprecato. Usare i moduli "{1}" per la funzionalità richiesta.|  
 

## <a name="error-0042"></a>Errore 0042  
 Si verifica un'eccezione quando non è possibile convertire la colonna in un altro tipo.  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile convertire la colonna nel tipo specificato.  Questo errore viene visualizzato se un modulo richiede un tipo di dati specifico, ad esempio DateTime, text, un numero a virgola mobile o Integer, ma non è possibile convertire una colonna esistente nel tipo richiesto.  
 
Ad esempio, è possibile selezionare una colonna e provare a convertirla in un tipo di dati numerico da usare in un'operazione matematica e ottenere questo errore se la colonna conteneva dati non validi. 

Un altro motivo per cui è possibile ottenere questo errore se si tenta di utilizzare una colonna contenente numeri a virgola mobile o molti valori univoci come colonna categorica. 
  
**Risoluzione:**

+ Aprire la pagina della Guida per il modulo che ha generato l'errore e verificare i requisiti del tipo di dati.
+ Esaminare i tipi di dati delle colonne nel set di dati di input.
+ Controllare i dati provenienti da origini dati senza schema denominate.
+ Controllare il set di dati per individuare i valori mancanti o i caratteri speciali che potrebbero bloccare la conversione nel tipo di dati desiderato. 
    + I tipi di dati numerici devono essere coerenti: ad esempio, verificare la presenza di numeri a virgola mobile in una colonna di numeri interi.
    + Cerca stringhe di testo o valori NA in una colonna numerica. 
    + I valori booleani possono essere convertiti in una rappresentazione appropriata a seconda del tipo di dati richiesto.
    + Esaminare le colonne di testo per i caratteri non Unicode, i caratteri di tabulazione o i caratteri di controllo
    + I dati DateTime devono essere coerenti per evitare errori di modellazione, ma la pulizia può essere complessa a causa dei diversi formati. Prendere in considerazione l'uso di <!--the [Execute R Script](execute-r-script.md) or -->Eseguire i moduli di [script Python](execute-python-script.md) per eseguire la pulizia.  
+ Se necessario, modificare i valori nel set di dati di input in modo che la colonna possa essere convertita correttamente. La modifica può includere operazioni di suddivisione in contenitori, troncamento o arrotondamento, eliminazione di outlier o imputazione di valori mancanti. Vedere gli articoli seguenti per alcuni scenari comuni di trasformazione dei dati in Machine Learning:
    + [Pulisci i dati mancanti](clean-missing-data.md)
    + [Normalizza i dati](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->
 
> [!TIP]
> Risoluzione non chiara o non applicabile al caso? È possibile inviare commenti e suggerimenti su questo articolo e fornire informazioni sullo scenario, inclusi il modulo e il tipo di dati della colonna. Queste informazioni vengono usate per fornire procedure più dettagliate per la risoluzione dei problemi in futuro.  
  
|Messaggi di eccezione|  
|------------------------|  
|Conversione non consentita.|  
|Non è stato possibile convertire la colonna di tipo {0} nella colonna di tipo {1}.|  
|Impossibile convertire la colonna "{2}" di tipo {0} nella colonna di tipo {1}.|  
|Impossibile convertire la colonna "{2}" di tipo {0} nella colonna "{3}" di tipo {1}.|  
  

## <a name="error-0043"></a>Errore 0043  
 Si verifica un'eccezione quando il tipo di elemento non implementa in modo esplicito Equals.  
  
 Questo errore in Azure Machine Learning non è utilizzato e verrà deprecato.  
  
**Risoluzione:** Nessuno.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è stato trovato alcun metodo esplicito accessibile.|  
|Impossibile confrontare i valori per la colonna \\ "{0} \\" di tipo {1}. Non è stato trovato alcun metodo esplicito accessibile.|  


## <a name="error-0044"></a>Errore 0044  
 Si verifica un'eccezione quando non è possibile derivare il tipo di elemento della colonna dai valori esistenti.  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile dedurre il tipo di una colonna o di colonne in un set di dati. Questo problema si verifica in genere durante la concatenazione di due o più set di impostazioni con tipi di elementi diversi. Se Azure Machine Learning non è in grado di determinare un tipo comune in grado di rappresentare tutti i valori di una colonna o di colonne senza perdita di informazioni, verrà generato questo errore.  
  
**Risoluzione:** Assicurarsi che tutti i valori di una determinata colonna in entrambi i set di dati combinati siano dello stesso tipo (numeric, Boolean, categoric, String, date e così via) o possano essere assegnati allo stesso tipo.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è possibile derivare il tipo di elemento della colonna.|  
|Non è possibile derivare il tipo di elemento per la colonna "{0}"--tutti gli elementi sono riferimenti null.|  
|Non è possibile derivare il tipo di elemento per la colonna "{0}" del set di dati "{1}": tutti gli elementi sono riferimenti null.|  
  

## <a name="error-0045"></a>Errore 0045  
 Si verifica un'eccezione quando non è possibile creare una colonna a causa di tipi di elemento misti nell'origine.  
  
 Questo errore in Azure Machine Learning viene generato quando i tipi di elementi di due set di impostazioni combinati sono diversi.  
  
**Risoluzione:** Verificare che tutti i valori di una determinata colonna in entrambi i set di dati combinati siano dello stesso tipo (numeric, Boolean, categoric, String, date e così via).  
  
|Messaggi di eccezione|  
|------------------------|  
|Impossibile creare una colonna con tipi di elemento misti.|  
|Impossibile creare una colonna con ID "{0}" di tipi di elemento misti: \ n\tType di dati [{1}, {0}] è {2} \n\tType di dati [{3}, {0}] è {4}.|  
  

## <a name="error-0046"></a>Errore 0046  
 Si verifica un'eccezione quando non è possibile creare la directory nel percorso specificato.  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile creare una directory nel percorso specificato. Questo errore viene visualizzato se una parte del percorso della directory di output per una query hive non è corretta o è inaccessibile.  
  
**Risoluzione:** Rivedere il modulo e verificare che il percorso della directory sia formattato correttamente e che sia accessibile con le credenziali correnti.  
  
|Messaggi di eccezione|  
|------------------------|  
|Specificare una directory di output valida.|  
|Directory: non è possibile creare {0}. Specificare un percorso valido.|  
  

## <a name="error-0047"></a>Errore 0047  
 Si verifica un'eccezione se il numero di colonne di funzioni in alcuni set di impostazioni passati al modulo è troppo piccolo.  
  
 Questo errore in Azure Machine Learning si verifica se il set di dati di input per il training non contiene il numero minimo di colonne richieste dall'algoritmo. In genere il set di dati è vuoto o contiene solo colonne di training.  
  
**Risoluzione:** Rivedere il set di dati di input per assicurarsi che esistano una o più colonne aggiuntive separate dalla colonna Label.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il numero di colonne di funzioni nel set di dati di input è inferiore al minimo consentito.|  
|Il numero di colonne di funzioni nel set di dati di input è inferiore al minimo consentito di {0} colonna/e.|  
|Il numero di colonne di funzioni nel set di dati di input "{0}" è inferiore al minimo consentito di {1} colonna/e.|  
  

## <a name="error-0048"></a>Errore 0048  
 Si verifica un'eccezione nel caso in cui non sia possibile aprire un file.  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile aprire un file per la lettura o la scrittura. Questo errore può essere visualizzato per i motivi seguenti:  
  
-   Il contenitore o il file (BLOB) non esiste  
  
-   Il livello di accesso del file o del contenitore non consente di accedere al file  
  
-   Il file è troppo grande per essere letto o il formato non è corretto  
  
**Risoluzione:** Rivedere il modulo e il file che si sta tentando di leggere.  
  
 Verificare che i nomi del contenitore e del file siano corretti.  
  
 Usare il portale di Azure classico o uno strumento di archiviazione di Azure per verificare di disporre delle autorizzazioni per accedere al file.  
  
  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->
  
|Messaggi di eccezione|  
|------------------------|  
|Non è possibile aprire un file.|  
|Errore durante l'apertura del file: {0}.|  


## <a name="error-0049"></a>Errore 0049  
 Si verifica un'eccezione nel caso in cui non sia possibile analizzare un file.  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile analizzare un file. Questo errore viene visualizzato se il formato di file selezionato nel modulo [Import Data](import-data.md) non corrisponde al formato effettivo del file o se il file contiene un carattere non riconoscibile.  
  
**Risoluzione:** Rivedere il modulo e correggere la selezione del formato di file se non corrisponde al formato del file. Se possibile, controllare il file per verificare che non contenga caratteri non validi.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è possibile analizzare un file.|  
|Errore durante l'analisi del file: {0}.|  
  

## <a name="error-0050"></a>Errore 0050  
 Si verifica un'eccezione nel caso in cui i file di input e di output siano uguali.  
  
**Risoluzione:** Questo errore in Azure Machine Learning non è utilizzato e verrà deprecato.  
  
|Messaggi di eccezione|  
|------------------------|  
|I file specificati per l'input e l'output non possono essere uguali.|


## <a name="error-0051"></a>Errore 0051  
 Si verifica un'eccezione nel caso in cui diversi file di output corrispondano.  
  
**Risoluzione:** Questo errore in Azure Machine Learning non è utilizzato e verrà deprecato.  
  
|Messaggi di eccezione|  
|------------------------|  
|I file specificati per gli output non possono essere uguali.|


## <a name="error-0052"></a>Errore 0052  
 Si verifica un'eccezione se la chiave dell'account di archiviazione di Azure non è stata specificata correttamente.  
  
 Questo errore si verifica Azure Machine Learning se la chiave usata per accedere all'account di archiviazione di Azure non è corretta. Ad esempio, è possibile che venga visualizzato questo errore se la chiave di archiviazione di Azure è stata troncata quando è stata copiata e incollata o se è stata usata una chiave errata.  
  
 Per altre informazioni su come ottenere la chiave per un account di archiviazione di Azure, vedere [visualizzare, copiare e rigenerare le chiavi di accesso alle archiviazione](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  
  
**Risoluzione:** Rivedere il modulo e verificare che la chiave di archiviazione di Azure sia corretta per l'account; Se necessario, copiare di nuovo la chiave dal portale di Azure classico.  
  
|Messaggi di eccezione|  
|------------------------|  
|La chiave dell'account di archiviazione di Azure non è corretta.|  
  

## <a name="error-0053"></a>Errore 0053  
 Si verifica un'eccezione nel caso in cui non siano presenti caratteristiche utente o elementi per le raccomandazioni della scatola di fiammiferi.  
  
 Questo errore in Azure Machine Learning viene generato quando non è possibile trovare un vettore di funzionalità.  
  
**Risoluzione:** Verificare che nel set di dati di input sia presente un vettore di funzionalità.  
  
|Messaggi di eccezione|  
|------------------------|  
|Le funzionalità utente o/e gli elementi sono obbligatori, ma non sono disponibili.|  

## <a name="error-0054"></a>Errore 0054  
 Si verifica un'eccezione se è presente un numero troppo basso di valori distinct nella colonna per completare l'operazione.  
  
**Risoluzione:** Questo errore in Azure Machine Learning non è utilizzato e verrà deprecato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il numero di valori distinct nella colonna specificata non è sufficiente per completare l'operazione.|  
|Il numero di valori distinct nella colonna specificata non è sufficiente per completare l'operazione. Il valore minimo richiesto è {0} elementi.|  
|Il numero di valori distinct nella colonna "{1}" non è sufficiente per completare l'operazione. Il valore minimo richiesto è {0} elementi.|  
  

## <a name="error-0055"></a>Errore 0055  
 Si verifica un'eccezione quando si chiama un modulo deprecato.  Questo errore in Azure Machine Learning viene visualizzato se si tenta di chiamare un modulo che è stato deprecato.
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|Accesso al modulo deprecato.|  
|Il modulo "{0}" è deprecato.|  

## <a name="error-0056"></a>Errore 0056  
 Si verifica un'eccezione se le colonne selezionate per un'operazione violano i requisiti.  
  
 Questo errore in Azure Machine Learning si verifica quando si scelgono le colonne per un'operazione che richiede che la colonna sia di un tipo di dati specifico. 
 
 Questo errore può verificarsi anche se il tipo di dati della colonna è corretto, ma il modulo in uso richiede che la colonna sia contrassegnata anche come una caratteristica, un'etichetta o una colonna categorica.  
  
  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->
  
**Risoluzione:**
  
1.  Esaminare il tipo di dati delle colonne attualmente selezionate. 

2. Verificare se le colonne selezionate sono di categoria, etichetta o colonne di funzionalità.  
  
3.  Esaminare l'argomento della Guida per il modulo in cui è stata effettuata la selezione delle colonne, per determinare se sono presenti requisiti specifici per il tipo di dati o l'utilizzo delle colonne.  
  
3.  Utilizzare [Modifica metadati](edit-metadata.md) per modificare il tipo di colonna per la durata di questa operazione. Assicurarsi di ripristinare il valore originale del tipo di colonna, usando un'altra istanza di [Modifica metadati](edit-metadata.md), se necessario per le operazioni downstream.  
  
|Messaggi di eccezione|  
|------------------------|  
|Una o più colonne selezionate non si trovavano in una categoria consentita.|  
|La colonna denominata "{0}" non è in una categoria consentita.|  
  

## <a name="error-0057"></a>Errore 0057  
 Si verifica un'eccezione quando si tenta di creare un file o un BLOB già esistente.  
  
 Questa eccezione si verifica quando si usa il modulo [Export Data](export-data.md) o un altro modulo per salvare i risultati di una pipeline in Azure Machine Learning nell'archiviazione BLOB di Azure, ma si tenta di creare un file o un BLOB già esistente.   
  
**Risoluzione:**
 
 Questo errore viene visualizzato solo se in precedenza è stata impostata la proprietà **modalità di scrittura archiviazione BLOB di Azure** su **errore**. In base alla progettazione, questo modulo genera un errore se si tenta di scrivere un set di dati in un BLOB già esistente.
 
 - Aprire le proprietà del modulo e impostare la proprietà **modalità di scrittura archiviazione BLOB di Azure** su **Sovrascrivi**.
 - In alternativa, è possibile digitare il nome di un BLOB o un file di destinazione diverso e assicurarsi di specificare un BLOB che non esiste già.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il file o il BLOB esiste già.|  
|Il file o il BLOB "{0}" esiste già.|  
  

## <a name="error-0058"></a>Errore 0058  
 Questo errore si verifica Azure Machine Learning se il set di dati non contiene la colonna di etichetta prevista.  
  
 Questa eccezione può verificarsi anche quando la colonna Label specificata non corrisponde al tipo di dati o al tipo di dati previsto dallo Learner oppure ha i valori errati. Ad esempio, questa eccezione viene generata quando si usa una colonna di etichetta con valori reali durante il training di un classificatore binario.  
  
**Risoluzione:** La risoluzione dipende dallo discente o dal trainer usato e dai tipi di dati delle colonne nel set di dati. Prima di tutto, verificare i requisiti dell'algoritmo di Machine Learning o del modulo di training.  
  
 Rivedere il set di dati di input. Verificare che la colonna da considerare come etichetta abbia il tipo di dati corretto per il modello che si sta creando.  
  
 Controllare gli input per i valori mancanti ed eliminarli o sostituirli, se necessario.  
  
 Se necessario, aggiungere il modulo [Edit Metadata](edit-metadata.md) e verificare che la colonna Label sia contrassegnata come etichetta.  
  
|Messaggi di eccezione|  
|------------------------|  
|La colonna di etichette non è quella prevista|  
|La colonna Label non è come previsto in "{0}".|  
|La colonna di etichette "{0}" non è prevista in "{1}".|  
  

## <a name="error-0059"></a>Errore 0059  
 Si verifica un'eccezione se non è possibile analizzare un indice di colonna specificato in un selettore di colonna.  
  
 Questo errore si verifica Azure Machine Learning se non è possibile analizzare un indice di colonna specificato quando si utilizza il selettore di colonna.  Questo errore viene visualizzato quando l'indice di colonna è in un formato non valido che non può essere analizzato.  
  
**Risoluzione:** Modificare l'indice di colonna per utilizzare un valore di indice valido.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è stato possibile analizzare uno o più indici di colonna o intervalli di indice specificati.|  
|Non è stato possibile analizzare l'indice o l'intervallo di colonne "{0}".|  
  

## <a name="error-0060"></a>Errore 0060  
 Si verifica un'eccezione quando un intervallo di colonne non compreso nell'intervallo viene specificato in un selettore di colonna.  
  
 Questo errore in Azure Machine Learning si verifica quando nel selettore di colonna viene specificato un intervallo di colonne non compreso nell'intervallo. Questo errore viene visualizzato se l'intervallo di colonne nel selettore di colonna non corrisponde alle colonne nel set di dati.  
  
**Risoluzione:** Modificare l'intervallo di colonne nella selezione colonne in modo che corrisponda alle colonne nel set di dati.  
  
|Messaggi di eccezione|  
|------------------------|  
|Intervallo di indice di colonna non valido o non compreso nell'intervallo specificato.|  
|L'intervallo di colonne "{0}" non è valido o non è compreso nell'intervallo.|  
  

## <a name="error-0061"></a>Errore 0061  
 Si verifica un'eccezione quando si tenta di aggiungere una riga a un oggetto DataTable con un numero diverso di colonne rispetto alla tabella.  
  
 Questo errore in Azure Machine Learning si verifica quando si tenta di aggiungere una riga a un set di dati con un numero diverso di colonne rispetto al set di dati.  Questo errore viene visualizzato se la riga aggiunta al set di dati contiene un numero diverso di colonne del set di dati di input.  Non è possibile aggiungere la riga al set di dati se il numero di colonne è diverso.  
  
**Risoluzione:** Modificare il set di dati di input in modo che includa lo stesso numero di colonne della riga aggiunta oppure modificare la riga aggiunta per avere lo stesso numero di colonne del set di dati.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tutte le tabelle devono avere lo stesso numero di colonne.|  
  

## <a name="error-0062"></a>Errore 0062  
 Si verifica un'eccezione quando si tenta di confrontare due modelli con tipi di apprendimento diversi.  
  
 Questo errore in Azure Machine Learning viene generato quando non è possibile confrontare le metriche di valutazione per due diversi set di risultati con punteggio. In questo caso, non è possibile confrontare l'efficacia dei modelli utilizzati per produrre i due set di risultati con punteggio.  
  
**Risoluzione:** Verificare che i risultati con punteggio siano prodotti dallo stesso tipo di modello di apprendimento automatico (classificazione binaria, regressione, classificazione multiclasse, raccomandazione, clustering, rilevamento di anomalie e così via) Tutti i modelli confrontati devono avere lo stesso tipo di discente.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tutti i modelli devono avere lo stesso tipo di apprendimento.|  
  

 <!--## Error 0063  
 This exception is raised when R script evaluation fails with an error.  
  
 This error occurs when you have provided an R script in one of the [R language modules](r-language-modules.md) in Azure Machine Learning, and the R code contains internal syntax errors. The exception can also occur if you provide the wrong inputs to the R script. 
 
 The error can also occur if the script is too large to execute in the workspace. The maximum script size for the **Execute R Script** module is 1,000 lines or 32 KB of work space, whichever is lesser.
  
**Resolution:**

1. In Azure Machine Learning Studio, right-click the module that has the error, and select **View Log**.
2. Examine the standard error log of the module, which contains the stack trace.
    + Lines beginning with [ModuleOutput] indicate output from R.
    + Messages from R marked as **warnings** typically do not cause the pipeline to fail.
3. Resolve script issues.  
    + Check for R syntax errors. Check for variables that are defined but never populated.
    + Review the input data and the script to determine if either the data or variables in the script use characters not supported by Azure Machine Learning.
    + Check whether all package dependencies are installed.
    + Check whether your code loads required libraries that are not loaded by default.
    + Check whether the required packages are the correct version.
    + Make sure that any dataset that you want to output is converted to a data frame.  
4.  Resubmit the pipeline.

 <!--
> [!NOTE]
> These topics contains examples of R code that you can use, as well as links to pipelines in the [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com) that use R script.
> + [Execute R Script](execute-r-script.md)
> + [Create R Model](create-r-model.md)
-->  
|Messaggi di eccezione|  
|------------------------|  
|Errore durante la valutazione dello script R.|  
|Si è verificato il seguente errore durante la valutazione dello script R:----------inizio del messaggio di errore da R----------{0}-----------fine del messaggio di errore da R-----------|  
|Durante la valutazione dello script R "{1}" si è verificato l'errore seguente:----------avvio del messaggio di errore da R----------{0}-----------fine del messaggio di errore da R-----------|  
  


## <a name="error-0064"></a>Errore 0064  
 Si verifica un'eccezione se la chiave di archiviazione o il nome dell'account di archiviazione di Azure non è stato specificato correttamente.  
  
 Questo errore si verifica Azure Machine Learning se la chiave di archiviazione o il nome dell'account di archiviazione di Azure non è stato specificato correttamente. Questo errore viene visualizzato se si immette un nome di account o una password non corretta per l'account di archiviazione. Questo problema può verificarsi se si immette manualmente il nome o la password dell'account. Può inoltre verificarsi se l'account è stato eliminato.  
  
**Risoluzione:** Verificare che il nome dell'account e la password siano stati immessi correttamente e che l'account esista.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il nome dell'account di archiviazione di Azure o la chiave di archiviazione non è corretta.|  
|Il nome dell'account di archiviazione di Azure "{0}" o la chiave di archiviazione per il nome dell'account non è corretto.|  
  

## <a name="error-0065"></a>Errore 0065  
 Si verifica un'eccezione se il nome del BLOB di Azure non è stato specificato correttamente.  
  
 Questo errore in Azure Machine Learning si verifica se il nome del BLOB di Azure non è stato specificato correttamente.  Verrà visualizzato l'errore se:  
  
-   Impossibile trovare il BLOB nel contenitore specificato.  
  
 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->
  
-   Solo il contenitore è stato specificato come origine in una richiesta di [importazione dati](import-data.md) quando il formato era Excel o CSV con codifica; la concatenazione del contenuto di tutti i BLOB all'interno di un contenitore non è consentita con questi formati.  
  
-   Un URI SAS non contiene il nome di un BLOB valido.  
  
**Risoluzione:** Rivedere il modulo che genera l'eccezione. Verificare che il blob specificato esista nel contenitore dell'account di archiviazione e che le autorizzazioni consentano di visualizzare il BLOB. Verificare che l'input sia nel formato **containerName/filename** se si dispone di Excel o CSV con formati di codifica. Verificare che un URI di firma di accesso condiviso contenga il nome di un BLOB valido.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il BLOB di archiviazione di Azure non è corretto.|  
|Il nome del BLOB di archiviazione di Azure "{0}" non è corretto|  
  

## <a name="error-0066"></a>Errore 0066  
 Si verifica un'eccezione se non è stato possibile caricare una risorsa in un BLOB di Azure.  
  
 Questo errore in Azure Machine Learning si verifica se non è stato possibile caricare una risorsa in un BLOB di Azure.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Entrambi vengono salvati nello stesso account di archiviazione di Azure dell'account che contiene il file di input.  
  
**Risoluzione:** Rivedere il modulo. Verificare che il nome dell'account Azure, la chiave di archiviazione e il contenitore siano corretti e che l'account disponga delle autorizzazioni per la scrittura nel contenitore.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è stato possibile caricare la risorsa nell'archiviazione di Azure.|  
|Non è stato possibile caricare il file "{0}" in archiviazione di Azure come {1}.|  
  

## <a name="error-0067"></a>Errore 0067  
 Si verifica un'eccezione se il numero di colonne di un set di dati è diverso da quello previsto.  
  
 Questo errore in Azure Machine Learning si verifica se un set di dati contiene un numero di colonne diverso dal previsto.  Questo errore viene visualizzato quando il numero di colonne nel set di dati è diverso dal numero di colonne previsto dal modulo durante l'esecuzione.  
  
**Risoluzione:** Modificare il set di dati di input o i parametri.  
  
|Messaggi di eccezione|  
|------------------------|  
|Numero imprevisto di colonne nell'oggetto DataTable.|  
|Sono previste colonne "{0}", ma sono state rilevate colonne "{1}".|  
  

## <a name="error-0068"></a>Errore 0068  
 Si verifica un'eccezione se lo script hive specificato non è corretto.  
  
 Questo errore in Azure Machine Learning si verifica in presenza di errori di sintassi in uno script hive QL oppure se l'interprete hive rileva un errore durante l'esecuzione della query o dello script.  
  
**Risoluzione:**

Il messaggio di errore di hive viene in genere riportato nel log degli errori di, in modo che sia possibile eseguire un'azione in base all'errore specifico. 

+ Aprire il modulo ed esaminare la query per individuare eventuali errori.  
+ Verificare che la query funzioni correttamente al di fuori di Azure Machine Learning eseguendo l'accesso alla console hive del cluster Hadoop ed eseguendo la query.  
+ Provare a inserire commenti nello script hive in una riga distinta, anziché combinare istruzioni e commenti eseguibili in un'unica riga.  

### <a name="resources"></a>resources

Vedere gli articoli seguenti per informazioni sull'esecuzione di query hive per Machine Learning:

+ [Creare tabelle hive e caricare dati dall'archivio BLOB di Azure](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Esplorare i dati nelle tabelle con query hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Creare funzionalità per i dati in un cluster Hadoop con query Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Foglio informativo di hive per utenti SQL (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Messaggi di eccezione|  
|------------------------|  
|Lo script hive non è corretto.|  
|@No__t_0 script hive non è corretto.|  
  

## <a name="error-0069"></a>Errore 0069  
 Si verifica un'eccezione se lo script SQL specificato non è corretto.  
  
 Questo errore in Azure Machine Learning si verifica se lo script SQL specificato presenta problemi di sintassi o se le colonne o la tabella specificata nello script non sono valide. 
 
 Questo errore viene visualizzato se il motore SQL rileva un errore durante l'esecuzione della query o dello script. Il messaggio di errore SQL viene in genere riportato nel log degli errori di, in modo che sia possibile eseguire un'azione in base all'errore specifico.  
  
**Risoluzione:** Rivedere il modulo ed esaminare la query SQL per individuare eventuali errori.  
  
 Verificare che la query funzioni correttamente all'esterno di Azure ML accedendo direttamente al server di database ed eseguendo la query.  
  
 Se è presente un messaggio generato da SQL segnalato dall'eccezione del modulo, eseguire un'azione in base all'errore segnalato. I messaggi di errore, ad esempio, includono a volte istruzioni specifiche sul probabile errore:
+ *Nessuna colonna di questo tipo o database mancante*, a indicare che è possibile che sia stato digitato un nome di colonna errato. Se si è certi che il nome della colonna sia corretto, provare a utilizzare le parentesi quadre o le virgolette per racchiudere l'identificatore di colonna.
+ *Errore di logica SQL vicino \<SQL parola chiave \>* , che indica che è possibile che sia presente un errore di sintassi prima della parola chiave specificata

  
|Messaggi di eccezione|  
|------------------------|  
|Lo script SQL non è corretto.|  
|La query SQL "{0}" non è corretta.|  
|La query SQL "{0}" non è corretta: {1}|  
  

## <a name="error-0070"></a>Errore 0070  
 Si verifica un'eccezione quando si tenta di accedere a una tabella di Azure inesistente.  
  
 Questo errore in Azure Machine Learning si verifica quando si tenta di accedere a una tabella di Azure non esistente. Questo errore viene visualizzato se si specifica una tabella in archiviazione di Azure, che non esiste durante la lettura o la scrittura nell'archiviazione tabelle di Azure. Questo problema può verificarsi se il nome della tabella desiderata viene digitato in maniera errata o se il nome di destinazione e il tipo di archiviazione non corrispondono. Ad esempio, si intende leggere da una tabella, ma è stato immesso il nome di un BLOB.  
  
**Risoluzione:** Rivedere il modulo per verificare che il nome della tabella sia corretto.  
  
|Messaggi di eccezione|  
|------------------------|  
|La tabella di Azure non esiste.|  
|La tabella di Azure "{0}" non esiste.|  
  
## <a name="error-0071"></a>Errore 0071  
 Si verifica un'eccezione se le credenziali specificate non sono corrette.  
  
 Questo errore si verifica Azure Machine Learning se le credenziali specificate non sono corrette.  
  
 Questo errore può essere visualizzato anche se il modulo non è in grado di connettersi a un cluster HDInsight.  
  
**Risoluzione:** Esaminare gli input per il modulo e verificare il nome e la password dell'account.  
  
 Verificare i seguenti problemi che possono causare un errore:  
  
-   Lo schema del set di dati non corrisponde allo schema della DataTable di destinazione.  
  
-   Nomi di colonna mancanti o digitati in errore  
  
-   Si sta scrivendo in una tabella con nomi di colonna con caratteri non validi. In genere è possibile racchiudere tali nomi di colonna tra parentesi quadre, ma se non funziona, modificare i nomi delle colonne in modo da usare solo lettere e caratteri di sottolineatura (_)  
  
-   Le stringhe che si sta tentando di scrivere includono virgolette singole  
  
 Se si sta provando a connettersi a un cluster HDInsight, verificare che il cluster di destinazione sia accessibile con le credenziali fornite.  
  
|Messaggi di eccezione|  
|------------------------|  
|Vengono passate credenziali non corrette.|  
|Nome utente errato "{0}" o password superata|  
  

## <a name="error-0072"></a>Errore 0072  
 Si verifica un'eccezione in caso di timeout della connessione.  
  
 Questo errore in Azure Machine Learning si verifica quando si verifica il timeout di una connessione. Questo errore viene visualizzato se si verificano attualmente problemi di connettività con l'origine dati o la destinazione, ad esempio la connettività Internet lenta, oppure se il set di dati è di grandi dimensioni e/o la query SQL da leggere nei dati esegue un'elaborazione complessa.  
  
**Risoluzione:** Determinare se sono attualmente presenti problemi con le connessioni lente ad archiviazione di Azure o a Internet.  
  
|Messaggi di eccezione|  
|------------------------|  
|Timeout della connessione.|  
  

## <a name="error-0073"></a>Errore 0073  
 Si verifica un'eccezione se si verifica un errore durante la conversione di una colonna in un altro tipo.  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile convertire la colonna in un altro tipo.  Questo errore viene visualizzato se un modulo richiede un tipo particolare e non è possibile convertire la colonna nel nuovo tipo.  
  
**Risoluzione:** Modificare il set di dati di input in modo che la colonna possa essere convertita in base all'eccezione interna.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è stato possibile convertire la colonna.|  
|Non è stato possibile convertire la colonna in {0}.|  
  

## <a name="error-0074"></a>Errore 0074  
 Si verifica un'eccezione quando i [metadati di modifica](edit-metadata.md) tentano di convertire una colonna di tipo sparse in categorico.  
  
 Questo errore in Azure Machine Learning si verifica quando i [metadati di modifica](edit-metadata.md) tentano di convertire una colonna di tipo sparse in categorico.  Questo errore viene visualizzato quando si tenta di convertire le colonne di tipo sparse in categorico con l'opzione **make CATEGORICAL** .  Azure Machine Learning non supporta le matrici categoriche sparse, pertanto il modulo avrà esito negativo.  
  
 <!--**Resolution:**
 Make the column dense by using [Convert to Dataset](convert-to-dataset.md) first or do not convert the column to categorical.  -->
  
|Messaggi di eccezione|  
|------------------------|  
|Non è possibile convertire le colonne di tipo sparse in categorico.|  
  

## <a name="error-0075"></a>Errore 0075  
Si verifica un'eccezione quando viene utilizzata una funzione di suddivisione in contenitori non valida durante la quantizzatura di un DataSet.  
  
Questo errore in Azure Machine Learning si verifica quando si tenta di suddividere i dati utilizzando un metodo non supportato o quando le combinazioni di parametri non sono valide.  
  
**Risoluzione:**

La gestione degli errori per questo evento è stata introdotta in una versione precedente di Azure Machine Learning che consentiva una maggiore personalizzazione dei metodi di contenitori. Attualmente tutti i metodi di suddivisione in contenitori sono basati su una selezione da un elenco a discesa, quindi tecnicamente non dovrebbe più essere possibile ottenere questo errore.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->
  
|Messaggi di eccezione|  
|------------------------|  
|Utilizzata funzione di suddivisione in contenitori non valida.|  
  

## <a name="error-0077"></a>Errore 0077  
 Si verifica un'eccezione quando viene passata la modalità di scrittura del file BLOB sconosciuta.  
  
 Questo errore in Azure Machine Learning si verifica se un argomento non valido viene passato nelle specifiche per una destinazione o un'origine di file BLOB.  
  
**Risoluzione:** In quasi tutti i moduli che importano o esportano dati da e verso l'archiviazione BLOB di Azure, i valori dei parametri che controllano la modalità di scrittura vengono assegnati usando un elenco a discesa; Pertanto, non è possibile passare un valore non valido e questo errore non dovrebbe essere visualizzato. Questo errore verrà deprecato in una versione successiva.  
  
|Messaggi di eccezione|  
|------------------------|  
|Modalità di scrittura BLOB non supportata.|  
|Modalità di scrittura BLOB non supportata: {0}.|  
  

## <a name="error-0078"></a>Errore 0078  
 Si verifica un'eccezione quando l'opzione HTTP per i [dati di importazione](import-data.md) riceve un codice di stato 3xx che indica il reindirizzamento.  
  
 Questo errore in Azure Machine Learning si verifica quando l'opzione HTTP per i [dati di importazione](import-data.md) riceve un codice di stato 3xx (301, 302, 304 e così via) che indica il reindirizzamento. Questo errore viene visualizzato se si tenta di connettersi a un'origine HTTP che reindirizza il browser a un'altra pagina. Per motivi di sicurezza, il reindirizzamento dei siti Web non è consentito come origini dati per Azure Machine Learning.  
  
**Risoluzione:** Se il sito Web è un sito Web attendibile, immettere direttamente l'URL reindirizzato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Reindirizzamento HTTP non consentito|  
  

## <a name="error-0079"></a>Errore 0079  
 Si verifica un'eccezione se il nome del contenitore di archiviazione di Azure non è stato specificato correttamente.  
  
 Questo errore in Azure Machine Learning si verifica se il nome del contenitore di archiviazione di Azure non è stato specificato correttamente. Questo errore viene visualizzato se non sono stati specificati il nome del contenitore e del BLOB (file) usando **l'opzione percorso del BLOB che inizia con il contenitore durante la** scrittura nell'archiviazione BLOB di Azure.  
  
**Risoluzione:** Rivedere il modulo [Export Data](export-data.md) e verificare che il percorso specificato per il BLOB contenga sia il contenitore che il nome del file, nel formato **container/filename**.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il nome del contenitore di archiviazione di Azure non è corretto.|  
|Il nome del contenitore di archiviazione di Azure "{0}" non è corretto; è previsto un nome di contenitore del formato contenitore/BLOB.|  
  

## <a name="error-0080"></a>Errore 0080  
 Si verifica un'eccezione quando la colonna con tutti i valori mancanti non è consentita dal modulo.  
  
 Questo errore in Azure Machine Learning viene generato quando una o più colonne utilizzate dal modulo contengono tutti i valori mancanti. Se, ad esempio, un modulo elabora statistiche di aggregazione per ogni colonna, non può funzionare su una colonna che non contiene dati. In questi casi, l'esecuzione del modulo viene interrotta con questa eccezione.  
  
**Risoluzione:** Rivedere il set di dati di input e rimuovere le colonne che contengono tutti i valori mancanti.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non sono consentite colonne con tutti i valori mancanti.|  
|La colonna {0} contiene tutti i valori mancanti.|  
  

## <a name="error-0081"></a>Errore 0081  
 Si verifica un'eccezione nel modulo PCA se il numero di dimensioni da ridurre è uguale al numero di colonne di funzioni nel set di dati di input, che contiene almeno una colonna di funzionalità sparse.  
  
 Questo errore in Azure Machine Learning viene generato se vengono soddisfatte le condizioni seguenti: (a) il set di dati di input presenta almeno una colonna di tipo sparse e (b) il numero finale di dimensioni richiesto è uguale al numero di dimensioni di input.  
  
**Risoluzione:** Provare a ridurre il numero di dimensioni nell'output in modo che sia inferiore al numero di dimensioni nell'input. Questo è tipico nelle applicazioni di PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->
  
|Messaggi di eccezione|  
|------------------------|  
|Per il set di dati contenente le colonne di tipo sparse, il numero di dimensioni da ridurre deve essere inferiore al numero di colonne di funzioni.|  
 

## <a name="error-0082"></a>Errore 0082  
 Si verifica un'eccezione quando un modello non può essere deserializzato correttamente.  
  
 Questo errore in Azure Machine Learning si verifica quando un modello di apprendimento automatico salvato o una trasformazione non può essere caricata da una versione più recente del runtime Azure Machine Learning a causa di una modifica di rilievo.  
  
**Risoluzione:** La pipeline di training che ha prodotto il modello o la trasformazione deve essere rieseguita e il modello o la trasformazione deve essere salvato nuovamente.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è stato possibile deserializzare il modello perché è probabile che sia serializzato con un formato di serializzazione precedente. Ripetere il training del modello e risalvarlo.|  
  

## <a name="error-0083"></a>Errore 0083  
 Si verifica un'eccezione se non è possibile usare il set di dati usato per il training per il tipo concreto di Learner.  
  
 Questo errore in Azure Machine Learning viene generato quando il set di dati è incompatibile con lo Learner sottoposto a training. Il set di dati, ad esempio, potrebbe contenere almeno un valore mancante in ogni riga e, di conseguenza, l'intero set di dati verrebbe ignorato durante il training. In altri casi, alcuni algoritmi di Machine Learning, ad esempio il rilevamento di anomalie, non prevedono la presenza di etichette e possono generare questa eccezione se nel set di dati sono presenti etichette.  
  
**Risoluzione:** Consultare la documentazione dello Learner usato per verificare i requisiti per il set di dati di input. Esaminare le colonne per verificare che siano presenti tutte le colonne necessarie.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il set di dati usato per il training non è valido.|  
|{0} contiene dati non validi per il training.|  
|{0} contiene dati non validi per il training. Tipo di apprendimento: {1}.|  
  

## <a name="error-0084"></a>Errore 0084  
 Si verifica un'eccezione quando vengono valutati i punteggi prodotti da uno script R. Questa operazione non è attualmente supportata.  
  
 Questo errore in Azure Machine Learning si verifica se si tenta di usare uno dei moduli per la valutazione di un modello con output da uno script R che contiene punteggi.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|La valutazione dei punteggi prodotti da R non è attualmente supportata.|  
  

## <a name="error-0085"></a>Errore 0085  
 Si verifica un'eccezione quando la valutazione dello script non riesce e restituisce un errore.  
  
 Questo errore in Azure Machine Learning si verifica quando si esegue uno script personalizzato che contiene errori di sintassi.  
  
**Risoluzione:** Esaminare il codice in un editor esterno e verificare la presenza di errori.  
  
|Messaggi di eccezione|  
|------------------------|  
|Errore durante la valutazione dello script.|  
|Si è verificato il seguente errore durante la valutazione dello script, visualizzare il log di output per ulteriori informazioni:----------l'avvio del messaggio di errore da {0} interprete----------{1}----------fine del messaggio di errore dall'interprete {0}----------|  
  

## <a name="error-0086"></a>Errore 0086  
 Si verifica un'eccezione quando una trasformazione Conteggio non è valida.  
  
 Questo errore in Azure Machine Learning si verifica quando si seleziona una trasformazione basata su una tabella di conteggio, ma la trasformazione selezionata non è compatibile con i dati correnti oppure con la nuova tabella Count.  
  
**Risoluzione:** Il modulo supporta il salvataggio dei conteggi e delle regole che compongono la trasformazione in due formati diversi. Se si uniscono tabelle di conteggio, verificare che entrambe le tabelle che si intende unire usino lo stesso formato.  
  
In generale, una trasformazione basata su conteggi può essere applicata solo a set di dati con lo stesso schema del set di dati in cui è stata originariamente creata la trasformazione.  
  
 <!-- For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Messaggi di eccezione|  
|------------------------|  
|Trasformazione Conteggio non valida specificata.|  
|La trasformazione Conteggio nella porta di input ' {0}' non è valida.|  
|Impossibile unire la trasformazione Conteggio nella porta di input ' {0}' con la trasformazione Conteggio nella porta di input ' {1}'. Controllare per verificare i metadati usati per il conteggio delle corrispondenze.|  
  

## <a name="error-0087"></a>Errore 0087  
 Si verifica un'eccezione quando viene specificato un tipo di tabella di conteggio non valido per l'apprendimento con i moduli dei conteggi.  
  
 Questo errore in Azure Machine Learning si verifica quando si tenta di importare una tabella di conteggio esistente, ma la tabella non è compatibile con i dati correnti oppure con la nuova tabella Count.  
  
**Risoluzione:** Sono disponibili diversi formati per salvare i conteggi e le regole che compongono la trasformazione. Se si uniscono tabelle di conteggio, verificare che entrambi usino lo stesso formato.  
  
 In genere, una trasformazione basata su conteggi può essere applicata solo a set di dati con lo stesso schema del set di dati in cui è stata originariamente creata la trasformazione.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). -->
  

## <a name="error-0088"></a>Errore 0088  
 Si verifica un'eccezione quando viene specificato un tipo di conteggio non valido per l'apprendimento con i moduli dei conteggi.  
  
 Questo errore in Azure Machine Learning si verifica quando si tenta di usare un metodo di conteggio diverso da quello supportato per i conteggi basati sul conteggio.  
  
**Risoluzione:** In generale, il conteggio dei metodi viene scelto da un elenco a discesa, pertanto questo errore non dovrebbe essere visualizzato.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Messaggi di eccezione|  
|------------------------|  
|È stato specificato un tipo di conteggio non valido.|  
|Il tipo di conteggio ' {0}' specificato non è un tipo di conteggio valido.|  
  

## <a name="error-0089"></a>Errore 0089  
 Si verifica un'eccezione quando il numero di classi specificato è inferiore al numero effettivo di classi in un set di dati utilizzato per il conteggio.  
  
 Questo errore in Azure Machine Learning si verifica quando si crea una tabella di conteggio e la colonna Label contiene un numero diverso di classi rispetto a quanto specificato nei parametri del modulo.  
  
**Risoluzione:** Controllare il set di dati e individuare esattamente il numero di valori distinct (classi possibili) presenti nella colonna Label. Quando si crea la tabella Count, è necessario specificare almeno questo numero di classi.  
  
 La tabella count non è in grado di determinare automaticamente il numero di classi disponibili.  
  
 Quando si crea la tabella Count, non è possibile specificare 0 o un numero inferiore al numero effettivo di classi nella colonna Label.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il numero di classi non è corretto. Verificare che il numero di classi specificato nel riquadro dei parametri sia maggiore o uguale al numero di classi nella colonna etichetta.|  
|Il numero di classi specificato è' {0}', che non è maggiore di un valore di etichetta ' {1}' nel set di dati usato per il conteggio. Verificare che il numero di classi specificato nel riquadro dei parametri sia maggiore o uguale al numero di classi nella colonna etichetta.|  
  

## <a name="error-0090"></a>Errore 0090  
 Si verifica un'eccezione quando la creazione della tabella hive non riesce.  
  
 Questo errore in Azure Machine Learning si verifica quando si usa [Esporta dati](export-data.md) o un'altra opzione per salvare i dati in un cluster HDInsight e non è possibile creare la tabella hive specificata.  
  
**Risoluzione:** Verificare il nome dell'account di archiviazione di Azure associato al cluster e verificare che sia in uso lo stesso account nelle proprietà del modulo.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è stato possibile creare la tabella hive. Per un cluster HDInsight, verificare che il nome dell'account di archiviazione di Azure associato al cluster corrisponda a quello passato tramite il parametro Module.|  
|Non è stato possibile creare la tabella hive "{0}". Per un cluster HDInsight, verificare che il nome dell'account di archiviazione di Azure associato al cluster corrisponda a quello passato tramite il parametro Module.|  
|Non è stato possibile creare la tabella hive "{0}". Per un cluster HDInsight, verificare che il nome dell'account di archiviazione di Azure associato al cluster sia "{1}".|  
 

## <a name="error-0100"></a>Errore 0100  
 Si verifica un'eccezione quando viene specificata una lingua non supportata per un modulo personalizzato.  
  
 Questo errore si verifica in Azure Machine Learning durante la compilazione di un modulo personalizzato e la proprietà Name dell'elemento del **linguaggio** in un file di definizione XML del modulo personalizzato ha un valore non valido. Attualmente, l'unico valore valido per questa proprietà è `R`. ad esempio:  
  
 `<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Risoluzione:** Verificare che la proprietà nome dell'elemento del **linguaggio** nel file di definizione XML del modulo personalizzato sia impostata su `R`. Salvare il file, aggiornare il pacchetto zip del modulo personalizzato e provare ad aggiungere di nuovo il modulo personalizzato.  
  
|Messaggi di eccezione|  
|------------------------|  
|È stato specificato un linguaggio del modulo personalizzato non supportato|  
  

## <a name="error-0101"></a>Errore 0101  
 Tutti gli ID di porta e parametri devono essere univoci.  
  
 Questo errore in Azure Machine Learning si verifica quando a una o più porte o parametri viene assegnato lo stesso valore ID in un file di definizione XML del modulo personalizzato.  
  
**Risoluzione:** Verificare che i valori ID in tutte le porte e i parametri siano univoci. Salvare il file XML, aggiornare il pacchetto zip del modulo personalizzato e provare ad aggiungere di nuovo il modulo personalizzato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tutti gli ID porta e parametro per un modulo devono essere univoci|  
|Il modulo ' {0}' contiene ID di porta/argomento duplicati. Tutti gli ID porta/argomento devono essere univoci per un modulo.|  
  

## <a name="error-0102"></a>Errore 0102  
 Generata quando non è possibile estrarre un file ZIP.  
  
 Questo errore in Azure Machine Learning si verifica quando si importa un pacchetto compresso con estensione zip, ma il pacchetto non è un file zip oppure il file non usa un formato zip supportato.  
  
**Risoluzione:** Verificare che il file selezionato sia un file con estensione zip valido e che sia stato compresso utilizzando uno degli algoritmi di compressione supportati.  
  
 Se si verifica questo errore quando si importano i set di dati in formato compresso, verificare che tutti i file contenuti usino uno dei formati di file supportati e siano in formato Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->
  
 Provare a Riaggiungere i file desiderati a una nuova cartella compressa compressa e provare ad aggiungere di nuovo il modulo personalizzato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il formato del file ZIP specificato non è corretto|  


## <a name="error-0103"></a>Errore 0103  
 Generata quando un file ZIP non contiene file con estensione XML  
  
 Questo errore in Azure Machine Learning si verifica quando il pacchetto zip del modulo personalizzato non contiene file di definizione del modulo (con estensione XML). Questi file devono trovarsi nella radice del pacchetto zip, ad esempio, non all'interno di una sottocartella.  
  
**Risoluzione:** Verificare che uno o più file di definizione del modulo XML si trovino nella cartella radice del pacchetto zip eseguendone l'estrazione in una cartella temporanea nell'unità disco. Tutti i file XML devono trovarsi direttamente nella cartella in cui è stato estratto il pacchetto zip. Assicurarsi che quando si crea il pacchetto zip non si selezioni una cartella che contiene i file XML da comprimere, in quanto verrà creata una sottocartella all'interno del pacchetto zip con lo stesso nome della cartella selezionata per la compressione.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il file ZIP specificato non contiene alcun file di definizione del modulo (file con estensione XML)|  


## <a name="error-0104"></a>Errore 0104  
 Generata quando un file di definizione del modulo fa riferimento a uno script che non può essere individuato  
  
 Questo errore in Azure Machine Learning viene generato quando un file di definizione XML del modulo personalizzato fa riferimento a un file di script nell'elemento di **linguaggio** che non esiste nel pacchetto zip. Il percorso del file di script è definito nella proprietà **SourceFile** dell'elemento **Language** . Il percorso del file di origine è relativo alla radice del pacchetto zip (la stessa posizione dei file di definizione XML del modulo). Se il file di script si trova in una sottocartella, è necessario specificare il percorso relativo del file di script. Ad esempio, se tutti gli script sono stati archiviati in una cartella di **script** all'interno del pacchetto zip, l'elemento del **linguaggio** deve aggiungere questo percorso alla proprietà **SourceFile** come indicato di seguito. ad esempio:  
  
 `<Language name="R" sourceFile="myScripts/CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Risoluzione:** Verificare che il valore della proprietà **SourceFile** nell'elemento **Language** della definizione XML del modulo personalizzato sia corretto e che il file di origine esista nel percorso relativo corretto nel pacchetto zip.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il file di script R a cui si fa riferimento non esiste.|  
|Impossibile trovare il file di script R ' {0}' a cui si fa riferimento. Verificare che il percorso relativo del file sia corretto dal percorso delle definizioni.|  


## <a name="error-0105"></a>Errore 0105  
 Questo errore viene visualizzato quando un file di definizione del modulo contiene un tipo di parametro non supportato  
  
 Questo errore in Azure Machine Learning viene generato quando si crea una definizione XML del modulo personalizzato e il tipo di un parametro o di un argomento nella definizione non corrisponde a un tipo supportato.  
  
**Risoluzione:** Verificare che la proprietà Type di qualsiasi elemento **arg** nel file di definizione XML del modulo personalizzato sia un tipo supportato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tipo di parametro non supportato.|  
|Il tipo di parametro ' {0}' specificato non è supportato.|  


## <a name="error-0106"></a>Errore 0106  
 Generata quando un file di definizione del modulo definisce un tipo di input non supportato  
  
 Questo errore in Azure Machine Learning viene generato quando il tipo di una porta di input in una definizione XML del modulo personalizzato non corrisponde a un tipo supportato.  
  
**Risoluzione:** Verificare che la proprietà Type di un elemento input nel file di definizione XML del modulo personalizzato sia un tipo supportato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tipo di input non supportato.|  
|Il tipo di input ' {0}' specificato non è supportato.|  


## <a name="error-0107"></a>Errore 0107  
 Generata quando un file di definizione del modulo definisce un tipo di output non supportato  
  
 Questo errore in Azure Machine Learning viene generato quando il tipo di una porta di output in una definizione XML del modulo personalizzato non corrisponde a un tipo supportato.  
  
**Risoluzione:** Verificare che la proprietà Type di un elemento output nel file di definizione XML del modulo personalizzato sia un tipo supportato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tipo di output non supportato.|  
|Il tipo di output ' {0}' specificato non è supportato.|  


## <a name="error-0108"></a>Errore 0108  
 Generata quando un file di definizione del modulo definisce più porte di input o di output rispetto a quelle supportate  
  
 Questo errore in Azure Machine Learning viene generato quando vengono definite troppe porte di input o output in una definizione XML del modulo personalizzato.  
  
**Risoluzione:** Garantisce che il numero massimo di porte di input e di output definite nella definizione XML del modulo personalizzato non superi il numero massimo di porte supportate.  
  
|Messaggi di eccezione|  
|------------------------|  
|È stato superato il numero di porte di input o di output supportato.|  
|È stato superato il numero di porte ' {0}' supportate. Il numero massimo consentito di porte ' {0}' è' {1}'.| 

## <a name="error-0109"></a>Errore 0109  
 Generata quando un file di definizione del modulo definisce un selettore di colonna in modo errato  
  
 Questo errore in Azure Machine Learning viene generato quando la sintassi per un argomento di selezione colonne contiene un errore in una definizione XML del modulo personalizzato.  
  
**Risoluzione:** Questo errore viene generato quando la sintassi per un argomento di selezione colonne contiene un errore in una definizione XML del modulo personalizzato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Sintassi non supportata per il selettore di colonna.|  
  

## <a name="error-0110"></a>Errore 0110  
 Generata quando un file di definizione del modulo definisce un selettore di colonna che fa riferimento a un ID porta di input non esistente  
  
 Questo errore in Azure Machine Learning viene generato quando la proprietà *ID porta* all'interno dell'elemento Properties di un oggetto arg di tipo ColumnPicker non corrisponde al valore ID di una porta di input.  
  
**Risoluzione:** Verificare che la proprietà ID porta corrisponda al valore ID di una porta di input definita nella definizione XML del modulo personalizzato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Selezione colonne fa riferimento a un ID porta di input non esistente.|  
|Il selettore di colonna fa riferimento a un ID porta di input non esistente ' {0}'.|  
  

## <a name="error-0111"></a>Errore 0111  
 Generata quando un file di definizione del modulo definisce una proprietà non valida  
  
 Questo errore in Azure Machine Learning viene generato quando una proprietà non valida viene assegnata a un elemento nella definizione XML del modulo personalizzato.  
  
**Risoluzione:** Verificare che la proprietà sia supportata dall'elemento modulo personalizzato.  
  
|Messaggi di eccezione|  
|------------------------|  
|La definizione della proprietà non è valida.|  
|La definizione di proprietà' {0}' non è valida.|  
  

## <a name="error-0112"></a>Errore 0112  
 Generata quando non è possibile analizzare un file di definizione del modulo  
  
 Questo errore in Azure Machine Learning viene generato quando si verifica un errore nel formato XML che impedisce che la definizione XML del modulo personalizzato venga analizzata come un file XML valido.  
  
**Risoluzione:** Verificare che ogni elemento venga aperto e chiuso correttamente. Verificare che non siano presenti errori nella formattazione XML.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è possibile analizzare il file di definizione del modulo.|  
|Non è possibile analizzare il file di definizione del modulo ' {0}'.|  
  

## <a name="error-0113"></a>Errore 0113  
 Generata quando un file di definizione del modulo contiene errori.  
  
 Questo errore in Azure Machine Learning viene generato quando il file di definizione XML del modulo personalizzato può essere analizzato, ma contiene errori, ad esempio la definizione di elementi non supportati dai moduli personalizzati.  
  
**Risoluzione:** Assicurarsi che il file di definizione del modulo personalizzato definisca gli elementi e le proprietà supportati dai moduli personalizzati.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il file di definizione del modulo contiene errori.|  
|Il file di definizione del modulo ' {0}' contiene errori.|  
|Il file di definizione del modulo ' {0}' contiene errori. {1}|  
  

## <a name="error-0114"></a>Errore 0114  
 Generata quando si verifica un errore nella compilazione di un modulo personalizzato.  
  
 Questo errore in Azure Machine Learning viene generato quando la compilazione di un modulo personalizzato ha esito negativo. Questo errore si verifica quando vengono rilevati uno o più errori correlati ai moduli personalizzati durante l'aggiunta del modulo personalizzato. Gli errori aggiuntivi vengono segnalati in questo messaggio di errore.  
  
**Risoluzione:** Risolvere gli errori segnalati in questo messaggio di eccezione.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è stato possibile compilare il modulo personalizzato.|  
|Le compilazioni di moduli personalizzati non sono riuscite con errori: {0}|  
  

## <a name="error-0115"></a>Errore 0115  
 Generata quando uno script predefinito del modulo personalizzato ha un'estensione non supportata.  
  
 Questo errore in Azure Machine Learning si verifica quando si fornisce uno script per un modulo personalizzato che usa un'estensione di file sconosciuta.  
  
**Risoluzione:** Verificare il formato del file e l'estensione del nome file di tutti i file di script inclusi nel modulo personalizzato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Extent non supportato per lo script predefinito.|  
|Estensione di file non supportata {0} per lo script predefinito.|  
  

## <a name="error-0121"></a>Errore 0121  
 Generata quando le scritture SQL non riescono perché la tabella non è scrivibile  
  
 Questo errore in Azure Machine Learning viene generato quando si usa il modulo [Export Data](export-data.md) per salvare i risultati in una tabella di un database SQL e non è possibile scrivere la tabella in. Questo errore viene in genere visualizzato se il modulo [Export Data](export-data.md) stabilisce correttamente una connessione con l'istanza di SQL Server, ma non è in grado di scrivere il contenuto del set di dati di Azure ml nella tabella.  
  
**Risoluzione:**
 - Aprire il riquadro proprietà del modulo [Esporta dati](export-data.md) e verificare che i nomi di database e di tabella siano stati immessi correttamente. 
 - Esaminare lo schema del set di dati che si sta esportando e assicurarsi che i dati siano compatibili con la tabella di destinazione.
 - Verificare che l'accesso SQL associato al nome utente e alla password disponga delle autorizzazioni per la scrittura nella tabella. 
 - Se l'eccezione contiene informazioni aggiuntive sull'errore provenienti da SQL Server, utilizzare tali informazioni per apportare correzioni.  
  
|Messaggi di eccezione|  
|------------------------|  
|Connessione al server: Impossibile scrivere nella tabella.|  
|Impossibile scrivere nella tabella SQL: {0}|  


## <a name="error-0122"></a>Errore 0122  
 Si verifica un'eccezione se sono specificate più colonne di ponderazione e ne è consentito solo uno.  
  
 Questo errore in Azure Machine Learning si verifica quando sono state selezionate troppe colonne come colonne ponderate.  
  
**Risoluzione:** Esaminare il set di dati di input e i relativi metadati. Verificare che solo una colonna contenga pesi.  
  
|Messaggi di eccezione|  
|------------------------|  
|Sono state specificate più colonne di ponderazione.|  


## <a name="error-0123"></a>Errore 0123  
 Si verifica un'eccezione se la colonna di vettori è specificata per l'etichetta della colonna.  
  
 Questo errore in Azure Machine Learning si verifica se si usa un vettore come colonna di etichetta.  
  
**Risoluzione:** Modificare il formato dati della colonna, se necessario, oppure scegliere una colonna diversa.  
  
|Messaggi di eccezione|  
|------------------------|  
|La colonna di vettori viene specificata come colonna di etichetta.|  


## <a name="error-0124"></a>Errore 0124  
 Si verifica un'eccezione se vengono specificate colonne non numeriche come colonna di ponderazione.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|La colonna non numerica viene specificata come colonna di ponderazione.|  
  


## <a name="error-0125"></a>Errore 0125  
 Generata quando lo schema per più set di impostazioni non corrisponde.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|Lo schema del set di dati non corrisponde.|  


## <a name="error-0126"></a>Errore 0126  
 Si verifica un'eccezione se l'utente specifica un dominio SQL non supportato in Azure ML.  
  
 Questo errore viene generato quando l'utente specifica un dominio SQL non supportato in Azure Machine Learning. Questo errore viene visualizzato se si tenta di connettersi a un server di database in un dominio non incluso nell'elenco elementi consentiti. Attualmente, i domini SQL consentiti sono: ". database.windows.net", ". cloudapp.net" o ". database.secure.windows.net". Ovvero, il server deve essere un server di Azure SQL o un server in una macchina virtuale in Azure.  
  
**Risoluzione:** Rivedere il modulo. Verificare che il server di database SQL appartenga a uno dei domini accettati:  
  
-   . database.windows.net  
  
-   . cloudapp.net  
  
-   . database.secure.windows.net  
  
|Messaggi di eccezione|  
|------------------------|  
|Dominio SQL non supportato.|  
|Il {0} del dominio SQL non è attualmente supportato in Azure ML|  
  

## <a name="error-0127"></a>Errore 0127  
 Le dimensioni in pixel dell'immagine superano il limite consentito  
  
 Questo errore si verifica se si leggono immagini da un set di dati dell'immagine per la classificazione e le immagini sono più grandi di quelle che possono essere gestite dal modello.  
  
 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  
  
-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->
  
|Messaggi di eccezione|  
|------------------------|  
|Le dimensioni in pixel dell'immagine superano il limite consentito.|  
|Le dimensioni in pixel dell'immagine nel file ' {0}' superano il limite consentito:' {1}'|  


## <a name="error-0128"></a>Errore 0128  
 Il numero di probabilità condizionali per le colonne categoriche supera il limite.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|Il numero di probabilità condizionali per le colonne categoriche supera il limite.|  
|Il numero di probabilità condizionali per le colonne categoriche supera il limite. Le colonne ' {0}' è {1}' sono la coppia problematica.|  


## <a name="error-0129"></a>Errore 0129  
 Il numero di colonne nel set di dati supera il limite consentito.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|Il numero di colonne nel set di dati supera il limite consentito.|  
|Il numero di colonne nel set di dati in ' {0}' è superiore a quello consentito .'|  
|Il numero di colonne nel set di dati in ' {0}' supera il limite consentito di ' {1}' .'|  
|Il numero di colonne nel set di dati in ' {0}' supera il limite ' {1}' consentito di ' {2}'.|  
## <a name="error-0130"></a>Errore 0130  
 Si verifica un'eccezione quando tutte le righe del set di dati di training contengono valori mancanti.  
  
 Questo errore si verifica quando una colonna del set di dati di training è vuota.  
  
**Risoluzione:** Usare il modulo [Clean Missing data](clean-missing-data.md) per rimuovere le colonne con tutti i valori mancanti.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tutte le righe nel set di dati di training contengono valori mancanti.  Provare a usare il modulo clean Missing data per rimuovere i valori mancanti.|  
 

## <a name="error-0131"></a>Errore 0131  
 Si verifica un'eccezione se non è possibile decomprimere e registrare correttamente uno o più set di impostazioni in un file zip  
  
 Questo errore viene generato quando non è possibile decomprimere e leggere correttamente uno o più set di impostazioni in un file zip. Questo errore viene visualizzato se l'operazione di decompressione ha esito negativo perché il file zip o uno dei file in esso contenuti è danneggiato o si è verificato un errore di sistema durante il tentativo di decomprimere ed espandere un file.  
  
**Risoluzione:** Usare i dettagli forniti nel messaggio di errore per determinare il modo in cui procedere.  
  
|Messaggi di eccezione|  
|------------------------|  
|I set di impostazioni di caricamento compressi non sono riusciti|  
|Il set di dati compresso {0} non è riuscito con il messaggio seguente: {1}|  
|Il set di dati compresso {0} non è riuscito con un'eccezione {1} con messaggio: {2}|  
  

## <a name="error-0132"></a>Errore 0132  
 Nessun nome di file specificato per la decompressione. sono stati trovati più file nel file zip.  
  
 Questo errore viene generato quando non è stato specificato alcun nome file per la decompressione. sono stati trovati più file nel file zip. Questo errore viene visualizzato se il file con estensione zip contiene più di un file compresso, ma non è stato specificato un file da estrarre nella casella di testo **set di dati da decomprimere** , nel riquadro delle **Proprietà** del modulo. Attualmente, è possibile estrarre un solo file ogni volta che viene eseguito il modulo.  
  
**Risoluzione:** Il messaggio di errore fornisce un elenco dei file trovati nel file con estensione zip. Copiare il nome del file desiderato e incollarlo nella casella **di testo set di dati da decomprimere** .  
  
|Messaggi di eccezione|  
|------------------------|  
|Il file zip contiene più file; è necessario specificare il file da espandere.|  
|Il file contiene più di un file. Specificare il file da espandere. Sono stati trovati i file seguenti: {0}|  
  

## <a name="error-0133"></a>Errore 0133  
 Il file specificato non è stato trovato nel file zip  
  
 Questo errore viene generato quando il nome file immesso nel **set di dati per decomprimere** il campo del riquadro **Proprietà** non corrisponde al nome di un file trovato nel file con estensione zip. Le cause più comuni di questo errore sono un errore di digitazione o la ricerca nel file di archivio errato del file da espandere.  
  
**Risoluzione:** Rivedere il modulo. Se il nome del file che si intende decomprimere viene visualizzato nell'elenco dei file trovati, copiare il nome del file e incollarlo nella casella **set di dati per** decomprimere la proprietà. Se non viene visualizzato il nome del file desiderato nell'elenco, verificare di avere il file con estensione zip corretto e il nome corretto per il file desiderato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il file specificato non è stato trovato nel file con estensione zip.|  
|Il file specificato non è stato trovato. Sono stati trovati i file seguenti: {0}|  
  

## <a name="error-0134"></a>Errore 0134
Si verifica un'eccezione quando la colonna dell'etichetta è mancante o ha un numero insufficiente di righe con etichetta.  
  
Questo errore si verifica quando il modulo richiede una colonna di etichetta, ma non ne è stata inclusa una nella selezione delle colonne oppure nella colonna dell'etichetta mancano troppi valori.

Questo errore può verificarsi anche quando un'operazione precedente modifica il set di dati in modo che le righe non sufficienti siano disponibili per un'operazione downstream. Si supponga, ad esempio, di usare un'espressione nel modulo **Partition and Sample** per dividere un set di dati in base ai valori. Se non vengono trovate corrispondenze per l'espressione, uno dei set di impostazioni risultante dalla partizione sarà vuoto.

Risoluzione: 

 Se si include una colonna etichetta nella selezione colonna ma non è riconosciuta, utilizzare il modulo [Modifica metadati](edit-metadata.md) per contrassegnarla come colonna etichetta.
  
  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->Quindi, è possibile usare il modulo [Clean Missing data](clean-missing-data.md) per rimuovere le righe con valori mancanti nella colonna Label. 

 Controllare i set di dati di input per assicurarsi che contengano dati validi e un numero sufficiente di righe per soddisfare i requisiti dell'operazione. Molti algoritmi genereranno un messaggio di errore se richiedono un numero minimo di righe di dati, ma i dati contengono solo poche righe o solo un'intestazione.
  
|Messaggi di eccezione|
|------------------------|
|Si verifica un'eccezione quando la colonna dell'etichetta è mancante o ha un numero insufficiente di righe con etichetta.|  
|Si verifica un'eccezione quando la colonna dell'etichetta è mancante o è minore di {0} righe con etichetta|  
  

## <a name="error-0135"></a>Errore 0135  
 È supportato solo il cluster basato su baricentro.  
  
**Risoluzione:** Questo messaggio di errore può essere visualizzato se si è provato a valutare un modello di clustering basato su un algoritmo di clustering personalizzato che non utilizza centroidi per inizializzare il cluster.  
  
  <!--You can use [Evaluate Model](evaluate-model.md) to evaluate clustering models that are based on the  [K-Means Clustering](k-means-clustering.md) module. For custom algorithms, use the [Execute R Script](execute-r-script.md) module to create a custom evaluation script.  -->
  
|Messaggi di eccezione|  
|------------------------|  
|È supportato solo il cluster basato su baricentro.|  
  

## <a name="error-0136"></a>Errore 0136  
 Non è stato restituito alcun nome file; non è possibile elaborare il file di conseguenza.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|Non è stato restituito alcun nome file; non è possibile elaborare il file di conseguenza.|  
  

## <a name="error-0137"></a>Errore 0137  
 Azure Storage SDK ha rilevato un errore durante la conversione tra proprietà della tabella e colonne del set di dati durante la lettura o la scrittura.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|Errore di conversione tra la proprietà di archiviazione tabelle di Azure e la colonna DataSet.|  
|Errore di conversione tra la proprietà di archiviazione tabelle di Azure e la colonna DataSet. Ulteriori informazioni: {0}|  

## <a name="error-0138"></a>Errore 0138  
 La memoria è stata esaurita, non è possibile completare l'esecuzione del modulo. Sottocampionando il set di dati può aiutare a risolvere il problema.  
  
 Questo errore si verifica quando il modulo che esegue richiede una quantità di memoria superiore a quella disponibile nel contenitore di Azure. Questa situazione può verificarsi se si utilizza un set di dati di grandi dimensioni e l'operazione corrente non può rientrare nella memoria.  
  
**Risoluzione:** Se si sta tentando di leggere un set di dati di grandi dimensioni e non è possibile completare l'operazione, sottocampionando il set di dati potrebbe essere utile.  
  
  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  
  
 Sometimes transient loads can lead to this error. Machine support also changes over time. 
  
 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->
  
|Messaggi di eccezione|  
|------------------------|  
|La memoria è stata esaurita, non è possibile completare l'esecuzione del modulo.|  
  

## <a name="error-0139"></a>Errore 0139  
 Si verifica un'eccezione quando non è possibile convertire una colonna in un altro tipo.  
  
 Questo errore in Azure Machine Learning si verifica quando si tenta di convertire una colonna in un tipo di dati diverso, ma tale tipo non è supportato dall'operazione corrente o dal modulo.  
  
 L'errore potrebbe essere visualizzato anche quando un modulo tenta di convertire in modo implicito i dati per soddisfare i requisiti del modulo corrente, ma la conversione non è possibile.  
  
**Risoluzione:**

1. Esaminare i dati di input e determinare il tipo di dati esatto della colonna che si desidera utilizzare e il tipo di dati della colonna che genera l'errore. Talvolta si potrebbe pensare che il tipo di dati sia corretto, ma che un'operazione upstream abbia modificato il tipo di dati o l'utilizzo di una colonna. Usare il modulo [Modifica metadati](edit-metadata.md) per ripristinare lo stato originale dei metadati della colonna. 
2. Esaminare la pagina della guida del modulo per verificare i requisiti per l'operazione specificata. Determinare quali tipi di dati sono supportati dal modulo corrente e quale intervallo di valori è supportato. 
 <!--3. If values need to be truncated, rounded, or outliers removed, use the [Apply Math Operation](apply-math-operation.md) or [Clip Values](clip-values.md) modules to make corrections.-->
4. Valutare se è possibile convertire o eseguire il cast della colonna in un tipo di dati diverso. I moduli seguenti forniscono tutti una notevole flessibilità e potenza per la modifica dei dati: 
 <!--
   + [Apply SQL Transformation](apply-sql-transformation.md)
   + [Execute R Script](execute-r-script.md)
-->   
   + [Eseguire lo script Python](execute-python-script.md).  

> [!NOTE]
> Ancora non funziona? Valutare la possibilità di fornire commenti e suggerimenti sul problema per contribuire a sviluppare informazioni aggiuntive per la risoluzione dei problemi. È sufficiente inviare commenti e suggerimenti in questa pagina e specificare il nome del modulo che ha generato l'errore e la conversione del tipo di dati che ha avuto esito negativo.
  
|Messaggi di eccezione|  
|------------------------|  
|Conversione non consentita.|  
|Non è stato possibile convertire: {0}.|  
|Impossibile convertire: {0}, {1} di riga.|  
|Non è stato possibile convertire la colonna di tipo {0} nella colonna di tipo {1} all'{2} di riga.|  
|Non è stato possibile convertire la colonna "{2}" di tipo {0} in una colonna di tipo {1} nella {3} di riga.|  
|Impossibile convertire la colonna "{2}" di tipo {0} nella colonna "{3}" di tipo {1} all'{4} di riga.| 

## <a name="error-0140"></a>Errore 0140  
 Si verifica un'eccezione se l'argomento set di colonne passato non contiene altre colonne, ad eccezione della colonna Label.  
  
 Questo errore si verifica se è stato connesso un set di dati a un modulo che richiede più colonne, incluse le funzionalità, ma è stata specificata solo la colonna Label.  
  
**Risoluzione:** Scegliere almeno una colonna della funzionalità da includere nel set di dati.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il set di colonne specificato non contiene altre colonne, ad eccezione della colonna Label.|  
  

## <a name="error-0141"></a>Errore 0141  
 Si verifica un'eccezione se il numero di colonne numeriche selezionate e di valori univoci nelle colonne categoriche e stringa è troppo piccolo.  
  
 Questo errore in Azure Machine Learning si verifica quando nella colonna selezionata non sono presenti valori univoci sufficienti per eseguire l'operazione.  
  
**Risoluzione:** Alcune operazioni eseguono operazioni statistiche sulle colonne funzionalità e categoriche. se non sono disponibili valori sufficienti, l'operazione potrebbe non riuscire o restituire un risultato non valido. Controllare il set di dati per visualizzare il numero di valori presenti nelle colonne feature e Label e determinare se l'operazione che si sta tentando di eseguire sia statisticamente valida.  
  
 Se il set di dati di origine è valido, è anche possibile controllare se alcune operazioni di manipolazione dei dati o metadati upstream hanno modificato i dati e rimosso alcuni valori.  
  
 Se le operazioni upstream includono la suddivisione, il campionamento o il ricampionamento, verificare che gli output contengano il numero previsto di righe e valori.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il numero delle colonne numeriche selezionate e i valori univoci nelle colonne categoriche e stringa sono troppo piccoli.|  
|Il numero totale delle colonne numeriche selezionate e i valori univoci nelle colonne categoriche e stringa (attualmente {0}) devono essere almeno {1}|  
  

## <a name="error-0142"></a>Errore 0142  
 Si verifica un'eccezione quando il sistema non è in grado di caricare il certificato per l'autenticazione.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|Non è possibile caricare il certificato.|  
|Non è possibile caricare il certificato {0}. L'identificazione personale è {1}.|  
  

## <a name="error-0143"></a>Errore 0143  
 Non è possibile analizzare l'URL fornito dall'utente che deve essere da GitHub.  
  
 Questo errore in Azure Machine Learning si verifica quando si specifica un URL non valido e il modulo richiede un URL GitHub valido.  
  
**Risoluzione:** Verificare che l'URL faccia riferimento a un repository GitHub valido. Altri tipi di sito non sono supportati.  
  
|Messaggi di eccezione|  
|------------------------|  
|L'URL non è di github.com.|  
|L'URL non è da github.com: {0}|  

## <a name="error-0144"></a>Errore 0144  
 Nell'URL di GitHub fornito dall'utente manca la parte prevista.  
  
 Questo errore in Azure Machine Learning si verifica quando si specifica un'origine file GitHub usando un formato di URL non valido.  
  
**Risoluzione:** Verificare che l'URL del repository GitHub sia valido e termini con \blob\ o \tree \\.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è possibile analizzare l'URL di GitHub.|  
|Non è possibile analizzare l'URL di GitHub (previsto ' \blob \\' o ' \tree \\' dopo il nome del repository): {0}|  

## <a name="error-0145"></a>Errore 0145  
 Non è possibile creare la directory di replica per qualche motivo.  
  
 Questo errore in Azure Machine Learning si verifica quando il modulo non è in grado di creare la directory specificata.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|Impossibile creare la directory di replica.|  
  

## <a name="error-0146"></a>Errore 0146  
 Quando i file utente vengono decompressi nella directory locale, il percorso combinato potrebbe essere troppo lungo.  
  
 Questo errore in Azure Machine Learning si verifica quando si estraggono i file ma alcuni nomi di file sono troppo lunghi quando vengono decompressi.  
  
**Risoluzione:** Modificare i nomi dei file in modo che il percorso combinato e il nome file non superino i 248 caratteri.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il percorso di replica è più lungo di 248 caratteri, abbreviare il nome o il percorso dello script.|  

## <a name="error-0147"></a>Errore 0147  
 Non è stato possibile scaricare i contenuti da GitHub per qualche motivo  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile leggere o scaricare i file specificati da GitHub.  
  
**Risoluzione:** Il problema potrebbe essere temporaneo. è possibile provare ad accedere ai file in un altro momento. In alternativa, verificare di disporre delle autorizzazioni necessarie e che l'origine sia valida.  
  
|Messaggi di eccezione|  
|------------------------|  
|Errore di accesso a GitHub.|  
|Errore di accesso a GitHub. {0}|  
  

## <a name="error-0148"></a>Errore 0148  
 Problemi di accesso non autorizzati durante l'estrazione dei dati o la creazione della directory.  
  
 Questo errore in Azure Machine Learning si verifica quando si tenta di creare una directory o leggere i dati dalla risorsa di archiviazione, ma non si dispone delle autorizzazioni necessarie.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|Eccezione di accesso non autorizzato durante l'estrazione dei dati.|  
  

## <a name="error-0149"></a>Errore 0149  
 Il file utente non esiste all'interno del bundle GitHub.  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile trovare il file specificato.  
  
Risoluzione: 
  
|Messaggi di eccezione|  
|------------------------|  
|Il file GitHub non è stato trovato.|  
|Il file GitHub non è stato trovato.: {0}|  
  

## <a name="error-0150"></a>Errore 0150  
 Non è stato possibile decomprimere gli script provenienti dal pacchetto utente, probabilmente a causa di un conflitto con i file GitHub.  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile estrarre uno script, in genere quando esiste già un file con lo stesso nome.  
  
Risoluzione:
  
|Messaggi di eccezione|  
|------------------------|  
|Non è possibile decomprimere il bundle; possibile conflitto di nomi con i file GitHub.|  
  

## <a name="error-0151"></a>Errore 0151  
 Si è verificato un errore durante la scrittura nell'archiviazione cloud. Controllare l'URL.  
  
 Questo errore in Azure Machine Learning si verifica quando il modulo tenta di scrivere i dati nell'archiviazione cloud, ma l'URL non è disponibile o non è valido.  
  
Soluzione: controllare l'URL e verificare che sia scrivibile.  
  
|Messaggi di eccezione|  
|------------------------|  
|Errore durante la scrittura nell'archiviazione cloud (probabilmente un URL non valido).|  
|Errore durante la scrittura nell'archiviazione cloud: {0}. Controllare l'URL.|  
  
## <a name="error-0152"></a>Errore 0152  
 Il tipo di cloud di Azure è stato specificato in modo errato nel contesto del modulo.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tipo di cloud di Azure non valido|  
|Tipo di cloud di Azure non valido: {0}|  
  
## <a name="error-0153"></a>Errore 0153  
 L'endpoint di archiviazione specificato non è valido.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tipo di cloud di Azure non valido|  
|Endpoint di archiviazione non valido: {0}|  

## <a name="error-0154"></a>Errore 0154  
 Non è stato possibile risolvere il nome del server specificato  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è stato possibile risolvere il nome del server specificato|  
|Impossibile risolvere il server specificato {0}. documents.azure.com|

## <a name="error-0155"></a>Errore 0155  
 Il client DocDb ha generato un'eccezione  
  
|Messaggi di eccezione|  
|------------------------|  
|Il client DocDb ha generato un'eccezione|  
|Client DocDb: {0}|

## <a name="error-0156"></a>Errore 0156  
 Risposta non valida per il server HCatalog.  
  
|Messaggi di eccezione|  
|------------------------|  
|Risposta non valida per il server HCatalog. Verificare che tutti i servizi siano in esecuzione.|  
|Risposta non valida per il server HCatalog. Verificare che tutti i servizi siano in esecuzione. Dettagli errore: {0}|

## <a name="error-0157"></a>Errore 0157  
 Si è verificato un errore durante la lettura da Azure Cosmos DB a causa di schemi di documento non coerenti o diversi. Il lettore richiede che tutti i documenti abbiano lo stesso schema.  
  
|Messaggi di eccezione|  
|------------------------|  
|Sono stati rilevati documenti con schemi diversi. Verificare che tutti i documenti abbiano lo stesso schema|

## <a name="error-1000"></a>Errore 1000  
Eccezione interna della libreria.  
  
Questo errore viene fornito per acquisire errori interni del motore altrimenti non gestiti. La cause di questo errore potrebbe quindi variare a seconda del modulo che ha generato l'errore.  
  
Per ottenere ulteriore assistenza, è consigliabile pubblicare il messaggio dettagliato che accompagna l'errore nel forum di Azure Machine Learning, insieme a una descrizione dello scenario, inclusi i dati utilizzati come input. Questo feedback ci aiuterà a classificare in ordine di priorità gli errori e a identificare i problemi più importanti per un ulteriore lavoro.  
  
|Messaggi di eccezione|  
|------------------------|  
|Eccezione della libreria.|  
|Eccezione libreria: {0}|  
|{0} eccezione libreria: {1}|  
