---
title: Distribuire contenitori con Helm in Azure Kubernetes
description: Usare lo strumento di creazione dei pacchetti Helm per distribuire i contenitori in un cluster Kubernetes nel servizio contenitore di Azure
services: container-service
author: sauryadas
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: e9e214aeacde2f338d3e59507269d1eb4b689418
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
---
# <a name="use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>Usare Helm per distribuire i contenitori in un cluster Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

[Helm](https://github.com/kubernetes/helm/) è uno strumento di creazione dei pacchetti open source che consente di installare e gestire il ciclo di vita delle applicazioni Kubernetes. Analogamente ai responsabili di pacchetto Linux, ad esempio Apt-get e Yum, Helm viene usato per gestire i grafici Kubernetes, che sono pacchetti di risorse Kubernetes preconfigurati. In questo articolo viene illustrato come usare Helm in un cluster Kubernetes distribuito nel servizio contenitore di Azure.

Helm presenta due componenti: 
* L'**interfaccia della riga di comando Helm** è un client in esecuzione nel computer locale o nel cloud  

* **Tiller** è un server che viene eseguito sul cluster Kubernetes e gestisce il ciclo di vita delle applicazioni Kubernetes 
 
## <a name="prerequisites"></a>Prerequisiti

* [Creare un cluster Kubernetes](container-service-kubernetes-walkthrough.md) nel servizio contenitore di Azure

* [Installare e configurare `kubectl`](../container-service-connect.md) in un computer locale

* [Installare Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) in un computer locale

## <a name="helm-basics"></a>Nozioni di base di Helm 

Per visualizzare informazioni sul cluster Kubernetes in cui si installa Tiller e si distribuiscono le applicazioni, digitare il comando seguente:

```bash
kubectl cluster-info 
```
![kubectl cluster-info](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Dopo aver installato Helm, installare Tiller sul cluster Kubernetes digitando il comando seguente:

```bash
helm init --upgrade
```
Dopo che l'operazione viene completata correttamente, viene visualizzato un output simile al seguente:

![Installazione di Tiller](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Per visualizzare tutti i grafici di Helm disponibili nel repository, digitare il comando seguente:

```bash 
helm search 
```

Viene visualizzato un output simile al seguente:

![Ricerca Helm](./media/container-service-kubernetes-helm/helm-search.png)
 
Per aggiornare i grafici in modo da ottenere le versioni più recenti, digitare:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Distribuire un grafico controller in ingresso Nginx 
 
Per distribuire un grafico controller in ingresso Nginx, digitare un singolo comando:

```bash
helm install stable/nginx-ingress 
```
![Distribuire il controller in ingresso](./media/container-service-kubernetes-helm/nginx-ingress.png)

Se si digita `kubectl get svc` per visualizzare tutti i servizi che sono in esecuzione nel cluster, si noti che al controller in ingresso viene assegnato un indirizzo IP. Mentre è in corso l'assegnazione, viene visualizzato `<pending>`. Il completamento dell'operazione richiede alcuni minuti. 

Dopo che è stato assegnato l'indirizzo IP, passare al valore dell'indirizzo IP esterno per visualizzare l'esecuzione back-end di Nginx. 
 
![Indirizzo IP in ingresso](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Per visualizzare un elenco dei grafici installati nel cluster, digitare:

```bash
helm list 
```

È possibile abbreviare il comando in `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Distribuire un client e un grafico di MariaDB

Distribuire ora un grafico MariaDB e un client MariaDB per connettersi al database.

Per distribuire il grafico MariaDB, digitare il comando seguente:

```bash
helm install --name v1 stable/mariadb
```

dove `--name` è un tag usato per le versioni.

> [!TIP]
> Se la distribuzione non riesce, eseguire `helm repo update` e riprovare.
>
 
 
Per visualizzare tutti i grafici distribuiti nel cluster, digitare:

```bash 
helm list
```
 
Per visualizzare tutte le distribuzioni in esecuzione nel cluster, digitare:

```bash
kubectl get deployments 
``` 
 
 
Infine, per eseguire un pod per accedere al client, digitare:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Per connettersi al client, digitare il comando seguente sostituendo `v1-mariadb` con il nome della distribuzione:

```bash
sudo mysql –h v1-mariadb
```
 
 
È ora possibile usare i comandi standard di SQL per creare database, tabelle e così via. Ad esempio, `Create DATABASE testdb1;` crea un database vuoto. 
 
 
 
## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla gestione dei grafici Kubernetes, vedere la [documentazione di Helm](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

