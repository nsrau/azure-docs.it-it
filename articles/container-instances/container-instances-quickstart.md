---
title: 'Guida introduttiva: Creare il primo contenitore di Istanze di contenitore di Azure'
description: Distribuire e iniziare a usare Istanze di contenitore di Azure
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: f8fe53f834e4fcf7f16174222cb51d89e40305ec
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Creare il primo contenitore in Istanze di contenitore di Azure
Istanze di contenitore di Azure semplifica la creazione e gestione di contenitori Docker in Azure, senza dover eseguire il provisioning di macchine virtuali o di adottare un servizio di livello superiore. In questa guida introduttiva viene creato un contenitore in Azure, che viene quindi esposto a Internet con un nome di dominio completo (FQDN). Per completare questa operazione, è sufficiente un solo comando. In pochi secondi, nel browser verrà visualizzato quanto segue:

![App distribuita usando Istanze di contenitore di Azure visualizzata nel browser][aci-app-browser]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito][azure-account] prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per completare questa guida introduttiva è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questa guida introduttiva richiede la versione 2.0.27 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0][azure-cli-install].

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Le istanze di contenitore di Azure sono risorse di Azure e devono essere inserite in un gruppo di risorse di Azure, una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create][az-group-create].

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Creare un contenitore

Può essere creato un contenitore specificando un nome, un'immagine Docker e un gruppo di risorse di Azure al comando [az container create][az-container-create]. Facoltativamente, è possibile esporre il contenitore in Internet specificando un'etichetta del nome DNS. In questa guida introduttiva si distribuisce un contenitore che ospita un'app Web di piccole dimensioni scritta in [Node.js][node-js].

Eseguire il comando seguente per avviare un'istanza di contenitore. Il valore `--dns-name-label` deve essere univoco all'interno dell'area di Azure in cui si crea l'istanza, quindi potrebbe essere necessario modificare questo valore per garantire l'univocità.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --dns-name-label aci-demo --ports 80
```

In pochi secondi, verrà visualizzata una risposta alla richiesta. Il contenitore inizialmente presenta lo stato **Creazione in corso**, ma viene avviato entro alcuni secondi. È possibile controllare lo stato usando il comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

Quando si esegue il comando, vengono visualizzati il nome di dominio completo (FQDN) del contenitore e il relativo lo stato di provisioning:

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Una volta che il contenitore passa allo stato **Completato**, è possibile passare al contenitore nel browser tramite il relativo FQDN:

![Screenshot del browser che mostra l'applicazione in esecuzione in un'istanza di contenitore di Azure][aci-app-browser]

## <a name="pull-the-container-logs"></a>Effettuare il pull dei log del contenitore

È possibile effettuare il pull dei log per il contenitore creato usando il comando [az container logs][az-container-logs]:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Output:

```bash
listening on port 80
::ffff:10.240.255.107 - - [29/Nov/2017:20:48:50 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
::ffff:10.240.255.107 - - [29/Nov/2017:20:48:50 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://52.224.178.107/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
```

## <a name="delete-the-container"></a>Eliminare il contenitore

Quando il contenitore non è più necessario, è possibile rimuoverlo usando il comando [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Per verificare che il contenitore sia stato eliminato, eseguire il comando [az container list](/cli/azure/container#az_container_list):

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

Il contenitore **mycontainer** non deve essere visualizzato nell'output del comando. Se nel gruppo di risorse non sono presenti altri contenitori, non viene visualizzato alcun output.

## <a name="next-steps"></a>Passaggi successivi

Tutto il codice per il contenitore usato in questa guida introduttiva è disponibile [in GitHub][app-github-repo], con il relativo documento Dockerfile. Per provare a compilarlo personalmente e a distribuirlo in Istanze di contenitore di Azure usando il Registro contenitori di Azure, passare all'esercitazione su Istanze di contenitore di Azure.

> [!div class="nextstepaction"]
> [Esercitazioni su Istanze di contenitore di Azure](./container-instances-tutorial-prepare-app.md)

Per provare le opzioni per l'esecuzione di contenitori in un sistema di orchestrazione in Azure, vedere la guida introduttiva di [Service Fabric] [ service-fabric] o del [servizio contenitore di Azure] [ container-service].

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group?view=azure-cli-latest#az_group_create
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-delete]: /cli/azure/container?view=azure-cli-latest#az_container_delete
[az-container-list]: /cli/azure/container?view=azure-cli-latest#az_container_list
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
