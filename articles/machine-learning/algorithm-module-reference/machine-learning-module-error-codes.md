---
title: Risolvere gli errori del modulo
titleSuffix: Azure Machine Learning service
description: Risolvere i problemi delle eccezioni di modulo in Azure Machine Learning Studio usando i codici di errore
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09a2b616e2bba93be86241c64d37daec7d6dea3b
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029761"
---
# <a name="exceptions-and-error-codes-for-algorithm--module-reference"></a>Le eccezioni e codici di errore per l'algoritmo di & riferimento al modulo

Informazioni sui messaggi di errore e i codici di eccezione che potrebbero verificarsi usando i moduli in Azure Machine Learning Studio. 

Per risolvere il problema, cercare l'errore in questo articolo per conoscere le cause più comuni. Esistono due modi per ottenere il testo completo di un messaggio di errore in Studio:  
 
- Fare clic sul collegamento **View Output Log**, nel riquadro destro, quindi scorrere fino alla fine. Viene visualizzato il messaggio di errore dettagliati nelle ultime due righe della finestra.  
  
- Selezionare il modulo che contiene l'errore e fare clic sulla X rossa. Viene visualizzato solo il testo di errore pertinenti.  
  
Se il testo del messaggio non è utile, è possibile inviare informazioni sul contesto e qualsiasi desiderate aggiunte o modifiche. È possibile inviare commenti e suggerimenti sull'argomento errore o visitare il [forum di Azure Machine Learning STUDIO](https://aka.ms/aml-forum-studio) e pubblicare una domanda.  


## <a name="error-0001"></a>Errore 0001  
 Si verifica un'eccezione se non è stato possibile trovare uno o più colonne specificate del set di dati.  
  
 Si riceverà questo errore se viene effettuata una selezione di colonna per un modulo, ma le colonne selezionate non sono presenti nel set di dati di input. Questo errore può verificarsi se è stato immesso manualmente in un nome di colonna o se il selettore di colonna ha fornito una colonna suggerita che non esiste nel set di dati durante l'esecuzione dell'esperimento.  
  
**Risoluzione:** Rivedere il modulo che genera questa eccezione e verificare che il nome della colonna o i nomi siano corretti e che siano presenti tutte le colonne di cui viene fatto riferimento.  
  
|Messaggi di eccezione|  
|------------------------|  
|Uno o più colonne specificate non sono state trovate|  
|Colonna con nome o l'indice "{0}" non trovato|  
|Colonna con nome o l'indice "{0}"non esiste "{1}"|  
 

## <a name="error-0002"></a>Errore 0002  
 Si verifica un'eccezione se uno o più parametri non era possibile analizzare o convertire dal tipo specificato in richiesto dal tipo di metodo di destinazione.  
  
 Questo errore si verifica in Azure Machine Learning quando si specifica un parametro come input e il valore è diverso da quello previsto e non è possibile eseguire la conversione implicita.  
  
**Risoluzione:** Verificare i requisiti di modulo e determinare quale tipo di valore è obbligatorio (string, integer, double e così via.)  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è stato possibile analizzare il parametro|  
|Non è stato possibile analizzare "{0}" parametro|  
|Non è stato possibile analizzare (Converti) "{0}"parametro"{1}"|  
|Impossibile convertire "{0}"parametro da"{1}"a"{2}"|  
|Impossibile convertire "{0}"valore del parametro"{1}"da"{2}"a"{3}"|  
|Non è riuscito a convertire il valore "{0}"nella colonna"{1}"da"{2}"a"{3}"con l'utilizzo del formato"{4}" fornito|  
  

## <a name="error-0003"></a>Errore 0003  
 Si verifica un'eccezione se uno o più input sono null o vuoto.  
  
 Si riceverà questo errore in Azure Machine Learning se alcun input o i parametri a un modulo sono null o vuoto.  Questo errore potrebbe verificarsi, ad esempio, quando non è stato digitato in tutti i valori per un parametro. Può inoltre verificarsi se si sceglie un dataset che contiene i valori mancanti, o un dataset vuoto.  
  
**Risoluzione:**
 
+ Aprire il modulo che ha generato l'eccezione e verificare che tutti gli input sono stati specificati. Verificare che tutti gli input sono specificati richieste. 
+ Assicurarsi che i dati caricati dall'archiviazione di Azure sono accessibili e che il nome di account o la chiave non è stato modificato.  
+ Controllare i dati di input per i valori mancanti o i valori null.
+ Se si usa una query su un'origine dati, verificare che i dati viene restituiti nel formato che previsto. 
+ Verificare la presenza di errori ortografici o altre modifiche nella specifica dei dati.
  
|Messaggi di eccezione|  
|------------------------|  
|Uno o più input sono null o vuoto|  
|Input "{0}" è null o vuoto|  
  

## <a name="error-0004"></a>Errore 0004  
 Si verifica un'eccezione se il parametro è minore o uguale al valore specifico.  
  
 Si riceverà questo errore in Azure Machine Learning, se il parametro nel messaggio è inferiore al valore limite necessario per il modulo a elaborare i dati.  
  
**Risoluzione:** Rivedere il modulo che genera l'eccezione e modificare il parametro in modo che sia maggiore del valore specificato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Parametro deve essere maggiore del valore limite.|  
|Parametro "{0}" valore deve essere maggiore di {1}.|  
|Parametro "{0}"con valore"{1}" che deve essere maggiore di {2}|  
  


## <a name="error-0005"></a>Errore 0005  
 Si verifica un'eccezione se il parametro è minore rispetto a un valore specifico.  
  
 Riceverà questo errore in Azure Machine Learning, se il parametro nel messaggio è inferiore o uguale al valore limite necessario per il modulo a elaborare i dati.  
  
**Risoluzione:** Rivedere il modulo che genera l'eccezione e modificare il parametro in modo che sia maggiore o uguale al valore specificato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Parametro deve essere maggiore o uguale al valore limite.|  
|Parametro "{0}" valore deve essere maggiore o uguale a {1}.|  
|Parametro "{0}"con valore"{1}" che deve essere maggiore o uguale a {2}.|  
  

## <a name="error-0006"></a>Errore 0006  
 Si verifica un'eccezione se parametro è maggiore o uguale al valore specificato.  
  
 Si riceverà questo errore in Azure Machine Learning, se il parametro nel messaggio è maggiore o uguale al valore limite necessario per il modulo a elaborare i dati.  
  
**Risoluzione:** Rivedere il modulo che genera l'eccezione e modificare il parametro in modo che sia minore del valore specificato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Parametri non corrispondenti. Uno dei parametri deve essere minore di un altro.|  
|Parametro "{0}"il valore deve essere minore del parametro"{1}" valore.|  
|Parametro "{0}"con valore"{1}" che deve essere minore di {2}.|  
  

## <a name="error-0007"></a>Errore 0007  
 Si verifica un'eccezione se parametro è maggiore di un valore specifico.  
  
 Si riceverà questo errore in Azure Machine Learning se, nelle proprietà per il modulo, è stato specificato un valore che è superiore al limite consentito. Ad esempio, è possibile specificare una data che non rientra nell'intervallo di date supportate oppure si potrebbe indicare che cinque colonne usata quando sono disponibili solo tre colonne. 
 
 È possibile visualizzare questo errore se si specifica due set di dati che devono corrispondere in qualche modo. Ad esempio, se si siano rinominando le colonne e specificare le colonne in base all'indice, il numero di nomi forniti deve corrispondere al numero di indici di colonna. Un altro esempio potrebbe essere un'operazione matematica che usa due colonne, in cui le colonne devono avere lo stesso numero di righe. 
  
**Risoluzione:**
 
 + Aprire il modulo in questione ed esaminare le impostazioni delle proprietà numerica.
 + Assicurarsi che i valori dei parametri siano compresi nell'intervallo supportato di valori per tale proprietà.
 + Se il modulo accetta più input, assicurarsi che gli input sono le stesse dimensioni.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Controllare se il set di dati o un'origine dati è stato modificato. In alcuni casi un valore che ha lavorato con una versione precedente dei dati avrà esito negativo dopo il numero di colonne, i tipi di dati di colonna o le dimensioni dei dati sono stato modificato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Parametri non corrispondenti. Uno dei parametri deve essere minore o uguale a un altro.|  
|Parametro "{0}"il valore deve essere minore o uguale al parametro"{1}" valore.|  
|Parametro "{0}"con valore"{1}" che deve essere minore o uguale a {2}.|  
  

## <a name="error-0008"></a>Errore 0008  
 Si verifica un'eccezione se parametro non è compreso nell'intervallo.  
  
 Si riceverà questo errore in Azure Machine Learning, se il parametro nel messaggio è esterno ai limiti necessari per il modulo a elaborare i dati.  
  
 Ad esempio, questo errore viene visualizzato se si prova a usare [Add Rows](add-rows.md) per combinare due set di dati che contengono un numero diverso di colonne.  
  
**Risoluzione:** Rivedere il modulo che genera l'eccezione e modificare il parametro in modo che sia all'interno dell'intervallo specificato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Valore del parametro non è compreso nell'intervallo specificato.|  
|Parametro "{0}" valore non è compreso nell'intervallo.|  
|Parametro "{0}" valore deve essere compreso nell'intervallo [{1}, {2}].|  
  

## <a name="error-0009"></a>Errore 0009  
 Eccezione si verifica quando il nome di account di archiviazione di Azure o il nome del contenitore è stato specificato correttamente.  
  
Questo errore si verifica in Azure Machine Learning Studio quando si specificano i parametri per un account di archiviazione di Azure, ma il nome o la password non può essere risolto. Errori di password o nomi di account possono verificarsi per diversi motivi:
 
 + L'account è il tipo non corretto. Alcuni nuovi tipi di account non sono supportati per l'uso con Machine Learning Studio. Visualizzare [Import Data](import-data.md) per informazioni dettagliate.
 + È stato immesso il nome di account non corretto
 + L'account non esiste più
 + La password dell'account di archiviazione non è corretto o è stato modificato
 + Non è stato specificato il nome del contenitore o il contenitore non esiste
 + È completamente non specificare il percorso del file (percorso del blob)
   
**Risoluzione:**

Spesso tali problemi si verificano quando si tenta di immettere manualmente il percorso dell'account di nome, password o contenitore. È consigliabile usare la nuova procedura guidata per la [Import Data](import-data.md) modulo, che consente di cercare e controllare i nomi.

Controllare inoltre se l'account, contenitore o blob è stato eliminato. Usare un'altra utilità di archiviazione di Azure per verificare che il nome dell'account e la password sia stato immesso correttamente e che il contenitore esiste. 

Alcuni tipi di account più recenti non sono supportati da Azure Machine Learning. Ad esempio, il nuovo "accesso frequente" o i tipi di archiviazione "fredda" non possono essere usati per machine learning. Account di archiviazione classici e gli account di archiviazione creati come "Utilizzo generico" funzionare correttamente.

Se è stato specificato il percorso completo di un blob, verificare che il percorso è specificato come **contenitore/blobname**, e che sia il contenitore e il blob esiste nell'account.  
  
 Il percorso non deve contenere una barra iniziale. Ad esempio **/contenitore/blob** non è corretto e deve essere specificato come **contenitore/blob**.  

  
|Messaggi di eccezione|  
|------------------------|  
|Il nome di account di archiviazione di Azure o il nome del contenitore non è corretto.|  
|Il nome di account di archiviazione di Azure "{0}"o nome del contenitore"{1}" non è corretto; è previsto un nome di contenitore del formato contenitore/blob.|  
  

## <a name="error-0010"></a>Errore 0010  
 Si verifica un'eccezione se i set di dati di input include nomi di colonna che dovrebbero corrispondere ma non lo sono.  
  
 Si riceverà questo errore in Azure Machine Learning, se l'indice di colonna nel messaggio ha nomi di colonna diversi in due set di dati di input.  
  
**Risoluzione:** Uso [Edit Metadata](edit-metadata.md) o modificare il set di dati originale per avere lo stesso nome di colonna per l'indice di colonna specificata.  
  
|Messaggi di eccezione|  
|------------------------|  
|Le colonne con indice corrispondente nei set di dati di input hanno nomi diversi.|  
|I nomi delle colonne non corrispondono per la colonna {0} (in base zero) del set di dati di input ({1} e {2} rispettivamente).|  
  

## <a name="error-0011"></a>Errore 0011  
 Si verifica un'eccezione se viene passato argomento di set di colonne non è applicabile ad alcuna delle colonne di set di dati.  
  
 Si riceverà questo errore in Azure Machine Learning, se la selezione della colonna specificata non corrisponde a una o più colonne in set di dati specificato.  
  
 È anche possibile ottenere questo errore se non è stata selezionata una colonna ed è necessaria almeno una colonna per il funzionamento del modulo.  
  
**Risoluzione:** Modificare la selezione delle colonne nel modulo in modo che venga applicato alle colonne nel set di dati.  
  
 Se il modulo, è necessario selezionare una colonna specifica, ad esempio una colonna di etichetta, verificare che sia selezionata la colonna a destra.  
  
 Se sono selezionate colonne inappropriate, rimuoverli e rieseguire l'esperimento.  
  
|Messaggi di eccezione|  
|------------------------|  
|Set di colonne specificato non è applicabile ad alcuna delle colonne di set di dati.|  
|Set di colonne specificato "{0}" non è applicabile ad alcuna delle colonne di set di dati.|  
  

## <a name="error-0012"></a>Errore 0012  
 Si verifica un'eccezione se non è stato possibile creare l'istanza della classe con set di argomenti passato.  
  
**Risoluzione:** Questo errore non è possibile eseguibile dall'utente e verrà deprecato in una versione futura.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non sottoposto a training del modello, eseguire il training del modello prima di tutto.|  
|Modello senza Training ({0}), usare il modello con training.|  
  

## <a name="error-0013"></a>Errore 0013  
 Si verifica un'eccezione se lo strumento di apprendimento passato al modulo è un tipo non valido.  
  
 Questo errore si verifica ogni volta che un modello con Training non è compatibile con il modulo di assegnazione dei punteggi connesso. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the experiment is run.  -->
  
**Risoluzione:**

Determinare il tipo di strumento di apprendimento viene generato dal modulo di formazione e definire il modulo di punteggio appropriato per lo strumento di apprendimento. 

Se il modello è stato eseguito il training usando uno dei moduli di formazione specializzata, connettere il modello con training solo per il modulo di assegnazione dei punteggi specializzato corrispondente. 


|Tipo di modello|Modulo di formazione| Modulo di punteggio|
|----|----|----|
|qualsiasi funzione di classificazione|[Train Model](train-model.md) |[Score Model](score-model.md)|
|qualsiasi modello di regressione|[Train Model](train-model.md) |[Score Model](score-model.md)|
<!--| I modelli di clustering| [Train Clustering Model](train-clustering-model.md) o [durante lo Sweep di Clustering](sweep-clustering.md)| [Assegna i dati ai cluster](assign-data-to-clusters.md)|
| rilevamento delle anomalie in - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| rilevamento delle anomalie - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Valutare il modello sono necessari alcuni passaggi aggiuntivi. |
| rilevamento delle anomalie - serie temporale|  [Rilevamento delle anomalie delle serie temporali](time-series-anomaly-detection.md) |Modello esegue il training da dati e genera i punteggi. Il modulo non viene creato uno strumento di apprendimento con training e non di assegnazione dei punteggi aggiuntivi è necessaria. |
| modello di raccomandazione| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| classificazione delle immagini | [Pre-addestrati Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Modelli Vowpal Wabbit| [Eseguire il training di Vowpal Wabbit versione 7-4 modello](train-vowpal-wabbit-version-7-4-model.md) | [Assegnare un punteggio Vowpal Wabbit versione 7-4 modello](score-vowpal-wabbit-version-7-4-model.md) |   
|Modelli Vowpal Wabbit| [Eseguire il training di Vowpal Wabbit versione 7-10 modello](train-vowpal-wabbit-version-7-10-model.md) | [Assegnare un punteggio Vowpal Wabbit versione 7-10 modello](score-vowpal-wabbit-version-7-10-model.md) |
|Modelli Vowpal Wabbit| [Eseguire il training del modello di versione 8 Vowpal Wabbit](score-vowpal-wabbit-version-8-model.md) | [Versione 8 di Vowpal Wabbit Score Model](score-vowpal-wabbit-version-8-model.md) |-->
  
|Messaggi di eccezione|  
|------------------------|  
|Strumento di apprendimento di tipo non valido viene passato.|  
|Strumento di apprendimento "{0}" è di tipo valido.|  


## <a name="error-0014"></a>Errore 0014  
 Si verifica un'eccezione se il numero di valori univoci di colonna è superiore a quella consentita.  
  
 Questo errore si verifica quando una colonna contiene troppi valori univoci.  Ad esempio, questo errore può verificarsi se si specifica che una colonna gestita come dati categorici, ma sono presenti troppi valori univoci nella colonna per consentire di completare l'elaborazione. È possibile visualizzare questo errore se è presente una mancata corrispondenza tra il numero di valori univoci nei due input.   
  
**Risoluzione:**

Aprire il modulo che ha generato l'errore e identificare le colonne utilizzate come input. Per alcuni moduli, è possibile fare doppio clic su set di dati di input e selezionare **Visualize** per ottenere le statistiche su singole colonne, incluso il numero di valori univoci e la relativa distribuzione.

Per le colonne che si intendono usare per la categorizzazione o di raggruppamento, intervenire per ridurre il numero di valori univoci nelle colonne. È possibile ridurre in modi diversi, a seconda del tipo di dati della colonna. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Impossibile trovare una soluzione che corrisponde allo scenario? È possibile fornire commenti e suggerimenti su questo argomento che include il nome del modulo che ha generato l'errore e il tipo di dati e la cardinalità della colonna. Si userà le informazioni per fornire più mirate risoluzione dei problemi per gli scenari comuni.   
  
|Messaggi di eccezione|  
|------------------------|  
|Numero di valori univoci della colonna è superiore a quella consentita.|  
|Numero di valori univoci nella colonna: "{0}" supera il numero di tuple di {1}.|  
  

## <a name="error-0015"></a>Errore 0015  
 Si verifica un'eccezione se la connessione del database non è riuscita.  
  
 Si riceverà questo errore se si immette un nome dell'account SQL non corretta, password, server di database o nome del database o se non viene stabilita una connessione con il database a causa di problemi con il database o server.  
  
**Risoluzione:** Verificare che il nome dell'account, password, server di database e database sia stato immesso correttamente e che l'account specificato disponga del livello di autorizzazioni appropriato. Verificare che il database è attualmente accessibile.  
  
|Messaggi di eccezione|  
|------------------------|  
|Errore durante la connessione al database.|  
|Errore durante la connessione al database: {0}.|  
  


## <a name="error-0016"></a>Errore 0016  
 Si verifica un'eccezione se i set di dati input passati al modulo dovrebbero essere tipi di colonne compatibili ma non li supportano.  
  
 Si riceverà questo errore in Azure Machine Learning, se i tipi di colonne passate in due o più set di dati non sono compatibili tra loro.  
  
**Risoluzione:** Uso [Edit Metadata](edit-metadata.md) o modificare il set di dati di input originale<!--, or use [Convert to Dataset](convert-to-dataset.md)--> Per garantire che i tipi delle colonne siano compatibili.  
  
|Messaggi di eccezione|  
|------------------------|  
|Le colonne con indice corrispondente nei set di dati di input hanno tipi incompatibili.|  
|Le colonne {0} e {1} sono incompatibili.|  
|Tipi di elementi non sono compatibili per la colonna {0} (in base zero) del set di dati di input ({1} e {2} rispettivamente).|  
  

## <a name="error-0017"></a>Errore 0017  
 Si verifica un'eccezione se una colonna selezionata utilizza un tipo di dati che non è supportato dal modulo corrente.  
  
 Ad esempio, si potrebbe essere visualizzato questo errore in Azure Machine Learning, se la selezione della colonna è inclusa una colonna con tipo di dati che non può essere elaborato dal modulo, ad esempio una colonna stringa per un'operazione matematica o una colonna di punteggio in cui è una colonna di funzioni categoriche richiesto.  
  
**Risoluzione:**
 1. Identificare la colonna che è il problema.
 2. Esaminare i requisiti del modulo.
 3. Modificare la colonna per renderli conformi ai requisiti. Potrebbe essere necessario usare diversi moduli seguenti per apportare modifiche, a seconda della colonna e la conversione di che si sta tentando:
    + Uso [Edit Metadata](edit-metadata.md) per modificare il tipo di dati delle colonne o per modificare l'utilizzo delle colonne di funzionalità numerico, categorico a non-categoriche, e così via.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Come ultima risorsa, si potrebbe essere necessario modificare il set di dati di input originale.

> [!TIP]
> Impossibile trovare una soluzione che corrisponde allo scenario? È possibile fornire commenti e suggerimenti su questo argomento che include il nome del modulo che ha generato l'errore e il tipo di dati e la cardinalità della colonna. Si userà le informazioni per fornire più mirate risoluzione dei problemi per gli scenari comuni. 
  
|Messaggi di eccezione|  
|------------------------|  
|Non è possibile elaborare la colonna del tipo corrente. Il tipo non è supportato dal modulo.|  
|Non è possibile elaborare la colonna di tipo {0}. Il tipo non è supportato dal modulo.|  
|Non è possibile elaborare la colonna "{1}" di tipo {0}. Il tipo non è supportato dal modulo.|  
|Non è possibile elaborare la colonna "{1}" di tipo {0}. Il tipo non è supportato dal modulo. Nome parametro: {2}|  
  

## <a name="error-0018"></a>Errore 0018  
 Si verifica un'eccezione se i set di dati di input non è valido.  
  
**Risoluzione:** Questo errore in Azure Machine Learning può essere visualizzato in molti contesti, pertanto non c'è non una singola soluzione. In generale, l'errore indica che i dati forniti come input per un modulo hanno un numero errato di colonne o che il tipo di dati non soddisfa i requisiti del modulo. Ad esempio:   
  
-   Il modulo richiede una colonna di etichetta, ma nessuna colonna è contrassegnata come etichetta, o ancora non è stata selezionata una colonna di etichetta.  
  
-   Il modulo richiede che i dati sia categorici ma i dati sono numerici.  
  
<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->
  
-   I dati sono in formato non corretto.  
  
-   I dati importati contengano caratteri non validi, valori errati, o valori fuori intervallo.  
-   La colonna è vuota o contiene troppi valori mancanti.  
  
 Per determinare i requisiti e come i dati potrebbero, rivedere l'argomento della Guida per il modulo che utilizzerà il set di dati come input.  
  
 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  
  
|Messaggi di eccezione|  
|------------------------|  
|Set di dati non è valido.|  
|{0} contiene dati non validi.|  
|{0} e {1} deve essere saggia colonna coerente.|  
  

## <a name="error-0019"></a>Errore 0019  
 Si verifica un'eccezione se è previsto che la colonna contenga valori ordinati, ma non esiste.  
  
 Si riceverà questo errore in Azure Machine Learning, se i valori della colonna specificata non sono in ordinati.  
  
**Risoluzione:** Ordinare i valori della colonna, modificando manualmente il set di dati di input ed eseguire nuovamente il modulo.  
  
|Messaggi di eccezione|  
|------------------------|  
|I valori nella colonna non sono ordinati.|  
|I valori nella colonna "{0}" non sono ordinati.|  
|I valori nella colonna "{0}"del set di dati"{1}" non sono ordinati.|  
  

## <a name="error-0020"></a>Errore 0020  
 Si verifica un'eccezione se il numero di colonne in alcuni set di dati passati al modulo è troppo piccolo.  
  
 Verrà visualizzato questo errore in Azure Machine Learning se non sono state selezionate di colonne insufficiente per un modulo.  
  
**Risoluzione:** Rivedere il modulo e assicurarsi che il selettore di colonna abbia il numero corretto di colonne selezionate.  
  
|Messaggi di eccezione|  
|------------------------|  
|Numero di colonne nel set di dati di input è inferiore al minimo consentito.|  
|Numero di colonne nel set di dati di input è inferiore al minimo consentito di {0} una o più colonne.|  
|Numero di colonne nel set di dati di input "{0}" è inferiore al minimo consentito di {1} una o più colonne.|

## <a name="error-0021"></a>Errore 0021  
 Si verifica un'eccezione se il numero di righe in alcuni set di dati passati al modulo è troppo piccolo.  
  
 Questo errore in visualizzato in Azure Machine Learning quando sono presenti non sono sufficienti le righe nel set di dati per eseguire l'operazione specificata. Ad esempio, questo errore può verificarsi se il set di dati di input è vuota o se si sta provando a eseguire un'operazione che richiede un numero minimo di righe sia valido. Tali operazioni possono includere (ma non sono limitate a) il raggruppamento o la classificazione basata su metodi statistici, determinati tipi di binning e la formazione con i conteggi.  
  
**Risoluzione:**
 
 + Aprire il modulo che ha restituito l'errore e controllare le proprietà di set di dati e modulo di input. 
 + Verificare che il set di dati di input non sia vuoto e sono presenti un numero di righe sufficiente di dati per soddisfare i requisiti descritti nella Guida di modulo.  
 + Se i dati vengono caricati da un'origine esterna, assicurarsi che l'origine dati sia disponibile e che non si verificano errori o modificare nella definizione di dati che fa sì che il processo di importazione ottenere un minor numero di righe.
 + Se si esegue un'operazione su dati a monte del modulo che può influire il tipo di dati o il numero di valori, ad esempio la pulizia, la suddivisione, o operazioni di join, controllare gli output di queste operazioni per determinare il numero di righe restituite.  



## <a name="error-0022"></a>Errore 0022  
 Si verifica un'eccezione se il numero di colonne selezionate nel set di dati di input non corrisponde al numero previsto.  
  
 Questo errore in Azure Machine Learning può verificarsi quando l'operazione o il modulo downstream richiede un numero specifico di colonne o gli input, e sono state specificate troppe o troppo poche colonne o gli input. Ad esempio:   
  
-   Si specifica una colonna di etichetta singola o una colonna chiave e si seleziona accidentalmente più colonne.  
  
-   Si siano rinominando le colonne, ma forniti nomi più o meno rispetto alle colonne.  
  
-   Il numero di colonne nell'origine o destinazione è stato modificato o non corrisponde al numero di colonne utilizzate dal modulo.  
  
-   È stato specificato un elenco delimitato da virgole di valori per gli input, ma non corrisponde al numero di valori o più input non sono supportati.  
  
**Risoluzione:** Rivedere il modulo e controllare la selezione delle colonne per garantire che sia selezionato il numero corretto di colonne. Verificare gli output dei moduli a monte e i requisiti di operazioni downstream.  
  
 Se è stata usata una delle opzioni di selezione di colonna che è possono selezionare più colonne (indici di colonna, tutte le funzionalità, tutti numeriche e così via), convalidare il numero esatto di colonne restituite dalla selezione.  
  
 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->
  
 Verificare che il numero o il tipo di colonne a monte non è stato modificato.  
  
 Se si usa un set di dati di raccomandazione per addestrare un modello, tenere presente che il sistema di raccomandazione prevede un numero limitato di colonne, corrispondenti a coppie utente-elemento o utente-elemento-classificazione. Rimuovere colonne aggiuntive prima di training del modello o suddividere i set di dati di raccomandazione. Per altre informazioni, vedere [Split Data](split-data.md).  
  
|Messaggi di eccezione|  
|------------------------|  
|Numero di colonne selezionate nel set di dati di input non è uguale al numero previsto.|  
|Numero di colonne selezionate nel set di dati di input non è uguale a {0}.|  
|Criterio di selezione colonna "{0}" fornisce una serie di colonne selezionate nel set di dati input non è uguale a {1}.|  
|Criterio di selezione colonna "{0}" è previsto che fornisca {1} numero di colonne selezionate nel set di dati di input, ma {2} una o più colonne è/sono disponibili.|  



## <a name="error-0023"></a>Errore 0023  
 Si verifica un'eccezione se la colonna di destinazione del set di dati di input non è valida per il modulo trainer corrente.  
  
 Se la colonna di destinazione (come selezionato nei parametri del modulo) non è del tipo di dati valido, inclusi tutti i valori mancanti o non è stato categorica come previsto, si verifica questo errore in Azure Machine Learning.  
  
**Risoluzione:** Rivedere l'input per esaminarne il contenuto della colonna di etichetta e la destinazione del modulo. Assicurarsi che non dispone tutti i valori mancanti. Se il modulo prevede che la colonna di destinazione come categorica, assicurarsi che esistono più valori distinct nella colonna di destinazione.  
  
|Messaggi di eccezione|  
|------------------------|  
|Set di dati di input è supportata la colonna di destinazione.|  
|Set di dati di input non è di colonna di destinazione supportato "{0}".|  
|Set di dati di input ha non supportato di colonna di destinazione "{0}" per strumento di apprendimento di tipo {1}.|  
 

## <a name="error-0024"></a>Errore 0024  
Si verifica un'eccezione se il set di dati non contiene una colonna di etichetta.  

 Questo errore in Azure Machine Learning si verifica quando il modulo richiede una colonna di etichetta e il set di dati non dispone di una colonna di etichetta. Ad esempio, la valutazione di un set di dati con punteggio richiede in genere che una colonna di etichetta sia presente per calcolare la metrica di accuratezza.  
 
Può anche verificarsi che una colonna di etichetta è presente nel set di dati, ma non correttamente rilevati da Azure Machine Learning.
  
**Risoluzione:**

+ Aprire il modulo che ha generato l'errore e determinare se è presente una colonna di etichetta. Il tipo di dati o nome della colonna non è rilevante, purché la colonna contiene un singolo risultato (o la variabile dipendente) che si sta tentando di stimare. Se non si conosce quale colonna è associata l'etichetta, cercare un nome generico, ad esempio *classe* oppure *destinazione*. 
+  Se il set di dati non include una colonna di etichetta, è possibile che la colonna etichetta è stato in modo esplicito o accidentalmente rimosso a monte. È inoltre possibile che il set di dati non è l'output di un modulo di assegnazione dei punteggi a monte.
+ Per contrassegnare in modo esplicito la colonna come colonna etichetta, aggiungere il [Edit Metadata](edit-metadata.md) modulo e connettere il set di dati. Selezionare solo la colonna di etichetta e selezionare **Label** dalle **campi** nell'elenco a discesa. 
+ Se la colonna errata viene scelto come etichetta, è possibile selezionare **etichetta chiaro** dalle **campi** per correggere i metadati della colonna. 
  
|Messaggi di eccezione|  
|------------------------|  
|Non sono presenti colonne di etichetta nel set di dati.|  
|Nessuna colonna di etichetta in "{0}".|  
  

## <a name="error-0025"></a>Errore 0025  
 Si verifica un'eccezione se il set di dati non contiene una colonna di punteggio.  
  
 Questo errore in Azure Machine Learning si verifica se l'input per il modello di stima non contiene alcun punteggio valido colonne. Ad esempio, l'utente tenta di valutare un set di dati prima che è stato assegnato un punteggio con un modello con training corretto oppure la colonna di punteggio è stata eliminata in modo esplicito a monte. Questa eccezione si verifica anche se le colonne di punteggio in due set di dati non sono compatibili. Ad esempio, è possibile che si stia a confronto l'accuratezza del regressore lineare con quello di un classificatore binario.  
  
**Risoluzione:** Rivedere l'input per il modello di stima ed esaminare se contiene una o più colonne di punteggio. In caso contrario, il set di dati non è stato assegnato un punteggio o le colonne di punteggio sono state eliminate in un modulo a monte.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non sono presenti colonne di punteggio nel set di dati.|  
|Nessuna colonna di punteggio in "{0}".|  
|Nessuna colonna di punteggio in "{0}" che viene generato da un "{1}". Assegnare un punteggio di set di dati usando il tipo di strumento di apprendimento corretto.|  
  

## <a name="error-0026"></a>Errore 0026  
 Si verifica un'eccezione se non sono consentite colonne con lo stesso nome.  
  
 Se più colonne con lo stesso nome, si verifica questo errore in Azure Machine Learning. È possibile ricevere questo errore, è se il set di dati non dispone di una riga di intestazione e i nomi delle colonne vengono assegnati automaticamente: Col0, Col1, etc.  
  
**Risoluzione:** Se le colonne hanno stesso nome, inserire un [Edit Metadata](edit-metadata.md) modulo tra il set di dati di input e il modulo. Usare il selettore di colonna nelle [Edit Metadata](edit-metadata.md) per selezionare colonne da rinominare, digitando i nomi dei nuovi nel **nuovi nomi di colonna** nella casella di testo.  
  
|Messaggi di eccezione|  
|------------------------|  
|Negli argomenti vengono specificati i nomi di colonna uguali. Non sono consentiti nomi di colonna uguali dal modulo.|  
|Nomi di colonna negli argomenti uguali "{0}"e"{1}" non sono consentiti. Specificare nomi diversi.|  
  

## <a name="error-0027"></a>Errore 0027  
 Nel caso in cui quando due oggetti devono essere della stessa dimensione ma non si verifica un'eccezione.  
  
 Questo è un errore più comune in Azure Machine Learning e può essere causato da molte condizioni.  
  
**Risoluzione:** Non vi è alcuna risoluzione specifica. Tuttavia, è possibile controllare condizioni come la seguente:  
  
-   Se si rinomina le colonne, assicurarsi che ogni elenco (le colonne di input e l'elenco di nuovi nomi) ha lo stesso numero di elementi.  
  
-   Se si usa aggiunta o la concatenazione di due set di dati, assicurarsi che hanno lo stesso schema.  
  
-   Se si sta unendo in join due set di dati con più colonne, assicurarsi che le colonne chiave abbiano gli stessi dati digitare e selezionare l'opzione **consentire duplicati e mantenere l'ordine delle colonne nella selezione**.  
  
|Messaggi di eccezione|  
|------------------------|  
|La dimensione degli oggetti passati non è coerente.|  
|La dimensione di "{0}"è incoerente con dimensione pari a"{1}".|  
  

## <a name="error-0028"></a>Errore 0028  
 Eccezione si verifica nel caso quando set di colonne contiene nomi di colonna duplicati e non è consentito.  
  
 Questo errore in Azure Machine Learning si verifica quando i nomi delle colonne vengono duplicati; vale a dire, non è univoco.  
  
**Risoluzione:** Se tutte le colonne con stesso nome, aggiungere un'istanza del [Edit Metadata](edit-metadata.md) tra set di dati di input e il modulo che genera l'errore. Usare il selettore di colonna nelle [Edit Metadata](edit-metadata.md) per selezionare colonne da rinominare e digitare i nuovi nomi di colonne nel **nuovi nomi di colonna** nella casella di testo. Se si siano rinominando più colonne, assicurarsi che i valori digitati nella **nuovi nomi di colonna** siano univoci.  
  
|Messaggi di eccezione|  
|------------------------|  
|Set di colonne contiene nomi di colonna duplicati.|  
|Il nome "{0}" è duplicato.|  
|Il nome "{0}"è duplicato "{1}".|  
  

## <a name="error-0029"></a>Errore 0029  
 Si verifica un'eccezione nel caso in cui quando viene passato un URI non valido.  
  
 Questo errore in Azure Machine Learning si verifica quando viene passato un URI non valido.  Si riceverà questo errore se viene soddisfatta una delle condizioni seguenti:, o.  
  
-   Il pubblico o l'URI SAS forniti per l'archiviazione Blob di Azure per la lettura o scrittura contiene un errore.  
  
-   L'intervallo di tempo per la firma di accesso condiviso è scaduto.  
  
-   L'URL Web tramite origine HTTP rappresenta un file o un URI di loopback.  
  
-   L'URL Web tramite HTTP contiene un URL formato non corretto.  
  
-   L'URL non può essere risolto dall'origine remota.  
  
**Risoluzione:** Rivedere il modulo e verificare il formato dell'URI. Se l'origine dati è un URL Web tramite HTTP, verificare che l'origine desiderata non è un file o un URI (localhost) di loopback.  
  
|Messaggi di eccezione|  
|------------------------|  
|Viene passato un Uri non valido.|  
  

## <a name="error-0030"></a>Errore 0030  
 Eccezione si verifica nel caso quando non è possibile scaricare un file.  
  
 Questa eccezione in Azure Machine Learning si verifica quando non è possibile scaricare un file. Si riceve questa eccezione quando una lettura effettuata è fallita da un'origine HTTP non riuscita dopo tre (3) tentativi tentativi.  
  
**Risoluzione:** Verificare che l'URI per l'origine HTTP sia corretto e che il sito è attualmente accessibile tramite Internet.  
  
|Messaggi di eccezione|  
|------------------------|  
|Se non è possibile scaricare un file.|  
|Errore durante il download del file: {0}.|  
  

## <a name="error-0031"></a>Errore 0031  
 Si verifica un'eccezione se il numero di colonne nel set di colonne è inferiore al necessario.  
  
 Se il numero delle colonne selezionate è inferiore al necessario, si verifica questo errore in Azure Machine Learning.  Si riceverà questo errore se il requisito minimo necessario di numero di colonne non selezionato.  
  
**Risoluzione:** Aggiungere colonne aggiuntive per la selezione della colonna usando il **Column Selector**.  
  
|Messaggi di eccezione|  
|------------------------|  
|Numero di colonne nel set di colonne è minore di quanto richiesto.|  
|{0} una o più colonne devono essere specificate. Il numero effettivo di colonne specificate è {1}.|  

## <a name="error-0032"></a>Errore 0032  
 Si verifica un'eccezione se l'argomento non è un numero.  
  
 Si riceverà questo errore in Azure Machine Learning, se l'argomento è NaN o double.  
  
**Risoluzione:** Modificare l'argomento specificato per l'uso di un valore valido.  
  
|Messaggi di eccezione|  
|------------------------|  
|Argomento non è un numero.|  
|"{0}" non è un numero.|  
  

## <a name="error-0033"></a>Errore 0033  
 Si verifica un'eccezione se l'argomento è infinito.  
  
 Se l'argomento è infinito, si verifica questo errore in Azure Machine Learning. Si riceverà questo errore se l'argomento può essere `double.NegativeInfinity` o `double.PositiveInfinity`.  
  
**Risoluzione:** Modificare l'argomento specificato per essere un valore valido.  
  
|Messaggi di eccezione|  
|------------------------|  
|L'argomento è deve essere limitato.|  
|"{0}" non è finito.|  
  

## <a name="error-0034"></a>Errore 0034  
 Si verifica un'eccezione se non esiste più di una classificazione per una coppia utente-elemento specificato.  
  
 Questo errore in Azure Machine Learning si verifica nell'indicazione se una coppia utente-elemento ha il valore di più di una classificazione.  
  
**Risoluzione:** Assicurarsi che la coppia utente-elemento sia disponibile un solo valore di classificazione.  
  
|Messaggi di eccezione|  
|------------------------|  
|È presente più di una classificazione per i valori nel set di dati.|  
|Più di una classificazione per utente {0} ed elemento {1} nella tabella di dati di stima di classificazione.|  
  

## <a name="error-0035"></a>Errore 0035  
 Si verifica un'eccezione se nessuna funzione fornita per un determinato utente o un elemento.  
  
 Si sta tentando di usare un modello di raccomandazione per il punteggio, ma non è stato trovato un vettore di funzionalità, si verifica questo errore in Azure Machine Learning.  
  
**Risoluzione:**

Il sistema di raccomandazione Matchbox implica determinati requisiti che devono essere soddisfatti quando si usano funzioni elemento o funzioni utente.  Questo errore indica che manca un vettore di funzionalità per un utente o un elemento specificato come input.  È necessario assicurarsi che un vettore di funzionalità è disponibile nei dati per ogni utente o un elemento.  
  
 Ad esempio, se eseguito il training di una raccomandazione usando funzionalità quali età dell'utente, ubicazione o reddito del modello, ma ora si vuole creare i punteggi ai nuovi utenti che non sono stati visualizzati durante il training, è necessario fornire alcuni set di funzionalità equivalenti (ovvero età, il percorso e i valori di reddito) per i nuovi utenti per effettuare stime appropriate per tale. 
 
 Se non hai alcuna funzionalità per questi utenti, prendere in considerazione la progettazione delle funzionalità per generare funzionalità appropriate.  Ad esempio, se non hai valori età o reddito singolo utente, si potrebbero generare valori approssimati da utilizzare per un gruppo di utenti. 
 
<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).
 
For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->
  
 > [!TIP]
 > Risoluzione non applicabile al caso? Utenti sono invitati a inviare commenti e suggerimenti in questo articolo e fornire informazioni sullo scenario, tra cui il modulo e il numero di righe nella colonna. Si userà queste informazioni per fornire più dettagliata di risoluzione dei problemi in futuro.
   
|Messaggi di eccezione|  
|------------------------|  
|Nessuna funzione fornita per un utente richiesto o un elemento.|  
|Le funzionalità per {0} richiesta ma non fornita.|  
  

## <a name="error-0036"></a>Errore 0036  
 Si verifica un'eccezione se sono stati forniti più vettori di funzionalità per un determinato utente o un elemento.  
  
 Se un vettore di funzionalità non è definito più volte, si verifica questo errore in Azure Machine Learning.  
  
**Risoluzione:** Assicurarsi che il vettore di funzionalità non è definito più volte.  
  
|Messaggi di eccezione|  
|------------------------|  
|Definizione di funzione per un utente o un elemento duplicata.|  
|Definizione di funzione per duplicata {0}.|  
  

## <a name="error-0037"></a>Errore 0037  
 Si verifica un'eccezione se sono specificate più colonne di etichetta e ne è consentita.  
  
 Se più di una colonna è selezionata per la nuova colonna di etichetta, si verifica questo errore in Azure Machine Learning. Più algoritmi di apprendimento supervisionato richiedono una singola colonna da contrassegnare come etichetta di destinazione.  
  
**Risoluzione:** Assicurarsi di selezionare una singola colonna come la nuova colonna di etichetta.  
  
|Messaggi di eccezione|  
|------------------------|  
|Sono specificate più colonne di etichetta.|  
  

## <a name="error-0038"></a>Errore 0038  
 Eccezione si verifica se il numero di elementi prevista deve essere un valore esatto, ma non è.  
  
 Questo errore in Azure Machine Learning, si verifica se il numero di elementi prevista deve essere un valore esatto, ma non è.  Si riceverà questo errore se il numero di elementi non è uguale al valore previsto valido.  
  
**Risoluzione:** Modificare l'oggetto contiene il numero corretto di elementi di input.  
  
|Messaggi di eccezione|  
|------------------------|  
|Numero di elementi non è valido.|  
|Numero di elementi in "{0}" non è valido.|  
|Numero di elementi in "{0}" non è uguale al numero valido di {1} uno o più elementi.|  
  

## <a name="error-0039"></a>Errore 0039  
 Se un'operazione ha esito negativo, si verifica un'eccezione.  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile completare un'operazione interna.  
  
**Risoluzione:** Questo errore è causato da molte condizioni e non esiste alcun remedy specifico.  
 Nella tabella seguente contiene i messaggi generici per questo errore, seguiti da una descrizione della condizione specifica. 
 
 Se non sono disponibili, dettagli [inviare commenti e suggerimenti](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) e fornire informazioni sui moduli che ha generato l'errore e le condizioni correlate.
  
|Messaggi di eccezione|  
|------------------------|  
|Operazione non riuscita.|  
|Errore durante il completamento dell'operazione: {0}.|  
  

## <a name="error-0040"></a>Errore 0040  
 Eccezione si verifica quando la chiamata a un modulo deprecato.  
  
 Questo errore in Azure Machine Learning viene generato quando si chiama un modulo deprecato.  
  
**Risoluzione:** Sostituire il modulo deprecato con quelle supportate. Vedere il log di output del modulo per le informazioni su quale modulo invece di utilizzare.  
  
|Messaggi di eccezione|  
|------------------------|  
|L'accesso a modulo deprecato.|  
|Modulo "{0}" è deprecata. Modulo di utilizzo "{1}" invece.|  
 

## <a name="error-0041"></a>Errore 0041  
 Eccezione si verifica quando la chiamata a un modulo deprecato.  
  
 Questo errore in Azure Machine Learning viene generato quando si chiama un modulo deprecato.  
  
**Risoluzione:** Sostituire il modulo deprecato con un set di quelle supportate. Queste informazioni verranno visualizzate nel log di output modulo.  
  
|Messaggi di eccezione|  
|------------------------|  
|L'accesso a modulo deprecato.|  
|Modulo "{0}" è deprecata. Usare i moduli "{1}" per la funzionalità richiesta.|  
 

## <a name="error-0042"></a>Errore 0042  
 Eccezione si verifica quando non è possibile convertire la colonna in un altro tipo.  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile convertire la colonna nel tipo specificato.  Si riceverà questo errore se un modulo richiede un particolare tipo di dati, ad esempio datetime, testo, un numero a virgola mobile o numero intero, ma non è possibile convertire una colonna esistente per il tipo richiesto.  
 
Ad esempio, si potrebbe selezionare una colonna e provare a convertirlo in un tipo di dati numerici per l'uso in un'operazione matematica e visualizzato questo errore se la colonna contiene dati non validi. 

Un altro motivo che è che venga visualizzato questo errore se si prova a usare una colonna contenente numeri a virgola mobile o molti valori univoci come una colonna categorica. 
  
**Risoluzione:**

+ Aprire la pagina della Guida per il modulo che ha generato l'errore e verificare i requisiti del tipo di dati.
+ Esaminare i tipi di dati delle colonne nel set di dati di input.
+ Esaminare i dati che hanno origine in origini dati senza schema cosiddette.
+ Controllare il set di dati per valori mancanti o caratteri speciali che potrebbero bloccare la conversione al tipo di dati desiderato. 
    + Tipi di dati numerici dovrebbero essere coerenti: ad esempio, verificare la presenza di numeri a virgola mobile in una colonna di numeri interi.
    + Cercare le stringhe di testo o i valori ND in una colonna numerica. 
    + I valori booleani possono essere convertiti in una rappresentazione appropriata a seconda del tipo di dati necessari.
    + Esaminare le colonne di testo per i caratteri non unicode, tabulazioni o caratteri di controllo
    + I dati di data/ora dovrebbero essere coerenti per evitare errori di modellazione, ma la pulitura può risultare complessa a causa di numerosi formati. È consigliabile usare <!--the [Execute R Script](execute-r-script.md) or -->[Esegui Script Python](execute-python-script.md) moduli per eseguire la pulizia.  
+ Se necessario, modificare i valori nel set di dati di input in modo che la colonna può essere convertita correttamente. La modifica potrebbe includere creazione di contenitori, il troncamento o operazioni di arrotondamento, azzerare gli outlier o imputazione dei valori mancanti. Vedere gli articoli seguenti per alcuni scenari comuni di trasformazione dei dati nell'apprendimento automatico:
    + [Pulisci dati mancanti](clean-missing-data.md)
    + [Normalizzare i dati](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->
 
> [!TIP]
> Risoluzione chiara o non applicabile al caso? Utenti sono invitati a inviare commenti e suggerimenti in questo articolo e fornire informazioni sullo scenario, tra cui il modulo e il tipo di dati della colonna. Si userà queste informazioni per fornire più dettagliata di risoluzione dei problemi in futuro.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è consentita la conversione.|  
|Impossibile convertire la colonna di tipo {0} alla colonna di tipo {1}.|  
|Impossibile convertire la colonna "{2}" di tipo {0} alla colonna di tipo {1}.|  
|Impossibile convertire la colonna "{2}" di tipo {0} alla colonna "{3}" di tipo {1}.|  
  

## <a name="error-0043"></a>Errore 0043  
 Eccezione si verifica quando il tipo di elemento non implementa Equals in modo esplicito.  
  
 Questo errore in Azure Machine Learning viene usato e verrà deprecato.  
  
**Risoluzione:** No.  
  
|Messaggi di eccezione|  
|------------------------|  
|Nessun metodo accessibile esplicito è uguale a trovato.|  
|Non è possibile confrontare i valori per la colonna \\"{0}\\" di tipo {1}. Nessun metodo accessibile esplicito è uguale a trovato.|  


## <a name="error-0044"></a>Errore 0044  
 Eccezione si verifica quando non è possibile derivare il tipo di elemento di colonna da valori esistenti.  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile dedurre il tipo di una o più colonne in un set di dati. Ciò accade solitamente quando si concatenano due o più set di dati con tipi di elementi diversi. Se Azure Machine Learning è possibile determinare un tipo comune che è in grado di rappresentare tutti i valori in una o più colonne senza perdita di informazioni, verrà generato questo errore.  
  
**Risoluzione:** Assicurarsi che tutti i valori in una determinata colonna in entrambi i set di dati combinati sono entrambi dello stesso tipo (numerico, booleano, categorica, stringa, data, e così via) o può essere assegnato lo stesso tipo.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è possibile derivare il tipo di elemento della colonna.|  
|Non è possibile derivare il tipo di elemento per la colonna "{0}": tutti gli elementi sono riferimenti null.|  
|Non è possibile derivare il tipo di elemento per la colonna "{0}"del set di dati"{1}": tutti gli elementi sono riferimenti null.|  
  

## <a name="error-0045"></a>Errore 0045  
 Eccezione si verifica quando non è possibile creare una colonna a causa di tipi di elemento misti nell'origine.  
  
 Questo errore in Azure Machine Learning viene generato quando i tipi di elementi di due set di dati combinati sono diversi.  
  
**Risoluzione:** Assicurarsi che tutti i valori in una determinata colonna in entrambi i set di dati combinati siano dello stesso tipo (numerico, booleano, categorica, stringa, data e così via).  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è possibile creare una colonna con tipi di elemento misti.|  
|Non è possibile creare una colonna con ID "{0}" dei tipi di elemento misti: \n\tType dei dati [{1}, {0}] è {2}\n\tType dei dati [{3}, {0}] è {4}.|  
  

## <a name="error-0046"></a>Errore 0046  
 Eccezione si verifica quando non è possibile creare la directory nel percorso specificato.  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile creare una directory nel percorso specificato. Se qualsiasi parte del percorso della directory di output per una Query Hive è inaccessibile o errato, si riceverà l'errore.  
  
**Risoluzione:** Visitare di nuovo il modulo e verificare che il percorso della directory è formattato correttamente e che sia accessibile con le credenziali correnti.  
  
|Messaggi di eccezione|  
|------------------------|  
|Specificare una directory di output valido.|  
|Directory: {0} non può essere creato. Specificare un percorso valido.|  
  

## <a name="error-0047"></a>Errore 0047  
 Si verifica un'eccezione se il numero di colonne di funzioni in alcuni set di dati passati al modulo è troppo piccolo.  
  
 Se il set di dati di input per il training non contiene il numero minimo di colonne richiesti dall'algoritmo, si verifica questo errore in Azure Machine Learning. In genere entrambi i set di dati è vuoto o contiene solo le colonne di training.  
  
**Risoluzione:** Rivedere il set di dati di input per assicurarsi che ci uno o più colonne aggiuntive oltre alla colonna di etichetta.  
  
|Messaggi di eccezione|  
|------------------------|  
|Numero di colonne di funzioni nel set di dati di input è inferiore al minimo consentito.|  
|Numero di colonne di funzioni nel set di dati di input è inferiore al minimo consentito di {0} una o più colonne.|  
|Numero di colonne di funzioni nel set di dati di input "{0}" è inferiore al minimo consentito di {1} una o più colonne.|  
  

## <a name="error-0048"></a>Errore 0048  
 Eccezione si verifica nel caso quando non è possibile aprire un file.  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile aprire un file per la lettura o scrittura. È possibile ricevere questo errore per questi motivi:  
  
-   Il file (blob) o il contenitore non esiste  
  
-   Il livello di accesso del file o del contenitore non consente di accedere al file  
  
-   Il file è troppo grande per essere di lettura o in un formato non corretto  
  
**Risoluzione:** Rivedere il modulo e il file di cui che si sta tentando di leggere.  
  
 Verificare che i nomi del contenitore. file siano corretti.  
  
 Usare il portale di Azure classico o uno strumento di archiviazione di Azure per verificare di avere l'autorizzazione per accedere al file.  
  
  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->
  
|Messaggi di eccezione|  
|------------------------|  
|Impossibile aprire un file.|  
|Errore durante l'apertura del file: {0}.|  


## <a name="error-0049"></a>Errore 0049  
 Eccezione si verifica nel caso quando non è possibile analizzare un file.  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile analizzare un file. Si riceverà questo errore se il formato del file selezionato nel [Import Data](import-data.md) modulo non corrisponde al formato effettivo del file, o se il file contiene un carattere non riconoscibile.  
  
**Risoluzione:** Rivedere il modulo e correggere la selezione del formato di file se il formato del file non corrisponde. Se possibile, esaminare il file da selezionare per confermare che non contenga caratteri non validi.  
  
|Messaggi di eccezione|  
|------------------------|  
|Se non è possibile analizzare un file.|  
|Errore durante l'analisi del file: {0}.|  
  

## <a name="error-0050"></a>Errore 0050  
 Nel caso di input quando si verifica un'eccezione e i file di output sono uguali.  
  
**Risoluzione:** Questo errore in Azure Machine Learning viene usato e verrà deprecato.  
  
|Messaggi di eccezione|  
|------------------------|  
|I file specificati per l'input e output non possono essere lo stesso.|


## <a name="error-0051"></a>Errore 0051  
 Eccezione si verifica nel caso quando diversi file di output sono uguali.  
  
**Risoluzione:** Questo errore in Azure Machine Learning viene usato e verrà deprecato.  
  
|Messaggi di eccezione|  
|------------------------|  
|I file specificati per gli output non possono essere lo stesso.|


## <a name="error-0052"></a>Errore 0052  
 Si verifica un'eccezione se chiave dell'account di archiviazione di Azure è stato specificato correttamente.  
  
 Se la chiave usata per accedere all'account di archiviazione di Azure non è corretta, si verifica questo errore in Azure Machine Learning. Ad esempio, questo errore può verificarsi se la chiave di archiviazione di Azure sono stata troncata quando copiati e incollati o se è stata usata la chiave è errata.  
  
 Per altre informazioni su come ottenere la chiave per un account di archiviazione di Azure, vedere [visualizzare, copiare e rigenerare chiavi di accesso delle risorse di archiviazione](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  
  
**Risoluzione:** Rivedere il modulo e verificare che la chiave di archiviazione di Azure sia corretta per l'account; copiare anche in questo caso la chiave dal portale di Azure classico se necessario.  
  
|Messaggi di eccezione|  
|------------------------|  
|La chiave di account di archiviazione di Azure non è corretta.|  
  

## <a name="error-0053"></a>Errore 0053  
 Eccezione si verifica nel caso quando non sono disponibili funzioni utente o elementi per le raccomandazioni matchbox.  
  
 Questo errore in Azure Machine Learning viene generato quando non è stato trovato un vettore di funzionalità.  
  
**Risoluzione:** Assicurarsi che un vettore di funzionalità sia presente nel set di dati di input.  
  
|Messaggi di eccezione|  
|------------------------|  
|Le funzionalità di utente o / e gli elementi richiesti ma non specificati.|  

## <a name="error-0054"></a>Errore 0054  
 Si verifica un'eccezione se sono troppo pochi valori distinct nella colonna per completare l'operazione.  
  
**Risoluzione:** Questo errore in Azure Machine Learning viene usato e verrà deprecato.  
  
|Messaggi di eccezione|  
|------------------------|  
|I dati presenti troppo pochi valori distinti nella colonna specificata per completare l'operazione.|  
|I dati presenti troppo pochi valori distinti nella colonna specificata per completare l'operazione. Necessari almeno {0} elementi.|  
|I dati presenti troppo pochi valori distinct nella colonna "{1}" per completare l'operazione. Necessari almeno {0} elementi.|  
  

## <a name="error-0055"></a>Errore 0055  
 Eccezione si verifica quando la chiamata a un modulo deprecato.  Questo errore in Azure Machine Learning viene visualizzato se si prova a chiamare un modulo che è stato deprecato.
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|L'accesso a modulo deprecato.|  
|Modulo "{0}" è deprecata.|  

## <a name="error-0056"></a>Errore 0056  
 Si verifica un'eccezione se le colonne selezionate per un'operazione viola i requisiti.  
  
 Quando si scelgono le colonne per un'operazione che richiede la colonna di un particolare tipo di dati, si verifica questo errore in Azure Machine Learning. 
 
 Questo errore può verificarsi anche se la colonna è il tipo di dati corretto, ma il modulo in uso richiede che la colonna essere anche contrassegnati come una funzionalità, un'etichetta o colonna categorica.  
  
  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->
  
**Risoluzione:**
  
1.  Esaminare il tipo di dati delle colonne attualmente selezionate. 

2. Verificare se le colonne selezionate siano categoriche, etichetta o colonne di funzioni.  
  
3.  Rivedere l'argomento della Guida per il modulo in cui è stata effettuata la selezione della colonna, per determinare se sono presenti requisiti specifici per l'utilizzo di colonna o tipo di dati.  
  
3.  Uso [Edit Metadata](edit-metadata.md) per modificare il tipo di colonna per la durata dell'operazione. Assicurarsi di modificare il tipo di colonna il valore originale, di un'altra istanza di [Edit Metadata](edit-metadata.md)se ti serve per le operazioni downstream.  
  
|Messaggi di eccezione|  
|------------------------|  
|Uno o più colonne selezionate non sono in una categoria consentita.|  
|Colonna con nome "{0}" non è in una categoria consentita.|  
  

## <a name="error-0057"></a>Errore 0057  
 Eccezione si verifica quando si prova a creare un file o i blob che esiste già.  
  
 Questa eccezione si verifica quando si usa la [esportazione di dati](export-data.md) modulo o un altro modulo per salvare i risultati di un esperimento in Azure Machine Learning in archiviazione blob di Azure, ma si tenta di creare un file o un blob che esiste già.   
  
**Risoluzione:**
 
 Si riceverà l'errore solo se la proprietà viene impostata in precedenza **modalità di scrittura nell'archiviazione blob di Azure** al **errore**. Per impostazione predefinita, questo modulo genera un errore se si prova a scrivere un set di dati in un blob che esiste già.
 
 - Aprire le proprietà del modulo e impostare la proprietà **modalità di scrittura nell'archiviazione blob di Azure** al **Sovrascrivi**.
 - In alternativa, è possibile digitare il nome di un blob di destinazione diverso o di file e verificare di aver specificato un blob che non esiste già.  
  
|Messaggi di eccezione|  
|------------------------|  
|File o un Blob esiste già.|  
|Blob o file "{0}" esiste già.|  
  

## <a name="error-0058"></a>Errore 0058  
 Se il set di dati non contiene la colonna di etichetta prevista, si verifica questo errore in Azure Machine Learning.  
  
 Questa eccezione può verificarsi anche quando la colonna di etichetta specificato non corrisponde ai dati o un tipo di dati previsto per lo strumento di apprendimento o ha valori errati. Ad esempio, questa eccezione viene generata quando si usa una colonna di etichetta con valori reali durante il training di un classificatore binario.  
  
**Risoluzione:** La risoluzione dipende lo strumento di apprendimento o trainer che si sta utilizzando e i tipi di dati delle colonne nel set di dati. In primo luogo, verificare i requisiti dell'algoritmo di machine learning o il modulo di formazione.  
  
 Rivedere il set di dati di input. Verificare che la colonna si prevede di essere considerato come l'etichetta con destra tipo di dati per il modello che si sta creando.  
  
 Controllare gli input per i valori mancanti ed eliminare o sostituirli se necessario.  
  
 Se necessario, aggiungere il [Edit Metadata](edit-metadata.md) modulo e assicurarsi che la colonna etichetta è contrassegnata come etichetta.  
  
|Messaggi di eccezione|  
|------------------------|  
|La colonna di etichette non risulta come previsto|  
|La colonna di etichette non risulta come previsto "{0}".|  
|La colonna etichetta "{0}"non è previsto "{1}".|  
  

## <a name="error-0059"></a>Errore 0059  
 Si verifica un'eccezione se un indice di colonna specificato in un selettore di colonna non può essere analizzato.  
  
 Questo errore in Azure Machine Learning si verifica se un indice di colonna specificato quando si utilizza il selettore di colonna non può essere analizzato.  Si riceverà questo errore quando l'indice di colonna è un formato non valido che non può essere analizzato.  
  
**Risoluzione:** Modificare l'indice di colonna per usare un valore di indice valido.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è stato possibile analizzare uno o più indici di colonna specificata o intervalli di indice.|  
|Indice di colonna o un intervallo "{0}" nelze analyzovat.|  
  

## <a name="error-0060"></a>Errore 0060  
 Eccezione si verifica quando viene specificato un fuori intervallo a colonne intervallo in un selettore di colonna.  
  
 Questo errore in Azure Machine Learning si verifica quando viene specificato un intervallo di out-of-range colonna nel selettore di colonna. Si riceverà questo errore se l'intervallo della colonna nel selettore di colonna non corrispondono alle colonne nel set di dati.  
  
**Risoluzione:** Modificare l'intervallo della colonna nel selettore di colonna in modo che corrispondano alle colonne nel set di dati.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è valido o non compreso nell'intervallo dell'indice di colonna intervallo specificato.|  
|Intervallo della colonna "{0}" non è valido o non compreso nell'intervallo.|  
  

## <a name="error-0061"></a>Errore 0061  
 Eccezione si verifica quando si tenta di aggiungere una riga a un oggetto DataTable che contiene un numero diverso di colonne rispetto alla tabella.  
  
 Quando si tenta di aggiungere una riga a un set di dati che contiene un numero diverso di colonne rispetto al set di dati, si verifica questo errore in Azure Machine Learning.  Si riceverà questo errore se la riga che viene aggiunto al set di dati ha un numero diverso di colonne dal set di dati di input.  Impossibile aggiungere la riga al set di dati se il numero di colonne è diverso.  
  
**Risoluzione:** Modificare il set di dati di input con lo stesso numero di colonne della riga aggiunta, o modificare la riga aggiunta alla hanno lo stesso numero di colonne del set di dati.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tutte le tabelle devono avere lo stesso numero di colonne.|  
  

## <a name="error-0062"></a>Errore 0062  
 Eccezione si verifica quando si tenta di confrontare due modelli con tipi di strumenti di apprendimento diversi.  
  
 Questo errore in Azure Machine Learning viene generato quando le metriche di valutazione per due i set di dati con punteggio diversi non possono essere confrontate. In questo caso, non è possibile confrontare la validità dei modelli usati per produrre due set di dati con punteggio.  
  
**Risoluzione:** Verificare che i risultati con punteggio vengono generati per lo stesso tipo di modello di machine learning (classificazione binaria, regressione, classificazione multiclasse, raccomandazione, clustering, rilevamento di anomalie e così via). Tutti i modelli che si confrontano devono avere lo stesso tipo di strumento di apprendimento.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tutti i modelli devono avere lo stesso tipo di strumento di apprendimento.|  
  

 <!--## Error 0063  
 This exception is raised when R script evaluation fails with an error.  
  
 This error occurs when you have provided an R script in one of the [R language modules](r-language-modules.md) in Azure Machine Learning, and the R code contains internal syntax errors. The exception can also occur if you provide the wrong inputs to the R script. 
 
 The error can also occur if the script is too large to execute in the workspace. The maximum script size for the **Execute R Script** module is 1,000 lines or 32 KB of work space, whichever is lesser.
  
**Resolution:**

1. In Azure Machine Learning Studio, right-click the module that has the error, and select **View Log**.
2. Examine the standard error log of the module, which contains the stack trace.
    + Lines beginning with [ModuleOutput] indicate output from R.
    + Messages from R marked as **warnings** typically do not cause the experiment to fail.
3. Resolve script issues.  
    + Check for R syntax errors. Check for variables that are defined but never populated.
    + Review the input data and the script to determine if either the data or variables in the script use characters not supported by Azure Machine Learning.
    + Check whether all package dependencies are installed.
    + Check whether your code loads required libraries that are not loaded by default.
    + Check whether the required packages are the correct version.
    + Make sure that any dataset that you want to output is converted to a data frame.  
4.  Resubmit the experiment.

 <!--
> [!NOTE]
> These topics contains examples of R code that you can use, as well as links to experiments in the [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com) that use R script.
> + [Execute R Script](execute-r-script.md)
> + [Create R Model](create-r-model.md)
-->  
|Messaggi di eccezione|  
|------------------------|  
|Errore durante la valutazione dello script R.|  
|Si è verificato l'errore seguente durante la valutazione dello script R:---inizio del messaggio di errore da R--- {0} ---fine del messaggio di errore da R--|  
|Durante la valutazione dello script R "{1}" si è verificato l'errore seguente:---inizio del messaggio di errore da R--- {0} ---fine del messaggio di errore da R--|  
  


## <a name="error-0064"></a>Errore 0064  
 Si verifica un'eccezione se il nome di account di archiviazione di Azure o la chiave di archiviazione è stata specificata correttamente.  
  
 Se il nome di account di archiviazione di Azure o una chiave di archiviazione è stata specificata correttamente, si verifica questo errore in Azure Machine Learning. Si riceverà questo errore se si immette un nome di account non corretto o la password dell'account di archiviazione. Ciò può verificarsi se si immette manualmente il nome dell'account o la password. Si potrebbe verificare anche se l'account è stato eliminato.  
  
**Risoluzione:** Verificare che il nome dell'account e la password sia stato immesso correttamente e che l'account esiste.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il nome di account di archiviazione di Azure o una chiave di archiviazione non è corretta.|  
|Il nome di account di archiviazione di Azure "{0}" o chiave di archiviazione per il nome dell'account non è corretto.|  
  

## <a name="error-0065"></a>Errore 0065  
 Si verifica un'eccezione se il nome di blob di Azure è stato specificato correttamente.  
  
 Se il nome di blob di Azure è stato specificato correttamente, si verifica questo errore in Azure Machine Learning.  Si riceverà l'errore se:  
  
-   Impossibile trovare il blob nel contenitore specificato.  
  
 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->
  
-   Solo il contenitore è stato specificato come origine in un [Import Data](import-data.md) richiesta quando è stato il formato Excel o CSV con codifica; concatenazione dei contenuti di tutti i BLOB all'interno di un contenitore non è consentita con questi formati.  
  
-   Un URI SAS non contiene il nome di un blob valido.  
  
**Risoluzione:** Rivedere il modulo che genera l'eccezione. Verificare che il blob specificato esiste nel contenitore nell'account di archiviazione e che le autorizzazioni consentono di visualizzare il blob. Verificare che l'input è nel formato **containername/filename** se si dispone di Excel o CSV con formati di codifica. Verificare che un URI SAS contiene il nome di un blob valido.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il blob di archiviazione di Azure non è corretto.|  
|Il nome del blob di archiviazione di Azure "{0}" non è corretto|  
  

## <a name="error-0066"></a>Errore 0066  
 Si verifica un'eccezione se una risorsa non è stato possibile caricare un Blob di Azure.  
  
 Se una risorsa non è stato possibile caricare un Blob di Azure, si verifica questo errore in Azure Machine Learning.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Entrambi vengono salvati per lo stesso account di archiviazione di Azure come l'account che contiene il file di input.  
  
**Risoluzione:** Rivedere il modulo. Verificare che il nome dell'account Azure, la chiave di archiviazione e contenitore siano corretti e che l'account disponga delle autorizzazioni di scrittura al contenitore.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è stato possibile caricare la risorsa di archiviazione di Azure.|  
|Il file "{0}" non è stato possibile caricare in archiviazione di Azure come {1}.|  
  

## <a name="error-0067"></a>Errore 0067  
 Si verifica un'eccezione se un set di dati contiene un numero diverso di colonne maggiore del previsto.  
  
 Se un set di dati contiene un numero diverso di colonne maggiore del previsto, si verifica questo errore in Azure Machine Learning.  Si riceverà questo errore quando il numero di colonne nel set di dati è diverso rispetto al numero di colonne in cui il modulo prevede durante l'esecuzione.  
  
**Risoluzione:** Modificare il set di dati di input o i parametri.  
  
|Messaggi di eccezione|  
|------------------------|  
|Numero imprevisto di colonne in datatable.|  
|Previsto "{0}"colonne ma trovato"{1}" colonne invece.|  
  

## <a name="error-0068"></a>Errore 0068  
 Si verifica un'eccezione se lo script Hive specificato non è corretto.  
  
 Se sono presenti errori di sintassi in uno script Hive QL o se l'interprete Hive rileva un errore durante l'esecuzione di query o script, si verifica questo errore in Azure Machine Learning.  
  
**Risoluzione:**

Il messaggio di errore da Hive è in genere restituito nel Log degli errori in modo che sia possibile eseguire azioni in base all'errore specifico. 

+ Aprire il modulo ed esaminare la query per gli errori.  
+ Verificare che la query funzioni correttamente all'esterno di Azure Machine Learning accedendo alla console di Hive dei cluster Hadoop ed eseguire la query.  
+ Provare a inserire commenti in formato nello script Hive in una riga separata anziché la combinazione di istruzioni eseguibili e i commenti in una singola riga.  

### <a name="resources"></a>Risorse

Vedere gli articoli seguenti per assistenza con le query Hive per machine learning:

+ [Creare tabelle Hive e caricare i dati dall'archiviazione Blob di Azure](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Esplorare i dati nelle tabelle con le query Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Creare funzionalità per i dati in un cluster Hadoop con query Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Hive per foglio informativo sugli utenti SQL (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Messaggi di eccezione|  
|------------------------|  
|Lo script hive non è corretto.|  
|Lo script hive {0} non è corretto.|  
  

## <a name="error-0069"></a>Errore 0069  
 Si verifica un'eccezione se lo script SQL specificato non è corretto.  
  
 Se lo script SQL specificato presenta problemi di sintassi o se le colonne o tabella specificata nello script non è valido, si verifica questo errore in Azure Machine Learning. 
 
 Si riceverà questo errore se il motore di SQL rileva un errore durante l'esecuzione di query o script. Il messaggio di errore SQL viene in genere segnalato nel Log degli errori in modo che sia possibile eseguire azioni in base all'errore specifico.  
  
**Risoluzione:** Rivedere il modulo ed esaminare la query SQL per gli errori.  
  
 Verificare che la query funzioni correttamente all'esterno di Azure Machine Learning accedendo direttamente al server di database ed eseguire la query.  
  
 Se è presente un messaggio SQL generato segnalato dall'eccezione modulo, intervenire di conseguenza l'errore segnalato. Ad esempio, i messaggi di errore talvolta includono indicazioni specifiche sull'errore probabile:
+ *Il database mancante o non trovato colonna*, che indica che potrebbe aver digitato un nome di colonna non corretto. Se si conosce il nome della colonna sia corretto, provare a usare parentesi quadre o virgolette per racchiudere l'identificatore della colonna.
+ *SQL per la logica corretta in prossimità \<parola chiave SQL\>*, che indica che potrebbe essere un errore di sintassi prima della parola chiave specificata

  
|Messaggi di eccezione|  
|------------------------|  
|Script SQL non è corretto.|  
|Query SQL "{0}" non è corretto.|  
|Query SQL "{0}" non è corretto: {1}|  
  

## <a name="error-0070"></a>Errore 0070  
 Eccezione si verifica durante il tentativo di accedere alla tabella Azure inesistente.  
  
 Questo errore in Azure Machine Learning si verifica quando si tenta di accedere a una tabella Azure inesistente. Si riceverà questo errore se si specifica una tabella in archiviazione di Azure che non esiste durante la lettura o scrittura in archiviazione tabelle di Azure. Questa situazione può verificarsi se si digita il nome della tabella desiderata, o si dispone di una mancata corrispondenza tra il nome di destinazione e il tipo di archiviazione. Ad esempio, si intende leggere da una tabella ma immesso invece il nome di un blob.  
  
**Risoluzione:** Rivedere il modulo per verificare che il nome della tabella sia corretto.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tabelle di Azure non esiste.|  
|Tabelle di Azure "{0}" non esiste.|  
  
## <a name="error-0071"></a>Errore 0071  
 Si verifica un'eccezione se fornite credenziali non sono corrette.  
  
 Se le credenziali specificate non sono corretti, si verifica questo errore in Azure Machine Learning.  
  
 È anche possibile ricevere questo errore se il modulo non può connettersi a un cluster HDInsight.  
  
**Risoluzione:** Esaminare gli input per il modulo e verificare il nome dell'account e la password.  
  
 Verificare i problemi seguenti che possono causare un errore:  
  
-   Lo schema del set di dati non corrisponde allo schema di destinazione datatable.  
  
-   I nomi delle colonne sono mancanti o non è corretto  
  
-   Si sta scrivendo su una tabella contenente i nomi di colonna con caratteri non validi. In genere è possibile racchiudere tali nomi di colonna parentesi quadre, ma se il problema persiste, modificare i nomi di colonna da usare solo lettere e caratteri di sottolineatura (_)  
  
-   Le stringhe che si siano tentando di scrivere contenere virgolette singole  
  
 Se si sta provando a connettersi a un cluster HDInsight, verificare che il cluster di destinazione sia accessibile con le credenziali fornite.  
  
|Messaggi di eccezione|  
|------------------------|  
|Vengono passate credenziali non corrette.|  
|Nome utente non corretto "{0}" o password viene passata|  
  

## <a name="error-0072"></a>Errore 0072  
 Si verifica un'eccezione nel caso di timeout della connessione.  
  
 Questo errore in Azure Machine Learning si verifica quando una connessione va in timeout. Se non esistono attualmente problemi di connettività con l'origine dati o la destinazione, ad esempio la connettività internet lenta, o se il set di dati è grande e/o la query SQL per la lettura dei dati esegue un'elaborazione complessa, si riceverà l'errore.  
  
**Risoluzione:** Determinare se esistono attualmente problemi relativi a connessioni lente per archiviazione di Azure o da internet.  
  
|Messaggi di eccezione|  
|------------------------|  
|Si è verificato un timeout di connessione.|  
  

## <a name="error-0073"></a>Errore 0073  
 Se si verifica un errore durante la conversione di una colonna in un altro tipo, si verifica un'eccezione.  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile convertire la colonna in un altro tipo.  Si riceverà questo errore se un modulo richiede un tipo particolare e non è possibile convertire la colonna per il nuovo tipo.  
  
**Risoluzione:** Modificare il set di dati di input in modo che la colonna può essere convertita in base all'eccezione interna.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è stato possibile convertire la colonna.|  
|Non è riuscito a convertire la colonna a {0}.|  
  

## <a name="error-0074"></a>Errore 0074  
 Si verifica un'eccezione quando la [Edit Metadata](edit-metadata.md) prova a convertire una colonna di tipo sparse in categorico.  
  
 Questo errore in Azure Machine Learning si verifica quando la [Edit Metadata](edit-metadata.md) prova a convertire una colonna di tipo sparse in categorico.  Si riceverà questo errore quando si tenta di convertire le colonne di tipo sparse in categorico con il **rendere categoriche** opzione.  Azure machine Learning non supporta matrici sparse categoriche, il modulo avrà esito negativo.  
  
 <!--**Resolution:**
 Make the column dense by using [Convert to Dataset](convert-to-dataset.md) first or do not convert the column to categorical.  -->
  
|Messaggi di eccezione|  
|------------------------|  
|Colonne di tipo sparse non possono essere convertite in categorie.|  
  

## <a name="error-0075"></a>Errore 0075  
Eccezione si verifica quando viene utilizzata una funzione di binning non valida durante la quantizzazione di un set di dati.  
  
Questo errore in Azure Machine Learning viene generato quando si tenta di suddividere i dati usando un metodo non supportato oppure quando le combinazioni di parametri non sono validi.  
  
**Risoluzione:**

Gestione degli errori per questo evento è stato introdotto in una versione precedente di Azure Machine Learning che consente più la personalizzazione di creazione di contenitori per i metodi. Tutti i metodi di binning siano attualmente basati su una selezione da un elenco a discesa, tecnicamente che non deve più essere possibile che venga visualizzato questo errore.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->
  
|Messaggi di eccezione|  
|------------------------|  
|Funzione di binning non valida utilizzata.|  
  

## <a name="error-0077"></a>Errore 0077  
 Eccezione si verifica quando il file blob sconosciuta scrive modalità passata.  
  
 Se un argomento non valido viene passato nelle specifiche per un'origine o destinazione del file blob, si verifica questo errore in Azure Machine Learning.  
  
**Risoluzione:** In quasi tutti i moduli che importare o esportare dati da e verso archiviazione blob di Azure, vengono assegnati i valori dei parametri controllare la modalità di scrittura usando un elenco a discesa; Pertanto, non è possibile passare un valore non valido e questo errore non deve essere visualizzati. Questo errore verrà deprecato in una versione successiva.  
  
|Messaggi di eccezione|  
|------------------------|  
|Blob non supportato scrive modalità.|  
|Blob non supportato scrive modalità: {0}.|  
  

## <a name="error-0078"></a>Errore 0078  
 Si verifica un'eccezione quando l'opzione di HTTP per [Import Data](import-data.md) riceve un codice di stato 3xx che indica il reindirizzamento.  
  
 In Azure Machine Learning questo errore si verifica quando l'opzione di HTTP per [Import Data](import-data.md) riceve un 3xx (301, 302, 304, e così via) codice di stato che indica il reindirizzamento. Si riceverà questo errore se si prova a connettersi a un'origine HTTP che reindirizza il browser a un'altra pagina. Per la sicurezza motivi, il reindirizzamento di siti Web non sono consentiti come origini dati per Azure Machine Learning.  
  
**Risoluzione:** Se il sito Web è un sito Web attendibili, è possibile immettere direttamente l'URL reindirizzato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Reindirizzamento HTTP non consentito|  
  

## <a name="error-0079"></a>Errore 0079  
 Si verifica un'eccezione se il nome di contenitore di archiviazione di Azure è stato specificato correttamente.  
  
 Se il nome del contenitore di archiviazione di Azure è stata specificata correttamente, si verifica questo errore in Azure Machine Learning. Si riceverà questo errore se non è stato specificato il contenitore e il nome di blob (file) usando **il percorso in un blob che inizia con contenitore** opzione durante la scrittura in archiviazione Blob di Azure.  
  
**Risoluzione:** Rivedere le [Export Data](export-data.md) modulo e verificare che il percorso specificato per il blob contiene sia il contenitore e il nome di file, nel formato **contenitore/filename**.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il nome del contenitore di archiviazione di Azure non è corretto.|  
|Il nome del contenitore di archiviazione di Azure "{0}" non è corretto; è previsto un nome di contenitore del formato contenitore/blob.|  
  

## <a name="error-0080"></a>Errore 0080  
 Eccezione si verifica quando la colonna con tutti i valori mancanti non è consentita dal modulo.  
  
 Questo errore in Azure Machine Learning viene generato quando uno o più colonne utilizzate dal modulo contiene tutti i valori mancanti. Ad esempio, se un modulo è computing statistiche aggregate per ogni colonna, non può funzionare su una colonna che non contengono dati. In questi casi, l'esecuzione del modulo viene interrotta con questa eccezione.  
  
**Risoluzione:** Rivedere il set di dati di input e rimuovere tutte le colonne che contengono tutti i valori mancanti.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non sono consentite colonne con tutti i valori mancanti.|  
|Colonna {0} dispone di tutti i valori mancanti.|  
  

## <a name="error-0081"></a>Errore 0081  
 Si verifica un'eccezione nel modulo PCA se il numero di dimensioni per ridurre a è uguale al numero di colonne di funzioni nel set di dati input contenente almeno una colonna di funzioni di tipo sparse.  
  
 Questo errore in Azure Machine Learning viene generato se vengono soddisfatte le condizioni seguenti: (a) il set di dati di input dispone di almeno una colonna di tipo sparse e (b) numero finale di dimensioni richiesto è identico al numero di dimensioni di input.  
  
**Risoluzione:** Provare a ridurre il numero di dimensioni nell'output sia inferiore al numero di dimensioni nell'input. Questo è tipico nelle applicazioni di analisi in componenti principali.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->
  
|Messaggi di eccezione|  
|------------------------|  
|Per set di dati contenente le colonne di funzioni di tipo sparse di dimensioni per ridurre il numero deve essere minore numero di colonne di funzioni.|  
 

## <a name="error-0082"></a>Errore 0082  
 Eccezione si verifica quando un modello non può essere deserializzato.  
  
 Questo errore in Azure Machine Learning si verifica quando un modello salvato di apprendimento o trasformazione non può essere caricata da una versione più recente del runtime di Azure Machine Learning in seguito a una modifica di rilievo.  
  
**Risoluzione:** L'esperimento di training che ha generato il modello o la trasformazione è necessario eseguire di nuovo e il modello o trasformazione deve essere salvato nuovamente.  
  
|Messaggi di eccezione|  
|------------------------|  
|Modello non è stato possibile deserializzare perché probabilmente è stato serializzato con un formato di serializzazione precedente. Ripetere il training e salvare di nuovo il modello.|  
  

## <a name="error-0083"></a>Errore 0083  
 Si verifica un'eccezione se usare set di dati per training può essere usato per il tipo concreto dello strumento di apprendimento.  
  
 Questo errore in Azure Machine Learning viene generato quando il set di dati non è compatibile con lo strumento di apprendimento viene eseguito il training. Ad esempio, il set di dati potrebbe contenere almeno un valore mancante in ogni riga e di conseguenza, l'intero set di dati viene ignorato durante il training. In altri casi, alcuni algoritmi di machine learning, ad esempio il rilevamento delle anomalie non prevede che le etichette deve essere presente e possono generare questa eccezione se le etichette sono presenti nel set di dati.  
  
**Risoluzione:** Consultare la documentazione dello strumento di apprendimento usata per verificare i requisiti per il set di dati di input. Esaminare le colonne per visualizzare tutte le colonne necessarie siano presenti.  
  
|Messaggi di eccezione|  
|------------------------|  
|Set di dati usato per il training non è valido.|  
|{0} contiene dati non validi per il training.|  
|{0} contiene dati non validi per il training. Tipo di strumento di apprendimento: {1}.|  
  

## <a name="error-0084"></a>Errore 0084  
 Eccezione si verifica quando vengono valutati dei punteggi prodotti da uno Script R. Attualmente non è supportata.  
  
 Se si prova a usare uno dei moduli per la valutazione di un modello con l'output da uno script R che contiene i punteggi, si verifica questo errore in Azure Machine Learning.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|La valutazione dei punteggi prodotti da R è attualmente supportata.|  
  

## <a name="error-0085"></a>Errore 0085  
 Eccezione si verifica quando la valutazione dello script genera un errore.  
  
 Questo errore in Azure Machine Learning si verifica quando si esegue uno script personalizzato che contiene gli errori di sintassi.  
  
**Risoluzione:** Esaminare il codice in un editor esterno e verificare gli errori.  
  
|Messaggi di eccezione|  
|------------------------|  
|Errore durante la valutazione dello script.|  
|Si è verificato l'errore seguente durante la valutazione di script e visualizzare il log di output per altre informazioni:---inizio del messaggio di errore dal {0} interprete--- {1} ---fine del messaggio di errore da {0} interprete-- ------|  
  

## <a name="error-0086"></a>Errore 0086  
 Eccezione si verifica quando una trasformazione conteggio non è valida.  
  
 Quando si seleziona una trasformazione in base a una tabella di conteggio, ma la trasformazione selezionata non è compatibile con i dati correnti o con la nuova tabella di conteggio, si verifica questo errore in Azure Machine Learning.  
  
**Risoluzione:** Il modulo supporta salvando i conteggi e le regole che compongono la trasformazione in due formati diversi. Se si uniscono le tabelle di conteggio, verificare che entrambe le tabelle che si prevede di unire utilizzino lo stesso formato.  
  
In generale, una trasformazione basata sui conteggi applicabile solo ai set di dati che hanno lo stesso schema del set di dati in cui è stato originariamente creato il file di trasformazione.  
  
 <!-- For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Messaggi di eccezione|  
|------------------------|  
|Trasformazione del conteggio sulla non valida specificata.|  
|La trasformazione del conteggio sulla porta di input '{0}' non è valido.|  
|La trasformazione del conteggio sulla porta di input '{0}'non può essere unito con la trasformazione del conteggio sulla porta di input'{1}'. Controllare per verificare i metadati utilizzati per il conteggio corrispondano.|  
  

## <a name="error-0087"></a>Errore 0087  
 Eccezione si verifica quando viene specificato un tipo di tabella di conteggio non valido per l'apprendimento con i moduli di conteggio.  
  
 Quando si prova a importare una tabella di conteggio esistente, ma la tabella non è compatibile con i dati correnti o con la nuova tabella di conteggio, si verifica questo errore in Azure Machine Learning.  
  
**Risoluzione:** Esistono diversi formati di salvataggio i conteggi e le regole che compongono la trasformazione. Se si uniscono le tabelle di conteggio, verificare che usano lo stesso formato.  
  
 In generale, una trasformazione basata sui conteggi applicabile solo ai set di dati che hanno lo stesso schema del set di dati in cui è stato originariamente creato il file di trasformazione.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). -->
  

## <a name="error-0088"></a>Errore 0088  
 Eccezione si verifica quando un conteggio non valido viene specificato per l'apprendimento con i moduli di conteggio.  
  
 Questo errore in Azure Machine Learning si verifica quando si tenta di usare un metodo di conteggio diverso da quello è supportata per la funzionalità basate sui conteggi.  
  
**Risoluzione:** In generale, metodi di conteggio vengono scelti da un elenco a discesa, in modo che non dovrebbe essere visualizzato questo errore.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Messaggi di eccezione|  
|------------------------|  
|Tipo di conteggio non valido specificato.|  
|Il tipo di conteggio specificato '{0}' non è un tipo valido.|  
  

## <a name="error-0089"></a>Errore 0089  
 Eccezione si verifica quando il numero di classi specificato è inferiore al numero effettivo di classi in un set di dati utilizzato per il conteggio.  
  
 Quando si sta creando una tabella di conteggio e la colonna di etichetta contiene un numero diverso di classi rispetto a quanto specificato nei parametri del modulo, si verifica questo errore in Azure Machine Learning.  
  
**Risoluzione:** Controllare il set di dati e scoprire esattamente come molti valori distinti (le possibili classi) sono presenti nella colonna di etichetta. Quando si crea la tabella di conteggi, è necessario specificare almeno questo numero di classi.  
  
 La tabella di conteggio non è possibile determinare automaticamente il numero di classi disponibili.  
  
 Quando si crea la tabella di conteggi, è possibile specificare 0 o tutti i numeri che è inferiore al numero effettivo di classi nella colonna di etichetta.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il numero di classi non è corretto. Assicurarsi che il numero di classi specificato nel riquadro del parametro è maggiore o uguale al numero di classi nella colonna di etichetta.|  
|Il numero di classi specificato è '{0}', che non è maggiore del valore di etichetta'{1}' nel set di dati usato da contare. Assicurarsi che il numero di classi specificato nel riquadro del parametro è maggiore o uguale al numero di classi nella colonna di etichetta.|  
  

## <a name="error-0090"></a>Errore 0090  
 Eccezione si verifica quando si verifica un errore di creazione di una tabella Hive.  
  
 Questo errore in Azure Machine Learning si verifica quando si usa [Export Data](export-data.md) o un'altra opzione per salvare i dati in un cluster HDInsight e la tabella Hive specificata non è possibile creare.  
  
**Risoluzione:** Controllare il nome di account di archiviazione di Azure associato al cluster e verificare che si stia utilizzando lo stesso account nelle proprietà del modulo.  
  
|Messaggi di eccezione|  
|------------------------|  
|Impossibile creare la tabella Hive. Per un cluster HDInsight, verificare che il nome di account di archiviazione di Azure associato cluster equivale ciò che viene passato tramite il parametro del modulo.|  
|La tabella Hive "{0}" non è stato possibile creare. Per un cluster HDInsight, verificare che il nome di account di archiviazione di Azure associato cluster equivale ciò che viene passato tramite il parametro del modulo.|  
|La tabella Hive "{0}" non è stato possibile creare. Per un cluster HDInsight, verificare che il nome di account di archiviazione di Azure associato al cluster è "{1}".|  
 

## <a name="error-0100"></a>Errore 0100  
 Eccezione si verifica quando viene specificata una lingua non supportata per un modulo personalizzato.  
  
 Questo errore in Azure Machine Learning si verifica quando si compila un modulo personalizzato e la proprietà name del **linguaggio** elemento in un file di definizione xml di modulo personalizzato ha un valore non valido. Attualmente, l'unico valore valido per questa proprietà è `R`. Ad esempio:   
  
 `<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Risoluzione:** Verificare che la proprietà name del **Language** elemento nel file di definizione xml del modulo personalizzato viene impostato su `R`. Salvare il file, aggiornare il pacchetto di file zip del modulo personalizzate e provare ad aggiungere nuovamente il modulo personalizzato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Linguaggio del modulo personalizzato non supportato specificato|  
  

## <a name="error-0101"></a>Errore 0101  
 Tutti gli ID porta e di parametro devono essere univoci.  
  
 Questo errore in Azure Machine Learning si verifica quando uno o più porte o i parametri vengono assegnati lo stesso valore di ID in un file di definizione XML di modulo personalizzato.  
  
**Risoluzione:** Verificare che i valori di ID tra tutte le porte e i parametri siano univoci. Salvare il file xml, aggiornare il pacchetto di file zip del modulo personalizzate e provare ad aggiungere nuovamente il modulo personalizzato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tutte le porte e parametro ID per un modulo deve essere univoco|  
|Modulo '{0}' dispone di porta/argomento gli ID duplicato. Tutti gli ID porta/argomento devono essere univoci per un modulo.|  
  

## <a name="error-0102"></a>Errore 0102  
 Generata quando non è possibile estrarre un file con estensione ZIP.  
  
 Questo errore in Azure Machine Learning si verifica quando si importa un pacchetto compresso con estensione zip, ma il pacchetto non si trova un file con estensione zip o il file non usa un formato zip supportati.  
  
**Risoluzione:** Assicurarsi che il file selezionato è un file zip valido e che il file è stato compresso usando uno degli algoritmi di compressione supportati.  
  
 Se questo errore si verifica quando si importano i set di dati in formato compresso, verificare che tutti i file inclusi è usano uno dei formati di file supportati e sono disponibili in formato Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->
  
 Provare a aggiungere nuovamente i file desiderati a una nuova cartella compressa e provare ad aggiungere nuovamente il modulo personalizzato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Specificato con estensione ZIP file non è nel formato corretto|  


## <a name="error-0103"></a>Errore 0103  
 Generata quando un file ZIP non contiene tutti i file con estensione XML  
  
 Quando il pacchetto zip modulo personalizzato non contiene alcun file di definizione (con estensione XML) del modulo, si verifica questo errore in Azure Machine Learning. Questi file devono trovarsi nella radice del pacchetto zip (ad esempio, non all'interno di una sottocartella).  
  
**Risoluzione:** Verificare che siano uno o più file di definizione del modulo xml nella cartella radice del pacchetto zip è necessario fare riferimento a una cartella temporanea sul disco rigido. Tutti i file xml devono essere direttamente nella cartella di cui che è stato estratto il pacchetto zip. Assicurarsi che quando si crea il pacchetto zip che non si seleziona una cartella che contiene i file xml per comprimere come questa verrà creata una sottocartella all'interno del pacchetto con estensione zip con lo stesso nome di cartella selezionata da comprimere.  
  
|Messaggi di eccezione|  
|------------------------|  
|Specificato con estensione ZIP file non contiene alcun file di definizione moduli (file con estensione XML)|  


## <a name="error-0104"></a>Errore 0104  
 Generata quando un file di definizione del modulo fa riferimento a uno script che non è stato trovato  
  
 Questo errore in Azure Machine Learning viene generato quando si fa riferimento a un file di definizione xml di modulo personalizzato in un file script di **linguaggio** elemento che non esiste nel pacchetto zip. Il percorso del file script è definito nel **sourceFile** proprietà delle **Language** elemento. Il percorso del file di origine è relativo alla radice del pacchetto zip (stesso percorso dei file di definizione xml di modulo). Se il file di script è in una sottocartella, è necessario specificare il percorso relativo del file di script. Ad esempio, se tutti gli script sono stati archiviati in un **myScripts** cartella all'interno del pacchetto con estensione zip, la **Language** sarebbe necessario aggiungere il percorso all'elemento il **sourceFile** proprietà come di seguito. Ad esempio:   
  
 `<Language name="R" sourceFile="myScripts/CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Risoluzione:** Assicurarsi che il valore della **sourceFile** proprietà nel **Language** elemento della definizione xml del modulo personalizzato sia corretto e che il file di origine esista nel percorso relativo corretto nel pacchetto zip.  
  
|Messaggi di eccezione|  
|------------------------|  
|Riferimento file di script R non esiste.|  
|Riferimento file di script R '{0}' non è stata trovata. Assicurarsi che il percorso relativo del file sia corretto dal percorso delle definizioni.|  


## <a name="error-0105"></a>Errore 0105  
 Questo errore viene visualizzato quando un file di definizione del modulo contiene un tipo di parametro non supportato  
  
 Questo errore in Azure Machine Learning viene generato quando la si crea una definizione xml di modulo personalizzato e il tipo di un parametro o un argomento nella definizione non corrisponde un tipo supportato.  
  
**Risoluzione:** Assicurarsi che la proprietà type di qualsiasi **Arg** elemento nel file di definizione xml del modulo personalizzato è un tipo supportato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tipo di parametro non supportato.|  
|Tipo di parametro non supportato '{0}' specificato.|  


## <a name="error-0106"></a>Errore 0106  
 Generata quando un file di definizione del modulo viene definito un tipo di input non supportato  
  
 Questo errore in Azure Machine Learning viene generato quando il tipo di una porta di input in un modulo personalizzato di definizione XML corrisponde a un tipo supportato.  
  
**Risoluzione:** Assicurarsi che la proprietà type di un elemento di Input nel file di definizione XML del modulo personalizzato è un tipo supportato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tipo di input non supportato.|  
|Tipo di input non supportato '{0}' specificato.|  


## <a name="error-0107"></a>Errore 0107  
 Generata quando un file di definizione del modulo viene definito un tipo di output non supportato  
  
 Questo errore in Azure Machine Learning viene generato quando il tipo di una porta di output in una definizione xml di modulo personalizzato non corrisponde a un tipo supportato.  
  
**Risoluzione:** Assicurarsi che la proprietà type di un elemento di Output nel file di definizione xml del modulo personalizzato è un tipo supportato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tipo di output non supportato.|  
|Tipo di output non supportato '{0}' specificato.|  


## <a name="error-0108"></a>Errore 0108  
 Viene generato quando un file di definizione del modulo definito più inpue o outpue porte di quelle supportate  
  
 Questo errore in Azure Machine Learning viene generato quando troppe porte di input o outpue vengono definite in una definizione xml di modulo personalizzato.  
  
**Risoluzione:** Garantisce che il numero massimo di porte di input e outpue definiti nella definizione xml del modulo personalizzato non superi il numero massimo di porte supportate.  
  
|Messaggi di eccezione|  
|------------------------|  
|È stato superato il numero di porte di input o outpue supportato.|  
|È stato superato il numero di supportata '{0}' porte. Numero massimo consentito di '{0}'è di porte'{1}'.| 

## <a name="error-0109"></a>Errore 0109  
 Generata quando un file di definizione del modulo viene definito un selettore di colonna in modo non corretto  
  
 Questo errore in Azure Machine Learning viene generato quando la sintassi per un argomento del selettore di colonna contiene un errore in una definizione xml di modulo personalizzato.  
  
**Risoluzione:** Questo errore viene generato quando la sintassi per un argomento del selettore di colonna contiene un errore in una definizione xml di modulo personalizzato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Sintassi non supportata per il selettore di colonna.|  
  

## <a name="error-0110"></a>Errore 0110  
 Generata quando un file di definizione di modulo viene definito un selettore di colonna che fa riferimento a un ID porta di input non esistente  
  
 Questo errore in Azure Machine Learning viene generato quando il *portId* proprietà all'interno dell'elemento di proprietà di un argomento di tipo ColumnPicker non corrisponde al valore di ID di una porta di input.  
  
**Risoluzione:** Assicurarsi che la proprietà portId corrisponde al valore di ID di una porta di input definito nella definizione xml del modulo personalizzato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il selettore di colonna fa riferimento a un ID porta di input non esistente.|  
|Il selettore di colonna fa riferimento a un ID porta di input non esistente '{0}'.|  
  

## <a name="error-0111"></a>Errore 0111  
 Viene generato quando un file di definizione del modulo definito una proprietà non valida  
  
 Questo errore in Azure Machine Learning viene generato quando una proprietà non valida viene assegnata a un elemento del modulo personalizzate di definizione XML.  
  
**Risoluzione:** Assicurarsi che la proprietà è supportata dall'elemento di modulo personalizzato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Definizione della proprietà non è valida.|  
|Definizione della proprietà '{0}' non è valido.|  
  

## <a name="error-0112"></a>Errore 0112  
 Generata quando non è possibile analizzare un file di definizione moduli  
  
 Questo errore in Azure Machine Learning viene generato quando si verifica un errore in formato xml che impedisce il modulo personalizzato di definizione XML da analizzato come un file XML valido.  
  
**Risoluzione:** Assicurarsi che ogni elemento viene aperto e chiuso correttamente. Assicurarsi che non siano presenti errori nella formattazione XML.  
  
|Messaggi di eccezione|  
|------------------------|  
|Impossibile analizzare il file di definizione del modulo.|  
|Impossibile analizzare il file di definizione del modulo '{0}'.|  
  

## <a name="error-0113"></a>Errore 0113  
 Generata quando un file di definizione del modulo contiene errori.  
  
 Questo errore in Azure Machine Learning viene generato quando il file di definizione XML di modulo personalizzato può essere analizzato ma contiene errori, ad esempio di definizione degli elementi non supportati da moduli personalizzati.  
  
**Risoluzione:** Assicurarsi che il file di definizione del modulo personalizzato definisce gli elementi e le proprietà supportate dai moduli personalizzati.  
  
|Messaggi di eccezione|  
|------------------------|  
|File di definizione del modulo contiene errori.|  
|File di definizione del modulo '{0}' contiene errori.|  
|File di definizione del modulo '{0}' contiene errori. {1}|  
  

## <a name="error-0114"></a>Errore 0114  
 Generata quando si compila un modulo personalizzato ha esito negativo.  
  
 Questo errore in Azure Machine Learning viene generato quando la compilazione di un modulo personalizzato non riesce. Ciò si verifica quando uno o più personalizzati relativi al modulo di errori durante l'aggiunta del modulo personalizzato. Gli altri errori vengono segnalati all'interno di questo messaggio di errore.  
  
**Risoluzione:** Risolvere gli errori segnalati all'interno di questo messaggio di eccezione.  
  
|Messaggi di eccezione|  
|------------------------|  
|Impossibile compilare il modulo personalizzato.|  
|Modulo personalizzato si basa non riuscita con errori: {0}|  
  

## <a name="error-0115"></a>Errore 0115  
 Generata quando uno script predefinito modulo personalizzato presenta un'estensione non supportata.  
  
 Questo errore in Azure Machine Learning si verifica quando viene fornito uno script per un modulo personalizzato che usa un'estensione di file sconosciuti.  
  
**Risoluzione:** Verificare l'estensione di formato e il nome di file di qualsiasi file di script inclusi nel modulo personalizzato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non supportata dell'estensione per script predefinito.|  
|Estensione di file non supportato {0} per uno script predefinito.|  
  

## <a name="error-0121"></a>Errore 0121  
 Generata quando SQL scrive ha esito negativo perché la tabella non è scrivibile  
  
 Questo errore in Azure Machine Learning viene generato quando si usa la [esportazione di dati](export-data.md) modulo per salvare i risultati in una tabella in un database SQL e la tabella non può essere scritto. In genere, verrà visualizzato questo errore se il [esportazione di dati](export-data.md) modulo correttamente stabilisce una connessione con l'istanza di SQL Server, ma non può quindi scrivere il contenuto del set di dati di Azure Machine Learning nella tabella.  
  
**Risoluzione:**
 - Aprire il riquadro delle proprietà del [esportazione di dati](export-data.md) modulo e verificare che i nomi di database e tabella siano state immesse correttamente. 
 - Esaminare lo schema del set di dati si sta esportando e assicurarsi che i dati siano compatibili con la tabella di destinazione.
 - Verificare che il codice SQL l'accesso associato al nome utente e password disponga delle autorizzazioni per scrivere nella tabella. 
 - Se l'eccezione contiene informazioni aggiuntive sull'errore da SQL Server, utilizzare tali informazioni per apportare correzioni.  
  
|Messaggi di eccezione|  
|------------------------|  
|Connesso al server, non è possibile scrivere alla tabella.|  
|Impossibile scrivere nella tabella Sql: {0}|  


## <a name="error-0122"></a>Errore 0122  
 Si verifica un'eccezione se sono specificate più colonne di peso e ne è consentita.  
  
 Questo errore in Azure Machine Learning si verifica quando sono state selezionate troppe colonne come colonne di peso.  
  
**Risoluzione:** Esaminare il set di dati di input e i relativi metadati. Assicurarsi che solo una colonna contiene i pesi.  
  
|Messaggi di eccezione|  
|------------------------|  
|Sono specificate più colonne di peso.|  


## <a name="error-0123"></a>Errore 0123  
 Si verifica un'eccezione se colonna di vettori viene specificata alla colonna di etichetta.  
  
 Se si usa un vettore come colonna etichetta, si verifica questo errore in Azure Machine Learning.  
  
**Risoluzione:** Modificare il formato di dati della colonna se necessario, o scegliere una colonna diversa.  
  
|Messaggi di eccezione|  
|------------------------|  
|Colonna di vettori viene specificata come colonna etichetta.|  


## <a name="error-0124"></a>Errore 0124  
 Si verifica un'eccezione se le colonne numeriche vengono specificate sia la colonna di ponderazione.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|Una colonna non numerica viene specificata come colonna di ponderazione.|  
  


## <a name="error-0125"></a>Errore 0125  
 Generata quando lo schema per più set di dati non corrisponde.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|Schema DataSet non corrisponde.|  


## <a name="error-0126"></a>Errore 0126  
 Si verifica un'eccezione se l'utente specifica un dominio SQL non è supportato in Azure Machine Learning.  
  
 Questo errore viene generato quando l'utente specifica un dominio SQL non è supportato in Azure Machine Learning. Si riceverà questo errore se si sta tentando di connettersi a un server di database in un dominio che non è consentito. Attualmente, i domini SQL consentiti sono: ". database.windows.net",". cloudapp.net", o ". database.secure.windows.net". Vale a dire, il server deve essere un server SQL di Azure o un server in una macchina virtuale in Azure.  
  
**Risoluzione:** Rivedere il modulo. Verificare che il server di database SQL a cui appartiene a uno dei domini accettati:  
  
-   .database.windows.net  
  
-   .cloudapp.net  
  
-   .database.secure.windows.net  
  
|Messaggi di eccezione|  
|------------------------|  
|Dominio SQL non supportato.|  
|Il dominio SQL {0} non è attualmente supportato in Azure Machine Learning|  
  

## <a name="error-0127"></a>Errore 0127  
 Dimensioni in pixel dell'immagine superano il limite consentito  
  
 Questo errore si verifica se si legge le immagini da un set di dati di immagine per la classificazione e le immagini sono maggiori del grado di gestire il modello.  
  
 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  
  
-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->
  
|Messaggi di eccezione|  
|------------------------|  
|Dimensioni in pixel dell'immagine superano il limite consentito.|  
|Le dimensioni in pixel nel file di immagine '{0}' superano il limite consentito: '{1}'|  


## <a name="error-0128"></a>Errore 0128  
 Superato il numero delle probabilità condizionali per le colonne categoriche.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|Superato il numero delle probabilità condizionali per le colonne categoriche.|  
|Superato il numero delle probabilità condizionali per le colonne categoriche. Colonne{0}'e'{1}' sono la coppia di problematica.|  


## <a name="error-0129"></a>Errore 0129  
 Numero di colonne nel set di dati supera il massimo consentito.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|Numero di colonne nel set di dati supera il massimo consentito.|  
|Numero di colonne nel set di dati in '{0}'supera consentiti.'|  
|Numero di colonne nel set di dati in '{0}'supera il limite consentito di'{1}'.'|  
|Numero di colonne nel set di dati in '{0}'supera consentiti'{1}'con limite di'{2}'.'|  
## <a name="error-0130"></a>Errore 0130  
 Eccezione si verifica quando tutte le righe del set di dati di training contengono valori mancanti.  
  
 Ciò si verifica quando alcune colonne del set di dati di training sono vuota.  
  
**Risoluzione:** Usare la [Clean Missing Data](clean-missing-data.md) modulo per rimuovere le colonne con tutti i valori mancanti.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tutte le righe nel set di dati di training contengono valori mancanti.  È consigliabile usare il modulo Clean Missing Data per rimuovere valori mancanti.|  
 

## <a name="error-0131"></a>Errore 0131  
 Si verifica un'eccezione se uno o più set di dati in un file zip non riesce a essere decompressa e registrata correttamente  
  
 Questo errore viene generato quando uno o più set di dati in un file zip non riesce a essere decompresso e leggere in modo corretto. Si riceverà questo errore se la decompressione ha esito negativo perché il file con estensione zip o uno dei file in essa è danneggiato o si verifica un errore di sistema durante il tentativo di decomprimere ed espandere un file.  
  
**Risoluzione:** Usare i dettagli forniti nel messaggio di errore per determinare come procedere.  
  
|Messaggi di eccezione|  
|------------------------|  
|Caricare set di dati compresso non è riuscita|  
|Set di dati compressi {0} non è riuscita con messaggio analogo al seguente: {1}|  
|Set di dati compressi {0} non è riuscita con un {1} eccezione con messaggio: {2}|  
  

## <a name="error-0132"></a>Errore 0132  
 Per decomprimere; è stato specificato alcun nome file sono stati trovati più file in file con estensione zip.  
  
 Questo errore viene generato quando è stato specificato alcun nome file per l'installazione del pacchetto; sono stati trovati più file in file con estensione zip. Si riceverà questo errore se il file con estensione zip contiene più di un file compresso, ma non è stato specificato un file per l'estrazione nella **set di dati per la decompressione** nella casella di testo nel **proprietà** riquadro del modulo. Attualmente, è possibile estrarre un solo file ogni volta che viene eseguito il modulo.  
  
**Risoluzione:** Il messaggio di errore fornisce un elenco dei file disponibili nel file con estensione zip. Copiare il nome del file desiderato e incollarlo nella **set di dati per la decompressione** casella di testo.  
  
|Messaggi di eccezione|  
|------------------------|  
|File con estensione zip contiene più file. è necessario specificare il file da espandere.|  
|Il file contiene più di un file. Specificare il file da espandere. Sono stati trovati i file seguenti: {0}|  
  

## <a name="error-0133"></a>Errore 0133  
 Il file specificato non è stato trovato nel file zip  
  
 Questo errore viene generato quando il nome file immesso nella **set di dati per la decompressione** campo le **proprietà** riquadro non corrisponde al nome di ogni file trovato nel file con estensione zip. Le cause più comuni di questo errore sono un errore di digitazione o la ricerca nel file di archivio non corretto per il file da espandere.  
  
**Risoluzione:** Rivedere il modulo. Se il nome del file che si desiderava decomprimere viene visualizzato nell'elenco di file trovati, copiare il nome del file e incollarlo nella **set di dati per la decompressione** finestra delle proprietà. Se non è possibile visualizzare il nome del file desiderato nell'elenco, verificare di avere il file ZIP corretto e il nome corretto per il file desiderato.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il file specificato non è stato trovato il file zip int.|  
|Il file specificato non è stato trovato. Trovare i file seguenti: {0}|  
  

## <a name="error-0134"></a>Errore 0134
Eccezione si verifica quando la colonna etichetta manca o con numero di righe con etichette insufficiente.  
  
Questo errore si verifica quando il modulo richiede una colonna di etichetta, ma non sono inclusi uno nella selezione della colonna o la colonna di etichetta manca troppi valori.

Questo errore può verificarsi anche quando un'operazione precedente viene modificato il set di dati in modo che siano disponibili per un'operazione a valle righe insufficienti. Ad esempio, si supponga di utilizza un'espressione nel **Partition and Sample** modulo dividere un set di dati per i valori. Se viene trovata alcuna corrispondenza per l'espressione, uno dei set di dati risultante dalla partizione potrebbe essere vuoto.

Risoluzione: 

 Se si include una colonna di etichetta nella selezione colonna, ma non è riconosciuto, usare il [Edit Metadata](edit-metadata.md) modulo a contrassegnarla come colonna etichetta.
  
  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->È quindi possibile usare la [Clean Missing Data](clean-missing-data.md) modulo per rimuovere le righe con valori mancanti nella colonna di etichetta. 

 Controllare il set di dati di input per assicurarsi che contengono dati validi e un numero di righe sufficiente per soddisfare i requisiti dell'operazione. Molti algoritmi genererà un messaggio di errore se richiedono alcune righe numero minimi di dati, ma i dati contengono solo alcune righe o solo un'intestazione.
  
|Messaggi di eccezione|
|------------------------|
|Eccezione si verifica quando la colonna etichetta manca o con numero di righe con etichette insufficiente.|  
|Si verifica un'eccezione quando la colonna etichetta è manca o è minore di {0} con etichetta righe|  
  

## <a name="error-0135"></a>Errore 0135  
 Solo i cluster basati su centroide è supportato.  
  
**Risoluzione:** Questo messaggio di errore riscontrati se si è provato a valutare un modello di clustering basato su un algoritmo di clustering personalizzato che non usa centroidi per inizializzare il cluster.  
  
  <!--You can use [Evaluate Model](evaluate-model.md) to evaluate clustering models that are based on the  [K-Means Clustering](k-means-clustering.md) module. For custom algorithms, use the [Execute R Script](execute-r-script.md) module to create a custom evaluation script.  -->
  
|Messaggi di eccezione|  
|------------------------|  
|Solo i cluster basati su centroide è supportato.|  
  

## <a name="error-0136"></a>Errore 0136  
 È stato restituito alcun nome file. Impossibile elaborare il file di conseguenza.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|È stato restituito alcun nome file. Impossibile elaborare il file di conseguenza.|  
  

## <a name="error-0137"></a>Errore 0137  
 SDK di archiviazione di Azure ha rilevato un errore durante la conversione tra le proprietà della tabella e le colonne di set di dati durante la lettura o scrittura.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|Errore di conversione tra colonne di proprietà e set di dati di archiviazione tabelle di Azure.|  
|Errore di conversione tra colonne di proprietà e set di dati di archiviazione tabelle di Azure. Informazioni aggiuntive: {0}|  

## <a name="error-0138"></a>Errore 0138  
 Memoria esaurita, non è possibile esecuzione completata del modulo. Il sottocampionamento il set di dati può aiutare a risolvere il problema.  
  
 Questo errore si verifica quando il modulo che è in esecuzione richiede più memoria rispetto a quella disponibile nel contenitore di Azure. Questa situazione può verificarsi se si lavora con un set di dati di grandi dimensioni e l'operazione corrente non è disponibile memoria sufficiente.  
  
**Risoluzione:** Se si sta tentando di leggere un set di dati di grandi dimensioni e non è possibile completare l'operazione, può essere utile il sottocampionamento il set di dati.  
  
  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  
  
 Sometimes transient loads can lead to this error. Machine support also changes over time. 
  
 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->
  
|Messaggi di eccezione|  
|------------------------|  
|Memoria esaurita, non è possibile esecuzione completata del modulo.|  
  

## <a name="error-0139"></a>Errore 0139  
 Eccezione si verifica quando non è possibile convertire una colonna in un altro tipo.  
  
 Quando si tenta di convertire una colonna in un tipo di dati diverso, ma che tipo non è supportato dall'operazione corrente o dal modulo, si verifica questo errore in Azure Machine Learning.  
  
 L'errore potrebbe essere visualizzato anche quando un modulo tenta di convertire in modo implicito i dati per soddisfare i requisiti del modulo corrente, ma la conversione non è possibile.  
  
**Risoluzione:**

1. Esaminare i dati di input e determinare il tipo di dati esatto della colonna che si desidera utilizzare e il tipo di dati della colonna che si verifica l'errore. In alcuni casi si potrebbe pensare al tipo di dati sia corretto, ma trova che un'operazione upstream è stato modificato il tipo di dati o l'utilizzo di una colonna. Usare la [Edit Metadata](edit-metadata.md) modulo reimpostare i metadati delle colonne allo stato originale. 
2. Esaminare la pagina della Guida del modulo per verificare i requisiti per l'operazione specificata. Determinare i tipi di dati supportati dal modulo corrente e quali intervallo di valori è supportata. 
 <!--3. If values need to be truncated, rounded, or outliers removed, use the [Apply Math Operation](apply-math-operation.md) or [Clip Values](clip-values.md) modules to make corrections.-->
4. È consigliabile se è possibile convertire o eseguire il cast di colonna da un tipo di dati diverso. Tutti i moduli seguenti offrono una notevole flessibilità e potenza per la modifica dei dati: 
 <!--
   + [Apply SQL Transformation](apply-sql-transformation.md)
   + [Execute R Script](execute-r-script.md)
-->   
   + [Esegui Script Python](execute-python-script.md).  

> [!NOTE]
> Ancora non funziona? Si consiglia di fornire commenti e suggerimenti aggiuntivi sul problema, per consentire agli utenti di sviluppare meglio alla risoluzione dei problemi. È sufficiente inviare commenti e suggerimenti in questa pagina e fornire il nome del modulo che ha generato l'errore e la conversione di tipi di dati che non è riuscita.
  
|Messaggi di eccezione|  
|------------------------|  
|Non è consentita la conversione.|  
|Non è stato possibile convertire: {0}.|  
|Non è stato possibile convertire: {0}, nella riga {1}.|  
|Impossibile convertire la colonna di tipo {0} alla colonna di tipo {1} nella riga {2}.|  
|Impossibile convertire la colonna "{2}" di tipo {0} alla colonna di tipo {1} nella riga {3}.|  
|Impossibile convertire la colonna "{2}" di tipo {0} alla colonna "{3}" di tipo {1} sulla riga {4}.| 

## <a name="error-0140"></a>Errore 0140  
 Si verifica un'eccezione se viene passato argomento di set di colonne non contiene altre colonne ad eccezione di colonna di etichetta.  
  
 Questo errore si verifica se un set di dati si è connessi a un modulo che richiede più colonne, incluse le funzionalità, ma è stato specificato solo la colonna di etichetta.  
  
**Risoluzione:** Scegliere almeno una colonna di funzionalità da includere nel set di dati.  
  
|Messaggi di eccezione|  
|------------------------|  
|Set di colonne specificato non contiene altre colonne ad eccezione di colonna di etichetta.|  
  

## <a name="error-0141"></a>Errore 0141  
 Si verifica un'eccezione se il numero di colonne numeriche selezionate e valori univoci nella categoria e colonne di tipo stringa è troppo piccolo.  
  
 Questo errore in Azure Machine Learning si verifica quando non sono sufficienti i valori univoci nella colonna selezionata per eseguire l'operazione.  
  
**Risoluzione:** Alcune operazioni eseguono operazioni statistiche sulle funzionalità e le colonne categoriche, e se non sono presenti valori sufficienti, l'operazione potrebbe non riuscire o restituire un risultato non valido. Controllare il set di dati per visualizzare quanti valori sono presenti funzioni ed etichette delle colonne e determinare se l'operazione che si sta provando a eseguire è statisticamente valido.  
  
 Se il set di dati di origine è valida, si potrebbe controllare anche se alcune operazioni di manipolazione o metadati dati upstream ha modificato i dati e rimossi alcuni valori.  
  
 Se sono operazioni a monte di suddivisione, campionamento o ripetere il campionamento, verificare che gli output contengono il numero previsto di righe e valori.  
  
|Messaggi di eccezione|  
|------------------------|  
|Il numero di colonne numeriche selezionate e valori univoci nella categoria e colonne di tipo stringa è troppo piccolo.|  
|Il numero totale di colonne numeriche selezionate e valori univoci nella categoria e colonne di tipo stringa (attualmente {0}) deve essere almeno {1}|  
  

## <a name="error-0142"></a>Errore 0142  
 Eccezione si verifica quando il sistema non è possibile caricare il certificato per l'autenticazione.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|Impossibile caricare il certificato.|  
|Il certificato {0} non può essere caricato. L'identificazione personale è {1}.|  
  

## <a name="error-0143"></a>Errore 0143  
 Non è possibile analizzare l'URL fornito dall'utente che dovrà per essere da GitHub.  
  
 Quando si specifica un URL non valido e il modulo richiede un URL valido di GitHub, si verifica questo errore in Azure Machine Learning.  
  
**Risoluzione:** Verificare che l'URL fa riferimento a un repository di GitHub valido. Non sono supportati altri tipi di sito.  
  
|Messaggi di eccezione|  
|------------------------|  
|URL non è disponibile da github.|  
|URL non è disponibile da github: {0}|  

## <a name="error-0144"></a>Errore 0144  
 Url di GitHub fornito dall'utente è mancante previsto.  
  
 Quando si specifica un'origine di file GitHub usando un formato di URL non valido, si verifica questo errore in Azure Machine Learning.  
  
**Risoluzione:** Verificare che l'URL del repository GitHub sia valido e termina con \blob\ o \tree\\.  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è possibile analizzare l'URL di GitHub.|  
|Non è possibile analizzare l'URL di GitHub (è previsto ' \blob\\' o ' \tree\\' dopo il nome del repository): {0}|  

## <a name="error-0145"></a>Errore 0145  
 Impossibile creare la directory di replica per qualche motivo.  
  
 Questo errore in Azure Machine Learning si verifica quando il modulo non riesce a creare la directory specificata.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|Impossibile creare la directory di replica.|  
  

## <a name="error-0146"></a>Errore 0146  
 Quando i file utente vengono decompressi nella directory locale, il percorso combinato potrebbe essere troppo lungo.  
  
 Quando i file vengono estratti, ma alcuni nomi di file sono troppo lunghi quando decompresso, si verifica questo errore in Azure Machine Learning.  
  
**Risoluzione:** Modificare i nomi dei file che combinati percorso e nome file non viene più di 248 caratteri.  
  
|Messaggi di eccezione|  
|------------------------|  
|Percorso di replica contiene più di 248 caratteri, abbreviare il nome dello script o il percorso.|  

## <a name="error-0147"></a>Errore 0147  
 Impossibile eseguire il download stuff da GitHub per qualche motivo  
  
 Questo errore in Azure Machine Learning si verifica quando non è possibile leggere o scaricare i file specificati da GitHub.  
  
**Risoluzione:** Il problema potrebbe essere temporaneo. è possibile tentare di accedere ai file in un secondo momento. O verificare di disporre delle autorizzazioni necessarie e che l'origine è valida.  
  
|Messaggi di eccezione|  
|------------------------|  
|Errore di accesso di GitHub.|  
|Errore di accesso di GitHub. {0}|  
  

## <a name="error-0148"></a>Errore 0148  
 Problemi di accesso non autorizzato durante l'estrazione dei dati o la creazione della directory.  
  
 Quando si sta tentando di creare una directory o leggere i dati dall'archiviazione ma non dispone delle autorizzazioni necessarie, si verifica questo errore in Azure Machine Learning.  
  
**Risoluzione:**
  
|Messaggi di eccezione|  
|------------------------|  
|Eccezione di accesso non autorizzato durante l'estrazione dei dati.|  
  

## <a name="error-0149"></a>Errore 0149  
 Il file di utente non esiste all'interno di bundle di GitHub.  
  
 Questo errore in Azure Machine Learning si verifica quando non è stato trovato nel file specificato.  
  
Risoluzione: 
  
|Messaggi di eccezione|  
|------------------------|  
|File GitHub non viene trovato.|  
|GitHub non è stato trovato.: {0}|  
  

## <a name="error-0150"></a>Errore 0150  
 Gli script forniti dal pacchetto dell'utente non può essere decompressi, molto probabilmente a causa di un conflitto con i file di GitHub.  
  
 Si verifica questo errore in Azure Machine Learning non è possibile estrarre uno script, in genere quando è presente un file esistente con lo stesso nome.  
  
Risoluzione:
  
|Messaggi di eccezione|  
|------------------------|  
|Non è possibile decomprimere il bundle. conflitto di nomi possibili con i file di GitHub.|  
  

## <a name="error-0151"></a>Errore 0151  
 Si è verificato un errore di scrittura in archiviazione cloud. Verificare che l'URL.  
  
 Questo errore in Azure Machine Learning si verifica quando il modulo tenta di scrivere dati nell'archiviazione cloud, ma l'URL non è disponibile o non valido.  
  
Risoluzione: Verificare che l'URL e verificare che sia accessibile in scrittura.  
  
|Messaggi di eccezione|  
|------------------------|  
|Errore durante la scrittura (probabilmente un url errato) di archiviazione cloud.|  
|Errore di scrittura in archiviazione cloud: {0}. Verificare che l'url.|  
  
## <a name="error-0152"></a>Errore 0152  
 Il tipo di cloud di Azure è stato specificato correttamente nel contesto di modulo.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tipo di Cloud di Azure non valida|  
|Tipo di Cloud di Azure non valida: {0}|  
  
## <a name="error-0153"></a>Errore 0153  
 L'endpoint di archiviazione specificato non è valido.  
  
|Messaggi di eccezione|  
|------------------------|  
|Tipo di Cloud di Azure non valida|  
|Endpoint di archiviazione non valida: {0}|  

## <a name="error-0154"></a>Errore 0154  
 Non è stato possibile risolvere il nome del server specificato  
  
|Messaggi di eccezione|  
|------------------------|  
|Non è stato possibile risolvere il nome del server specificato|  
|Il server specificato {0}. documents.azure.com non può essere risolto|

## <a name="error-0155"></a>Errore 0155  
 Il DocDb Client ha generato un'eccezione  
  
|Messaggi di eccezione|  
|------------------------|  
|Il DocDb Client ha generato un'eccezione|  
|DocDb Client: {0}|

## <a name="error-0156"></a>Errore 0156  
 Risposta non valida per il HCatalog Server.  
  
|Messaggi di eccezione|  
|------------------------|  
|Risposta non valida per il HCatalog Server. Verificare che tutti i servizi siano in esecuzione.|  
|Risposta non valida per il HCatalog Server. Verificare che tutti i servizi siano in esecuzione. Dettagli errore: {0}|

## <a name="error-0157"></a>Errore 0157  
 Si è verificato un errore durante la lettura da Azure Cosmos DB a causa di schemi di documento diverso o non coerenti. Lettore richiede lo stesso schema di tutti i documenti.  
  
|Messaggi di eccezione|  
|------------------------|  
|Rilevato documenti con schemi diversi. Assicurarsi che tutti i documenti con lo stesso schema|

## <a name="error-1000"></a>Errore di 1000  
Eccezione interna della libreria.  
  
Questo errore viene fornito per acquisire in caso contrario, gli errori interni del motore non gestiti. Pertanto, la causa di questo errore potrebbe essere diversa a seconda del modulo che ha generato l'errore.  
  
Per ottenere altre informazioni, è consigliabile che si registra il messaggio dettagliato che accompagna l'errore al forum di Azure Machine Learning, con una descrizione dello scenario, inclusi i dati usati come input. Questi commenti e suggerimenti contribuiscono a definire la priorità degli errori e identificare i problemi più importanti per la successiva elaborazione.  
  
|Messaggi di eccezione|  
|------------------------|  
|Eccezione di libreria.|  
|Eccezione di libreria: {0}|  
|{0} eccezione di libreria: {1}|  
