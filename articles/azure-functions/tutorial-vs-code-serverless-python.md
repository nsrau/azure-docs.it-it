---
title: Creare e distribuire funzioni di Azure in Python con Visual Studio Code
description: Come usare l'estensione Visual Studio Code per funzioni di Azure per creare funzioni senza server in Python e distribuirle in Azure.
services: functions
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: glenga
ms.openlocfilehash: 43fee2ce25e358bbcff915d2fbef96bf4b7c1a0c
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233110"
---
# <a name="deploy-python-to-azure-functions-with-visual-studio-code"></a>Distribuisci Python in funzioni di Azure con Visual Studio Code

In questa esercitazione si usano Visual Studio Code e l'estensione funzioni di Azure per creare un endpoint HTTP senza server con Python e aggiungere anche una connessione (o "binding") alla risorsa di archiviazione. Funzioni di Azure esegue il codice in un ambiente senza server senza dover eseguire il provisioning di una macchina virtuale o pubblicare un'app Web. L'estensione funzioni di Azure per Visual Studio Code semplifica notevolmente il processo di utilizzo delle funzioni gestendo automaticamente molti problemi di configurazione.

In questa esercitazione si imparerà a:

> [!div class="checklist"]
> * Installare l'estensione Funzioni di Azure
> * Creare una funzione attivata tramite HTTP
> * Esegui debug localmente
> * Sincronizzare le impostazioni dell'applicazione
> * Visualizzare i log in streaming
> * Connettersi ad archiviazione di Azure

Se si verificano problemi con i passaggi descritti in questa esercitazione, è opportuno conoscere i dettagli. Usare il pulsante **ho eseguito un problema** alla fine di ogni sezione per inviare commenti e suggerimenti dettagliati.

## <a name="prerequisites"></a>Prerequisiti

- Una [sottoscrizione di Azure](#azure-subscription).
- [Visual Studio Code con l'estensione funzioni di Azure](#visual-studio-code-python-and-the-azure-functions-extension) .
- [Azure Functions Core Tools](#azure-functions-core-tools).

### <a name="azure-subscription"></a>Sottoscrizione di Azure

Se non si ha una sottoscrizione di Azure, [iscriversi adesso](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension) per ottenere un account gratuito di 30 giorni con $200 in crediti di Azure per provare qualsiasi combinazione di servizi.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, Python e l'estensione funzioni di Azure

Installare il software seguente:

- Python 3.6. x come richiesto da funzioni di Azure. [Python 3.6.8 tramite](https://www.python.org/downloads/release/python-368/) è la versione 3.6. x più recente.
- [Visual Studio Code](https://code.visualstudio.com/).
- L' [estensione Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) come descritto in [Visual Studio Code i prerequisiti dell'esercitazione su Python](https://code.visualstudio.com/docs/python/python-tutorial).
- [Estensione funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). Per informazioni generali, visitare il [repository GitHub VSCODE-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Seguire le istruzioni per il sistema operativo in uso [con Azure Functions Core Tools](functions-run-local.md#v2). Gli strumenti stessi vengono scritti in .NET Core e il pacchetto degli strumenti di base viene installato meglio con node. js Package Manager, NPM, per cui è necessario installare attualmente .NET Core e node. js, anche per il codice Python. È tuttavia possibile ignorare i requisiti di .NET Core utilizzando "bundle di estensione", come descritto nella documentazione precedente. Qualunque sia il caso, è necessario installare questi componenti solo una volta, dopo di che Visual Studio Code richiede automaticamente l'installazione di eventuali aggiornamenti.

### <a name="sign-in-to-azure"></a>Accedi ad Azure

Dopo aver installato l'estensione funzioni, accedere al proprio account Azure passando ad **Azure: Esplora** funzioni selezionare **Accedi ad Azure**e seguire le istruzioni.

![Accedi ad Azure tramite Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-sign-in.png)

Dopo l'accesso, verificare che l'account di posta elettronica della sottoscrizione di Azure sia visualizzato nella barra di stato:

![Barra di stato Visual Studio Code che mostra l'account Azure](media/tutorial-vs-code-serverless-python/azure-account-status-bar.png)

Il nome assegnato alla sottoscrizione viene visualizzato anche in **Azure: Esplora** funzioni ("primario" nell'immagine seguente):

![Visual Studio Code app Azure Service Explorer che mostra le sottoscrizioni](media/tutorial-vs-code-serverless-python/azure-subscription-view.png)

> [!NOTE]
> Se si verifica l'errore **"Impossibile trovare la sottoscrizione con il nome [ID sottoscrizione]"** , è possibile che l'utente si trovi dietro un proxy e non sia in grado di raggiungere l'API di Azure. Configurare `HTTP_PROXY` le `HTTPS_PROXY` variabili di ambiente e con le informazioni sul proxy nel terminale:
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```ps
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```

### <a name="verify-prerequisites"></a>Verificare i prerequisiti

Per verificare che siano installati tutti gli strumenti di funzioni di Azure, aprire il riquadro comandi Visual Studio Code (F1), **Selezionare il terminale: Creare un nuovo comando** terminale integrato e, una volta aperto il terminale, eseguire `func`il comando:

![Verifica dei prerequisiti degli strumenti di base di funzioni di Azure](media/tutorial-vs-code-serverless-python/check-prereqs.png)

L'output che inizia con il logo di funzioni di Azure (è necessario scorrere l'output verso l'alto) indica che sono presenti le Azure Functions Core Tools.

Se il `func` comando non è riconosciuto, verificare che la cartella in cui è stato installato il Azure Functions Core Tools sia inclusa nella variabile di ambiente Path.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=01-verify-prerequisites)

## <a name="create-the-function"></a>Creare la funzione

1. Il codice per funzioni di Azure viene gestito all'interno di un _progetto_di funzioni, creato prima di creare il codice. In **Azure: Esplora** funzioni (aperto usando l'icona di Azure sul lato sinistro), selezionare l'icona del comando **nuovo progetto** oppure aprire il riquadro comandi (F1) e selezionare **funzioni di Azure: Create New Project**(ASA: Crea nuovo progetto).

    ![Pulsante Crea nuovo progetto in Esplora funzioni](media/tutorial-vs-code-serverless-python/project-create-new.png)

1. Nei prompt che seguono:

    | Prompt | Value | Descrizione | 
    | --- | --- | --- |
    | Specificare una cartella per il progetto | Cartella aperta corrente | Cartella in cui creare il progetto. Potrebbe essere necessario creare il progetto in una sottocartella. |
    | Selezionare un linguaggio per il progetto di app per le funzioni | **Python** | Linguaggio da utilizzare per la funzione, che determina il modello utilizzato per il codice. |
    | Selezionare un modello per la prima funzione del progetto | **Trigger HTTP** | Una funzione che usa un trigger HTTP viene eseguita ogni volta che viene effettuata una richiesta HTTP all'endpoint della funzione. Sono disponibili diversi altri trigger per funzioni di Azure. Per altre informazioni, vedere [che cosa è possibile fare con le funzioni?](functions-overview.md#what-can-i-do-with-functions)). |
    | Specificare un nome di funzione | HttpExample | Il nome viene usato per una sottocartella che contiene il codice della funzione insieme ai dati di configurazione e definisce anche il nome dell'endpoint HTTP. Usare "HttpExample" invece di accettare il valore predefinito "HTTPTrigger" per distinguere la funzione stessa dal trigger. |
    | Livello di autorizzazione | **Funzione** | Le chiamate effettuate all'endpoint della funzione richiedono un [tasto funzione](functions-bindings-http-webhook.md#authorization-keys). |
    | Specificare come aprire il progetto | **Apri nella finestra corrente** | Apre il progetto nella finestra di Visual Studio Code corrente. |

1. Dopo un breve periodo di tempo, un messaggio per indicare che il nuovo progetto è stato creato. Nella finestra di **esplorazione**è presente la sottocartella creata per la funzione. 

1. Se non è già aperto, aprire il  *\_ \_file init\_\_. py* che contiene il codice della funzione predefinito:

    [![Risultato della creazione di un nuovo progetto di funzioni Python](media/tutorial-vs-code-serverless-python/project-create-results.png)](media/tutorial-vs-code-serverless-python/project-create-results.png)

    > [!NOTE]
    > Quando Visual Studio Code indica che non è stato selezionato un interprete Python **quando si apre  *\_ \_\_init\_. py*, aprire il riquadro comandi (F1), selezionare Python: Selezionare** comando interprete e quindi selezionare l'ambiente virtuale nella cartella locale `.env` (creata come parte del progetto). L'ambiente deve essere basato su Python 3.6 x in modo specifico, come indicato in precedenza in [prerequisiti](#prerequisites).
    >
    > ![Selezione dell'ambiente virtuale creato con il progetto](media/tutorial-vs-code-serverless-python/select-venv-interpreter.png)

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=02-create-function)

## <a name="examine-the-code-files"></a>Esaminare i file di codice

Nella sottocartella della _funzione HttpExample_ appena creata sono presenti tre file  *\_:\_ \_\_init. py* contiene il codice della funzione, *Function. JSON* descrive la funzione in Azure Functions ed *Sample. dat* è un file di dati di esempio. È possibile eliminare *Sample. dat* se lo si desidera, perché esiste solo per mostrare che è possibile aggiungere altri file alla sottocartella.

Esaminiamo prima di tutto *Function. JSON* , quindi il codice in  *\_ \_init\_\_. py*.

### <a name="functionjson"></a>function.json

Il file function. JSON fornisce le informazioni di configurazione necessarie per l'endpoint di funzioni di Azure:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

La `scriptFile` proprietà identifica il file di avvio per il codice e il codice deve contenere una funzione Python denominata `main`. È possibile fattorizzare il codice in più file purché il file specificato contenga una `main` funzione.

L' `bindings` elemento contiene due oggetti, uno per descrivere le richieste in ingresso e l'altro per descrivere la risposta http. Per le richieste in ingresso`"direction": "in"`(), la funzione risponde alle richieste HTTP GET o post e richiede di specificare il tasto funzione. La risposta (`"direction": "out"`) è una risposta HTTP che restituisce qualsiasi valore restituito `main` dalla funzione Python.

### <a name="__initpy__"></a>\_\_init.py\_\_

Quando si crea una nuova funzione, funzioni di Azure fornisce codice Python predefinito  *\_in\_ \_\_init. py*:

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

Di seguito sono riportate le parti importanti del codice:

- È necessario importare `func` da `azure.functions`. l'importazione del modulo di registrazione è facoltativa ma consigliata.
- La funzione `main` Python necessaria riceve un `func.request` oggetto denominato `req`e restituisce un valore di tipo `func.HttpResponse`. È possibile ottenere altre informazioni sulle funzionalità di questi oggetti in [Func. HttpRequest](/python/api/azure-functions/azure.functions.httprequest?view=azure-python) e [Func. ](/python/api/azure-functions/azure.functions.httpresponse?view=azure-python)Riferimenti a HttpResponse.
- Il corpo di `main` elabora quindi la richiesta e genera una risposta. In questo caso, il codice cerca un `name` parametro nell'URL. In caso contrario, verifica se il corpo della richiesta contiene JSON ( `func.HttpRequest.get_json`using) e che il codice JSON `name` contiene un valore ( `get` usando il metodo dell'oggetto JSON restituito `get_json`da).
- Se viene trovato un nome, il codice restituisce la stringa "Hello" con il nome aggiunto; in caso contrario, viene restituito un messaggio di errore.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=03-examine-code-files)

## <a name="debug-locally"></a>Esegui debug localmente

1. Quando si crea il progetto di funzioni, l'estensione Visual Studio Code crea anche una configurazione di `.vscode/launch.json` avvio in che contiene una singola configurazione denominata **Connetti alle funzioni di Python**. Questa configurazione significa che è possibile selezionare **F5** o utilizzare Esplora debug per avviare il progetto:

    ![Debug Explorer che mostra la configurazione di avvio delle funzioni](media/tutorial-vs-code-serverless-python/launch-configuration.png)

1. Quando si avvia il debugger, viene aperto un terminale che mostra l'output di funzioni di Azure, incluso un riepilogo degli endpoint disponibili. L'URL potrebbe essere diverso se è stato usato un nome diverso da "HttpExample":

    ```output
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. Usare **CTRL + clic** o **cmd + clic** sull'URL nella finestra di **output** Visual Studio Code per aprire un browser all'indirizzo oppure avviare un browser e incollare lo stesso URL. In entrambi i casi, l'endpoint `api/<function_name>`è, in questo `api/HttpExample`caso. Tuttavia, poiché tale URL non include un parametro Name, la finestra del browser dovrebbe mostrare solo ", passare un nome nella stringa di query o nel corpo della richiesta", a seconda del percorso nel codice.

1. A questo punto, provare ad aggiungere un parametro Name all'uso `http://localhost:7071/api/HttpExample?name=VS%20Code`, ad esempio, e la finestra del browser visualizzerà il messaggio "Hello Visual Studio Code!", dimostrando che il percorso del codice è stato eseguito.

1. Per passare il valore del nome in un corpo della richiesta JSON, è possibile usare uno strumento come curl con JSON inline:

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data {"name":"Visual Studio Code"} http://localhost:7071/api/HttpExample
    ```

    ```ps
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    In alternativa, creare un file come *Data. JSON* che contiene `{"name":"Visual Studio Code"}` e usare il comando `curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample`.

1. Per eseguire il debug della funzione, impostare un punto di interruzione sulla `name = req.params.get('name')` riga che legge ed eseguire nuovamente una richiesta all'URL. Il debugger Visual Studio Code dovrebbe arrestarsi su tale riga, consentendo di esaminare le variabili ed eseguire il codice un'istruzione alla volta. Per una breve procedura dettagliata del debug di base, vedere [Visual Studio Code esercitazione-configurare ed eseguire il debugger](https://code.visualstudio.com/docs/python/python-tutorial.md#configure-and-run-the-debugger).

1. Quando si è soddisfatti che la funzione è stata testata in locale, arrestare il debugger (con il comando di menu **debug** > **Interrompi debug** oppure il comando **Disconnetti** sulla barra degli strumenti di debug).

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=04-test-debug)

## <a name="deploy-to-azure-functions"></a>Distribuire in Funzioni di Azure

In questa procedura si userà l'estensione funzioni per creare un'app per le funzioni in Azure, insieme ad altre risorse di Azure necessarie. Un'app per le funzioni consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione delle risorse. Richiede anche un account di archiviazione di Azure per i dati e un [piano di hosting](functions-scale.md#hosting-plan-support). Tutte queste risorse sono organizzate all'interno di un singolo gruppo di risorse.

1. Nell'area **Azure: Esplora** funzioni, selezionare il comando **Distribuisci in app per le funzioni** o aprire il riquadro comandi (F1 **) e selezionare funzioni di Azure: Eseguire la distribuzione** nel comando app per le funzioni. Anche in questo caso l'app per le funzioni è la posizione in cui viene eseguito il progetto Python in Azure.

    ![Comando Distribuisci in app per le funzioni](media/tutorial-vs-code-serverless-python/deploy-command.png)

1. Quando richiesto, selezionare **Crea nuovo app per le funzioni in Azure**e specificare un nome univoco in Azure, in genere usando il nome personale o della società insieme ad altri identificatori univoci. è possibile usare lettere, numeri e trattini. Se in precedenza è stato creato un app per le funzioni, il relativo nome viene visualizzato in questo elenco di opzioni.

1. L'estensione esegue le azioni seguenti, che è possibile osservare in Visual Studio Code messaggi popup e nella finestra di **output** (il processo richiede alcuni minuti):

    - Creare un gruppo di risorse usando il nome assegnato (rimuovendo i trattini).
    - In tale gruppo di risorse, creare l'account di archiviazione, il piano di hosting e l'app per le funzioni. Per impostazione predefinita, viene creato un [piano a consumo](functions-scale.md#consumption-plan) . Per eseguire le funzioni in un piano dedicato, è necessario [abilitare la pubblicazione con le opzioni di creazione avanzate](functions-develop-vs-code.md).
    - Distribuire il codice nell'app per le funzioni.

    **Azure: Esplora** funzioni Mostra anche lo stato di avanzamento:

    ![Indicatore di stato della distribuzione in Azure: Esplora funzioni](media/tutorial-vs-code-serverless-python/deploy-progress.png)

1. Al termine della distribuzione, l'estensione funzioni di Azure Visualizza un messaggio con i pulsanti per tre azioni aggiuntive:

    ![Messaggio che indica la corretta distribuzione con azioni aggiuntive](media/tutorial-vs-code-serverless-python/deployment-popup.png)

    Per i **log di flusso** e **le impostazioni di caricamento**, vedere le sezioni successive. Per **visualizzare l'output**, vedere il passaggio 5 seguente.

1. Dopo la distribuzione, la finestra di **output** Mostra anche l'endpoint pubblico in Azure:

    ```output
    HTTP Trigger Urls:
      HttpExample: https://vscode-azure-functions.azurewebsites.net/api/HttpExample
    ```

    Usare questo endpoint per eseguire gli stessi test effettuati localmente, usando i parametri URL e/o le richieste con dati JSON nel corpo della richiesta. I risultati dell'endpoint pubblico devono corrispondere ai risultati dell'endpoint locale testato in precedenza.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=05-deploy)

### <a name="stream-logs"></a>Eseguire lo streaming dei log

Il supporto per lo streaming dei log è attualmente in fase di sviluppo, come descritto nel [problema 589](https://github.com/microsoft/vscode-azurefunctions/issues/589) per l'estensione funzioni di Azure. Il pulsante dei **log di flusso** nel popup del messaggio di distribuzione collegherà infine l'output del log in Azure al Visual Studio Code. Sarà anche possibile avviare e arrestare il flusso di log in Esplora funzioni di **Azure** facendo clic con il pulsante destro del mouse sul progetto funzioni e scegliendo **Avvia log di streaming** o **Interrompi log in streaming**.

Attualmente, tuttavia, questi comandi non sono ancora operativi. Il flusso di log è invece disponibile in un browser eseguendo il comando seguente, `<app_name>` sostituendo con il nome dell'app per le funzioni in Azure:

```bash
# Replace <app_name> with the name of your Functions app on Azure
func azure functionapp logstream <app_name> --browser
```

### <a name="sync-local-settings-to-azure"></a>Sincronizzare le impostazioni locali in Azure

Il pulsante **Carica impostazioni** nel popup del messaggio di distribuzione applica le modifiche apportate al file *local. Settings. JSON* in Azure. È anche possibile richiamare il comando in Esplora **funzioni di Azure** espandendo il nodo del progetto funzioni, facendo clic con il pulsante destro del mouse su **Impostazioni applicazione**e scegliendo **Carica impostazioni locali.** È anche possibile usare il riquadro comandi per selezionare le **funzioni di Azure: Comando Carica impostazioni** locali.

Il caricamento delle impostazioni aggiorna le impostazioni esistenti e aggiunge le nuove impostazioni definite in *local. Settings. JSON*. Il caricamento non rimuove le impostazioni da Azure che non sono elencate nel file locale. Per rimuovere queste impostazioni, espandere il nodo **Impostazioni applicazioni** in Esplora **funzioni di Azure** , fare clic con il pulsante destro del mouse sull'impostazione e scegliere **Elimina impostazione.** È anche possibile modificare le impostazioni direttamente nel portale di Azure.

Per applicare le modifiche apportate tramite il portale o tramite **Azure Explorer** al file *local. Settings. JSON* , fare clic con il pulsante destro del mouse sul nodo **Impostazioni applicazione** e selezionare il comando **Scarica impostazioni remote...** . È anche possibile usare il riquadro comandi per selezionare le **funzioni di Azure: Comando Scarica impostazioni** remote.

## <a name="add-a-second-function"></a>Aggiungere una seconda funzione

Dopo la prima distribuzione, è possibile apportare modifiche al codice, ad esempio l'aggiunta di funzioni aggiuntive e la ridistribuzione nella stessa app per le funzioni.

1. Nell'area **Azure: Esplora** funzioni, selezionare il comando **Crea funzione** o usare **funzioni di Azure: Creare una** funzione dal riquadro comandi. Specificare i dettagli seguenti per la funzione:

    - Template: Trigger HTTP
    - Nome: "DigitsOfPi"
    - Livello di autorizzazione: Anonymous

1. In Visual Studio Code File Explorer è una sottocartella con il nome della funzione che contiene di nuovo i file denominati  *\_ \_\_init\_. py*, *Function. JSON*e *Sample. dat*.

1. Sostituire il contenuto di  *\_ \_init.py\_in modo che corrisponda al codice seguente, che genera una stringa contenente il valore di pi greco a un numero di cifre specificato nell'URL (questo codice usa solo un parametro URL)\_*

    ```python
    import logging

    import azure.functions as func

    """ Adapted from the second, shorter solution at http://www.codecodex.com/wiki/Calculate_digits_of_pi#Python
    """

    def pi_digits_Python(digits):
        scale = 10000
        maxarr = int((digits / 4) * 14)
        arrinit = 2000
        carry = 0
        arr = [arrinit] * (maxarr + 1)
        output = ""

        for i in range(maxarr, 1, -14):
            total = 0
            for j in range(i, 0, -1):
                total = (total * j) + (scale * arr[j])
                arr[j] = total % ((j * 2) - 1)
                total = total / ((j * 2) - 1)

            output += "%04d" % (carry + (total / scale))
            carry = total % scale

        return output;

    def main(req: func.HttpRequest) -> func.HttpResponse:
        logging.info('DigitsOfPi HTTP trigger function processed a request.')

        digits_param = req.params.get('digits')

        if digits_param is not None:
            try:
                digits = int(digits_param)
            except ValueError:
                digits = 10   # A default

            if digits > 0:
                digit_string = pi_digits_Python(digits)

                # Insert a decimal point in the return value
                return func.HttpResponse(digit_string[:1] + '.' + digit_string[1:])

        return func.HttpResponse(
             "Please pass the URL parameter ?digits= to specify a positive number of digits.",
             status_code=400
        )
    ```

1. Poiché il codice supporta solo HTTP GET, modificare *Function. JSON* in modo che `"methods"` la raccolta contenga solo `"get"` (ovvero, `"post"`Rimuovi). L'intero file dovrebbe apparire come segue:

    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ]
        },
        {
          "type": "http",
          "direction": "out",
          "name": "$return"
        }
      ]
    }
    ```

1. Avviare il debugger selezionando **F5** o selezionando il comando di menu **debug** > **Avvia debug** . La finestra di **output** dovrebbe ora visualizzare entrambi gli endpoint nel progetto:

    ```output
    Http Functions:

            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. In un browser o da CURL, effettuare una richiesta a `http://localhost:7071/api/DigitsOfPi?digits=125` e osservare l'output. (È possibile notare che l'algoritmo del codice non è completamente accurato, ma i miglioramenti verranno lasciati dall'utente). Al termine, arrestare il debugger.

1. Ridistribuire il codice usando il **app per le funzioni Distribuisci** in **Azure: Esplora** funzioni. Se richiesto, selezionare il app per le funzioni creato in precedenza.

1. Al termine della distribuzione (richiede qualche minuto), nella finestra di **output** vengono visualizzati gli endpoint pubblici con cui è possibile ripetere i test.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=06-second-function)

## <a name="add-a-binding-to-write-messages-to-azure-storage"></a>Aggiungere un'associazione per scrivere messaggi in archiviazione di Azure

Un' _associazione_ consente di connettere il codice della funzione alle risorse, ad esempio archiviazione di Azure, senza scrivere alcun codice di accesso ai dati. Un'associazione viene definita nel file *Function. JSON* e può rappresentare sia l'input che l'output. Una funzione può usare più associazioni di input e output, ma solo un trigger. Per altre informazioni, vedere [Concetti su trigger e binding di Funzioni di Azure](functions-triggers-bindings.md).

In questa sezione viene aggiunta un'associazione di archiviazione alla funzione HttpExample creata in precedenza in questa esercitazione. La funzione usa questa associazione per scrivere messaggi nell'archivio con ogni richiesta. Lo spazio di archiviazione in questione usa lo stesso account di archiviazione predefinito usato dall'app per le funzioni. Se si prevede di utilizzare l'archiviazione in modo intensivo, tuttavia, è consigliabile creare un account separato.

1. Sincronizzare le impostazioni remote per il progetto funzioni di Azure nel file *local. Settings. JSON* aprendo il riquadro comandi e selezionando **funzioni di Azure: Scaricare le impostazioni**remote. Aprire *local. Settings. JSON* e verificare che contenga un valore per `AzureWebJobsStorage`. Tale valore è la stringa di connessione per l'account di archiviazione.

1. Nella cartella, fare clic con il pulsante destro del mouse su *Function. JSON*, selezionare **Aggiungi binding:** `HttpExample`

    ![Aggiungere un comando di binding in Esplora Visual Studio Code](media/tutorial-vs-code-serverless-python/add-binding-command.png)

1. Nei prompt che seguono in Visual Studio Code selezionare o specificare i valori seguenti:

    | Prompt | Valore da fornire |
    | --- | --- |
    | Imposta direzione associazione | uscita |
    | Selezionare Binding con direzione in uscita | Archiviazione code di Azure |
    | Nome usato per identificare questa associazione nel codice | messaggio |
    | Coda a cui verrà inviato il messaggio | outqueue |
    | Selezionare l'impostazione da *local. Settings. JSON* (richiedendo la connessione di archiviazione) | AzureWebJobsStorage |

1. Dopo aver effettuato queste selezioni, verificare che il binding seguente venga aggiunto al file *Function. JSON* :

    ```json
        {
          "type": "queue",
          "direction": "out",
          "name": "msg",
          "queueName": "outqueue",
          "connection": "AzureWebJobsStorage"
        }
    ```

1. Ora che è stata configurata l'associazione, è possibile usarla nel codice della funzione. Anche in questo caso, l'associazione appena definita viene visualizzata nel codice come argomento `main` della funzione  *\_in\_ \_\_init. py*. Ad esempio, è possibile modificare il  *\_ \_file\_init\_. py* in HttpExample in modo che corrisponda a quanto segue, `msg` che mostra l'uso dell'argomento per scrivere un messaggio con timestamp con il nome usato nel richiesta. I commenti illustrano le modifiche specifiche:

    ```python
    import logging
    import datetime  # MODIFICATION: added import
    import azure.functions as func

    # MODIFICATION: the added binding appears as an argument; func.Out[func.QueueMessage]
    # is the appropriate type for an output binding with "type": "queue" (in function.json).
    def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
        logging.info('Python HTTP trigger function processed a request.')

        name = req.params.get('name')
        if not name:
            try:
                req_body = req.get_json()
            except ValueError:
                pass
            else:
                name = req_body.get('name')

        if name:
            # MODIFICATION: write the a message to the message queue, using msg.set
            msg.set(f"Request made for {name} at {datetime.datetime.now()}")

            return func.HttpResponse(f"Hello {name}!")
        else:
            return func.HttpResponse(
                 "Please pass a name on the query string or in the request body",
                 status_code=400
            )
    ```

1. Per testare queste modifiche localmente, avviare di nuovo il debugger in Visual Studio Code selezionando **F5** o selezionando il comando di menu **debug** > **Avvia debug** . Come prima che la finestra di **output** visualizzi gli endpoint nel progetto.

1. In un browser visitare l'URL `http://localhost:7071/api/HttpExample?name=VS%20Code` per creare una richiesta all'endpoint HttpExample, che dovrebbe anche scrivere un messaggio nella coda.

1. Per verificare che il messaggio sia stato scritto nella coda "outqueue" (come indicato nell'associazione), è possibile utilizzare uno dei tre metodi seguenti:

    1. Accedere al [portale di Azure](https://portal.azure.com)e passare al gruppo di risorse contenente il progetto di funzioni. All'interno di tale gruppo di risorse individuare e aprire l'account di archiviazione per il progetto, quindi passare a **Code**. In questa pagina, passare a "outqueue", che dovrebbe visualizzare tutti i messaggi registrati.

    1. Aprire ed esaminare la coda con l'Azure Storage Explorer, che si integra con Visual Studio, come descritto in [connettere le funzioni ad archiviazione di Azure usando Visual Studio Code](functions-add-output-binding-storage-queue-vs-code.md), in particolare la sezione [esaminare la coda di output](functions-add-output-binding-storage-queue-vs-code.md#examine-the-output-queue) .

    1. Usare l'interfaccia della riga di comando di Azure per eseguire una query sulla coda di archiviazione, come descritto in [eseguire query sulla coda di archiviazione](functions-add-output-binding-storage-queue-python.md#query-the-storage-queue)
    
1. Per eseguire il test nel cloud, ridistribuire il codice usando la **distribuzione per app per le funzioni** in **Azure: Esplora** funzioni. Se richiesto, selezionare il app per le funzioni creato in precedenza. Al termine della distribuzione (richiede qualche minuto), la finestra di **output** Mostra di nuovo gli endpoint pubblici con cui è possibile ripetere i test.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=07-storage-binding)

## <a name="clean-up-resources"></a>Pulire le risorse

Il app per le funzioni creato include risorse che possono comportare costi minimi (vedere [prezzi di funzioni](https://azure.microsoft.com/pricing/details/functions/)). Per pulire le risorse, fare clic con il pulsante destro del mouse **sul app per le funzioni in Azure: Esplora** funzioni e selezionare **Elimina app per le funzioni**. È anche possibile visitare il [portale di Azure](https://portal.azure.com), selezionare **gruppi di risorse** nel riquadro di spostamento a sinistra, selezionare il gruppo di risorse creato nel processo di questa esercitazione, quindi usare il comando **Elimina gruppo di risorse** .

## <a name="next-steps"></a>Passaggi successivi

Congratulazioni per aver completato questa procedura dettagliata sulla distribuzione di codice Python in funzioni di Azure. A questo punto si è pronti per creare molte altre funzioni senza server.

Come indicato in precedenza, è possibile ottenere altre informazioni sull'estensione funzioni visitando il relativo repository GitHub, [VSCODE-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions). Sono inoltre benvenuti i problemi e i contributi.

Vedere [Panoramica di funzioni di Azure](functions-overview.md) per esaminare i diversi trigger che è possibile usare.

Per altre informazioni sui servizi di Azure che è possibile usare da Python, inclusa l'archiviazione dei dati insieme AI servizi di intelligenza artificiale e Machine Learning, visitare il [centro per sviluppatori Python di Azure](/azure/python/?view=azure-python).

Sono disponibili anche altre estensioni di Azure per Visual Studio Code che possono risultare utili. È sufficiente eseguire una ricerca in "Azure" in Extensions Explorer:

![Estensioni di Azure per Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-extensions.png)

Di seguito sono riportate alcune estensioni comuni:

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Servizio app di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)
- [Strumenti dell'interfaccia della riga di comando di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
