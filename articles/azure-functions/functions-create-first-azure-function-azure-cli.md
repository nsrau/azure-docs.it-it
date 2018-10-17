---
title: Creare la prima funzione usando l'interfaccia della riga di comando di Azure
description: Informazioni su come creare la prima funzione di Azure per l'esecuzione senza server tramite l'interfaccia della riga di comando di Azure e Core Tools di Funzioni di Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 09/10/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: f65ff3e38e3768dccf04e9eaa4345ffcea4daa9e
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451121"
---
# <a name="create-your-first-function-from-the-command-line"></a>Creare la prima funzione dalla riga di comando

Questa guida introduttiva illustra come creare la prima funzione dalla riga di comando o terminale. Si userà l'interfaccia della riga di comando di Azure per creare un'app per le funzioni, ovvero l'infrastruttura [senza server](https://azure.microsoft.com/overview/serverless-computing/) che ospita la funzione. Il progetto del codice della funzione viene generato da un modello tramite [Core Tools di Funzioni di Azure](functions-run-local.md), che viene usato anche per distribuire il progetto dell'app della funzione in Azure.

È possibile eseguire queste procedure con un computer Mac, Windows o Linux.

## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire questo esempio, è necessario disporre di quanto segue:

+ Installare la [versione 2.x di Core Tools di Azure](functions-run-local.md#v2).

+ Installare l'[interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). Questo articolo richiede l'interfaccia della riga di comando di Azure 2.0 o versioni successive. Eseguire `az --version` per trovare la versione in uso. È possibile usare anche [Azure Cloud Shell](https://shell.azure.com/bash).

+ Una sottoscrizione di Azure attiva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Creare un progetto di app per la funzione locale

Eseguire il comando seguente dalla riga di comando per creare un progetto di app della funzione nella cartella `MyFunctionProj` della directory locale corrente. Viene creato anche un repository di GitHub in `MyFunctionProj`.

```bash
func init MyFunctionProj
```

Quando richiesto, usare i tasti di direzione per selezionare un runtime del ruolo di lavoro dalle seguenti opzioni linguaggio:

+ `dotnet`: crea un progetto libreria di classi .NET (.csproj).
+ `node`: crea un progetto JavaScript.

Quando viene eseguito il comando, viene visualizzato qualcosa di simile a quanto segue:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

## <a name="create-a-function"></a>Creare una funzione

Il comando seguente consente di passare al nuovo progetto e crea una funzione attivata tramite HTTP denominata `MyHtpTrigger`.

```bash
cd MyFunctionProj
func new --name MyHttpTrigger --template "HttpTrigger"
```

Quando viene eseguito il comando, viene visualizzato qualcosa di simile a quanto segue, che è una funzione di JavaScript:

```output
Writing C:\functions\MyFunctionProj\MyHttpTrigger\index.js
Writing C:\functions\MyFunctionProj\MyHttpTrigger\sample.dat
Writing C:\functions\MyFunctionProj\MyHttpTrigger\function.json
```

## <a name="edit-the-function"></a>Modificare la funzione

Per impostazione predefinita, il modello crea una funzione che richiede una chiave di funzione quando si effettuano richieste. Per rendere più semplice testare la funzione in Azure, è necessario aggiornare la funzione per consentire l'accesso anonimo. La modalità con cui si apporta questa modifica dipende dal linguaggio del progetto delle funzioni.

### <a name="c"></a>C\#

Aprire il file con codice MyHttpTrigger.cs che è la nuova funzione e aggiornare l'attributo **AuthorizationLevel** nella definizione di funzione con un valore di `anonymous` e salvare le modifiche.

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

Aprire il file function.json per la nuova funzione, aprirlo in un editor di testo, aggiornare la proprietà **authLevel** in **bindings.httpTrigger** su `anonymous` e salvare le modifiche.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
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
```

A questo punto è possibile chiamare la funzione in Azure senza la necessità di fornire la chiave della funzione. La chiave della funzione non è mai necessaria durante l'esecuzione in locale.

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Il comando seguente avvia l'app della funzione. L'app viene eseguita tramite lo stesso runtime di funzioni di Azure presente in Azure.

```bash
func host start --build
```

L'opzione `--build` è necessaria per compilare i progetti C#. Questa opzione non è necessaria per un progetto di JavaScript.

Quando viene avviato l'host delle funzioni, verrà visualizzata una schermata simile alla seguente, la quale è stata troncata per migliorarne la leggibilità:

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

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Copiare l'URL della funzione `HTTPTrigger` dall'output del runtime e incollarlo nella barra degli indirizzi del browser. Aggiungere la stringa di query `?name=<yourname>` all'URL ed eseguire la richiesta. Di seguito è illustrata la risposta nel browser alla richiesta GET restituita dalla funzione locale:

![Testare in locale nel browser](./media/functions-create-first-azure-function-azure-cli/functions-test-local-browser.png)

Ora che è stata eseguita la funzione in locale, è possibile creare l'app della funzione e altre risorse necessarie in Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

Per ospitare l'esecuzione delle funzioni è necessaria un'app per le funzioni. L'app per le funzioni offre un ambiente per l'esecuzione senza server del codice di funzione. Consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione delle risorse. Creare un'app per le funzioni usando il comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

Nel comando seguente sostituire il segnaposto `<app_name>` con il nome univoco dell'app per le funzioni e il nome dell'account di archiviazione con `<storage_name>`. Dato che verrà usato come dominio DNS predefinito per l'app per le funzioni, è necessario che `<app_name>` sia univoco tra tutte le app in Azure. Il parametro _deployment-source-url_ è un repository di esempio in GitHub che contiene una funzione attivata da HTTP "Hello World".

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name>  
```

L'impostazione del parametro _consumption-plan-location_ indica che l'app per le funzioni è ospitata in un piano di hosting a consumo. In questo piano serverless, le risorse vengono aggiunte in modo dinamico come richiesto dalle funzioni e si paga solo quando le funzioni sono in esecuzione. Per altre informazioni, vedere [Scegliere il piano di hosting corretto](functions-scale.md).

Al termine della creazione dell'app per le funzioni, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

## <a name="configure-the-function-app"></a>Configurare l'app per le funzioni

La versione 2.x di Core Tools consente di creare progetti usando i modelli per il runtime 2.x di funzioni di Azure. Per questo motivo, è necessario assicurarsi che venga usata la versione 2.x del runtime in Azure. Se si configura l'impostazione `FUNCTIONS_WORKER_RUNTIME` dell'applicazione su `~2`, l'app per le funzioni viene associata alla versione 2.x più recente. Configurare l'impostazione dell'applicazione con il comando [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set).

Nel comando seguente dell'interfaccia della riga di comando di Azure il valore "<app_name>" corrisponde al nome dell'app per le funzioni.

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings FUNCTIONS_WORKER_RUNTIME=~2
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
