---
title: 'Esercitazione: Attivare un gruppo di contenitori per funzione di Azure'
description: Creare una funzione di PowerShell serverless, attivata tramite HTTP, per automatizzare la creazione di istanze di Azure Container
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: ''
ms.openlocfilehash: 49eb0721972a92f33bda2532367bc78280b6e655
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533367"
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

> [!IMPORTANT]
> PowerShell per Funzioni di Azure è attualmente disponibile in versione di anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="prerequisites"></a>Prerequisiti

Per informazioni sui prerequisiti necessari per installare e usare Visual Studio Code con Funzioni di Azure nel sistema operativo in uso, vedere [Creare la prima funzione di PowerShell in Azure](../azure-functions/functions-create-first-function-powershell.md#prerequisites).

In alcuni passaggi di questo articolo si usa l'interfaccia della riga di comando di Azure. Per completare questi passaggi, è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="create-a-basic-powershell-function"></a>Creare una funzione di base di PowerShell

Per creare una funzione di PowerShell tramite il modello Trigger HTTP, seguire la procedura descritta in [Creare la prima funzione di PowerShell in Azure](../azure-functions/functions-create-first-function-powershell.md). Usare il nome predefinito di funzione di Azure, **HttpTrigger**. Come illustrato nell'argomento di avvio rapido, testare la funzione in locale e pubblicare il progetto in un'app per le funzioni di Azure. Questo esempio è una funzione di base attivata tramite HTTP che restituisce una stringa di testo. Nei passaggi successivi di questo articolo la funzione verrà modificata per creare un gruppo di contenitori.

Questo articolo presuppone che il progetto venga pubblicato con il nome *myfunctionapp* in un gruppo di risorse di Azure con il nome assegnato automaticamente in base al nome dell'app per le funzioni, anch'esso *myfunctionapp*. Nei passaggi successivi sostituire questi nomi con i nomi univoci dell'app per le funzioni e del gruppo di risorse.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Abilitare un'identità gestita di Azure nell'app per le funzioni

Abilitare ora un'[identità gestita](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#adding-a-system-assigned-identity) assegnata dal sistema nell'app per le funzioni. L'host di PowerShell che esegue l'app può eseguire automaticamente l'autenticazione usando questa identità, consentendo alle funzioni di eseguire azioni nei servizi di Azure per cui è stato concesso l'accesso all'identità. In questa esercitazione concedere all'identità gestita le autorizzazioni per creare risorse nel gruppo di risorse dell'app per le funzioni. 

Usare prima di tutto il comando [az group show][az-group-show] per ottenere l'ID del gruppo di risorse dell'app per le funzioni e archiviarlo in una variabile di ambiente. Questo esempio presuppone che il comando venga eseguito in una shell Bash.

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

Eseguire [az functionapp identity app assign][az-functionapp-identity-app-assign] per assegnare un'identità locale all'app per le funzioni e un ruolo di collaboratore al gruppo di risorse. Questo ruolo consente all'identità di creare risorse aggiuntive, ad esempio gruppi di contenitori nel gruppo di risorse.

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>Modificare la funzione HttpTrigger

Modificare il codice di PowerShell per la funzione **HttpTrigger** in modo da creare un gruppo di contenitori. Nel file `run.ps1` per la funzione trovare il blocco di codice seguente. Il codice visualizza un valore di nome, se ne è stato passato uno come stringa di query nell'URL della funzione:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

Sostituire questo codice con il blocco di esempio seguente. In questo esempio il valore di nome, se passato nella stringa di query, viene usato per denominare e creare un gruppo di contenitori tramite il cmdlet [New-AzContainerGroup][new-azcontainergroup]. Assicurarsi di sostituire il nome del gruppo di risorse *myfunctionapp* con il nome del gruppo di risorse per l'app per le funzioni:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    $body = "Started container group $name"
}
[...]
```

Questo esempio crea un gruppo di contenitori costituito da una singola istanza di contenitore che esegue l'immagine `alpine`. Il contenitore esegue un singolo comando `echo` e quindi termina. In un esempio reale, sarebbe possibile attivare la creazione di uno o più gruppi di contenitori per l'esecuzione di un processo batch.
 
## <a name="test-function-app-locally"></a>Testare l'app per le funzioni in locale

Assicurarsi che la funzione venga eseguita correttamente in locale prima di ripubblicare il progetto di app per le funzioni in Azure. Come illustrato nell'argomento di [avvio rapido su PowerShell](../azure-functions/functions-create-first-function-powershell.md), inserire un punto di interruzione locale nello script di PowerShell e una chiamata a `Wait-Debugger` al di sopra. Per le istruzioni sul debug, vedere [Eseguire il debug di Funzioni di Azure in PowerShell in locale](../azure-functions/functions-debug-powershell-local.md).


## <a name="republish-azure-function-app"></a>Ripubblicare l'app per le funzioni di Azure

Dopo aver verificato la corretta esecuzione della funzione nel computer locale, è possibile ripubblicare il progetto nell'app per le funzioni esistente in Azure.

> [!NOTE]
> Ricordare di rimuovere le chiamate a `Wait-Debugger` prima di pubblicare le funzioni in Azure.

1. In Visual Studio Code aprire il riquadro comandi. Cercare e selezionare `Azure Functions: Deploy to function app...`.
1. Selezionare la cartella di lavoro corrente per la decompressione e la distribuzione.
1. Selezionare la sottoscrizione e quindi il nome dell'app per le funzioni esistente (*myfunctionapp*). Confermare di voler sovrascrivere la distribuzione precedente.

Dopo la creazione dell'app per le funzioni e dopo l'applicazione del pacchetto di distribuzione viene visualizzata una notifica. Selezionare **Visualizza output** nella notifica per visualizzare i risultati della creazione e della distribuzione, incluse le risorse di Azure aggiornate.

## <a name="run-the-function-in-azure"></a>Eseguire la funzione in Azure

Al termine della distribuzione, ottenere l'URL della funzione. Ad esempio, usare l'area **Azure: Funzioni** in Visual Studio Code per copiare l'URL della funzione **HttpTrigger** oppure ottenere l'URL della funzione nel [portale di Azure](../azure-functions/functions-create-first-azure-function.md#test-the-function).

L'URL della funzione include un codice univoco ed è in formato:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>Eseguire la funzione senza passare un nome

Come primo test, eseguire il comando `curl` e passare l'URL della funzione senza aggiungere una stringa di query `name`. Assicurarsi di includere il codice univoco della funzione.

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

La funzione restituisce il codice di stato 400 e il testo `Please pass a name on the query string or in the request body`:

```
[...]
> GET /api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M== HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 400 
< content-length: 62
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:08:15 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Please pass a name on the query string or in the request body.
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Eseguire la funzione e passare il nome di un gruppo di contenitori

Ora eseguire il comando `curl` aggiungendo il nome di un gruppo di contenitori (*mycontainergroup*) come stringa di query `&name=mycontainergroup`:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

La funzione restituisce il codice di stato 200 e attiva la creazione del gruppo di contenitori:

```
[...]
> GET /api/HttpTrigger?ode=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 200 
< content-length: 28
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:15:30 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Started container group mycontainergroup
```

Verificare che il contenitore sia stato eseguito con il comando [az container logs][az-container-logs]:

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

Output di esempio:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse create in questa esercitazione non sono più necessarie, è possibile eseguire il comando [az group delete][az-group-delete] per rimuovere il gruppo di risorse e tutte le risorse al suo interno. Questo comando elimina il registro del contenitore creato, nonché il contenitore in esecuzione e tutte le risorse correlate.

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

Per le istruzioni dettagliate sulla creazione di funzioni di Azure e sulla pubblicazione di un progetto di funzione, vedere la [documentazione di Funzioni di Azure](/azure/azure-functions/). 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-functionapp-identity-app-assign]: /cli/azure/functionapp/identity#az-functionapp-identity-assign
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[az-container-logs]: /cli/azure/container#az-container-logs
