---
title: Esercitazione su Kubernetes in Azure - Aggiornare un'applicazione
description: In questa esercitazione sul servizio Azure Kubernetes viene illustrato come aggiornare la distribuzione di un'applicazione esistente nel servizio Azure Kubernetes con una nuova versione del codice dell'applicazione.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: twhitney
ms.custom: mvc
ms.openlocfilehash: 05eac7e673ad01e9d3e0fb25f261444fd7bc4e6d
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475507"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Esercitazione: Aggiornare un'applicazione nel servizio Azure Kubernetes

Dopo la distribuzione di un'applicazione in Kubernetes, è possibile aggiornarla specificando una nuova immagine del contenitore o una nuova versione dell'immagine. L'aggiornamento viene eseguito a fasi in modo che solo una parte della distribuzione venga aggiornata nello stesso momento. Questo aggiornamento a fasi consente all'applicazione di rimanere in esecuzione durante l'aggiornamento. Fornisce inoltre un meccanismo di ripristino dello stato precedente se si verifica un errore di distribuzione.

In questa esercitazione, parte sei di sette, viene aggiornata l'app Azure Vote di esempio. Si apprenderà come:

> [!div class="checklist"]
> * Aggiornare il codice dell'applicazione front-end
> * Creare un'immagine del contenitore aggiornata
> * Eseguire il push dell'immagine del contenitore in Registro Azure Container
> * Distribuire un'immagine del contenitore aggiornata

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in un'immagine del contenitore. L'immagine è stata poi caricata in Registro Azure Container ed è stato creato un cluster del servizio Azure Kubernetes. L'applicazione è stata quindi distribuita nel cluster AKS.

È stato clonato anche un repository di applicazione che include il codice sorgente dell'applicazione e un file Docker Compose creato in precedenza usato in questa esercitazione. Verificare di aver creato un clone del repository e di aver cambiato le directory nella directory clonata. Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, iniziare con l'[Esercitazione 1 - Creare immagini del contenitore][aks-tutorial-prepare-app].

Per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.53 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="update-an-application"></a>Aggiornare un'applicazione

Verrà apportata una modifica all'applicazione di esempio, quindi verrà aggiornata la versione già distribuita nel cluster servizio Azure Kubernetes. Assicurarsi di essere nella directory clonata *azure-voto-app-redis*. Il codice sorgente dell'applicazione di esempio è disponibile nella directory *azure-vote*. Aprire il file *config_file.cfg* con un editor, ad esempio `vi`:

```console
vi azure-vote/azure-vote/config_file.cfg
```

Sostituire i valori di *VOTE1VALUE* e *VOTE2VALUE* con valori diversi, ad esempio colori. L'esempio seguente illustra i valori aggiornati:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Salvare e chiudere il file. In `vi` usare `:wq`.

## <a name="update-the-container-image"></a>Aggiornare l'immagine del contenitore

Per ricreare l'immagine front-end e testare l'applicazione aggiornata, usare [docker-compose][docker-compose]. L'argomento `--build` viene usato per indicare a Docker Compose di ricreare l'immagine dell'applicazione:

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>Testare l'applicazione in locale

Per verificare che l'immagine del contenitore aggiornata mostri le modifiche, aprire un Web browser locale all'indirizzo `http://localhost:8080`.

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

I valori aggiornati specificati nel file *config_file.cfg* vengono visualizzati nell'applicazione in esecuzione.

## <a name="tag-and-push-the-image"></a>Applicare tag ed eseguire il push dell'immagine

Per usare correttamente l'immagine aggiornata, contrassegnare l'immagine *azure-vote-front* con il nome del server di accesso di Registro Azure Container. Ottenere il nome del server di accesso con il comando [az acr list](/cli/azure/acr):

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Usare [docker tag][docker-tag] per assegnare il tag all'immagine. Sostituire `<acrLoginServer>` con il nome del server di accesso di Registro Azure Container o con il nome host del registro pubblico e aggiornare la versione dell'immagine a *:v2* come di seguito:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

Usare ora [docker push][docker-push] per caricare l'immagine nel registro. Sostituire `<acrLoginServer>` con il nome del server di accesso di Registro Azure Container.

> [!NOTE]
> In caso di problemi durante l'esecuzione del push nell'istanza di Registro Azure Container, assicurarsi di essere ancora connessi. Eseguire il comando [az acr login][az-acr-login] con il nome del Registro Azure Container creato nel passaggio [Creare un Registro Azure Container](tutorial-kubernetes-prepare-acr.md#create-an-azure-container-registry). Ad esempio: `az acr login --name <azure container registry name>`.

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>Distribuire l'applicazione aggiornata

Per garantire il tempo di attività massimo, è necessario eseguire più istanze del pod dell'applicazione. Verificare il numero di istanze front-end in esecuzione con il comando [kubectl get pods][kubectl-get]:

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Se non si hanno più pod front-end, ridimensionare la distribuzione *azure-vote-front* come di seguito:

```console
kubectl scale --replicas=3 deployment/azure-vote-front
```

Per aggiornare l'applicazione, usare il comando [kubectl set][kubectl-set]. Aggiornare `<acrLoginServer>` con il server di accesso o il nome host del registro contenitori e specificare la versione dell'applicazione *v2*:

```console
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Per monitorare la distribuzione, usare il comando [kubectl get pod][kubectl-get]. Quando l'applicazione aggiornata viene distribuita, le unità vengono terminate e ricreate con la nuova immagine del contenitore.

```console
kubectl get pods
```

L'output di esempio seguente illustra i pod con arresto in corso e le nuove istanze in esecuzione durante l'avanzamento della distribuzione:

```
$ kubectl get pods

NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-the-updated-application"></a>Testare l'applicazione aggiornata

Per visualizzare l'applicazione aggiornata, ottenere prima l'indirizzo IP esterno del servizio `azure-vote-front`:

```console
kubectl get service azure-vote-front
```

Aprire ora un Web browser locale all'indirizzo IP del servizio:

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata aggiornata un'applicazione e l'aggiornamento è stato distribuito in un cluster del servizio Azure Kubernetes. Si è appreso come:

> [!div class="checklist"]
> * Aggiornare il codice dell'applicazione front-end
> * Creare un'immagine del contenitore aggiornata
> * Eseguire il push dell'immagine del contenitore in Registro Azure Container
> * Distribuire un'immagine del contenitore aggiornata

Passare all'esercitazione successiva per informazioni su come aggiornare un cluster servizio Azure Kubernetes a una nuova versione di Kubernetes.

> [!div class="nextstepaction"]
> [Aggiornare Kubernetes][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
