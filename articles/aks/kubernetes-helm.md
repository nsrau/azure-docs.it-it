---
title: Distribuire contenitori con Helm in Kubernetes in Azure
description: Usare lo strumento per la creazione di pacchetti Helm per distribuire contenitori in un cluster Kubernetes nel servizio Kubernetes di Azure
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 531e6d9368b2bf91c48fd41b1e9330879b0df49a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101547"
---
# <a name="use-helm-with-azure-kubernetes-service-aks"></a>Usare Helm con il servizio Kubernetes di Azure (AKS)

[Helm][helm] è uno strumento per la creazione di pacchetti open source che consente di installare e gestire il ciclo di vita delle applicazioni Kubernetes. Analogamente agli strumenti di gestione pacchetti di Linux, come *APT* e *Yum*, Helm viene usato per gestire i grafici per Kubernetes, che sono pacchetti di risorse Kubernetes preconfigurate.

Questo documento illustra la configurazione e l'uso di Helm in un cluster Kubernetes nel servizio Kubernetes di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

I passaggi dettagliati contenuti in questo documento presuppongono che sia stato creato un cluster del servizio Kubernetes di Azure e che sia stata stabilita una connessione kubectl al cluster. Se sono necessari questi elementi, vedere la [guida introduttiva al servizio Kubernetes di Azure][aks-quickstart].

## <a name="install-helm-cli"></a>Installare l'interfaccia della riga di comando di Helm

L'interfaccia della riga di comando di Helm è un client eseguito nel sistema di sviluppo che consente di avviare, arrestare e gestire applicazioni con Helm.

Se si usa Azure Cloud Shell, l'interfaccia della riga di comando di Helm è già installata. Per installare l'interfaccia della riga di comando di Helm in un computer Mac, usare `brew`. Per altre opzioni di installazione, vedere [Installing Helm][helm-install-options] (Installazione di Helm).

```console
brew install kubernetes-helm
```

Output:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="create-service-account"></a>Creare un account del servizio

Prima della configurazione di Helm in un cluster abilitato per il controllo degli accessi in base al ruolo, sono necessari un account del servizio e un'associazione di ruolo per il servizio Tiller. Per altre informazioni sulla sicurezza di Helm/Tiller in un cluster abilitato per il controllo degli accessi in base al ruolo, vedere [Tiller, Namespaces, and RBAC][tiller-rbac] (Tiller, spazi dei nomi ed RBAC). Tenere presente che se il cluster non è abilitato per il controllo degli accessi in base al ruolo, è necessario ignorare questo passaggio.

Creare un file denominato `helm-rbac.yaml` e copiarlo nel codice YAML seguente.

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
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

Creare l'account del servizio e l'associazione di ruolo con il comando `kubectl create`.

```
kubectl create -f helm-rbac.yaml
```

Quando si usa un cluster abilitato per il controllo degli accessi in base al ruolo, sono disponibili opzioni per definire il livello di accesso di Tiller al cluster. Per altre informazioni sulle opzioni di configurazione, vedere [Helm: role-based access controls][helm-rbac] (Helm: controlli degli accessi in base al ruolo).

## <a name="configure-helm"></a>Configurare Helm

Installare Tiller usando il comando [helm init][helm-init]. Se il cluster non è abilitato per il controllo degli accessi in base al ruolo, rimuovere l'argomento e il valore `--service-account`.

```
helm init --service-account tiller
```

## <a name="find-helm-charts"></a>Trovare i grafici Helm

I grafici Helm vengono usati per distribuire applicazioni in un cluster Kubernetes. Per cercare i grafici Helm già creati, usare il comando [helm search][helm-search].

```azurecli-interactive
helm search
```

L'output sarà simile al seguente, ma con molti più grafici.

```
NAME                            VERSION DESCRIPTION
stable/acs-engine-autoscaler    2.0.0   Scales worker nodes within agent pools
stable/artifactory              6.1.0   Universal Repository Manager supporting all maj...
stable/aws-cluster-autoscaler   0.3.1   Scales worker nodes within autoscaling groups.
stable/buildkite                0.2.0   Agent for Buildkite
stable/centrifugo               2.0.0   Centrifugo is a real-time messaging server.
stable/chaoskube                0.5.0   Chaoskube periodically kills random pods in you...
stable/chronograf               0.3.0   Open-source web application written in Go and R...
stable/cluster-autoscaler       0.2.0   Scales worker nodes within autoscaling groups.
stable/cockroachdb              0.5.0   CockroachDB is a scalable, survivable, strongly...
stable/concourse                0.7.0   Concourse is a simple and scalable CI system.
stable/consul                   0.4.1   Highly available and distributed service discov...
stable/coredns                  0.5.0   CoreDNS is a DNS server that chains middleware ...
stable/coscale                  0.2.0   CoScale Agent
stable/dask-distributed         2.0.0   Distributed computation in Python
stable/datadog                  0.8.0   DataDog Agent
...
```

Per aggiornare l'elenco dei grafici, usare il comando [helm repo update][helm-repo-update].

```azurecli-interactive
helm repo update
```

Output:

```
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Eseguire i grafici Helm

Per distribuire Wordpress con un grafico Helm, usare il comando [helm install][helm-install].

```azurecli-interactive
helm install stable/wordpress
```

L'output sarà simile al seguente, ma includerà informazioni aggiuntive, ad esempio istruzioni su come usare la distribuzione di Kubernetes.

```
NAME:   bilging-ibex
LAST DEPLOYED: Tue Jun  5 14:31:49 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                     READY  STATUS   RESTARTS  AGE
bilging-ibex-mariadb-7557b5474-dmdxn     0/1    Pending  0         1s
bilging-ibex-wordpress-7494c545fb-tskhz  0/1    Pending  0         1s

==> v1/Secret
NAME                    TYPE    DATA  AGE
bilging-ibex-mariadb    Opaque  2     1s
bilging-ibex-wordpress  Opaque  2     1s

==> v1/ConfigMap
NAME                        DATA  AGE
bilging-ibex-mariadb        1     1s
bilging-ibex-mariadb-tests  1     1s

==> v1/PersistentVolumeClaim
NAME                    STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
bilging-ibex-mariadb    Pending  default  1s
bilging-ibex-wordpress  Pending  default  1s

==> v1/Service
NAME                    TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)                     AGE
bilging-ibex-mariadb    ClusterIP     10.0.76.164   <none>       3306/TCP                    1s
bilging-ibex-wordpress  LoadBalancer  10.0.215.250  <pending>    80:30934/TCP,443:31134/TCP  1s

==> v1beta1/Deployment
NAME                    DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
bilging-ibex-mariadb    1        1        1           0          1s
bilging-ibex-wordpress  1        1        1           0          1s
...
```

## <a name="list-helm-releases"></a>Elencare le versioni di Helm

Per visualizzare un elenco di versioni installate nel cluster, usare il comando [helm list][helm-list].

```azurecli-interactive
helm list
```

Output:

```
NAME            REVISION    UPDATED                     STATUS      CHART           NAMESPACE
bilging-ibex    1           Tue Jun  5 14:31:49 2018    DEPLOYED    wordpress-1.0.9 default
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla gestione dei grafici per Kubernetes, vedere la documentazione di Helm.

> [!div class="nextstepaction"]
> [Documentazione di Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
