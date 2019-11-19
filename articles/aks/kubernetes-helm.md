---
title: Distribuire contenitori con Helm in Kubernetes in Azure
description: Informazioni su come usare lo strumento di creazione di pacchetti Helm per distribuire i contenitori in un cluster Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 05/23/2019
ms.author: zarhoads
ms.openlocfilehash: fc808fee66dee573aecd423e375d30bf3f5b696a
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170718"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Installare le applicazioni con Helm nel servizio Azure Kubernetes

[Helm][helm] è uno strumento di creazione dei pacchetti open source che consente di installare e gestire il ciclo di vita delle applicazioni Kubernetes. Analogamente agli strumenti di gestione pacchetti Linux, come *APT* e *Yum*, Helm viene usato per gestire i grafici per Kubernetes, che sono pacchetti di risorse Kubernetes preconfigurate.

Questo articolo illustra come configurare e usare Helm in un cluster Kubernetes nel servizio Azure Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster servizio Azure Kubernetes esistente. Se è necessario un cluster AKS, vedere la Guida introduttiva di AKS [usando l'interfaccia della][aks-quickstart-cli] riga di comando di Azure o [l'portale di Azure][aks-quickstart-portal].

È anche necessario che sia installata l'interfaccia della riga di comando di Helm, ovvero il client in esecuzione nel sistema di sviluppo. Consente di avviare, arrestare e gestire le applicazioni con Helm. Se si usa Azure Cloud Shell, l'interfaccia della riga di comando di Helm è già installata. Per le istruzioni di installazione nella piattaforma locale, vedere [installazione di Helm][helm-install].

> [!IMPORTANT]
> Helm è progettato per l'esecuzione su nodi Linux. Se nel cluster sono presenti nodi di Windows Server, è necessario assicurarsi che i pod Helm siano pianificati per l'esecuzione solo nei nodi Linux. È anche necessario assicurarsi che tutti i grafici Helm installati siano pianificati per l'esecuzione nei nodi corretti. I comandi di questo articolo usano i selettori di [nodo][k8s-node-selector] per assicurarsi che i pod siano pianificati per i nodi corretti, ma non tutti i grafici Helm possono esporre un selettore di nodo. È anche possibile prendere in considerazione l'uso di altre opzioni nel cluster, ad esempio [macchie][taints].

## <a name="create-a-service-account"></a>Creare un account del servizio

Prima di poter distribuire Helm in un cluster servizio Azure Kubernetes abilitato per il controllo degli accessi in base al ruolo, sono necessari un account del servizio e un'associazione di ruolo per il servizio Tiller. Per ulteriori informazioni sulla protezione di Helm/Tiller in un cluster abilitato per il controllo degli accessi in base al ruolo, vedere [Tiller, Namespaces e RBAC][tiller-rbac]. Se il cluster del servizio Azure Kubernetes non è abilitato per il controllo degli accessi in base al ruolo, ignorare questo passaggio.

Creare un file denominato `helm-rbac.yaml` e copiarlo nel codice YAML seguente:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Creare l'account del servizio e l'associazione di ruolo con il comando `kubectl apply`:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>Proteggere Tiller e Helm

Il client Helm e il servizio Tiller si autenticano e comunicano tra loro tramite TLS/SSL. Questo metodo di autenticazione consente di proteggere il cluster Kubernetes e di stabilire i servizi che possono essere distribuiti. Per migliorare la sicurezza, è possibile generare i propri certificati firmati. Ogni utente Helm riceverà il proprio certificato client e Tiller verrà inizializzato nel cluster Kubernetes con i certificati applicati. Per altre informazioni, vedere [uso di TLS/SSL tra Helm e Tiller][helm-ssl].

Con un cluster Kubernetes abilitato per il controllo degli accessi in base al ruolo, è possibile controllare il livello di accesso di Tiller al cluster. È possibile definire lo spazio dei nomi Kubernetes in cui viene distribuito Tiller e limitare gli spazi dei nomi in cui Tiller può distribuire le risorse. Questo approccio consente di creare istanze di Tiller in diversi spazi dei nomi, stabilire i limiti della distribuzione e stabilire l'ambito degli utenti di Helm in determinati spazi dei nomi. Per altre informazioni, vedere [controlli degli accessi in base al ruolo Helm][helm-rbac].

## <a name="configure-helm"></a>Configurare Helm

Per distribuire un Tiller di base in un cluster AKS, usare il comando [Helm init][helm-init] . Se il cluster non è abilitato per il controllo degli accessi in base al ruolo, rimuovere l'argomento e il valore `--service-account`. Negli esempi seguenti viene anche impostata la [cronologia-max][helm-history-max] su 200.

Se TLS/SSL non è configurato per Tiller e Helm, ignorare questo passaggio di inizializzazione di base e fornire invece il valore `--tiller-tls-` necessario come illustrato nell'esempio seguente.

```console
helm init --history-max 200 --service-account tiller --node-selectors "beta.kubernetes.io/os=linux"
```

Se tra Helm e Tiller è configurato TLS/SSL fornire i parametri e i nomi dei certificati `--tiller-tls-*` come illustrato nell'esempio seguente:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --history-max 200 \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os=linux"
```

## <a name="find-helm-charts"></a>Trovare i grafici Helm

I grafici Helm vengono usati per distribuire applicazioni in un cluster Kubernetes. Per cercare i grafici Helm già creati, usare il comando [Helm Search][helm-search] :

```console
helm search
```

L'output di esempio sintetico seguente mostra alcuni dei grafici di Helm disponibili per l'uso:

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

Per aggiornare l'elenco dei grafici, usare il comando [helm repo update][helm-repo-update]. L'esempio seguente illustra un aggiornamento riuscito del repository:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.
```

## <a name="run-helm-charts"></a>Eseguire i grafici Helm

Per installare i grafici con Helm, utilizzare il comando [Helm install][helm-install] e specificare il nome del grafico da installare. Per visualizzare l'installazione di un grafico Helm in azione, è necessario installare una distribuzione nginx di base usando un grafico Helm. Se TLS/SSL è configurato, aggiungere il parametro `--tls` per usare il certificato client Helm.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

L'output di esempio sintetico seguente mostra lo stato della distribuzione delle risorse Kubernetes create dal grafico Helm:

```
$ helm install stable/nginx-ingress --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME:   flailing-alpaca
LAST DEPLOYED: Thu May 23 12:55:21 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                      DATA  AGE
flailing-alpaca-nginx-ingress-controller  1     0s

==> v1/Pod(related)
NAME                                                            READY  STATUS             RESTARTS  AGE
flailing-alpaca-nginx-ingress-controller-56666dfd9f-bq4cl       0/1    ContainerCreating  0         0s
flailing-alpaca-nginx-ingress-default-backend-66bc89dc44-m87bp  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                                           TYPE          CLUSTER-IP  EXTERNAL-IP  PORT(S)                     AGE
flailing-alpaca-nginx-ingress-controller       LoadBalancer  10.0.109.7  <pending>    80:31219/TCP,443:32421/TCP  0s
flailing-alpaca-nginx-ingress-default-backend  ClusterIP     10.0.44.97  <none>       80/TCP                      0s
...
```

Sono necessari un minuto o due per l'indirizzo *IP esterno* del servizio nginx-ingress-controller da popolare e consentire l'accesso con un Web browser.

## <a name="list-helm-releases"></a>Elencare le versioni di Helm

Per visualizzare un elenco di versioni installate nel cluster, usare il comando [Helm list][helm-list] . L'esempio seguente illustra la versione di nginx-ingress distribuita nel passaggio precedente. Se TLS/SSL è configurato, aggiungere il parametro `--tls` per usare il certificato client Helm.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si distribuisce un grafico Helm, viene creato un certo numero di risorse di Kubernetes. Queste risorse includono pod, distribuzioni e servizi. Per pulire le risorse, usare il comando `helm delete` e specificare il nome della versione che si trova nel precedente comando `helm list`. Nell'esempio seguente viene eliminata la versione denominata *Agitator-Alpaca*:

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla distribuzione delle applicazioni Kubernetes con Helm, vedere la documentazione di Helm.

> [!div class="nextstepaction"]
> [Documentazione di Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://v2.helm.sh/docs/
[helm-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm-install]: https://v2.helm.sh/docs/using_helm/#installing-helm
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://v2.helm.sh/docs/helm/#helm-list
[helm-history-max]: https://v2.helm.sh/docs/using_helm/#initialize-helm-and-install-tiller
[helm-rbac]: https://v2.helm.sh/docs/using_helm/#role-based-access-control
[helm-repo-update]: https://v2.helm.sh/docs/helm/#helm-repo-update
[helm-search]: https://v2.helm.sh/docs/helm/#helm-search
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
[helm-ssl]: https://v2.helm.sh/docs/using_helm/#using-ssl-between-helm-and-tiller
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md
