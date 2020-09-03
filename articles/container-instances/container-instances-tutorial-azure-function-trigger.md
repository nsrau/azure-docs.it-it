---
title: 'Esercitazione: Attivare un gruppo di contenitori per funzione di Azure'
description: Creare una funzione di PowerShell serverless, attivata tramite HTTP, per automatizzare la creazione di istanze di Azure Container
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ec4b2273f6be6ea4aabed2b660e0b7553f861d0d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072044"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Esercitazione: Usare una funzione di Azure attivata tramite HTTP per creare un gruppo di contenitori

[Funzioni di Azure](../azure-functions/functions-overview.md) è un servizio di calcolo serverless che consente di eseguire script o codice in risposta a un'ampia varietà di eventi, ad esempio richieste HTTP, timer o messaggi inclusi in una coda di Archiviazione di Azure.

In questa esercitazione viene creata una funzione di Azure che accetta una richiesta HTTP e attiva la distribuzione di un [gruppo di contenitori](container-instances-container-groups.md). Questo esempio illustra l'uso di base di Funzioni di Azure per creare automaticamente risorse in Istanze di Azure Container. Modificare o estendere l'esempio per scenari più complessi o altri trigger di eventi. 

Si apprenderà come:

> [!div class="checklist"]
> * Usare Visual Studio Code con l'[estensione Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per creare una funzione di base di PowerShell attivata tramite HTTP.
> * Abilitare un'identità nell'app per le funzioni a cui assegnare le autorizzazioni per creare risorse di Azure.
> * Modificare e ripubblicare la funzione di PowerShell per automatizzare la distribuzione di un gruppo di contenitori con un singolo contenitore.
> * Verificare la distribuzione del contenitore attivata tramite HTTP.

## <a name="prerequisites"></a>Prerequisiti

Per informazioni sui prerequisiti necessari per installare e usare Visual Studio Code con l'estensione Funzioni di Azure nel sistema operativo in uso, vedere [Creare la prima funzione in Azure con Visual Studio Code](../azure-functions/functions-create-first-function-vs-code.md?pivots=programming-language-powershell#configure-your-environment).

I passaggi aggiuntivi in questo articolo usano Azure PowerShell. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare Azure PowerShell][azure-powershell-install] e [Accesso ad Azure](/powershell/azure/get-started-azureps#sign-in-to-azure).

## <a name="create-a-basic-powershell-function"></a>Creare una funzione di base di PowerShell

Per creare una funzione di PowerShell tramite il modello Trigger HTTP, seguire la procedura descritta in [Creare la prima funzione di PowerShell in Azure](../azure-functions/functions-create-first-function-vs-code.md?pivots=programming-language-powershell). Usare il nome predefinito di funzione di Azure, **HttpTrigger**. Come illustrato nell'argomento di avvio rapido, testare la funzione in locale e pubblicare il progetto in un'app per le funzioni di Azure. Questo esempio è una funzione di base attivata tramite HTTP che restituisce una stringa di testo. Nei passaggi successivi di questo articolo la funzione verrà modificata per creare un gruppo di contenitori.

Questo articolo presuppone che il progetto venga pubblicato con il nome *myfunctionapp* in un gruppo di risorse di Azure con il nome assegnato automaticamente in base al nome dell'app per le funzioni, anch'esso *myfunctionapp*. Nei passaggi successivi sostituire questi nomi con i nomi univoci dell'app per le funzioni e del gruppo di risorse.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Abilitare un'identità gestita di Azure nell'app per le funzioni

I comandi seguenti abilitano un'[identità gestita](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) assegnata dal sistema nell'app per le funzioni. L'host di PowerShell che esegue l'app può eseguire automaticamente l'autenticazione ad Azure usando questa identità, consentendo alle funzioni di eseguire azioni nei servizi di Azure per cui è stato concesso l'accesso all'identità. In questa esercitazione concedere all'identità gestita le autorizzazioni per creare risorse nel gruppo di risorse dell'app per le funzioni. 

[Aggiungere un'identità](../app-service/overview-managed-identity.md?tabs=dotnet#using-azure-powershell-1) all'app per le funzioni:

```powershell
Update-AzFunctionApp -Name myfunctionapp `
    -ResourceGroupName myfunctionapp `
    -IdentityType SystemAssigned
```

Assegnare all'identità il ruolo Collaboratore che ha come ambito il gruppo di risorse:

```powershell
$SP=(Get-AzADServicePrincipal -DisplayName myfunctionapp).Id
$RG=(Get-AzResourceGroup -Name myfunctionapp).ResourceId
New-AzRoleAssignment -ObjectId $SP -RoleDefinitionName "Contributor" -Scope $RG
```

## <a name="modify-httptrigger-function"></a>Modificare la funzione HttpTrigger

Modificare il codice di PowerShell per la funzione **HttpTrigger** in modo da creare un gruppo di contenitori. Nel file `run.ps1` per la funzione trovare il blocco di codice seguente. Il codice visualizza un valore di nome, se ne è stato passato uno come stringa di query nell'URL della funzione:

```powershell
[...]
if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}
[...]
```

Sostituire questo codice con il blocco di esempio seguente. In questo esempio il valore di nome, se passato nella stringa di query, viene usato per denominare e creare un gruppo di contenitori tramite il cmdlet [New-AzContainerGroup][new-azcontainergroup]. Assicurarsi di sostituire il nome del gruppo di risorse *myfunctionapp* con il nome del gruppo di risorse per l'app per le funzioni:

```powershell
[...]
if ($name) {
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    if ($?) {
        $body = "This HTTP triggered function executed successfully. Started container group $name"
    }
    else  {
        $body = "There was a problem starting the container group."
    }
[...]
```

Questo esempio crea un gruppo di contenitori costituito da una singola istanza di contenitore che esegue l'immagine `alpine`. Il contenitore esegue un singolo comando `echo` e quindi termina. In un esempio reale, sarebbe possibile attivare la creazione di uno o più gruppi di contenitori per l'esecuzione di un processo batch.
 
## <a name="test-function-app-locally"></a>Testare l'app per le funzioni in locale

Assicurarsi che la funzione venga eseguita in locale prima di ripubblicare il progetto di app per le funzioni in Azure. Quando viene eseguita in locale, la funzione non crea risorse di Azure. Tuttavia, è possibile testare il flusso della funzione con e senza passare un valore di nome in una stringa di query. Per eseguire il debug della funzione, vedere [Eseguire il debug di Funzioni di Azure in PowerShell in locale](../azure-functions/functions-debug-powershell-local.md).

## <a name="republish-azure-function-app"></a>Ripubblicare l'app per le funzioni di Azure

Dopo aver verificato che la funzione viene eseguita in locale, ripubblicare il progetto nell'app per le funzioni esistente in Azure.

1. In Visual Studio Code aprire il riquadro comandi. Cercare e selezionare `Azure Functions: Deploy to Function App...`.
1. Selezionare la cartella di lavoro corrente per la decompressione e la distribuzione.
1. Selezionare la sottoscrizione e quindi il nome dell'app per le funzioni esistente (*myfunctionapp*). Confermare di voler sovrascrivere la distribuzione precedente.

Dopo la creazione dell'app per le funzioni e dopo l'applicazione del pacchetto di distribuzione viene visualizzata una notifica. Selezionare **Visualizza output** nella notifica per visualizzare i risultati della creazione e della distribuzione, incluse le risorse di Azure aggiornate.

## <a name="run-the-function-in-azure"></a>Eseguire la funzione in Azure

Al termine della distribuzione, ottenere l'URL della funzione. Ad esempio, usare l'area **Azure: Funzioni** in Visual Studio Code per copiare l'URL della funzione **HttpTrigger** oppure ottenere l'URL della funzione nel [portale di Azure](../azure-functions/functions-create-first-azure-function.md#test-the-function).

Il formato dell'URL della funzione è il seguente:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger
```

### <a name="run-function-without-passing-a-name"></a>Eseguire la funzione senza passare un nome

Come primo test, eseguire il comando `curl` e passare l'URL della funzione senza aggiungere una stringa di query `name`. 

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger"
```

La funzione restituisce il codice di stato 200 e il testo `This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response`:

```
[...]
> GET /api/HttpTrigger? HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/1.1 200 OK
< Content-Length: 135
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:50:27 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.* Closing connection 0
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Eseguire la funzione e passare il nome di un gruppo di contenitori

Ora eseguire il comando `curl` aggiungendo il nome di un gruppo di contenitori (*mycontainergroup*) come stringa di query `?name=mycontainergroup`:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?name=mycontainergroup"
```

La funzione restituisce il codice di stato 200 e attiva la creazione del gruppo di contenitori:

```
[...]
> GET /api/HttpTrigger1?name=mycontainergroup HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
< HTTP/1.1 200 OK
< Content-Length: 92
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:54:31 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Started container group mycontainergroup* Closing connection 0
```

Verificare che il contenitore sia stato eseguito con il comando [Get-AzContainerInstanceLog][get-azcontainerinstancelog]:

```azurecli
Get-AzContainerInstanceLog -ResourceGroupName myfunctionapp `
  -ContainerGroupName mycontainergroup 
```

Output di esempio:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse create in questa esercitazione non sono più necessarie, è possibile eseguire il comando [az group delete][az-group-delete] per rimuovere il gruppo di risorse e tutte le risorse al suo interno. Questo comando elimina l'app per le funzioni creata, nonché il contenitore in esecuzione e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata una funzione di Azure che accetta una richiesta HTTP e attiva la distribuzione di un gruppo di contenitori. Si è appreso come:

> [!div class="checklist"]
> * Usare Visual Studio Code con l'estensione Funzioni di Azure per creare una funzione di base di PowerShell attivata tramite HTTP.
> * Abilitare un'identità nell'app per le funzioni a cui assegnare le autorizzazioni per creare risorse di Azure.
> * Modificare il codice della funzione di PowerShell per automatizzare la distribuzione di un gruppo di contenitori con un singolo contenitore.
> * Verificare la distribuzione del contenitore attivata tramite HTTP.

Per un esempio dettagliato dell'avvio e del monitoraggio di un processo aggiunto a contenitore, vedere il post di blog [Event-Driven Serverless Containers with PowerShell Azure Functions and Azure Container Instances](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) e l'[esempio di codice](https://github.com/anthonychu/functions-powershell-run-aci) associato.

Per le istruzioni dettagliate sulla creazione di funzioni di Azure e sulla pubblicazione di un progetto di funzione, vedere la [documentazione di Funzioni di Azure](../azure-functions/index.yml). 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-powershell-install]: /powershell/azure/install-az-ps
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[get-azcontainerinstancelog]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
