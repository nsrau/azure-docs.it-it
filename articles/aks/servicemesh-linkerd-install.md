---
title: Installare Linker nel servizio Azure Kubernetes (AKS)Install Linkerd in Azure Kubernetes Service (AKS)
description: Informazioni su come installare e usare Linkerd per creare una rete mesh del servizio in un cluster di Azure Kubernetes Service (AKS)Learn how to install and use Linkerd to create a service mesh in an Azure Kubernetes Service (AKS) cluster
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 419b61527b68299c82dec4f2f5da6b0220859cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593727"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>Installare Linker nel servizio Azure Kubernetes (AKS)Install Linkerd in Azure Kubernetes Service (AKS)

[Linkerd][linkerd-github] è una rete di servizio open source e [CNCF incubazione progetto][linkerd-cncf]. Linkerd è una rete di servizi ultraleggera che fornisce funzionalità che includono la gestione del traffico, l'identità e la sicurezza del servizio, l'affidabilità e l'osservabilità. Per ulteriori informazioni su Linkerd, vedere la documentazione ufficiale [di Linkerd FAQ][linkerd-faq] e Architettura [linker.][linkerd-architecture]

In questo articolo viene illustrato come installare Linkerd.This article shows you how to install Linkerd. Il file `linkerd` binario del client Linkerd viene installato nel computer client e i componenti Linkerd vengono installati in un cluster Kubernetes in AKS.

> [!NOTE]
> Queste istruzioni fanno `stable-2.6.0`riferimento alla versione del linker .
>
> Il Linkerd `stable-2.6.x` può essere eseguito su `1.13+`Kubernetes versioni . Ulteriori versioni stable e edge Linkerd sono disponibili in [GitHub - Linkerd Releases][linkerd-github-releases].

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Scaricare e installare il file binario del client linkerd Linkerd Linkerd
> * Installare Linkerd in AKS
> * Convalidare l'installazione di Linkerd
> * Accedere al dashboard
> * Disinstallare Linker d'AKS

## <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questo articolo presuppongono che sia stato creato `1.13` un cluster AKS (Kubernetes e versioni successive, con RBAC abilitato) e che sia stata stabilita una `kubectl` connessione con il cluster. Se occorre assistenza con uno di questi elementi, vedere la [Guida introduttiva al servizio Azure Kubernetes][aks-quickstart].

Tutti i pod Linker devono essere pianificati per l'esecuzione su nodi Linux - questa configurazione è l'impostazione predefinita nel metodo di installazione descritto di seguito e non richiede alcuna configurazione aggiuntiva.

In questo articolo vengono separate le linee guida per l'installazione di Linkerd in diversi passaggi discreti. Il risultato è lo stesso nella struttura del Linkerd ufficiale [guida][linkerd-getting-started]introduttiva .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>Installare Linkerd in AKS

Prima di installare Linkerd, verranno eseguiti i controlli di preinstallazione per determinare se il piano di controllo può essere installato nel cluster AKS:

```console
linkerd check --pre
```

Si dovrebbe vedere qualcosa di simile al seguente per indicare che il cluster AKS è una destinazione di installazione valida per Linkerd:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

Ora è il momento di installare i componenti Linkerd. Utilizzare `linkerd` i `kubectl` file binari e per installare i componenti Linkerd nel cluster AKS. Verrà `linkerd` creato automaticamente uno spazio dei nomi e i componenti verranno installati in questo spazio dei nomi.

```console
linkerd install | kubectl apply -f -
```

Linkerd distribuisce un numero di oggetti. Vedrai l'elenco dall'output del `linkerd install` comando precedente. Il completamento della distribuzione dei componenti Linkerd dovrebbe richiedere circa 1 minuto, a seconda dell'ambiente del cluster.

A questo punto, linkerè stato distribuito nel cluster AKS. Per garantire una corretta distribuzione di Linkerd, passiamo alla sezione successiva a [Validate the Linkerd installation](#validate-the-linkerd-installation).

## <a name="validate-the-linkerd-installation"></a>Convalidare l'installazione di Linkerd

Verificare che le risorse siano state create correttamente. Utilizzare i comandi [get svc][kubectl-get] e [kubectl get][kubectl-get] `linkerd` pod per eseguire una query sullo `linkerd install` spazio dei nomi, in cui i componenti Linkerd sono stati installati dal comando:

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

L'output di esempio seguente mostra i servizi e i pod (pianificati nei nodi Linux) che dovrebbero essere in esecuzione:

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

Linkerd fornisce un `linkerd` comando tramite il file binario client per convalidare che il piano di controllo Linkerd sia stato installato e configurato correttamente.

```console
linkerd check
```

Si dovrebbe vedere qualcosa di simile al seguente per indicare che l'installazione è stata completata:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>Accedere al dashboard

Linkerd viene fornito con un dashboard che fornisce informazioni dettagliate sulla rete di servizio e sui carichi di lavoro. Per accedere al dashboard, utilizzare il `linkerd dashboard` comando . Questo comando sfrutta [kubectl port-forward][kubectl-port-forward] per creare una connessione sicura tra il computer client e i pod pertinenti nel cluster AKS. Si aprirà quindi automaticamente il dashboard nel browser predefinito.

```console
linkerd dashboard
```

Il comando creerà anche un port forward e restituirà un collegamento per i dashboard Grafana.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>Disinstallare Linker d'AKS

> [!WARNING]
> L'eliminazione di Linkerd da un sistema in esecuzione può causare problemi relativi al traffico tra i servizi. Assicurarsi di aver effettuato disposizioni per il corretto funzionamento del sistema senza Linkerd prima di procedere.

In primo luogo è necessario rimuovere i proxy del piano dati. Rimuovere eventuali annotazioni Proxy Injection [automatiche][linkerd-automatic-proxy-injection] dagli spazi dei nomi del carico di lavoro e distribuire le distribuzioni del carico di lavoro. I carichi di lavoro non devono più avere componenti del piano dati associati.

Infine, rimuovere il piano di controllo come segue:

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>Passaggi successivi

Per esplorare altre opzioni di installazione e configurazione per Linkerd, vedere le seguenti linee guida ufficiali del Linkerd:

- [Linkerd - Installazione Helm][linkerd-install-with-helm]
- [Linkerd - Installazione in più fasi per soddisfare i privilegi di ruolo][linkerd-multi-stage-installation]

È inoltre possibile seguire scenari aggiuntivi utilizzando:You can also follow additional scenarios using:

- [Linkerd emojivoto demo][linkerd-demo-emojivoto]
- [Demo di libri lampeggiante][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
