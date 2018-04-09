---
title: Esercitazione di Istanze di contenitore di Azure - Distribuire un'app
description: Esercitazione di Istanze di contenitore di Azure - Parte 3 di 3 - Distribuire un'applicazione
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 29d7114f288f7387d0c7cd5c6afe2eaaa7a8c560
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-deploy-a-container-to-azure-container-instances"></a>Esercitazione: Distribuire un contenitore in Istanze di contenitore di Azure

Questa è l'ultima esercitazione di una serie in tre parti. Nelle parti precedenti della serie, [è stata creata un'immagine del contenitore](container-instances-tutorial-prepare-app.md) e ne è stato eseguito il [push in Registro contenitori di Azure](container-instances-tutorial-prepare-acr.md). Questo articolo completa la serie con la distribuzione del contenitore in Istanze di contenitore di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Distribuire il contenitore da Registro contenitori di Azure a Istanze di contenitore di Azure
> * Visualizzare l'applicazione in esecuzione nel browser
> * Visualizzare i log del contenitore

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>Distribuire il contenitore tramite l'interfaccia della riga di comando di Azure

In questa sezione si userà l'interfaccia della riga di comando di Azure per distribuire l'immagine che è stata creata nella [prima esercitazione](container-instances-tutorial-prepare-app.md) e di cui è stato eseguito il push in Registro contenitori di Azure nella [seconda esercitazione](container-instances-tutorial-prepare-acr.md). Prima di procedere, assicurarsi di aver completato tali esercitazioni.

### <a name="get-registry-credentials"></a>Ottenere le credenziali del registro

Quando si distribuire un'immagine ospitata in un registro contenitori privato come quello creato nella [seconda esercitazione](container-instances-tutorial-prepare-acr.md), è necessario specificare le credenziali del registro.

Per prima cosa, ottenere il nome completo del server di accesso del registro contenitori (sostituire `<acrName>` con il nome del registro):

```azurecli
az acr show --name <acrName> --query loginServer
```

Successivamente, ottenere la password del registro contenitori:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

### <a name="deploy-container"></a>Distribuire il contenitore

Usare quindi il comando [az container create][az-container-create] per distribuire il contenitore. Sostituire `<acrLoginServer>` e `<acrPassword>` con i valori ottenuti dai due comandi precedenti. Sostituire `<acrName>` con il nome del registro contenitori.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Entro pochi secondi si dovrebbe ricevere una risposta iniziale da Azure. Il valore `--dns-name-label` deve essere univoco all'interno dell'area di Azure in cui si crea l'istanza di contenitore. Se quando si esegue il comando viene visualizzato un messaggio di errore relativo all'**etichetta del nome DNS**, modificare il valore nel comando precedente.

### <a name="verify-deployment-progress"></a>Verificare lo stato di avanzamento della distribuzione

Per visualizzare lo stato della distribuzione, usare il comando [az container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Ripetere il comando [az container show][az-container-show] fino a quando lo stato cambia da *In sospeso* a *In esecuzione*, operazione che dovrebbe richiedere meno di un minuto. Quando il contenitore è *In esecuzione*, procedere al passaggio successivo.

## <a name="view-the-application-and-container-logs"></a>Visualizzare l'applicazione e i log dei contenitori

Dopo aver completato la distribuzione, visualizzare il nome di dominio completo (FQDN) del contenitore usando il comando [az container show][az-container-show]:

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

Ad esempio: 
```console
$ az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
"aci-demo.eastus.azurecontainer.io"
```

Per visualizzare l'applicazione in esecuzione, passare al nome DNS visualizzato in un browser a scelta:

![App Hello World nel browser][aci-app-browser]

È anche possibile visualizzare l'output del log del contenitore:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Output di esempio:

```bash
$ az container logs --resource-group myResourceGroup --name aci-tutorial-app
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se non è più necessaria alcuna delle risorse create in questa serie di esercitazioni, è possibile eseguire il comando [az group delete][az-group-delete] per rimuovere il gruppo di risorse e tutte le risorse in esso contenute. Questo comando elimina il registro del contenitore creato, nonché il contenitore in esecuzione e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato completato il processo di distribuzione del contenitore in Istanze di contenitore di Azure. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Il contenitore è stato distribuito da Registro contenitori di Azure con l'interfaccia della riga di comando di Azure
> * L'applicazione è stata visualizzata nel browser
> * I log del contenitore sono stati visualizzati

Dopo aver acquisito queste nozioni di base, approfondire la conoscenza di Istanze di contenitore di Azure, ad esempio in relazione al funzionamento dei gruppi di contenitori:

> [!div class="nextstepaction"]
> [Gruppi di contenitori in Istanze di contenitore di Azure](container-instances-container-groups.md)

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
