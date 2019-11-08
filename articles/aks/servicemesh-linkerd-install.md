---
title: Installare Linkerd nel servizio Azure Kubernetes (AKS)
description: Informazioni su come installare e usare Linkerd per creare una mesh di servizi in un cluster di Azure Kubernetes Service (AKS)
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: f70052a62152a20f808c1e491a663d1406fbd407
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747719"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>Installare Linkerd nel servizio Azure Kubernetes (AKS)

[Linkerd][linkerd-github] è un progetto open source mesh e [CNCF incubating][linkerd-cncf]. Linkerd è una rete di servizi ultraleggeri che fornisce funzionalità che includono la gestione del traffico, l'identità del servizio e la sicurezza, l'affidabilità e l'osservabilità. Per ulteriori informazioni su Linkerd, vedere le [domande frequenti su Linkerd][linkerd-faq] e la documentazione sull' [architettura Linkerd][linkerd-architecture] .

Questo articolo illustra come installare Linkerd. Il file binario client `linkerd` Linkerd viene installato nel computer client e i componenti Linkerd vengono installati in un cluster Kubernetes in AKS.

> [!NOTE]
> Queste istruzioni fanno riferimento a Linkerd Version `stable-2.6.0`.
>
> Il `stable-2.6.x` Linkerd può essere eseguito su `1.13+`versioni di Kubernetes. È possibile trovare altre versioni stabili e perimetrali di Linkerd in [GitHub-Linkerd Releases][linkerd-github-releases].

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Scaricare e installare il file binario client Linkerd di Linkerd
> * Installare Linkerd su AKS
> * Convalidare l'installazione di Linkerd
> * Accedere al dashboard
> * Disinstallare Linkerd da AKS

## <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questo articolo presuppongono che sia stato creato un cluster AKS (Kubernetes `1.13` e versioni successive, con RBAC abilitato) e che sia stata stabilita una connessione `kubectl` con il cluster. Per informazioni su questi elementi, vedere la [Guida introduttiva di AKS][aks-quickstart].

Tutti i pod Linkerd devono essere pianificati per l'esecuzione nei nodi Linux. questa configurazione è l'impostazione predefinita nel metodo di installazione descritto di seguito e non richiede alcuna configurazione aggiuntiva.

Questo articolo separa le linee guida per l'installazione di Linkerd in diversi passaggi discreti. Il risultato è lo stesso nella struttura delle [linee guida][linkerd-getting-started]introduttive per il Linkerd ufficiale.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>Installare Linkerd su AKS

Prima di installare Linkerd, verranno eseguiti i controlli di pre-installazione per determinare se il piano di controllo può essere installato nel cluster AKS:

```console
linkerd check --pre
```

Verrà visualizzata una schermata simile alla seguente per indicare che il cluster AKS è una destinazione di installazione valida per Linkerd:

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

A questo punto è possibile installare i componenti di Linkerd. Usare i file binari `linkerd` e `kubectl` per installare i componenti Linkerd nel cluster AKS. Verrà creato automaticamente uno spazio dei nomi `linkerd` e i componenti verranno installati in questo spazio dei nomi.

```console
linkerd install | kubectl apply -f -
```

Linkerd distribuisce un certo numero di oggetti. Verrà visualizzato l'elenco dall'output del comando `linkerd install` precedente. La distribuzione dei componenti Linkerd dovrebbe richiedere circa 1 minuto, a seconda dell'ambiente cluster.

A questo punto, è stato distribuito Linkerd nel cluster AKS. Per assicurarsi che la distribuzione di Linkerd venga completata correttamente, passare alla sezione successiva per [convalidare l'installazione di Linkerd](#validate-the-linkerd-installation).

## <a name="validate-the-linkerd-installation"></a>Convalidare l'installazione di Linkerd

Verificare che le risorse siano state create correttamente. Usare i comandi [kubectl Get SVC][kubectl-get] e [kubectl Get Pod][kubectl-get] per eseguire una query sullo spazio dei nomi `linkerd`, in cui sono stati installati i componenti Linkerd con il comando `linkerd install`:

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

L'output di esempio seguente mostra i servizi e i pod (pianificati sui nodi Linux) che ora devono essere in esecuzione:

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

Linkerd fornisce un comando tramite il file binario client di `linkerd` per verificare che il piano di controllo Linkerd sia stato installato e configurato correttamente.

```console
linkerd check
```

Verrà visualizzata una schermata simile alla seguente per indicare che l'installazione è stata completata correttamente:

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

Linkerd viene fornito con un dashboard che fornisce informazioni dettagliate sulla rete e sui carichi di lavoro del servizio. Per accedere al dashboard, usare il comando `linkerd dashboard`. Questo comando sfrutta la [porta kubectl][kubectl-port-forward] per creare una connessione sicura tra il computer client e i pod pertinenti nel cluster AKS. Il dashboard verrà aperto automaticamente nel browser predefinito.

```console
linkerd dashboard
```

Il comando creerà anche una porta e un collegamento per i dashboard di Grafana.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>Disinstallare Linkerd da AKS

> [!WARNING]
> L'eliminazione di Linkerd da un sistema in esecuzione può causare problemi correlati al traffico tra i servizi. Assicurarsi di aver fatto in modo che il sistema continui a funzionare correttamente senza Linkerd prima di procedere.

Prima di tutto è necessario rimuovere i proxy del piano dati. Rimuovere le [annotazioni][linkerd-automatic-proxy-injection] automatiche di proxy Injection dagli spazi dei nomi del carico di lavoro e implementare le distribuzioni del carico di lavoro. I carichi di lavoro non devono più avere alcun componente del piano dati associato.

Infine, rimuovere il piano di controllo come segue:

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'installazione e le opzioni di configurazione per Linkerd, vedere le indicazioni ufficiali Linkerd seguenti:

- [Installazione di Linkerd-Helm][linkerd-install-with-helm]
- [Linkerd-installazione in più fasi per soddisfare i privilegi del ruolo][linkerd-multi-stage-installation]

È anche possibile seguire altri scenari usando:

- [Demo di Linkerd emojivoto][linkerd-demo-emojivoto]
- [Demo di Linkerd Books][linkerd-demo-books]

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
