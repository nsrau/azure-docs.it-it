---
title: Installare Hashicorp console in Azure Kubernetes Service (AKS)
description: Informazioni su come installare e usare Consul per creare una mesh di servizi in un cluster Azure Kubernetes Service (AKS)
services: container-service
author: dstrebel
ms.service: container-service
ms.topic: article
ms.date: 06/19/2019
ms.author: dastrebe
ms.openlocfilehash: 7acd2533079499091427c7e63741b9c587ee29e5
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70189224"
---
# <a name="install-and-use-consul-connect-in-azure-kubernetes-service-aks"></a>Installare e usare console Connect in Azure Kubernetes Service (AKS)

[Console][consul-github] è una mesh di servizi open source che fornisce un set di chiavi di funzionalità tra i microservizi in un cluster Kubernetes. Queste funzionalità includono l'individuazione dei servizi, il controllo dell'integrità, la segmentazione dei servizi e l'osservabilità. Per ulteriori informazioni su Consul, vedere la documentazione ufficiale di [che cos'è Consul?][consul-docs-concepts] .

Questo articolo illustra come installare Console. I componenti Consul sono installati in un cluster Kubernetes in AKS.

> [!NOTE]
> Queste istruzioni fanno riferimento alla `1.5`versione di console.
>
> Le versioni `1.5.x` di Consul sono state testate dal team di Hashicorp per `1.12`le `1.14`versioni `1.14`di Kubernetes,,. Altre versioni di Consul sono disponibili nelle versioni di [GitHub-console][consul-github-releases] e informazioni su ognuna delle versioni in [console-note sulla versione][consul-release-notes].

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Installare i componenti di console su AKS
> * Convalidare l'installazione di Consul
> * Disinstalla console da AKS

## <a name="before-you-begin"></a>Prima di iniziare

I passaggi descritti in questo articolo presuppongono che sia stato creato un cluster AKS `1.11` (Kubernetes e versioni successive, con RBAC abilitato) e `kubectl` che sia stata stabilita una connessione con il cluster. Per informazioni su questi elementi, vedere la [Guida introduttiva di AKS][aks-quickstart].

È necessario [Helm][helm] per seguire queste istruzioni e installare Console. È consigliabile che nel cluster sia installata `2.12.2` e configurata correttamente la versione o una versione successiva. Per assistenza sull'installazione di Helm, vedere la Guida all' [installazione di AKS Helm][helm-install]. Anche tutti i pod console devono essere pianificati per l'esecuzione nei nodi Linux.

Questo articolo separa le linee guida per l'installazione di Consul in diversi passaggi discreti. Il risultato finale è lo stesso nella struttura delle [linee guida][consul-install-k8]per l'installazione ufficiale di console.

### <a name="install-the-consul-components-on-aks"></a>Installare i componenti di console su AKS

Viene prima di tutto clonato il repository ufficiale di HashiCorp in Kubernetes GitHub.

```bash
git clone https://github.com/hashicorp/consul-helm.git
cd consul-helm
```

Sarà quindi necessario creare un file di valori di Helm personalizzato per l'installazione di console. Creare il seguente file di valori personalizzati prima di installare Console.

```bash
vim consul-values.yaml
```

Quindi copiare e incollare i valori seguenti nel file Consul-values. yaml.

```yaml
# Sets datacenter name and version Consul to use
global:
  datacenter: dc1
  image: "consul:1.5.2"

# Enables proxies to be injected into pods
connectInject:
  enabled: true

# Enables UI on ClusterIP
ui:
  service:
    type: "ClusterIP"

# Enables GRCP that is required for connectInject
client:
  enabled: true
  grpc: true

# Sets replicase to 3 for HA
server:
  replicas: 3
  bootstrapExpect: 1
  disruptionBudget:
    enabled: true
    maxUnavailable: 1

# Syncs Kubernetes service discovery with Consul
syncCatalog:
  enabled: true
```

Ora che è disponibile il file di valori personalizzati, è possibile installare Consul nel cluster AKS

Bash

```bash
helm install -f consul-values.yaml --name consul --namespace consul .
```
Il `Consul` grafico Helm distribuisce un certo numero di oggetti. È possibile visualizzare l'elenco dall'output del `helm install` comando precedente. La distribuzione dei componenti Consul può richiedere da 4 a 5 minuti per il completamento, a seconda dell'ambiente cluster.

> [!NOTE]
> Tutti i pod console devono essere pianificati per l'esecuzione nei nodi Linux. Se sono presenti pool di nodi di Windows Server oltre ai pool di nodi Linux nel cluster, verificare che tutti i pod console siano stati pianificati per l'esecuzione nei nodi Linux.

A questo punto, è stato distribuito console per il cluster AKS. Per assicurarsi che la distribuzione di Consul venga completata, passare alla sezione successiva per convalidare l'installazione di Consul.

## <a name="validate-the-consul-installation"></a>Convalidare l'installazione di Consul

Verificare innanzitutto che siano stati creati i servizi previsti. Usare il comando [kubectl Get SVC][kubectl-get] per visualizzare i servizi in esecuzione. Eseguire una `consul` query sullo spazio dei nomi, `consul` in cui sono stati installati i componenti Consul dal grafico Helm:

```console
kubectl get svc --namespace consul
```

L'output di esempio indicato di seguito mostra i servizi attualmente in esecuzione:

```console
NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP             PORT(S)                                                                   AGE
consul                               ExternalName   <none>         consul.service.consul   <none>                                                                    38m
consul-consul-connect-injector-svc   ClusterIP      10.0.89.113    <none>                  443/TCP                                                                   40m
consul-consul-dns                    ClusterIP      10.0.166.82    <none>                  53/TCP,53/UDP                                                             40m
consul-consul-server                 ClusterIP      None           <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   40m
consul-consul-ui                     ClusterIP      10.0.117.164   <none>                  80/TCP                                                                    40m
```

Successivamente, verificare che siano stati creati i pod richiesti. Usare il comando [kubectl Get Pod][kubectl-get] e nuovamente eseguire una query `consul` sullo spazio dei nomi:

```console
kubectl get pods --namespace consul
```

L'output di esempio indicato di seguito illustra i pod in esecuzione:

```console
NAME                                                              READY   STATUS    RESTARTS   AGE
consul-consul-7cc9v                                               1/1     Running   0          37m
consul-consul-7klg7                                               1/1     Running   0          37m
consul-consul-connect-injector-webhook-deployment-57f88df8hgmfs   1/1     Running   0          37m
consul-consul-lq8qb                                               1/1     Running   0          37m
consul-consul-server-0                                            1/1     Running   0          37m
consul-consul-server-1                                            1/1     Running   0          36m
consul-consul-server-2                                            1/1     Running   0          36m
consul-consul-sync-catalog-7cf7d5bfff-jjbjv                       1/1     Running   2          37m
```

 Tutti i pod dovrebbero mostrare lo stato `Running`. Se i pod non presentano questo stato, attendere uno o due minuti fino a quando non viene visualizzato. Se i pod segnalano un problema, usare il [kubectl descrivere il Pod][kubectl-describe] comando per verificarne l'output e lo stato.

## <a name="accessing-the-consul-ui"></a>Accesso all'interfaccia utente di console

L'interfaccia utente di console è stata installata nella configurazione precedente che fornisce la configurazione basata sull'interfaccia utente per console. L'interfaccia utente per Consul non è esposta pubblicamente tramite un indirizzo IP esterno. Per accedere alle interfacce utente dei componenti aggiuntivi, usare il comando [kubectl Port-avanti][kubectl-port-forward] . Questo comando crea una connessione sicura tra il computer client e il Pod pertinente nel cluster AKS.

```bash
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```
È ora possibile aprire un browser e fare in modo `http://localhost:8080/ui` che punti a per aprire l'interfaccia utente di console. Quando si apre l'interfaccia utente, verrà visualizzato quanto segue:

![Interfaccia utente console](./media/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Disinstalla console da AKS

> [!WARNING]
> L'eliminazione di Consul da un sistema in esecuzione può causare problemi correlati al traffico tra i servizi. Assicurarsi di aver fatto in modo che il sistema continui a funzionare correttamente senza Consul prima di procedere.

### <a name="remove-consul-components-and-namespace"></a>Rimuovere i componenti e lo spazio dei nomi del console

Per rimuovere Consul dal cluster AKS, usare i comandi seguenti. Il grafico viene `kubectl delete ns` rimosso e il comando rimuoverà lo `consul` spazio dei nomi. `consul` `helm delete`

```bash
helm delete --purge consul
kubectl delete ns consul
```

## <a name="next-steps"></a>Passaggi successivi

Per esplorare altre opzioni di installazione e configurazione per Consul, vedere gli articoli seguenti del console ufficiale:

- [Guida all'installazione di Consul-Helm][consul-install-k8]
- [Opzioni di installazione di Consul-Helm][consul-install-helm-options]

È anche possibile seguire altri scenari usando l' [applicazione di esempio console][consul-app-example].

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

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
