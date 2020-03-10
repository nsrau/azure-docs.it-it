---
title: Usare Python e TensorFlow per Machine Learning in Azure
description: Usare Python, TensorFlow e Funzioni di Azure con un modello di Machine Learning per classificare un'immagine in base al contenuto.
author: anthonychu
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: c64d87b2430cc1d733a67bbc1e803590a37b1714
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190773"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Esercitazione: Applicare modelli di Machine Learning in Funzioni di Azure con Python e TensorFlow

Questo articolo illustra come usare Python, TensorFlow e Funzioni di Azure con un modello di Machine Learning per classificare un'immagine in base al contenuto. Dal momento che si lavora in locale e non vengono create risorse di Azure nel cloud, non è previsto alcun costo per completare questa esercitazione.

> [!div class="checklist"]
> * Inizializzare un ambiente locale per lo sviluppo di Funzioni di Azure in Python.
> * Importare un modello di Machine Learning TensorFlow personalizzato in un'app per le funzioni.
> * Creare un'API HTTP serverless per classificare un'immagine che contiene un cane o un gatto.
> * Utilizzare l'API da un'app Web.

## <a name="prerequisites"></a>Prerequisiti 

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4](https://www.python.org/downloads/release/python-374/). Python 3.7.4 e Python 3.6.x sono stati verificati con Funzioni di Azure; Python 3.8 e versioni successive non sono ancora supportati.
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Un editor di codice, ad esempio [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

1. In una finestra di comando o di terminale eseguire `func --version` per verificare che la versione di Azure Functions Core Tools sia 2.7.1846 o successiva.
1. Eseguire `python --version` (Linux/MacOS) o `py --version` (Windows) per verificare che la versione di Python sia 3.7.x.

## <a name="clone-the-tutorial-repository"></a>Clonare il repository dell'esercitazione

1. In una finestra di comando o di terminale clonare il repository seguente con Git:

    ```
    git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
    ```

1. Passare alla cartella ed esaminarne il contenuto.

    ```
    cd functions-python-tensorflow-tutorial
    ```

    - *start* è la cartella di lavoro per l'esercitazione.
    - *end* è il risultato finale e l'implementazione completa per riferimento.
    - *resources* contiene il modello di Machine Learning e le librerie helper.
    - *frontend* è un sito Web che chiama l'app per le funzioni.
    
## <a name="create-and-activate-a-python-virtual-environment"></a>Creare e attivare un ambiente virtuale Python

Passare alla cartella *start* ed eseguire i comandi seguenti per creare e attivare un ambiente virtuale denominato `.venv`. Assicurarsi di usare Python 3.7, che è supportato da Funzioni di Azure.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
cd start
```

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Se Python non ha installato il pacchetto venv nella distribuzione Linux, eseguire il comando seguente:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
```

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
```

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

È possibile eseguire tutti i comandi successivi in questo ambiente virtuale attivato. Per uscire dall'ambiente virtuale, eseguire `deactivate`.


## <a name="create-a-local-functions-project"></a>Creare un progetto per le funzioni locale

In Funzioni di Azure un progetto di funzione è un contenitore per una o più funzioni singole che rispondono ognuna a un trigger specifico. Tutte le funzioni di un progetto condividono le stesse configurazioni locali e di hosting. In questa sezione viene creato un progetto di funzione che contiene una singola funzione boilerplate denominata `classify` che fornisce un endpoint HTTP. Il codice più specifico verrà aggiunto in una sezione successiva.

1. Nella cartella *start* usare Azure Functions Core Tools per inizializzare un'app per le funzioni Python:

    ```
    func init --worker-runtime python
    ```

    Dopo l'inizializzazione la cartella *start* contiene vari file per il progetto, inclusi i file di configurazione denominati [local.settings.json](functions-run-local.md#local-settings-file) e [host.json](functions-host-json.md). Poiché *local.settings.json* può contenere segreti scaricati da Azure, per impostazione predefinita il file viene escluso dal controllo del codice sorgente nel file con estensione *gitignore*.

    > [!TIP]
    > Poiché un progetto di funzione è associato a un runtime specifico, tutte le funzioni del progetto devono essere scritte con lo stesso linguaggio.

1. Aggiungere una funzione al progetto usando il comando seguente, in cui l'argomento `--name` è il nome univoco della funzione e l'argomento `--template` specifica il trigger della funzione. `func new` crea una sottocartella corrispondente al nome della funzione che contiene un file di codice appropriato per il linguaggio scelto del progetto e un file di configurazione denominato *function.json*.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    Questo comando crea una cartella che corrisponde al nome della funzione, ovvero *classify*. In tale cartella sono presenti due file, ovvero *\_\_init\_\_.py*, che contiene il codice della funzione, e *function.json*, che descrive il trigger della funzione e le associazioni di input e output. Per informazioni dettagliate sul contenuto di questi file, vedere [Esaminare il contenuto del file](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#optional-examine-the-file-contents) nella guida di avvio rapido per Python.


## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

1. Avviare la funzione avviando l'host di runtime locale di Funzioni di Azure nella cartella *start*:

    ```
    func start
    ```
    
1. Quando nell'output viene visualizzato l'endpoint `classify`, passare all'URL ```http://localhost:7071/api/classify?name=Azure```. Nell'output dovrebbe essere visualizzato il messaggio "Hello Azure!".

1. Premere **CTRL**-**C** per arrestare l'host.


## <a name="import-the-tensorflow-model-and-add-helper-code"></a>Importare il modello TensorFlow e aggiungere il codice dell'helper

Per modificare la funzione `classify` per classificare un'immagine in base al contenuto, si usa un modello TensorFlow predefinito che è stato sottoposto a training ed esportato dal servizio Visione personalizzata di Azure. Il modello, contenuto nella cartella *resources* dell'esempio clonato in precedenza, consente di classificare un'immagine a seconda che contenga un cane o un gatto. Aggiungere quindi il codice helper e le dipendenze al progetto.

> [!TIP]
> Se si vuole creare un modello personalizzato usando il livello gratuito del servizio Visione personalizzata, è possibile seguire le [istruzioni disponibili nel repository del progetto di esempio](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md).

1. Nella cartella *start* eseguire il comando seguente per copiare i file del modello nella cartella *classify*. Assicurarsi di includere `\*` nel comando. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    cp ../resources/model/* classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\model\* classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\model\* classify
    ```
    
    ---
    
1. Verificare che la cartella *classify* contenga i file denominati *model.pb* e *labels.txt*. In caso contrario, verificare che il comando sia stato eseguito nella cartella *start*.

1. Nella cartella *start* eseguire il comando seguente per copiare un file con codice helper nella cartella *classify*:

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    cp ../resources/predict.py classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\predict.py classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\predict.py classify
    ```
    
    ---

1. Verificare che la cartella *classify* contenga un file denominato *predict.py*.

1. Aprire *start/requirements.txt* in un editor di testo e aggiungere le dipendenze seguenti richieste dal codice helper:

    ```txt
    tensorflow==1.14
    Pillow
    requests
    ```
    
1. Salvare il file *requirements.txt*.

1. Installare le dipendenze eseguendo il comando seguente nella cartella *start*. L'installazione può richiedere alcuni minuti, durante i quali è possibile procedere con la modifica della funzione nella sezione successiva.

    ```
    pip install --no-cache-dir -r requirements.txt
    ```
    
    È possibile che in Windows venga visualizzato un errore simile a "Non è stato possibile installare i pacchetti a causa di un errore EnvironmentError: [numero errore 2] non esiste alcun file o directory di questo tipo:" seguito da un percorso lungo a un file come *sharded_mutable_dense_hashtable.cpython-37.pyc*. Questo errore si verifica in genere perché la profondità del percorso della cartella diventa eccessiva. In tal caso, impostare la chiave del Registro di sistema `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` su `1` per abilitare i percorsi lunghi. In alternativa, controllare il percorso in cui è installato l'interprete Python. Se tale percorso è un percorso lungo, provare a reinstallare in una cartella con un percorso più breve.

> [!TIP]
> Quando si chiama *predict.py* per generare la prima previsione, una funzione denominata `_initialize` carica il modello TensorFlow dal disco e lo memorizza nella cache nelle variabili globali. Questa memorizzazione nella cache velocizza le previsioni successive. Per altre informazioni sull'uso delle variabili globali, vedere [Guida per sviluppatori Python per Funzioni di Azure](functions-reference-python.md#global-variables).

## <a name="update-the-function-to-run-predictions"></a>Aggiornare la funzione per generare previsioni

1. Aprire *classify/\_\_init\_\_.py* in un editor di testo e aggiungere le righe seguenti dopo le istruzioni `import` esistenti per importare la libreria JSON standard e gli helper di *predict*:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Sostituire l'intero contenuto della funzione `main` con il codice seguente:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="8-19":::

    Questa funzione riceve un URL di immagine in un parametro della stringa di query denominato `img`. Chiama quindi `predict_image_from_url` dalla libreria helper per scaricare e classificare l'immagine usando il modello TensorFlow. La funzione restituisce quindi una risposta HTTP con i risultati. 

    > [!IMPORTANT]
    > Dal momento che l'endpoint HTTP viene chiamato da una pagina Web ospitata in un altro dominio, la risposta include un'intestazione `Access-Control-Allow-Origin` per soddisfare i requisiti di condivisione di risorse tra le origini (CORS) del browser.
    >
    > In un'applicazione di produzione, per maggiore sicurezza sostituire `*` con l'origine specifica della pagina Web.

1. Salvare le modifiche e quindi, supponendo che le dipendenze abbiano terminato l'installazione, avviare di nuovo l'host della funzione locale con `func start`. Assicurarsi di eseguire l'host nella cartella *start* con l'ambiente virtuale attivato. In caso contrario, l'host verrà avviato, ma si verificheranno errori durante la chiamata della funzione.

    ```
    func start
    ```

1. In un browser aprire l'URL seguente per richiamare la funzione con l'URL dell'immagine di un gatto e verificare che l'oggetto JSON restituito classifichi l'immagine come gatto.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
    ```
    
1. Lasciare l'host in esecuzione perché verrà usato nel passaggio successivo. 

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>Eseguire il front-end dell'app Web locale per testare la funzione

Per eseguire il test richiamando l'endpoint della funzione da un'altra app Web, usare l'app semplice presente nella cartella *frontend* del repository.

1. Aprire un terminale o prompt dei comandi e attivare l'ambiente virtuale, come descritto in precedenza in [Creare e attivare un ambiente virtuale Python](#create-and-activate-a-python-virtual-environment).

1. Passare alla cartella *frontend* del repository.

1. Avviare un server HTTP con Python:

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash 
    python -m http.server
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. In un browser passare a `localhost:8000` e quindi immettere uno degli URL di foto seguenti nella casella di testo oppure usare l'URL di qualsiasi immagine accessibile pubblicamente.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`
    
1. Selezionare **Invia** per richiamare l'endpoint della funzione per classificare l'immagine.

    ![Screenshot del progetto finito](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

    Se il browser restituisce un errore quando si invia l'URL dell'immagine, controllare il terminale in cui si sta eseguendo l'app per le funzioni. Se viene visualizzato un errore simile a "Non sono stati trovati moduli 'PIL'", è possibile che l'app per le funzioni nella cartella *start* sia stata avviata senza aver prima attivato l'ambiente virtuale creato in precedenza. Se vengono ancora visualizzati errori, eseguire di nuovo `pip install -r requirements.txt` con l'ambiente virtuale attivato e cercare gli errori.

> [!NOTE]
> Il modello classifica sempre il contenuto dell'immagine come gatto o cane, indipendentemente dal fatto che l'immagine li contenga, usando come impostazione predefinita l'immagine del cane. Le immagini di tigri e pantere, ad esempio, vengono classificate in genere come gatto, ma quelle di elefanti, carote o aeroplani vengono classificate come cane.

## <a name="clean-up-resources"></a>Pulire le risorse

Dal momento che l'intera esercitazione viene eseguita in locale sul computer in uso, non sono presenti risorse o servizi di Azure di cui eseguire la pulizia.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come creare e personalizzare un endpoint dell'API HTTP con Funzioni di Azure per classificare immagini con un modello TensorFlow. È stato anche illustrato come chiamare l'API da un'app Web. È possibile usare le tecniche illustrate in questa esercitazione per creare API di qualsiasi complessità, eseguendo sempre il modello di calcolo serverless offerto da Funzioni di Azure.

> [!div class="nextstepaction"]
> [Distribuire la funzione in Funzioni di Azure con la Guida dell'interfaccia della riga di comando di Azure](./functions-run-local.md#publish)

Vedere anche la pagina relativa alla

- [Distribuire la funzione in Azure con Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Guida per sviluppatori Python per Funzioni di Azure](./functions-reference-python.md)
