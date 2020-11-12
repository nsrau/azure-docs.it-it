---
title: Distribuire un modello PyTorch come applicazione Funzioni di Azure
description: Usare una rete neurale profonda ResNet 18 con training preliminare di PyTorch con Funzioni di Azure per assegnare una delle 1000 etichette di ImageNet a un'immagine.
author: gvashishtha
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: gopalv
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 8891c29e5d8d06df6292d06ec06e5e57fb9880e7
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422842"
---
# <a name="tutorial-deploy-a-pre-trained-image-classification-model-to-azure-functions-with-pytorch"></a>Esercitazione: Distribuire un modello di classificazione delle immagini con training preliminare in Funzioni di Azure con PyTorch

Questo articolo illustra come usare Python, PyTorch e Funzioni di Azure per caricare un modello con training preliminare per la classificazione di un'immagine in base al contenuto. Dal momento che si lavora in locale e non vengono create risorse di Azure nel cloud, non è previsto alcun costo per completare questa esercitazione.

> [!div class="checklist"]
> * Inizializzare un ambiente locale per lo sviluppo di Funzioni di Azure in Python.
> * Importare un modello di Machine Learning PyTorch con training preliminare in un'app per le funzioni.
> * Creare un'API HTTP serverless per classificare un'immagine come una delle 1000 classi [ImageNet](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a).
> * Utilizzare l'API da un'app Web.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4 o versione successiva](https://www.python.org/downloads/release/python-374/). Con Funzioni di Azure sono stati verificati anche Python 3.8.x e Python 3.6.x.
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Un editor di codice, ad esempio [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

1. In una finestra di comando o di terminale eseguire `func --version` per verificare che la versione di Azure Functions Core Tools sia 2.7.1846 o successiva.
1. Eseguire `python --version` (Linux/MacOS) o `py --version` (Windows) per verificare che la versione di Python sia 3.7.x.

## <a name="clone-the-tutorial-repository"></a>Clonare il repository dell'esercitazione

1. In una finestra di comando o di terminale clonare il repository seguente con Git:

    ```
    git clone https://github.com/Azure-Samples/functions-python-pytorch-tutorial.git
    ```

1. Passare alla cartella ed esaminarne il contenuto.

    ```
    cd functions-python-pytorch-tutorial
    ```

    - *start* è la cartella di lavoro per l'esercitazione.
    - *end* è il risultato finale e l'implementazione completa per riferimento.
    - *resources* contiene il modello di Machine Learning e le librerie helper.
    - *frontend* è un sito Web che chiama l'app per le funzioni.

## <a name="create-and-activate-a-python-virtual-environment"></a>Creare e attivare un ambiente virtuale Python

Passare alla cartella *start* ed eseguire i comandi seguenti per creare e attivare un ambiente virtuale denominato `.venv`.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
cd start
python -m venv .venv
source .venv/bin/activate
```

Se Python non ha installato il pacchetto venv nella distribuzione Linux, eseguire il comando seguente:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
py -m venv .venv
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
py -m venv .venv
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

    Questo comando crea una cartella che corrisponde al nome della funzione, ovvero *classify*. In tale cartella sono presenti due file, ovvero *\_\_init\_\_.py* , che contiene il codice della funzione, e *function.json* , che descrive il trigger della funzione e le associazioni di input e output. Per informazioni dettagliate sul contenuto di questi file, vedere [Esaminare il contenuto del file](./create-first-function-cli-python.md#optional-examine-the-file-contents) nella guida di avvio rapido per Python.


## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

1. Avviare la funzione avviando l'host di runtime locale di Funzioni di Azure nella cartella *start* :

    ```
    func start
    ```

1. Quando nell'output viene visualizzato l'endpoint `classify`, passare all'URL ```http://localhost:7071/api/classify?name=Azure```. Nell'output dovrebbe essere visualizzato il messaggio "Hello Azure!".

1. Premere **CTRL**-**C** per arrestare l'host.


## <a name="import-the-pytorch-model-and-add-helper-code"></a>Importare il modello PyTorch e aggiungere il codice helper

Per modificare la funzione `classify` per classificare un'immagine in base al contenuto, si usa un modello [ResNet](https://arxiv.org/abs/1512.03385) con training preliminare. Tale modello, proveniente da [PyTorch](https://pytorch.org/hub/pytorch_vision_resnet/), classifica un'immagine come una delle 1000 [classi ImageNet](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a). Aggiungere quindi il codice helper e le dipendenze al progetto.

1. Nella cartella *start* eseguire questo comando per copiare il codice di previsione e le etichette nella cartella *classify*.

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    cp ../resources/predict.py classify
    cp ../resources/labels.txt classify
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    ---

1. Verificare che la cartella *classify* contenga i file denominati *predict.py* e *labels.txt*. In caso contrario, verificare che il comando sia stato eseguito nella cartella *start*.

1. Aprire *start/requirements.txt* in un editor di testo e aggiungere le dipendenze richieste dal codice helper, che si presenteranno come segue:

    ```txt
    azure-functions
    requests
    -f https://download.pytorch.org/whl/torch_stable.html
    torch==1.5.0+cpu
    torchvision==0.6.0+cpu
    ```

1. Salvare *requirements.txt* e quindi eseguire questo comando dalla cartella *start* per installare le dipendenze.


    ```
    pip install --no-cache-dir -r requirements.txt
    ```

L'installazione può richiedere alcuni minuti, durante i quali è possibile procedere con la modifica della funzione nella sezione successiva.
> [!TIP]
> >È possibile che in Windows venga visualizzato un errore simile a "Non è stato possibile installare i pacchetti a causa di un errore EnvironmentError: [numero errore 2] non esiste alcun file o directory di questo tipo:" seguito da un percorso lungo a un file come *sharded_mutable_dense_hashtable.cpython-37.pyc*. Questo errore si verifica in genere perché la profondità del percorso della cartella diventa eccessiva. In tal caso, impostare la chiave del Registro di sistema `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` su `1` per abilitare i percorsi lunghi. In alternativa, controllare il percorso in cui è installato l'interprete Python. Se tale percorso è un percorso lungo, provare a reinstallare in una cartella con un percorso più breve.

## <a name="update-the-function-to-run-predictions"></a>Aggiornare la funzione per generare previsioni

1. Aprire *classify/\_\_init\_\_.py* in un editor di testo e aggiungere le righe seguenti dopo le istruzioni `import` esistenti per importare la libreria JSON standard e gli helper di *predict* :

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Sostituire l'intero contenuto della funzione `main` con il codice seguente:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="8-19":::

    Questa funzione riceve un URL di immagine in un parametro della stringa di query denominato `img`. Chiama quindi `predict_image_from_url` dalla libreria helper per scaricare e classificare l'immagine usando il modello PyTorch. La funzione restituisce quindi una risposta HTTP con i risultati.

    > [!IMPORTANT]
    > Dal momento che l'endpoint HTTP viene chiamato da una pagina Web ospitata in un altro dominio, la risposta include un'intestazione `Access-Control-Allow-Origin` per soddisfare i requisiti di condivisione di risorse tra le origini (CORS) del browser.
    >
    > In un'applicazione di produzione, per maggiore sicurezza sostituire `*` con l'origine specifica della pagina Web.

1. Salvare le modifiche e quindi, supponendo che le dipendenze abbiano terminato l'installazione, avviare di nuovo l'host della funzione locale con `func start`. Assicurarsi di eseguire l'host nella cartella *start* con l'ambiente virtuale attivato. In caso contrario, l'host verrà avviato, ma si verificheranno errori durante la chiamata della funzione.

    ```
    func start
    ```

1. In un browser aprire l'URL seguente per richiamare la funzione con l'URL dell'immagine di un cane di razza Bovaro del bernese e verificare che l'oggetto JSON restituito classifichi l'immagine come cane di tale razza.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg
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

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg`
    - `https://github.com/Azure-Samples/functions-python-pytorch-tutorial/blob/master/resources/assets/bald-eagle.jpg?raw=true`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/penguin.jpg`

1. Selezionare **Invia** per richiamare l'endpoint della funzione per classificare l'immagine.

    ![Screenshot del progetto finito](media/machine-learning-pytorch/screenshot.png)

    Se il browser restituisce un errore quando si invia l'URL dell'immagine, controllare il terminale in cui si sta eseguendo l'app per le funzioni. Se viene visualizzato un errore simile a "Non sono stati trovati moduli 'PIL'", è possibile che l'app per le funzioni nella cartella *start* sia stata avviata senza aver prima attivato l'ambiente virtuale creato in precedenza. Se vengono ancora visualizzati errori, eseguire di nuovo `pip install -r requirements.txt` con l'ambiente virtuale attivato e cercare gli errori.

## <a name="clean-up-resources"></a>Pulire le risorse

Dal momento che l'intera esercitazione viene eseguita in locale sul computer in uso, non sono presenti risorse o servizi di Azure di cui eseguire la pulizia.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come creare e personalizzare un endpoint API HTTP con Funzioni di Azure per classificare le immagini usando un modello PyTorch. È stato anche illustrato come chiamare l'API da un'app Web. È possibile usare le tecniche illustrate in questa esercitazione per creare API di qualsiasi complessità, eseguendo sempre il modello di calcolo serverless offerto da Funzioni di Azure.

Vedere anche la pagina relativa alla

- [Distribuire la funzione in Azure con Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Guida per sviluppatori Python per Funzioni di Azure](./functions-reference-python.md)


> [!div class="nextstepaction"]
> [Distribuire la funzione in Funzioni di Azure con la Guida dell'interfaccia della riga di comando di Azure](./functions-run-local.md#publish)
