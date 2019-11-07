---
title: Eseguire script Python
description: Informazioni su come usare il modulo Execute Python script per usare il codice Python negli esperimenti di Machine Learning Studio (classico) e nei servizi Web.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: dea8fd7971f0572350306ea11ee1f9d897438d11
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684872"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Eseguire script di Python Machine Learning in Azure Machine Learning Studio (versione classica)

Python è uno strumento prezioso nello strumento di molti data scientist. Viene usato in ogni fase dei flussi di lavoro di apprendimento automatico tipici, tra cui esplorazione dei dati, estrazione di funzionalità, training e convalida del modello e distribuzione.

Questo articolo descrive come usare il modulo Execute Python script per usare il codice Python negli esperimenti di Azure Machine Learning Studio (classico) e nei servizi Web.

## <a name="using-the-execute-python-script-module"></a>Uso del modulo Execute Python script

L'interfaccia principale per Python in studio (classico) è tramite il modulo [Execute Python script][execute-python-script] . Accetta fino a tre input e produce fino a due output, analogamente al modulo [Execute R script][execute-r-script] . Il codice Python viene immesso nella casella parametro tramite una funzione del punto di ingresso denominata appositamente denominata `azureml_main`.

![Eseguire il modulo di script Python](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Codice Python di esempio nella casella parametri modulo](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Parametri di input

Gli input per il modulo Python vengono esposti come dataframe Pandas. La funzione `azureml_main` accetta un massimo di due frame di dataframe Panda facoltativi come parametri.

Il mapping delle porte di input ai parametri della funzione è di tipo posizionale:

- La prima porta di input connessa è mappata al primo parametro della funzione.
- Il secondo input, se connesso, è mappato al secondo parametro della funzione.
- Il terzo input viene usato per [importare moduli Python aggiuntivi](#import-modules).

Di seguito è riportata una semantica più dettagliata della modalità di mapping delle porte di input ai parametri della funzione `azureml_main`.

![Tabella delle configurazioni della porta di input e della firma Python risultante](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Valori restituiti di output

La funzione `azureml_main` deve restituire un singolo dataframe Pandas incluso in una [sequenza](https://docs.python.org/2/c-api/sequence.html) Python, ad esempio una tupla, un elenco o una matrice numpy. Il primo elemento di questa sequenza viene restituito alla prima porta di output del modulo. La seconda porta di output del modulo viene utilizzata per le [visualizzazioni](#visualizations) e non richiede un valore restituito. Questo schema è illustrato di seguito.

![Mapping delle porte di input ai parametri e del valore restituito alla porta di output](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Conversione dei tipi di dati di input e di output

I set di impostazioni di studio non corrispondono a quelli di Panda dataframes. Di conseguenza, i set di dati di input nella versione classica di studio vengono convertiti in dataframe Pandas e i frame di dati di output vengono riconvertiti in set di dati di studio (classiche). Durante questo processo di conversione vengono eseguite anche le seguenti traduzioni:

 **Tipo di dati Python** | **Procedura di traduzione in studio** |
| --- | --- |
| Stringhe e valori numerici| Tradotto come è |
| Pandas ' NA ' | Tradotto come ' missing value ' |
| Vettori di indice | Non supportato |
| Nomi di colonna non stringa | Chiama `str` per i nomi di colonna |
| Nomi di colonna duplicati | Aggiungere il suffisso numerico: (1), (2), (3) e così via.

**tutti i frame di dati di input nella funzione Python hanno sempre un indice numerico a 64 bit compreso tra 0 e il numero di righe meno 1*

## <a id="import-modules"></a>Importazione di moduli di script Python esistenti

Il back-end usato per eseguire Python si basa su [Anaconda](https://www.anaconda.com/distribution/), una distribuzione di Python scientifica ampiamente usata. Include quasi 200 dei pacchetti Python più comuni usati nei carichi di lavoro incentrati sui dati. La versione classica di studio attualmente non supporta l'uso di sistemi di gestione dei pacchetti come PIP o conda per l'installazione e la gestione di librerie esterne.  Se si ritiene che sia necessario incorporare librerie aggiuntive, utilizzare lo scenario seguente come guida.

Un caso d'uso comune consiste nell'incorporare gli script Python esistenti nella versione classica degli esperimenti in studio. Il modulo [Execute Python script (Esegui script Python][execute-python-script] ) accetta un file zip contenente i moduli Python alla terza porta di input. Il file viene decompresso in fase di esecuzione dal framework di esecuzione e il contenuto viene aggiunto al percorso della libreria dell'interprete Python. La funzione del punto di ingresso `azureml_main` può quindi importare questi moduli direttamente. 

Considerare, ad esempio, il file Hello.py che contiene una semplice funzione "Hello, World".

![Funzione definita dall'utente nel file Hello.py](./media/execute-python-scripts/figure4.png)

Viene quindi creato un file Hello.zip contenente Hello.py:

![File zip contenente il codice Python definito dall'utente](./media/execute-python-scripts/figure5.png)

Caricare il file zip come set di dati nella versione classica di studio. Quindi creare ed eseguire un esperimento che usa il codice Python nel file Hello. zip collegando il codice alla terza porta di input del modulo **Execute Python script (Esegui script Python** ), come illustrato nella figura seguente.

![Esperimento di esempio con Hello. zip come input per un modulo Execute Python script](./media/execute-python-scripts/figure6a.png)

![Codice Python definito dall'utente caricato come file zip](./media/execute-python-scripts/figure6b.png)

L'output del modulo indica che il file ZIP è stato estratto dal pacchetto e che la funzione `print_hello` è stata eseguita.

![Output del modulo che mostra la funzione definita dall'utente](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Accesso ai BLOB di archiviazione di Azure

È possibile accedere ai dati archiviati in un account di archiviazione BLOB di Azure seguendo questa procedura:

1. Scaricare il [pacchetto di archiviazione BLOB di Azure per Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) localmente.
1. Caricare il file zip nell'area di lavoro di studio (classica) come set di dati.
1. Creare l'oggetto BlobService con `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Disabilitare il **trasferimento sicuro necessario** nella scheda Impostazioni di **configurazione** dell'archiviazione

![Disabilitare il trasferimento sicuro necessario nel portale di Azure](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Rendere operativi gli script Python

Qualsiasi modulo [Execute Python script][execute-python-script] usato in un esperimento di assegnazione dei punteggi viene chiamato quando viene pubblicato come servizio Web. Ad esempio, l'immagine seguente mostra un esperimento di assegnazione dei punteggi contenente il codice per valutare una singola espressione Python.

![Area di lavoro di studio per un servizio Web](./media/execute-python-scripts/figure3a.png)

![Espressione Pandas Python](./media/execute-python-scripts/python-script-with-python-pandas.png)

Un servizio Web creato da questo esperimento intraprenderà le azioni seguenti:

1. Eseguire un'espressione Python come input (sotto forma di stringa)
1. Inviare l'espressione Python all'interprete Python
1. Restituisce una tabella contenente sia l'espressione che il risultato valutato.

## <a id="visualizations"></a>Uso delle visualizzazioni

I tracciati creati con MatplotLib possono essere restituiti dallo [script Execute Python][execute-python-script]. Tuttavia, i tracciati non vengono reindirizzati automaticamente alle immagini così come sono quando si usa R. Quindi, l'utente deve salvare in modo esplicito i tracciati in file PNG.

Per generare immagini da MatplotLib, è necessario eseguire le operazioni seguenti:

1. Passare il back-end a "AGG" dal renderer predefinito basato su QT.
1. Creare un nuovo oggetto figure.
1. Ottenere l'asse e generare tutti i tracciati.
1. Salvare la figura in un file PNG.

Questo processo è illustrato nelle immagini seguenti che creano una matrice di grafici a dispersione usando la funzione scatter_matrix in Pandas.

![Codice per salvare le figure MatplotLib in immagini](./media/execute-python-scripts/figure-v1-8.png)

![Fare clic su Visualizza in un modulo Esegui script Python per visualizzare le cifre](./media/execute-python-scripts/figure-v2-9a.png)

![Visualizzazione dei tracciati per un esperimento di esempio con il codice Python](./media/execute-python-scripts/figure-v2-9b.png)

È possibile restituire più cifre salvando le figure in immagini diverse. La versione classica di runtime di studio preleva tutte le immagini e le concatena per la visualizzazione.

## <a name="advanced-examples"></a>Esempi avanzati

L'ambiente Anaconda installato nella versione classica di studio contiene pacchetti comuni, ad esempio NumPy, SciPy e scikits-Learn. Questi pacchetti possono essere usati in modo efficace per l'elaborazione dei dati in una pipeline di machine learning.

Ad esempio, l'esperimento e lo script seguenti illustrano l'uso di ensemble Learning in scikits-Learn per calcolare i punteggi di importanza della funzionalità per un set di dati. I punteggi possono essere utilizzati per eseguire la selezione delle caratteristiche supervisionata prima di essere inseriti in un altro modello.

Di seguito è illustrata la funzione Python usata per calcolare i punteggi di rilevanza e ordinare le funzioni in base ai punteggi:

![Funzione per classificare le funzionalità in base ai punteggi](./media/execute-python-scripts/figure8.png)

L'esperimento seguente calcola quindi e restituisce i punteggi di importanza delle funzionalità nel set di dati "Pima Indian Diabetes" nella versione classica di Azure Machine Learning Studio:

![Provare a classificare le funzionalità nel set di dati del diabete Pima Indian con Python](./media/execute-python-scripts/figure9a.png)

![Visualizzazione dell'output del modulo Execute Python script](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Limitazioni

Il modulo [Execute Python script][execute-python-script] presenta attualmente le limitazioni seguenti:

### <a name="sandboxed-execution"></a>Esecuzione in modalità sandbox

Il runtime Python è attualmente in modalità sandbox e non consente l'accesso alla rete o al file system locale in modo permanente. Tutti i file salvati localmente sono isolati ed eliminati al termine del modulo. Il codice Python non è in grado di accedere alla maggior parte delle directory nel computer in cui è in esecuzione, ad eccezione della directory corrente e delle relative sottodirectory.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Mancanza di un supporto sofisticato per lo sviluppo e il debug

Il modulo Python non supporta attualmente le funzionalità dell'IDE, ad esempio IntelliSense e debug. In caso di errore del modulo durante il runtime, è disponibile l'analisi completa dello stack Python. È tuttavia necessario visualizzarla nel log di output per il modulo. Al momento si consiglia di sviluppare ed eseguire il debug degli script Python in un ambiente come IPython e quindi importare il codice nel modulo.

### <a name="single-data-frame-output"></a>Singolo output del frame di dati

Al punto di ingresso Python è consentito restituire un singolo frame di dati come output. Attualmente non è possibile restituire oggetti Python arbitrari, ad esempio i modelli di cui è stato eseguito il training, direttamente al runtime di studio (classico). Come per [Execute R script][execute-r-script], che ha la stessa limitazione, in molti casi è possibile raggruppare gli oggetti in una matrice di byte e quindi restituirla all'interno di un frame di dati.

### <a name="inability-to-customize-python-installation"></a>Impossibilità di personalizzare l'installazione di Python

Attualmente, è possibile aggiungere moduli Python personalizzati solo tramite il meccanismo con file ZIP descritto in precedenza. Sebbene sia possibile per i moduli di dimensioni ridotte, è complicato per i moduli di grandi dimensioni (in particolare i moduli con DLL native) o per un numero elevato di moduli.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori di Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script
