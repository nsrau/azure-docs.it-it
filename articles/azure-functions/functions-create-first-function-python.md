---
title: Creare la prima funzione Python in Azure
description: Informazioni su come creare in Azure la prima funzione Python usando Azure Functions Core Tools e l'interfaccia della riga di comando di Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 08/29/2018
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 2c14cdf963d1d6c792e6101d8a2179a560dcf096
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731258"
---
# <a name="create-your-first-python-function-in-azure-preview"></a>Creare la prima funzione Python in Azure (anteprima)

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

Questo articolo di avvio rapido illustra come usare l'interfaccia della riga di comando di Azure per creare la prima app per le funzioni Python [serverless](https://azure.com/serverless) eseguita in Linux. Il codice della funzione viene creato in locale e quindi distribuito in Azure usando [Azure Functions Core Tools](functions-run-local.md). Per altre considerazioni sull'anteprima per l'esecuzione delle app per le funzioni in Linux, vedere [questo articolo su Funzioni in Linux](https://aka.ms/funclinux).

I passaggi seguenti sono supportati in computer Mac, Windows o Linux.

## <a name="prerequisites"></a>Prerequisiti

Per compilare e testare l'app in locale, è necessario:

+ Installare [Python 3.6](https://www.python.org/downloads/)

+ Installare [Azure Functions Core Tools](functions-run-local.md#v2) versione 2.2.70 o successiva (richiede .NET Core 2.x SDK).

Per pubblicare ed eseguire l'app in Azure:

+ Installare l'[interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli) versione 2.x o successiva.

+ È necessaria una sottoscrizione di Azure attiva.
  [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Creare e attivare un ambiente virtuale

Per creare un progetto di Funzioni, è necessario che lavorare in un ambiente virtuale Python 3.6. Eseguire i comandi seguenti per creare e attivare un ambiente virtuale denominato `.env`.

```bash
# In Bash
python3.6 -m venv .env
source .env/bin/activate

# In PowerShell
py -3.6 -m venv .env
.env\scripts\activate
```

## <a name="create-a-local-functions-project"></a>Creare un progetto Funzioni locale

Ora è possibile creare un progetto Funzioni locale. Questa directory è l'equivalente di un'app per le funzioni in Azure. Può contenere più funzioni che condividono la stessa configurazione di hosting e locale.

In un prompt dei comandi o una finestra del terminale eseguire il comando seguente:

```bash
func init MyFunctionProj
```

Scegliere **python** come runtime.

```output
Select a worker runtime:
1. dotnet
2. node
3. python
```

L'output visualizzato sarà simile al seguente.

```output
Installing wheel package
Installing azure-functions package
Installing azure-functions-worker package
Running pip freeze
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing /MyFunctionProj/.vscode/extensions.json
```

Viene creata una nuova cartella denominata _MyFunctionProj_. Per continuare, passare alla directory di questa cartella.

```bash
cd MyFunctionProj
```

## <a name="create-a-function"></a>Creare una funzione

Eseguire il comando seguente per creare una funzione:

```bash
func new
```

Scegliere `HTTP Trigger` come modello e fornire `HttpTrigger` come **nome funzione**.

```output
Select a template:
1. Blob trigger
2. Cosmos DB trigger
3. Event Grid trigger
4. Event Hub trigger
5. HTTP trigger
6. Queue trigger
7. Service Bus Queue trigger
8. Service Bus Topic trigger
9. Timer trigger

Choose option: 5
Function name: HttpTrigger
```

L'output visualizzato sarà simile al seguente.

```output
Writing /MyFunctionProj/HttpTrigger/sample.dat
Writing /MyFunctionProj/HttpTrigger/__init__.py
Writing /MyFunctionProj/HttpTrigger/function.json
The function "HttpTrigger" was created successfully from the "HTTP trigger" template.
```

Viene creata una sottocartella denominata _HttpTrigger_. Questa contiene `__init__.py`, che è il file di script principale, e il file `function.json`, che descrive il trigger e le associazioni usate dalla funzione. Per altre informazioni sul modello di programmazione, vedere la [guida per sviluppatori di Funzioni di Azure con Python](functions-reference-python.md).

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Usare il comando seguente per eseguire l'host di funzioni in locale.

```bash
func host start
```

Quando viene avviato l'host di Funzioni, restituisce come output l'URL delle funzioni attivate da HTTP. Si noti che l'intero output è stato troncato per migliorare la leggibilità.

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %
...
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.
...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger
```

Copiare l'URL della funzione dall'output e incollarlo nella barra degli indirizzi del browser. Aggiungere la stringa di query `?name=<yourname>` all'URL ed eseguire la richiesta.

    http://localhost:7071/api/HttpTrigger?name=<yourname>

Lo screenshot seguente mostra la risposta dalla funzione, quando viene attivata dal browser:

![test](./media/functions-create-first-function-python/function-test-local-browser.png)

A questo punto si è pronti per creare un'app per le funzioni e le altre risorse necessarie per la pubblicazione in Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Creare un'app per le funzioni Linux in Azure

L'app per le funzioni offre un ambiente per l'esecuzione del codice delle funzioni. Consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione delle risorse. Creare un'**app per le funzioni Python eseguita in Linux** usando il comando [az functionapp create](/cli/azure/functionapp).

Eseguire il comando seguente sostituendo il segnaposto `<app_name>` con un nome univoco dell'app per le funzioni e il segnaposto `<storage_name>` con il nome dell'account di archiviazione. `<app_name>` è anche il dominio DNS predefinito per l'app per le funzioni. Questo nome deve essere univoco in tutte le app di Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <app_name> --storage-account  <storage_name>
```

> [!NOTE]
> Se esiste un gruppo di risorse esistente denominato `myResourceGroup` con eventuali app del servizio app non Linux, è necessario usare un gruppo di risorse diverso. Non è possibile ospitare app Windows e Linux nello stesso gruppo di risorse.  

Dopo aver creato l'app per le funzioni, verrà visualizzato il messaggio seguente:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

A questo punto si è pronti per pubblicare il progetto Funzioni locale nell'app per le funzioni in Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Implementare il progetto dell'app per le funzioni in Azure

Usando Azure Functions Core Tools, eseguire il comando seguente. Sostituire `<app_name>` con il nome del'app creata nel passaggio precedente.

```bash
func azure functionapp publish <app_name>
```

Verrà visualizzata una schermata simile alla seguente, che è stata troncata per migliorarne la leggibilità.

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sullo sviluppo di Funzioni di Azure con Python.

> [!div class="nextstepaction"]
> [Guida per sviluppatori di Funzioni di Azure con Python](functions-reference-python.md)
> [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
