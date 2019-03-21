---
title: Distribuire contenitori con Helm in Kubernetes in Azure
description: Informazioni su come usare lo strumento di creazione di pacchetti Helm per distribuire contenitori in un cluster Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/06/2019
ms.author: iainfou
ms.openlocfilehash: ccd98f5bf859673ae69b80dfcd0e5020ba289682
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768553"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Installare le applicazioni con Helm nel servizio Azure Kubernetes

[Helm][helm] è uno strumento per la creazione di pacchetti open source che consente di installare e gestire il ciclo di vita delle applicazioni Kubernetes. Analogamente agli strumenti di gestione pacchetti di Linux, come *APT* e *Yum*, Helm viene usato per gestire i grafici per Kubernetes, che sono pacchetti di risorse Kubernetes preconfigurate.

Questo articolo illustra come configurare e usare Helm in un cluster Kubernetes nel servizio Azure Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster AKS esistente. Se è necessario un cluster servizio Azure Kubernetes, vedere la Guida introduttiva su servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

È necessario installare l'interfaccia della riga di comando di Helm, un client eseguito nel sistema di sviluppo che consente di avviare, arrestare e gestire applicazioni con Helm. Se si usa Azure Cloud Shell, l'interfaccia della riga di comando di Helm è già installata. Per le istruzioni di installazione nella piattaforma locale, vedere [Installing Helm][helm-install] (Installazione di Helm).

## <a name="create-a-service-account"></a>Creare un account del servizio

Prima di poter distribuire Helm in un cluster servizio Azure Kubernetes abilitato per il controllo degli accessi in base al ruolo, sono necessari un account del servizio e un'associazione di ruolo per il servizio Tiller. Per altre informazioni sulla sicurezza di Helm/Tiller in un cluster abilitato per il controllo degli accessi in base al ruolo, vedere [Tiller, Namespaces, and RBAC][tiller-rbac] (Tiller, spazi dei nomi ed RBAC). Se il cluster del servizio Azure Kubernetes non è abilitato per il controllo degli accessi in base al ruolo, ignorare questo passaggio.

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

Il client Helm e il servizio Tiller si autenticano e comunicano tra loro tramite TLS/SSL. Questo metodo di autenticazione consente di proteggere il cluster Kubernetes e di stabilire i servizi che possono essere distribuiti. Per migliorare la sicurezza, è possibile generare i propri certificati firmati. Ogni utente Helm riceverà il proprio certificato client e Tiller verrà inizializzato nel cluster Kubernetes con i certificati applicati. Per altre informazioni, vedere [Using TLS/SSL between Helm and Tiller][helm-ssl] (Uso di TLS/SSL tra Helm e Tiller).

Con un cluster Kubernetes abilitato per il controllo degli accessi in base al ruolo, è possibile controllare il livello di accesso di Tiller al cluster. È possibile definire lo spazio dei nomi Kubernetes in cui viene distribuito Tiller e limitare gli spazi dei nomi in cui Tiller può distribuire le risorse. Questo approccio consente di creare istanze di Tiller in diversi spazi dei nomi, stabilire i limiti della distribuzione e stabilire l'ambito degli utenti di Helm in determinati spazi dei nomi. Per altre informazioni, vedere [Helm role-based access controls][helm-rbac] (Controllo degli accessi in base al ruolo di Helm).

## <a name="configure-helm"></a>Configurare Helm

Per distribuire un'applicazione Tiller di base in un cluster del servizio Azure Kubernetes, usare il comando [helm init][helm-init]. Se il cluster non è abilitato per il controllo degli accessi in base al ruolo, rimuovere l'argomento e il valore `--service-account`. Se TLS/SSL non è configurato per Tiller e Helm, ignorare questo passaggio di inizializzazione di base e fornire invece il valore `--tiller-tls-` necessario come illustrato nell'esempio seguente.

```console
helm init --service-account tiller
```

Se tra Helm e Tiller è configurato TLS/SSL fornire i parametri e i nomi dei certificati `--tiller-tls-*` come illustrato nell'esempio seguente:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --service-account tiller
```

## <a name="find-helm-charts"></a>Trovare i grafici Helm

I grafici Helm vengono usati per distribuire applicazioni in un cluster Kubernetes. Per cercare i grafici Helm già creati, usare il comando [helm search][helm-search]:

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

Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Eseguire i grafici Helm

Per installare i grafici con Helm, usare il comando [helm install] [ helm-install] e specificare il nome del grafico da installare. Per visualizzare questa azione, è possibile installare una distribuzione base di Wordpress con un grafico Helm. Se TLS/SSL è configurato, aggiungere il parametro `--tls` per usare il certificato client Helm.

```console
helm install stable/wordpress
```

L'output di esempio sintetico seguente mostra lo stato della distribuzione delle risorse Kubernetes create dal grafico Helm:

```
$ helm install stable/wordpress

NAME:   wishful-mastiff
LAST DEPLOYED: Wed Mar  6 19:11:38 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1beta1/Deployment
NAME                       DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
wishful-mastiff-wordpress  1        1        1           0          1s

==> v1beta1/StatefulSet
NAME                     DESIRED  CURRENT  AGE
wishful-mastiff-mariadb  1        1        1s

==> v1/Pod(related)
NAME                                        READY  STATUS   RESTARTS  AGE
wishful-mastiff-wordpress-6f96f8fdf9-q84sz  0/1    Pending  0         1s
wishful-mastiff-mariadb-0                   0/1    Pending  0         1s

==> v1/Secret
NAME                       TYPE    DATA  AGE
wishful-mastiff-mariadb    Opaque  2     2s
wishful-mastiff-wordpress  Opaque  2     2s

==> v1/ConfigMap
NAME                           DATA  AGE
wishful-mastiff-mariadb        1     2s
wishful-mastiff-mariadb-tests  1     2s

==> v1/PersistentVolumeClaim
NAME                       STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
wishful-mastiff-wordpress  Pending  default  2s

==> v1/Service
NAME                       TYPE          CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
wishful-mastiff-mariadb    ClusterIP     10.1.116.54  <none>       3306/TCP                    2s
wishful-mastiff-wordpress  LoadBalancer  10.1.217.64  <pending>    80:31751/TCP,443:31264/TCP  2s
...
```

L'indirizzo *EXTERNAL-IP* del servizio Wordpress viene popolato in uno o due minuti ed è possibile accedervi con un Web browser.

## <a name="list-helm-releases"></a>Elencare le versioni di Helm

Per visualizzare un elenco di versioni installate nel cluster, usare il comando [helm list][helm-list]. L'esempio seguente illustra la versione di Wordpress distribuita nel passaggio precedente. Se TLS/SSL è configurato, aggiungere il parametro `--tls` per usare il certificato client Helm.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART            APP VERSION    NAMESPACE
wishful-mastiff   1         Wed Mar  6 19:11:38 2019    DEPLOYED    wordpress-2.1.3  4.9.7          default
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si distribuisce un grafico Helm, viene creato un certo numero di risorse di Kubernetes. Queste risorse includono pod, distribuzioni e servizi. Per pulire le risorse, usare il comando `helm delete` e specificare il nome della versione che si trova nel precedente comando `helm list`. Nell'esempio seguente viene eliminata la versione denominata *wishful mastiff*:

```console
$ helm delete wishful-mastiff

release "wishful-mastiff" deleted
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla distribuzione delle applicazioni Kubernetes con Helm, vedere la documentazione di Helm.

> [!div class="nextstepaction"]
> [Documentazione di Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
[helm-ssl]: https://docs.helm.sh/using_helm/#using-ssl-between-helm-and-tiller

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
