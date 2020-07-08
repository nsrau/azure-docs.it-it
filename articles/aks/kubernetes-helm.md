---
title: Installare le applicazioni esistenti con Helm in AKS
description: Informazioni su come usare lo strumento di creazione di pacchetti Helm per distribuire i contenitori in un cluster Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/24/2020
ms.author: zarhoads
ms.openlocfilehash: 6ee99eee02e874208106d39c6442f54f59f95dad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361609"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Installare le applicazioni esistenti con Helm in Azure Kubernetes Service (AKS)

[Helm][helm] è uno strumento di creazione di pacchetti open source che consente di installare e gestire il ciclo di vita delle applicazioni Kubernetes. Analogamente ai gestori di pacchetti Linux, ad esempio *apt* e *yum*, Helm viene usato per gestire i grafici Kubernetes, che sono pacchetti di risorse Kubernetes preconfigurate.

Questo articolo illustra come configurare e usare Helm in un cluster Kubernetes nel servizio Azure Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

È anche necessario che sia installata l'interfaccia della riga di comando di Helm, ovvero il client in esecuzione nel sistema di sviluppo. Consente di avviare, arrestare e gestire le applicazioni con Helm. Se si usa Azure Cloud Shell, l'interfaccia della riga di comando di Helm è già installata. Per le istruzioni di installazione sulla piattaforma locale, vedere [installazione di Helm][helm-install].

> [!IMPORTANT]
> Helm è progettato per l'esecuzione su nodi Linux. Se nel cluster sono presenti nodi di Windows Server, è necessario assicurarsi che i pod Helm siano pianificati per l'esecuzione solo nei nodi Linux. È anche necessario assicurarsi che tutti i grafici Helm installati siano pianificati per l'esecuzione nei nodi corretti. I comandi di questo articolo usano i [selettori di nodo] [K8S-node-Selector] per assicurarsi che i pod siano pianificati per i nodi corretti, ma non tutti i grafici Helm possono esporre un selettore di nodo. È anche possibile prendere in considerazione l'uso di altre opzioni nel cluster, ad esempio [macchie][taints].

## <a name="verify-your-version-of-helm"></a>Verificare la versione di Helm

Usare il `helm version` comando per verificare che sia installato Helm 3:

```console
helm version
```

L'esempio seguente mostra la versione di Helm 3.0.0 installata:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>Installare un'applicazione con Helm V3

### <a name="add-the-official-helm-stable-charts-repository"></a>Aggiungere il repository dei grafici stable Helm ufficiale

Usare il comando [Helm repo][helm-repo-add] per aggiungere il repository dei grafici stable Helm ufficiale.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

### <a name="find-helm-charts"></a>Trovare i grafici Helm

I grafici Helm vengono usati per distribuire applicazioni in un cluster Kubernetes. Per cercare i grafici Helm già creati, usare il comando [Helm Search][helm-search] :

```console
helm search repo stable
```

L'output di esempio sintetico seguente mostra alcuni dei grafici di Helm disponibili per l'uso:

```console
$ helm search repo stable

NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION                                       
stable/acs-engine-autoscaler            2.2.2           2.1.1                           DEPRECATED Scales worker nodes within agent pools 
stable/aerospike                        0.3.1           v4.5.0.5                        A Helm chart for Aerospike in Kubernetes          
stable/airflow                          4.10.0          1.10.4                          Airflow is a platform to programmatically autho...
stable/ambassador                       4.4.7           0.85.0                          A Helm chart for Datawire Ambassador              
stable/anchore-engine                   1.3.7           0.5.2                           Anchore container analysis and policy evaluatio...
stable/apm-server                       2.1.5           7.0.0                           The server receives data from the Elastic APM a...
stable/ark                              4.2.2           0.10.2                          DEPRECATED A Helm chart for ark                   
stable/artifactory                      7.3.1           6.1.0                           DEPRECATED Universal Repository Manager support...
stable/artifactory-ha                   0.4.1           6.2.0                           DEPRECATED Universal Repository Manager support...
stable/atlantis                         3.8.4           v0.8.2                          A Helm chart for Atlantis https://www.runatlant...
stable/auditbeat                        1.1.0           6.7.0                           A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler           0.3.3                                           Scales worker nodes within autoscaling groups.    
stable/aws-iam-authenticator            0.1.1           1.0                             A Helm chart for aws-iam-authenticator            
stable/bitcoind                         0.2.2           0.17.1                          Bitcoin is an innovative payment network and a ...
stable/bookstack                        1.1.2           0.27.4-1                        BookStack is a simple, self-hosted, easy-to-use...
stable/buildkite                        0.2.4           3                               DEPRECATED Agent for Buildkite                    
stable/burrow                           1.5.2           0.29.0                          Burrow is a permissionable smart contract machine 
stable/centrifugo                       3.1.0           2.1.0                           Centrifugo is a real-time messaging server.       
stable/cerebro                          1.3.1           0.8.5                           A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager                     v0.6.7          v0.6.2                          A Helm chart for cert-manager                     
stable/chaoskube                        3.1.2           0.14.0                          Chaoskube periodically kills random pods in you...
stable/chartmuseum                      2.4.0           0.8.2                           Host your own Helm Chart Repository               
stable/chronograf                       1.1.0           1.7.12                          Open-source web application written in Go and R...
stable/clamav                           1.0.4           1.6                             An Open-Source antivirus engine for detecting t...
stable/cloudserver                      1.0.3           8.1.5                           An open-source Node.js implementation of the Am...
stable/cluster-autoscaler               6.2.0           1.14.6                          Scales worker nodes within autoscaling groups.    
stable/cluster-overprovisioner          0.2.6           1.0                             Installs the a deployment that overprovisions t...
stable/cockroachdb                      2.1.16          19.1.5                          CockroachDB is a scalable, survivable, strongly...
stable/collabora-code                   1.0.5           4.0.3.1                         A Helm chart for Collabora Office - CODE-Edition  
stable/concourse                        8.2.7           5.6.0                           Concourse is a simple and scalable CI system.     
stable/consul                           3.9.2           1.5.3                           Highly available and distributed service discov...
stable/contour                          0.1.0           v0.15.0                         Contour Ingress controller for Kubernetes         
stable/coredns                          1.7.4           1.6.4                           CoreDNS is a DNS server that chains plugins and...
stable/cosbench                         1.0.1           0.0.6                           A benchmark tool for cloud object storage services
stable/coscale                          1.0.0           3.16.0                          CoScale Agent                                     
stable/couchbase-operator               1.0.1           1.2.1                           A Helm chart to deploy the Couchbase Autonomous...
stable/couchdb                          2.3.0           2.3.1                           DEPRECATED A database featuring seamless multi-...
stable/dask                             3.1.0           1.1.5                           Distributed computation in Python with task sch...
stable/dask-distributed                 2.0.2                                           DEPRECATED: Distributed computation in Python     
stable/datadog                          1.38.3          6.14.0                          DataDog Agent 
...
```

Per aggiornare l'elenco dei grafici, usare il comando [helm repo update][helm-repo-update].

```console
helm repo update
```

L'esempio seguente illustra un aggiornamento riuscito del repository:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Eseguire i grafici Helm

Per installare i grafici con Helm, utilizzare il comando [Helm install][helm-install-command] e specificare il nome della versione e il nome del grafico da installare. Per visualizzare l'installazione di un grafico Helm in azione, è necessario installare una distribuzione nginx di base usando un grafico Helm.

```console
helm install my-nginx-ingress stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

L'output di esempio sintetico seguente mostra lo stato della distribuzione delle risorse Kubernetes create dal grafico Helm:

```console
$ helm install my-nginx-ingress stable/nginx-ingress \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-controller'
...
```

Usare il `kubectl get services` comando per ottenere l' *indirizzo IP esterno* del servizio.

```console
kubectl --namespace default get services -o wide -w my-nginx-ingress-controller
```

Ad esempio, il comando seguente mostra l' *IP esterno* per il servizio *My-nginx-ingress-controller* :

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-controller

NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
my-nginx-ingress-controller   LoadBalancer   10.0.123.1     <EXTERNAL-IP>   80:31301/TCP,443:31623/TCP   96s   app=nginx-ingress,component=controller,release=my-nginx-ingress
```

### <a name="list-releases"></a>Elenca versioni

Per visualizzare un elenco delle versioni installate nel cluster, usare il `helm list` comando.

```console
helm list
```

L'esempio seguente illustra la versione *My-nginx-ingress* distribuita nel passaggio precedente:

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Pulire le risorse

Quando si distribuisce un grafico Helm, viene creato un certo numero di risorse di Kubernetes. Queste risorse includono pod, distribuzioni e servizi. Per pulire queste risorse, usare il comando [Helm Uninstall][helm-cleanup] e specificare il nome della versione, come riportato nel comando precedente `helm list` .

```console
helm uninstall my-nginx-ingress
```

L'esempio seguente mostra che la versione denominata *My-nginx-ingress* è stata disinstallata:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla distribuzione delle applicazioni Kubernetes con Helm, vedere la documentazione di Helm.

> [!div class="nextstepaction"]
> [Documentazione di Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md
