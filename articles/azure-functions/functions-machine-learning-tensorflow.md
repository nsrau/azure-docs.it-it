---
title: 'Esercitazione: Usare Python e TensorFlow in Funzioni di Azure per eseguire inferenze di Machine Learning | Microsoft Docs'
description: Questa esercitazione illustra come applicare modelli di Machine Learning TensorFlow in Funzioni di Azure
services: functions
author: anthonychu
manager: gwallace
ms.service: azure-functions
ms.devlang: python
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: e243fd2f5c4a90e45f424ce39a97913df2332b2b
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677899"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Esercitazione: Applicare modelli di Machine Learning in Funzioni di Azure con Python e TensorFlow

Questo articolo illustra come Funzioni di Azure consente di usare Python e TensorFlow con un modello di Machine Learning per classificare un'immagine in base al contenuto.

In questa esercitazione si apprenderà come: 

> [!div class="checklist"]
> * Inizializzare un ambiente locale per lo sviluppo di funzioni di Azure in Python
> * Importare un modello di Machine Learning TensorFlow personalizzato in un'app per le funzioni
> * Creare un'API HTTP serverless per prevedere se una foto contiene un cane o un gatto
> * Utilizzare l'API da un'applicazione Web

![Screenshot del progetto finito](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti 

Per creare funzioni di Azure in Python, è necessario installare alcuni strumenti.

- [Python 3.6](https://www.python.org/downloads/release/python-360/)
- [Strumenti di base di Funzioni di Azure](functions-run-local.md#install-the-azure-functions-core-tools)
- Un editor di codice, ad esempio [Visual Studio Code](https://code.visualstudio.com/)

## <a name="clone-the-tutorial-repository"></a>Clonare il repository dell'esercitazione

Per iniziare, aprire un terminale e clonare il repository seguente con Git:

```console
git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
cd functions-python-tensorflow-tutorial
```

Il repository contiene alcune cartelle.

- *start*:  cartella di lavoro per l'esercitazione
- *end*: risultato finale e implementazione completa per riferimento
- *resources*: contiene il modello di Machine Learning e le librerie helper
- *frontend*: sito Web che chiama l'app per le funzioni

## <a name="create-and-activate-a-python-virtual-environment"></a>Creare e attivare un ambiente virtuale Python

Funzioni di Azure richiede Python 3.6.x. Si creerà un ambiente virtuale per assicurarsi di usare la versione di Python richiesta.

Passare alla cartella *start* come directory di lavoro corrente. Creare e attivare quindi un ambiente virtuale denominato *.venv*. A seconda dell'installazione di Python, i comandi per creare un ambiente virtuale Python 3.6 possono essere diversi da quelli delle istruzioni seguenti.

#### <a name="linux-and-macos"></a>Linux e macOS:

```bash
cd start
python3.6 -m venv .venv
source .venv/bin/activate
```

#### <a name="windows"></a>Windows:

```powershell
cd start
py -3.6 -m venv .venv
.venv\scripts\activate
```

Il prompt del terminale contiene ora il prefisso `(.venv)`, che indica che l'ambiente virtuale è stato attivato correttamente. Verificare che nell'ambiente virtuale `python` sia effettivamente Python 3.6.x.

```console
python --version
```

> [!NOTE]
> Nella parte restante dell'esercitazione si eseguiranno comandi nell'ambiente virtuale. Se è necessario riattivare l'ambiente virtuale in un terminale, eseguire il comando activate appropriato per il sistema operativo in uso.

## <a name="create-an-azure-functions-project"></a>Creare un progetto di Funzioni di Azure

Nella cartella *start* usare Azure Functions Core Tools per inizializzare un'app per le funzioni Python.

```console
func init --worker-runtime python
```

Un'app per le funzioni può contenere una o più funzioni di Azure. Aprire la cartella *start* in un editor ed esaminarne il contenuto.

- [*local.settings.json*](functions-run-local.md#local-settings-file): contiene le impostazioni dell'applicazione usate per lo sviluppo locale
- [*host.json*](functions-host-json.md): contiene le impostazioni per le estensioni e l'host di Funzioni di Azure
- [*requirements.txt*](functions-reference-python.md#python-version-and-package-management): contiene i pacchetti Python richiesti dall'applicazione

## <a name="create-an-http-function"></a>Creare una funzione HTTP

L'applicazione richiede un singolo endpoint API HTTP che accetta come input un URL di immagine e restituisce una previsione in merito al fatto che l'immagine contenga un cane o un gatto.

Nel terminale usare Azure Functions Core Tools per eseguire lo scaffolding di una nuova funzione HTTP denominata *classify*.

```console
func new --language python --template HttpTrigger --name classify
```

Viene creata una nuova cartella denominata *classify* contenente due file.

- *\_\_init\_\_.py*: file per la funzione main
- *function.json*:  file che descrive il trigger della funzione e le relative associazioni di input e di output

### <a name="run-the-function"></a>Eseguire la funzione

Nel terminale in cui è attivato l'ambiente virtuale Python avviare l'app per le funzioni.

```console
func start
```

Aprire un browser e passare all'URL seguente. La funzione verrà eseguita e restituirà *Hello Azure!*

```
http://localhost:7071/api/classify?name=Azure
```

Usare `Ctrl-C` per arrestare l'app per le funzioni.

## <a name="import-the-tensorflow-model"></a>Importare il modello TensorFlow

Si userà un modello TensorFlow predefinito che è stato sottoposto a training ed esportato dal servizio Visione personalizzata di Azure.

> [!NOTE]
> Se si vuole creare un proprio modello usando il livello gratuito del servizio Visione personalizzata, è possibile seguire le [istruzioni disponibili nel repository del progetto di esempio](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md).

Il modello è costituito da due file contenuti nella cartella *<RADICE_REPOSITORY>/resources/model*: *model.pb* e *labels.txt*. Copiarli nella cartella della funzione *classify*.

#### <a name="linux-and-macos"></a>Linux e macOS:

```bash
cp ../resources/model/* classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\model\* classify
```

Assicurarsi di includere \* nel comando precedente. Verificare che *classify* contenga ora i file denominati *model.pb* e *labels.txt*.

## <a name="add-the-helper-functions-and-dependencies"></a>Aggiungere le funzioni helper e le dipendenze

Alcune funzioni helper per la preparazione dell'immagine di input e l'esecuzione di una previsione con TensorFlow si trovano in un file denominato *predict.py* nella cartella *resources*. Copiare questo file nella cartella della funzione *classify*.

#### <a name="linux-and-macos"></a>Linux e macOS:

```bash
cp ../resources/predict.py classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\predict.py classify
```

Verificare che *classifiy* contenga ora un file denominato *predict.py*.

### <a name="install-dependencies"></a>Installare le dipendenze

La libreria helper presenta alcune dipendenze che devono essere installate. Aprire *start/requirements.txt* nell'editor e aggiungere le dipendenze seguenti al file.

```txt
tensorflow==1.15
Pillow
requests
```

Salvare il file.

Nel terminale in cui è attivato l'ambiente virtuale eseguire il comando seguente nella cartella *start* per installare le dipendenze. Il completamento di alcuni passaggi di installazione può richiedere alcuni minuti.

```console
pip install --no-cache-dir -r requirements.txt
```

### <a name="caching-the-model-in-global-variables"></a>Memorizzazione nella cache del modello in variabili globali

Nell'editor aprire *predict.py* ed esaminare la funzione `_initialize` nella parte iniziale del file. Si noti che il modello TensorFlow viene caricato dal disco alla prima esecuzione della funzione e viene salvato in variabili globali. Il caricamento dal disco viene omesso nelle successive esecuzioni della funzione `_initialize`. La memorizzazione nella cache in memoria del modello con questa tecnica velocizza le previsioni successive.

Per altre informazioni sulle variabili globali, vedere [Guida per sviluppatori Python per Funzioni di Azure](functions-reference-python.md#global-variables).

## <a name="update-function-to-run-predictions"></a>Aggiornare la funzione per eseguire previsioni

Aprire *classify/\_\_init\_\_.py* nell'editor. Importare la libreria *predict* aggiunta in precedenza alla stessa cartella. Aggiungere le istruzioni `import` seguenti sotto le altre importazioni già presenti nel file.

```python
import json
from .predict import predict_image_from_url
```

Sostituire il codice del modello della funzione con il codice seguente.

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    image_url = req.params.get('img')
    results = predict_image_from_url(image_url)

    headers = {
        "Content-type": "application/json",
        "Access-Control-Allow-Origin": "*"
    }
    return func.HttpResponse(json.dumps(results), headers = headers)
```

Assicurarsi di salvare le modifiche.

Questa funzione riceve un URL di immagine in un parametro della stringa di query denominato `img`. Chiama `predict_image_from_url` dalla libreria helper, che scarica l'immagine e restituisce una previsione usando il modello TensorFlow. La funzione restituisce quindi una risposta HTTP con i risultati.

Dato che l'endpoint HTTP viene chiamato da una pagina Web ospitata in un altro dominio, la risposta HTTP include un'intestazione `Access-Control-Allow-Origin` per soddisfare i requisiti di condivisione di risorse tra le origini (CORS) del browser.

> [!NOTE]
> In un'applicazione di produzione, per maggiore sicurezza sostituire `*` con l'origine specifica della pagina Web.

### <a name="run-the-function-app"></a>Eseguire l'app per le funzioni

Verificare che l'ambiente virtuale Python sia ancora attivato e avviare l'app per le funzioni con il comando seguente.

```console
func start
```

In un browser aprire questo URL, che chiama la funzione con l'URL di una foto di un gatto. Verificare che venga restituito un risultato di previsione valido.

```
http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
```

Mantenere l'app per le funzioni in esecuzione.

### <a name="run-the-web-app"></a>Eseguire l'app Web

Nella cartella *frontend* è presente una semplice app Web che utilizza l'API HTTP nell'app per le funzioni.

Aprire un terminale *separato* e passare alla cartella *frontend*. Avviare un server HTTP con Python 3.6.

#### <a name="linux-and-macos"></a>Linux e macOS:

```bash
cd <FRONT_END_FOLDER>
python3.6 -m http.server
```

#### <a name="windows"></a>Windows:

```powershell
cd <FRONT_END_FOLDER>
py -3.6  -m http.server
```

In un browser passare all'URL del server HTTP visualizzato nel terminale. Verrà visualizzata un'app Web. Immettere uno degli URL di foto seguenti nella casella di testo. È anche possibile usare un URL di una foto accessibile pubblicamente di un gatto o un cane.

- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`

Quando si fa clic su Submit (Invia), viene chiamata l'app per le funzioni e viene visualizzato un risultato nella pagina.

## <a name="clean-up-resources"></a>Pulire le risorse
Dato che l'intera esercitazione viene eseguita in locale sul computer in uso, non sono presenti risorse o servizi di Azure di cui eseguire la pulizia.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare e personalizzare un'API HTTP con Funzioni di Azure per eseguire previsioni con un modello TensorFlow. Si è anche appreso come chiamare l'API da un'applicazione Web.

È possibile usare le tecniche illustrate in questa esercitazione per creare API di qualsiasi complessità, eseguendo sempre il modello di calcolo serverless offerto da Funzioni di Azure.

Per distribuire l'app per le funzioni in Azure, usare [Azure Functions Core Tools](./functions-run-local.md#publish) o [Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).

> [!div class="nextstepaction"]
> [Guida per sviluppatori Python per Funzioni di Azure](./functions-reference-python.md)
