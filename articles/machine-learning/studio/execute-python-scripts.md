---
title: Eseguire gli script di Machine Learning di Python
titleSuffix: Azure Machine Learning Studio
description: Informazioni su come usare Python in Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 4b4f3877b56752756050de0af226571ac2a93293
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60750822"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Eseguire gli script di apprendimento automatico di Python in Azure Machine Learning Studio

Python è uno strumento prezioso in dotazione di strumenti di molti data Scientist. Viene usato in ogni fase dei flussi di lavoro di apprendimento tipico di macchina inclusi esplorazione dei dati, estrazione di funzioni, training del modello e la convalida e distribuzione.

Questo articolo descrive come è possibile usare il modulo Execute Python Script usare il codice Python gli esperimenti Azure Machine Learning Studio e dei servizi web.

## <a name="using-the-execute-python-script-module"></a>Usando il modulo Execute Python Script

L'interfaccia primaria di Python in Studio è tramite il [Execute Python Script] [ execute-python-script] modulo. Accetta un massimo di tre input e produce un massimo di due output, simile al [Execute R Script] [ execute-r-script] modulo. Codice Python viene immesso nella casella dei parametri tramite una funzione di punto di ingresso appositamente denominata denominata `azureml_main`.

![Eseguire il modulo di Script Python](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Codice python di esempio nella casella del parametro modulo](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Parametri di input

Gli input per il modulo Python vengono esposto come Dataframe Pandas. Il `azureml_main` funzione accetta fino a due facoltativi Dataframe di Pandas come parametri.

Il mapping delle porte di input ai parametri della funzione è di tipo posizionale:

- La prima porta di input connessa è mappata al primo parametro della funzione.
- Il secondo input, se connesso, è mappato al secondo parametro della funzione.
- Il terzo input viene usato per [importare altri moduli di Python](#import-modules).

Una semantica del modo in cui vengono mappate le porte di input ai parametri di più dettagliata di `azureml_main` funzione sono illustrate di seguito.

![Tabella di configurazioni di porta di input e della firma risultante Python](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Valori restituiti di output

Il `azureml_main` funzione deve restituire un DataFrame di Pandas singolo incluso nel pacchetto in Python [sequenza](https://docs.python.org/2/c-api/sequence.html) , ad esempio una tupla, elenco o una matrice NumPy. Il primo elemento di questa sequenza viene restituito per la prima porta di output del modulo. La seconda porta di output del modulo viene usata per [visualizzazioni](#visualizations) e non richiede un valore restituito. Questo schema è illustrato di seguito.

![Mapping di porte per i parametri di input e valore restituito alla porta di output](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Conversione dei tipi di dati di input e output

I set di dati di Studio non sono uguali come frame di dati Panda. Di conseguenza, set di dati di input in Studio vengono convertiti in frame di dati Pandas e frame di dati di output vengono riconvertiti in set di dati di Studio. Durante questo processo di conversione, vengono eseguite anche per le conversioni seguenti:

 **Tipo di dati Python** | **Routine di conversione di Studio** |
| --- | --- |
| Le stringhe e valori numerici| Convertita è |
| Pandas "ND" | Convertito come 'Missing value' |
| Vettori indice | Non supportato * |
| Nomi delle colonne non stringa | Chiamare `str` sui nomi di colonna |
| Nomi di colonna duplicati | Aggiungere un suffisso numerico: (1), (2), (3), e così via.

**Tutti i frame di dati di input della funzione Python hanno sempre un indice numerico a 64 bit compreso tra 0 e il numero di righe meno 1*

## <a id="import-modules"></a>L'importazione di moduli di script Python esistenti

Il back-end usato per eseguire Python si basa sul [Anaconda](https://store.continuum.io/cshop/anaconda/), ampiamente usato distribuzione Python scientifica. Include circa 200 dei più comuni pacchetti Python usati nei carichi di lavoro basati sui dati. Studio non supporta attualmente l'utilizzo di sistemi di gestione pacchetti, ad esempio Pip o Conda, installare e gestire librerie esterne.  Se si ritiene che sia necessario incorporare librerie aggiuntive, usare lo scenario seguente come guida.

Un caso d'uso comune è per incorporare gli script Python esistenti negli esperimenti di Studio. Il [Execute Python Script] [ execute-python-script] modulo accetta un file zip contenente i moduli Python alla terza porta di input. Il file viene decompresso in fase di esecuzione dal framework di esecuzione e il contenuto viene aggiunto al percorso della libreria dell'interprete Python. La funzione del punto di ingresso `azureml_main` può quindi importare questi moduli direttamente. 

Considerare, ad esempio, il file Hello.py che contiene una semplice funzione "Hello, World".

![Funzione definita dall'utente nel file Hello.py](./media/execute-python-scripts/figure4.png)

Viene quindi creato un file Hello.zip contenente Hello.py:

![File zip contenente codice Python definito dall'utente](./media/execute-python-scripts/figure5.png)

Caricare il file zip come set di dati in Studio. Creare quindi ed eseguire un esperimento che usa il codice Python nel file ZIP associandolo alla terza porta di input del **Execute Python Script** modulo, come illustrato nell'immagine seguente.

![Esperimento di esempio con zip associandolo come input per un modulo Execute Python Script](./media/execute-python-scripts/figure6a.png)

![Codice Python definito dall'utente caricato come file zip](./media/execute-python-scripts/figure6b.png)

L'output del modulo indica che il file ZIP è stato estratto dal pacchetto e che la funzione `print_hello` è stata eseguita.

![Output del modulo che mostra la funzione definita dall'utente](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Accedere ai BLOB di archiviazione di Azure

È possibile accedere ai dati archiviati in un account di archiviazione Blob di Azure attenendosi alla procedura seguente:

1. Scaricare il [pacchetto di archiviazione Blob di Azure per Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) in locale.
1. Caricare il file zip nell'area di lavoro di Studio come un set di dati.
1. Creare l'oggetto BlobService con `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Disabilitare **trasferimento sicuro obbligatorio** nella risorsa di archiviazione **configurazione** scheda delle impostazioni

![Disabilitare il trasferimento sicuro obbligatorio nel portale di Azure](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Rendere operativi gli script Python

Tutti i moduli [Execute Python Script][execute-python-script] (Esegui script Python) usati in un esperimento di assegnazione dei punteggi vengono chiamati al momento della pubblicazione come servizio Web. Ad esempio, l'immagine seguente mostra un esperimento di assegnazione dei punteggi contenente il codice per valutare una singola espressione Python.

![Area di lavoro di Studio per un servizio web](./media/execute-python-scripts/figure3a.png)

![Espressione di Pandas per Python](./media/execute-python-scripts/python-script-with-python-pandas.png)

Un servizio web creato da questo esperimento verrà intraprendere le azioni seguenti:

1. Accettare un'espressione di Python come input (sotto forma di stringa)
1. Invia l'espressione di Python all'interprete di Python
1. Restituisce una tabella che contiene l'espressione sia il risultato valutato.

## <a id="visualizations"></a>Utilizzo di visualizzazioni

Possono essere restituiti i tracciati creati con MatplotLib tramite il [Execute Python Script][execute-python-script]. Tracciati non vengono tuttavia reindirizzati automaticamente in immagini così come sono quando si usa R. Pertanto, l'utente deve salvare espressamente i tracciati in file PNG.

Per generare immagini da MatplotLib, è necessario eseguire i passaggi seguenti:

1. Passare al back-end impostandolo su "AGG" dal renderer predefinito basato su Qt.
1. Creare un nuovo oggetto figura.
1. Ottenere l'asse e generare tutti i tracciati al suo interno.
1. Salvare la figura in un file PNG.

Questo processo è illustrato nelle immagini seguenti che crea una matrice di grafico a dispersione tramite la funzione scatter_matrix in Pandas.

![Codice per salvare le figure di MatplotLib in immagini](./media/execute-python-scripts/figure-v1-8.png)

![Fare clic su Visualizza in un modulo Execute Python Script per visualizzare le cifre](./media/execute-python-scripts/figure-v2-9a.png)

![Visualizzazione di tracciati per un esperimento di esempio con il codice Python](./media/execute-python-scripts/figure-v2-9b.png)

È possibile restituire più figure salvandole in immagini diverse. Il runtime di Studio recupera tutte le immagini e le CONCATENA per la visualizzazione.

## <a name="advanced-examples"></a>Esempi avanzati

L'ambiente Anaconda installato in Studio contiene pacchetti comuni come NumPy, SciPy e Scikits-Learn. Questi pacchetti possono essere usati in modo efficace per l'elaborazione dati in una pipeline di machine learning.

Ad esempio, il seguente esperimento e lo script illustrano l'uso di strumenti di apprendimento dell'insieme di Scikits-Learn per calcolare i punteggi di importanza di funzionalità per un set di dati. I punteggi possono essere usati per eseguire una selezione della funzione supervisionata prima di essere inseriti in un altro modello.

Di seguito è illustrata la funzione Python usata per calcolare i punteggi di rilevanza e ordinare le funzioni in base ai punteggi:

![Funzione per classificare le funzioni per i punteggi](./media/execute-python-scripts/figure8.png)

L'esperimento seguente calcola e restituisce i punteggi di rilevanza delle funzioni nel set di dati "Pima Indian Diabetes" in Azure Machine Learning Studio:

![Esperimento per classificare le funzioni nel set di dati Pima Indian Diabetes usando Python](./media/execute-python-scripts/figure9a.png)

![Visualizzazione dell'output del modulo Execute Python Script](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Limitazioni

Il [Execute Python Script] [ execute-python-script] modulo attualmente presenta le limitazioni seguenti:

### <a name="sandboxed-execution"></a>Esecuzione in modalità sandbox

Il runtime di Python è attualmente in modalità sandbox e non consente l'accesso alla rete o nel file system locale in modo permanente. Tutti i file salvati localmente sono isolati ed eliminati al termine del modulo. Il codice Python non è in grado di accedere alla maggior parte delle directory nel computer in cui è in esecuzione, ad eccezione della directory corrente e delle relative sottodirectory.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Mancanza di sviluppo complesse e il supporto del debug

Il modulo Python non supporta attualmente le funzionalità dell'IDE, ad esempio IntelliSense e debug. In caso di errore del modulo durante il runtime, è disponibile l'analisi completa dello stack Python. È tuttavia necessario visualizzarla nel log di output per il modulo. Al momento si consiglia di sviluppare ed eseguire il debug degli script Python in un ambiente come IPython e quindi importare il codice nel modulo.

### <a name="single-data-frame-output"></a>Output di frame di dati Single

Al punto di ingresso Python è consentito restituire un singolo frame di dati come output. Non è attualmente possibile restituire oggetti Python arbitrari, ad esempio direttamente i modelli sottoposti a training al runtime di Studio. Come per [Execute R Script][execute-r-script] (Esegui script R), che presenta le stesse limitazioni, in molti casi è possibile inserire oggetti in una matrice di byte e quindi restituirla all'interno di un frame di dati.

### <a name="inability-to-customize-python-installation"></a>Impossibilità di personalizzare l'installazione di Python

Attualmente, è possibile aggiungere moduli Python personalizzati solo tramite il meccanismo con file ZIP descritto in precedenza. Sebbene questo sia fattibile per i moduli piccoli, risulta complesso per i moduli di grandi dimensioni (in particolare i moduli con DLL native) o un numero elevato di moduli.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori di Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script