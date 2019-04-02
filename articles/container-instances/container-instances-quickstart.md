---
title: Avvio rapido - Distribuire un contenitore Docker in Istanze di Azure Container - Interfaccia della riga di comando
description: In questo argomento di avvio rapido viene usata l'interfaccia della riga di comando di Azure per distribuire rapidamente un'app Web che viene eseguita in un'istanza di contenitore di Azure isolata
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 8e504a081f8685107871aed920077dd75a70dfa7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368511"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>Guida introduttiva: Distribuire un'istanza di contenitore in Azure con l'interfaccia della riga di comando di Azure

Il servizio Istanze di Azure Container consente di eseguire contenitori Docker serverless in Azure in modo semplice e rapido. Distribuire un'applicazione in un'istanza di contenitore su richiesta quando non è necessaria una piattaforma di orchestrazione di contenitori completa come il servizio Azure Kubernetes.

In questo argomento di avvio rapido viene usata l'interfaccia della riga di comando di Azure per distribuire un contenitore Docker isolato e renderne disponibile l'applicazione con un nome di dominio completo (FQDN). Pochi secondi dopo aver eseguito un singolo comando di distribuzione, è possibile passare all'applicazione in esecuzione nel contenitore:

![App distribuita in Istanze di Azure Container visualizzata nel browser][aci-app-browser]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito][azure-account] prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per completare questa guida introduttiva è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Se si preferisce l'interfaccia locale, è consigliabile usare la versione 2.0.55 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Le istanze di contenitore di Azure, analogamente a tutte le risorse di Azure, devono essere distribuite in un gruppo di risorse. I gruppi di risorse consentono di organizzare e gestire le risorse di Azure correlate.

Per iniziare, creare un gruppo di risorse denominato *myResourceGroup* nell'area *eastus* con il comando [az group create][az-group-create] seguente:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Creare un contenitore

Dopo aver creato il gruppo di risorse, è possibile eseguire un contenitore in Azure. Per creare un'istanza di contenitore con l'interfaccia della riga di comando di Azure, specificare il nome del gruppo di risorse, il nome dell'istanza di contenitore e l'immagine del contenitore Docker nel comando [az container create][az-container-create]. In questo argomento di avvio rapido viene usata l'immagine `mcr.microsoft.com/azuredocs/aci-helloworld` pubblica. In questa immagine è inclusa una piccola app Web scritta in Node.js che distribuisce una pagina HTML statica.

È possibile esporre i contenitori in Internet specificando una o più porte da aprire, un'etichetta del nome DNS o entrambi. In questa guida introduttiva si distribuisce un contenitore con un'etichetta del nome DNS in modo che l'app Web sia raggiungibile pubblicamente.

Per avviare un'istanza di contenitore, eseguire un comando simile al seguente. Impostare un valore di `--dns-name-label` univoco all'interno dell'area di Azure in cui si crea l'istanza. Se si riceve il messaggio di errore "L'etichetta del nome DNS non è disponibile", provare con un'etichetta del nome DNS diversa.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label aci-demo --ports 80
```

Entro pochi secondi si dovrebbe ricevere una risposta dall'interfaccia della riga di comando di Azure che indica che è stata completata la distribuzione. Controllarne lo stato usando il comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

Quando si esegue il comando, vengono visualizzati il nome di dominio completo (FQDN) del contenitore e il relativo lo stato di provisioning.

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Se il valore `ProvisioningState` del contenitore è **Operazione completata**, passare al relativo indirizzo FQDN nel browser. Se viene visualizzata una pagina Web simile all'immagine seguente, congratulazioni! È stata completata la distribuzione di un'applicazione in esecuzione in un contenitore Docker in Azure.

![Screenshot del browser che mostra l'applicazione in esecuzione in un'istanza di contenitore di Azure][aci-app-browser]

Se inizialmente l'applicazione non viene visualizzata, può essere necessario attendere alcuni secondi il completamento della propagazione DNS, quindi provare ad aggiornare il browser.

## <a name="pull-the-container-logs"></a>Effettuare il pull dei log del contenitore

Quando è necessario risolvere i problemi di un contenitore o dell'applicazione in esecuzione (o semplicemente visualizzarne l'output), iniziare visualizzando i log dell'istanza del contenitore.

Effettuare il pull dei log dell'istanza del contenitore con il comando [az container logs][az-container-logs]:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

L'output visualizza i log per il contenitore e dovrebbe mostrare le richieste HTTP GET generate quando l'applicazione è stata visualizzata nel browser.

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
listening on port 80
::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="attach-output-streams"></a>Collegare i flussi di output

Oltre a visualizzare i log, è possibile collegare i flussi di errore e di output standard locali a quello del contenitore.

Per prima cosa, eseguire il comando [az container attach][az-container-attach] per collegare la console locale ai flussi di output del contenitore:

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

Al termine, aggiornare il browser alcune volte per generare output aggiuntivo. Scollegare infine la console con `Control+C`. L'output dovrebbe essere simile al seguente:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 17:27:20+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:24+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Started container

Start streaming logs:
listening on port 80

::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:47:01 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.56 - - [21/Mar/2019:17:47:12 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il contenitore non è più necessario, rimuoverlo usando il comando [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Per verificare che il contenitore sia stato eliminato, eseguire il comando [az container list](/cli/azure/container#az-container-list):

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

Il contenitore **mycontainer** non deve essere visualizzato nell'output del comando. Se nel gruppo di risorse non sono presenti altri contenitori, non viene visualizzato alcun output.

Se il gruppo di risorse *myResourceGroup* e tutte le risorse contenute non sono più necessari, eliminarli con il comando [az group delete][az-group-delete]:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata un'istanza di contenitore di Azure usando un'immagine Microsoft pubblica. Per provare a creare un'immagine del contenitore e a distribuirla da un registro contenitori di Azure privato, passare all'esercitazione su Istanze di Azure Container.

> [!div class="nextstepaction"]
> [Esercitazione su Istanze di Azure Container](./container-instances-tutorial-prepare-app.md)

Per provare le opzioni per l'esecuzione di contenitori in un sistema di orchestrazione in Azure, vedere la [guida introduttiva per il servizio Azure Kubernetes][container-service].

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: https://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
