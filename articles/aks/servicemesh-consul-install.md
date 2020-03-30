---
title: Installare Console nel servizio Azure Kubernetes (AKS)Install Consul in Azure Kubernetes Service (AKS)
description: Informazioni su come installare e usare Consul per creare una rete mesh del servizio in un cluster di servizio Azure Kubernetes Service (AKS)
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 1601ab6d81b888fd2247e95f22c58e1fc91df698
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273732"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Installare e usare Conconsole nel servizio Azure Kubernetes (AKS)Install and use Consul in Azure Kubernetes Service (AKS)

[Consul][consul-github] è una rete di servizio open source che fornisce un set chiave di funzionalità tra i microservizi in un cluster Kubernetes.Consul is an open-source service mesh that provides a key set of functionality across the microservices in a Kubernetes cluster. Queste funzionalità includono l'individuazione dei servizi, il controllo dell'integrità, la segmentazione dei servizi e l'osservabilità. Per ulteriori informazioni su Consul, vedere la documentazione ufficiale [Che cos'è Console?][consul-docs-concepts] .

In questo articolo viene illustrato come installare Conconsole. I componenti Conconsole vengono installati in un cluster Kubernetes su AKS.

> [!NOTE]
> Queste istruzioni fanno `1.6.0`riferimento alla versione consul e utilizzano almeno Helm version `2.14.2`.
>
> Le versioni `1.6.x` Conconsole possono essere eseguite con `1.13+`le versioni Kubernetes . Ulteriori versioni di Conconsole sono disponibili in [GitHub - Consul Releases][consul-github-releases] e informazioni su ciascuna delle versioni in [Consul- Note sulla versione][consul-release-notes].

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Installare i componenti del console su AKS
> * Convalidare l'installazione del console
> * Disinstallare Console da AKS

## <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questo articolo presuppongono che sia stato creato `1.13` un cluster AKS (Kubernetes e versioni successive, con RBAC abilitato) e che sia stata stabilita una `kubectl` connessione con il cluster. Se occorre assistenza con uno di questi elementi, vedere la [Guida introduttiva al servizio Azure Kubernetes][aks-quickstart]. Verificare che il cluster disponga di almeno 3 nodi nel pool di nodi Linux.Ensure that your cluster has at least 3 nodes in the Linux node pool.

Avrai bisogno di [Helm][helm] per seguire queste istruzioni e installare Console. È consigliabile che nel cluster sia installata e configurata correttamente la versione stabile più recente. Se hai bisogno di aiuto per l'installazione di Helm, consulta le linee guida per [l'installazione][helm-install]di AKS Helm . Anche tutti i pod Console devono essere pianificati per l'esecuzione su nodi Linux.All Consul pods must also be scheduled to run on Linux nodes.

Questo articolo separa le linee guida per l'installazione di Consul in diversi passaggi discreti. Il risultato finale è lo stesso nella struttura delle [linee guida][consul-install-k8]ufficiali per l'installazione del Console.

### <a name="install-the-consul-components-on-aks"></a>Installare i componenti del console su AKS

Inizieremo scaricando la `v0.10.0` versione della tabella Consul Helm. Questa versione del grafico include `1.6.0`la versione Conconsole .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Usare Helm e `consul-helm` il grafico scaricato per `consul` installare i componenti Consul nello spazio dei nomi del cluster AKS. 

> [!NOTE]
> **Opzioni di installazione**
> 
> Stiamo utilizzando le seguenti opzioni come parte della nostra installazione:
> - `connectInject.enabled=true`- consentire l'iniezione di proxy nei baccelli
> - `client.enabled=true`- abilitare i client Conconsole per l'esecuzione su ogni nodo- enable Consul clients to run on every node
> - `client.grpc=true`- abilitare il listener gRPC per connectInject
> - `syncCatalog.enabled=true`- sincronizzare i servizi Kubernetes e Consul
>
> **Selettori di nodi**
>
> Il console deve essere pianificato per l'esecuzione su nodi Linux.Conconsole currently must be scheduled to run on Linux nodes. Se nel cluster sono presenti nodi di Windows Server, è necessario assicurarsi che i pod Conconsole siano pianificati solo per l'esecuzione sui nodi Linux. Useremo [i selettori][kubernetes-node-selectors] di nodo per assicurarci che i pod siano pianificati per i nodi corretti.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

Il `Consul` grafico Helm distribuisce un numero di oggetti. È possibile visualizzare l'elenco `helm install` dall'output del comando precedente. Il completamento della distribuzione dei componenti Conconsole può richiedere circa 3 minuti, a seconda dell'ambiente del cluster.

A questo punto, è stato distribuito Console nel cluster AKS. Per garantire una corretta distribuzione di Console, passiamo alla sezione successiva per convalidare l'installazione di Console.

## <a name="validate-the-consul-installation"></a>Convalidare l'installazione del console

Verificare che le risorse siano state create correttamente. Usate i comandi get svc e [kubectl get][kubectl-get] `consul` pod [per][kubectl-get] eseguire una query sullo `helm install` spazio dei nomi, in cui i componenti Consul sono stati installati dal comando:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

L'output di esempio seguente mostra i servizi e i pod (pianificati nei nodi Linux) che dovrebbero essere in esecuzione:

```output
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

Tutti i pod devono mostrare `Running`lo stato di . Se i pod non presentano questo stato, attendere uno o due minuti fino a quando non viene visualizzato. Se per uno dei pod viene segnalato un problema, usare il comando [kubectl describe pod][kubectl-describe] per esaminare l'output e lo stato.

## <a name="accessing-the-consul-ui"></a>Accesso all'interfaccia utente di Consul

L'interfaccia utente Console è stata installata nella configurazione precedente e fornisce la configurazione basata sull'interfaccia utente per Console. L'interfaccia utente per console non viene esposta pubblicamente tramite un indirizzo IP esterno. Per accedere all'interfaccia utente di Consul, utilizzare il comando [kubectl port-forward.][kubectl-port-forward] Questo comando crea una connessione sicura tra il computer client e il pod pertinente nel cluster AKS.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

È ora possibile aprire un `http://localhost:8080/ui` browser e puntarlo a per aprire l'interfaccia utente console. Quando si apre l'interfaccia utente, dovrebbe essere visualizzato quanto segue:

![Interfaccia utente console](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Disinstallare Console da AKS

> [!WARNING]
> L'eliminazione del console da un sistema in esecuzione può causare problemi relativi al traffico tra i servizi. Assicurarsi di aver effettuato disposizioni per il corretto funzionamento del sistema senza Console prima di procedere.

### <a name="remove-consul-components-and-namespace"></a>Rimuovere i componenti Consul e lo spazio dei nomi

Per rimuovere Conconsole dal cluster AKS, utilizzare i comandi seguenti. I `helm delete` comandi rimuoveranno il `consul` `kubectl delete namespace` grafico e `consul` il comando rimuoverà lo spazio dei nomi.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Passaggi successivi

Per esplorare altre opzioni di installazione e configurazione per Conconsole, vedere i seguenti articoli ufficiali di Consul:

- [Console - Guida all'installazione di Helm][consul-install-k8]
- [Consul - Opzioni di installazione Helm][consul-install-helm-options]

È inoltre possibile seguire scenari aggiuntivi utilizzando:You can also follow additional scenarios using:

- [Applicazione di esempio Consul][consul-app-example]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://www.consul.io/docs/platform/k8s/run.html
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-app-example]: https://github.com/hashicorp/demo-consul-101/tree/master/k8s
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
