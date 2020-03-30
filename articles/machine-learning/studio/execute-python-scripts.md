---
title: Eseguire script Python
titleSuffix: ML Studio (classic) - Azure
description: Informazioni su come usare il modulo Execute Python Script per usare il codice Python negli esperimenti e nei servizi Web di Machine Learning Studio (classici).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: c79f6bd63fa5d8d8c6b22ff271d8ca513a94fd64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218089"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Eseguire script di apprendimento automatico Python in Azure Machine Learning Studio (classico)Execute Python machine learning scripts in Azure Machine Learning Studio (classic)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Python è uno strumento prezioso nel forziere di molti data scientist. Viene usato in ogni fase dei flussi di lavoro tipici di apprendimento automatico, tra cui l'esplorazione dei dati, l'estrazione delle funzionalità, il training e la convalida dei modelli e la distribuzione.

Questo articolo descrive come usare il modulo Esegui script Python per usare il codice Python negli esperimenti e nei servizi Web di Azure Machine Learning Studio (classici).

## <a name="using-the-execute-python-script-module"></a>Utilizzo del modulo Execute Python Script

L'interfaccia principale di Python in Studio (classico) è tramite il modulo [Execute Python Script.][execute-python-script] Accetta fino a tre input e produce fino a due output, in modo simile al modulo [Execute R Script.][execute-r-script] Il codice Python viene inserito nella casella dei `azureml_main`parametri tramite una funzione di punto di ingresso denominata in modo speciale.

![Eseguire il modulo Python Script](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Esempio di codice pitone nella casella dei parametri del modulo](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Parametri di input

Gli input per il modulo Python vengono esposti come frame di dati Pandas.Inputs to the Python module are exposed as Pandas DataFrames. La `azureml_main` funzione accetta fino a due frame di dati Panda facoltativi come parametri.

Il mapping delle porte di input ai parametri della funzione è di tipo posizionale:

- La prima porta di input connessa è mappata al primo parametro della funzione.
- Il secondo input, se connesso, è mappato al secondo parametro della funzione.
- Il terzo input viene utilizzato per [importare moduli Python aggiuntivi.](#import-modules)

Di seguito viene illustrata una semantica più dettagliata `azureml_main` del modo in cui le porte di input vengono mappate ai parametri della funzione.

![Tabella delle configurazioni delle porte di input e della firma Python risultante](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Valori restituiti di output

La `azureml_main` funzione deve restituire un singolo frame di dati Pandas incluso in una [sequenza](https://docs.python.org/2/c-api/sequence.html) Python, ad esempio una tupla, un elenco o una matrice NumPy. Il primo elemento di questa sequenza viene restituito alla prima porta di output del modulo. La seconda porta di output del modulo viene utilizzata per [le visualizzazioni](#visualizations) e non richiede un valore restituito. Questo schema è mostrato di seguito.

![Mapping delle porte di input ai parametri e valore restituito alla porta di output](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Traduzione dei tipi di dati di input e output

I set di dati di Studio non sono gli stessi di Panda DataFrames. Di conseguenza, i set di dati di input in Studio (classico) vengono convertiti in Pandas DataFrame e i frame di dati di output vengono riconvertiti in set di dati di Studio (classici). Durante questo processo di conversione, vengono eseguite anche le seguenti traduzioni:

 **Tipo di dati Python** | **Procedura di traduzione in studio** |
| --- | --- |
| Stringhe e numeri| Tradotto così com'è |
| Panda 'NA' | Tradotto come 'Valore mancante' |
| Vettori di indice | Non supportato |
| Nomi di colonna non stringa | Chiamata `str` sui nomi delle colonne |
| Nomi di colonna duplicati | Aggiungere il suffisso numerico: (1), (2), (3) e così via.

**Tutti i frame di dati di input nella funzione Python hanno sempre un indice numerico a 64 bit compreso tra 0 e il numero di righe meno 1*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>Importazione di moduli di script Python esistenti

Il backend utilizzato per eseguire Python si basa su [Anaconda](https://www.anaconda.com/distribution/), una distribuzione scientifica di Python ampiamente utilizzata. Viene fornito con quasi 200 dei pacchetti Python più comuni utilizzati nei carichi di lavoro incentrati sui dati. Studio (classico) non supporta attualmente l'uso di sistemi di gestione dei pacchetti come Pip o Conda per installare e gestire librerie esterne.  Se si ritiene che sia necessario incorporare librerie aggiuntive, utilizzare lo scenario seguente come guida.

Un caso d'uso comune consiste nell'incorporare gli script Python esistenti negli esperimenti di Studio (classici). Il modulo [Execute Python Script][execute-python-script] accetta un file zip contenente i moduli Python sulla terza porta di input. Il file viene decompresso in fase di esecuzione dal framework di esecuzione e il contenuto viene aggiunto al percorso della libreria dell'interprete Python. La funzione del punto di ingresso `azureml_main` può quindi importare questi moduli direttamente. 

Ad esempio, si consideri il file Hello.py contenente una semplice funzione "Hello, World".

![Funzione definita dall'utente in Hello.py file](./media/execute-python-scripts/figure4.png)

Viene quindi creato un file Hello.zip contenente Hello.py:

![File zip contenente codice Python definito dall'utente](./media/execute-python-scripts/figure5.png)

Caricare il file zip come set di dati in Studio (classico). Creare quindi ed eseguire un esperimento che usa il codice Python nel file Hello.zip allegandolo alla terza porta di input del modulo **Esegui script Python,** come illustrato nell'immagine seguente.

![Esempio di esperimento con Hello.zip come input per un modulo Execute Python Script](./media/execute-python-scripts/figure6a.png)

![Codice Python definito dall'utente caricato come file zip](./media/execute-python-scripts/figure6b.png)

L'output del modulo indica che il file ZIP è stato estratto dal pacchetto e che la funzione `print_hello` è stata eseguita.

![Output del modulo che mostra la funzione definita dall'utente](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Accesso ai BLOB di Archiviazione di AzureAccessing Azure Storage Blobs

È possibile accedere ai dati archiviati in un account di archiviazione BLOB di Azure seguendo la procedura seguente:You can access data stored in an Azure Blob Storage account using these steps:

1. Scaricare il pacchetto di archiviazione BLOB di [Azure per Python in](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) locale.
1. Caricare il file zip nell'area di lavoro di Studio (classica) come set di dati.
1. Creare l'oggetto BlobService con`protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Disabilitare **il trasferimento sicuro richiesto** nella scheda delle impostazioni di **Configurazione** archiviazione

![Disabilitare il trasferimento sicuro richiesto nel portale di AzureDisable Secure transfer required in the Azure portal](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Rendere operativi gli script Python

Tutti i moduli [Execute Python Script][execute-python-script] (Esegui script Python) usati in un esperimento di assegnazione dei punteggi vengono chiamati al momento della pubblicazione come servizio Web. Ad esempio, l'immagine seguente mostra un esperimento di assegnazione del punteggio che contiene il codice per valutare una singola espressione Python.For example, the image below shows a scoring experiment that contains the code to evaluate a single Python expression.

![Area di lavoro di Studio per un servizio Web](./media/execute-python-scripts/figure3a.png)

![Espressione Python Pandas](./media/execute-python-scripts/python-script-with-python-pandas.png)

Un servizio Web creato da questo esperimento consente di eseguire le azioni seguenti:A web service created from this experiment would take the following actions:

1. Prendere un'espressione Python come input (come stringa)Take a Python expression as input (as a string)
1. Inviare l'espressione Python all'interprete Python
1. Restituisce una tabella contenente sia l'espressione che il risultato valutato.

## <a name="working-with-visualizations"></a><a id="visualizations"></a>Utilizzo di visualizzazioni

I grafici creati utilizzando MatplotLib possono essere restituiti da [Execute Python Script][execute-python-script]. Tuttavia, i grafici non vengono reindirizzati automaticamente alle immagini così come sono quando si usa R.However, plots aren't automatically redirected to images as they are when using R. Quindi l'utente deve salvare in modo esplicito tutte le stampa in file PNG.

Per generare immagini da MatplotLib, è necessario effettuare le seguenti operazioni:

1. Impostare il back-end su "AGG" dal renderer basato su Qt predefinito.
1. Creare un nuovo oggetto figura.
1. Ottenere l'asse e generare tutti i grafici in esso.
1. Salvare la figura in un file PNG.

Questo processo è illustrato nelle immagini seguenti che creano una matrice di grafici a dispersione utilizzando la funzione scatter_matrix in Panda.

![Codice per salvare le figure MatplotLib nelle immagini](./media/execute-python-scripts/figure-v1-8.png)

![Fare clic su visualize su un modulo Execute Python Script per visualizzare le figure](./media/execute-python-scripts/figure-v2-9a.png)

![Visualizzazione dei grafici per un esperimento di esempio con codice Python](./media/execute-python-scripts/figure-v2-9b.png)

È possibile restituire più figure salvandole in immagini diverse. Studio (classico) runtime raccoglie tutte le immagini e le concatena per la visualizzazione.

## <a name="advanced-examples"></a>Esempi avanzati

L'ambiente Anaconda installato in Studio (classico) contiene pacchetti comuni come NumPy, SciPy e Scikits-Learn. Questi pacchetti possono essere usati in modo efficace per l'elaborazione dei dati in una pipeline di apprendimento automatico.

Ad esempio, l'esperimento e lo script seguenti illustrano l'uso degli studenti di ensemble in Scikits-Learn per calcolare i punteggi di importanza delle funzionalità per un set di dati. I punteggi possono essere utilizzati per eseguire la selezione delle feature supervisionate prima di essere inseriti in un altro modello.

Di seguito è illustrata la funzione Python usata per calcolare i punteggi di rilevanza e ordinare le funzioni in base ai punteggi:

![Funzione per classificare le feature in base ai punteggi](./media/execute-python-scripts/figure8.png)

L'esperimento seguente calcola e restituisce i punteggi di importanza delle funzionalità nel set di dati "Pima Indian Diabetes" in Azure Machine Learning Studio (classico):The following experiment then computesands and returns the importance scores of features in the "Pima Indian Diabetes" dataset in Azure Machine Learning Studio (classic):

![Sperimenta per classificare le feature nel set di dati Pima Indian Diabetes usando Python](./media/execute-python-scripts/figure9a.png)

![Visualizzazione dell'output del modulo Execute Python Script](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Limitazioni

Il modulo [Execute Python Script][execute-python-script] ha attualmente le seguenti limitazioni:

### <a name="sandboxed-execution"></a>Esecuzione in modalità sandbox

Il runtime Python è attualmente in modalità sandbox e non consente l'accesso alla rete o al file system locale in modo permanente. Tutti i file salvati localmente sono isolati ed eliminati al termine del modulo. Il codice Python non è in grado di accedere alla maggior parte delle directory nel computer in cui è in esecuzione, ad eccezione della directory corrente e delle relative sottodirectory.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Mancanza di supporto sofisticato per lo sviluppo e il debug

 Il modulo Python non supporta attualmente le funzionalità dell'IDE, ad esempio IntelliSense e debug. In caso di errore del modulo durante il runtime, è disponibile l'analisi completa dello stack Python. È tuttavia necessario visualizzarla nel log di output per il modulo. Al momento si consiglia di sviluppare ed eseguire il debug degli script Python in un ambiente come IPython e quindi importare il codice nel modulo.

### <a name="single-data-frame-output"></a>Uscita singolo frame di dati

 Al punto di ingresso Python è consentito restituire un singolo frame di dati come output. Non è attualmente possibile restituire oggetti Python arbitrari, ad esempio modelli sottoposti a training direttamente al runtime di Studio (classico). Come per [Execute R Script][execute-r-script] (Esegui script R), che presenta le stesse limitazioni, in molti casi è possibile inserire oggetti in una matrice di byte e quindi restituirla all'interno di un frame di dati.

### <a name="inability-to-customize-python-installation"></a>Impossibilità di personalizzare l'installazione di Python

Attualmente, è possibile aggiungere moduli Python personalizzati solo tramite il meccanismo con file ZIP descritto in precedenza. Anche se questo è fattibile per i moduli di piccole dimensioni, è ingombrante per i moduli di grandi dimensioni (in particolare i moduli con DLL native) o un gran numero di moduli.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori di Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script
