---
title: Gestire i riavvii dei nodi Linux con KURED
titleSuffix: Azure Kubernetes Service
description: Informazioni su come aggiornare i nodi Linux e riavviarli automaticamente con KURED in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: 35c9e76c234e4b09fbb090eda363506ee3e11130
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88164241"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Applicare gli aggiornamenti di sicurezza e kernel ai nodi Linux in Azure Kubernetes Service (AKS)

Per proteggere i cluster, gli aggiornamenti della sicurezza vengono applicati automaticamente ai nodi Linux in AKS. Questi aggiornamenti includono correzioni della sicurezza del sistema operativo o gli aggiornamenti del kernel. Alcuni di questi aggiornamenti richiedono il riavvio di un nodo per completare il processo. AKS non riavvia automaticamente questi nodi Linux per completare il processo di aggiornamento.

Il processo di mantenimento dei nodi di Windows Server aggiornato è leggermente diverso. I nodi di Windows Server non ricevono gli aggiornamenti giornalieri. Viene invece eseguito un aggiornamento di AKS che distribuisce nuovi nodi con l'immagine e le patch più recenti della finestra di base del server. Per i cluster AKS che usano i nodi di Windows Server, vedere [aggiornare un pool di nodi in AKS][nodepool-upgrade].

Questo articolo illustra come usare il KURED Open Source [(daemon riavvio KUbernetes)][kured] per controllare i nodi Linux che richiedono un riavvio, quindi gestire automaticamente la ripianificazione dei pod in esecuzione e il processo di riavvio del nodo.

> [!NOTE]
> `Kured` è un progetto open source di Weaveworks. L'assistenza per questo progetto in servizio Azure Kubernetes è fornita con il massimo impegno possibile. Il supporto aggiuntivo è reperibile nel canale Slack #weave-community.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

È anche necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.59 o versione successiva. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Comprendere l'esperienza di aggiornamento del nodo del servizio Azure Kubernetes

In un cluster servizio Azure Kubernetes, i nodi Kubernetes vengono eseguiti come macchine virtuali di Azure (VM). Queste macchine virtuali, basate su Linux, usano un'immagine di Ubuntu con il sistema operativo configurato per cercare automaticamente gli aggiornamenti tutte le notti. Se sono disponibili aggiornamenti di sicurezza o del kernel, questi vengono scaricati e installati automaticamente.

![Processo di aggiornamento e riavvio del nodo servizio Azure Kubernetes con kured](media/node-updates-kured/node-reboot-process.png)

Alcuni aggiornamenti di sicurezza, ad esempio gli aggiornamenti del kernel, richiedono un riavvio del nodo per finalizzare il processo. Un nodo Linux che richiede un riavvio crea un file denominato */var/run/reboot-required*. Questo processo di riavvio non avviene automaticamente.

È possibile usare i propri flussi di lavoro e processi per gestire i riavvii del nodo, oppure utilizzare `kured` per dirigere il processo. Con `kured` viene distribuito un [DaemonSet][DaemonSet] che esegue un pod in ogni nodo Linux del cluster. Questi pod in DaemonSet controllano l'esistenza del file */var/run/reboot-required* e quindi avviano un processo per riavviare i nodi.

### <a name="node-upgrades"></a>Aggiornamenti del nodo

Esiste un processo aggiuntivo nel servizio Azure Kubernetes che permette di *aggiornare* un cluster. In genere, un aggiornamento è un passaggio a una versione più recente di Kubernetes, non solo l'applicazione di aggiornamenti di sicurezza del nodo. Un aggiornamento di servizio Azure Kubernetes esegue le azioni seguenti:

* Un nuovo nodo viene distribuito con gli aggiornamenti di sicurezza e la versione di Kubernetes applicati più recenti.
* Un nodo precedente viene chiuso e svuotato.
* I pod vengono pianificati sul nuovo nodo.
* Il nodo precedente viene eliminato.

Non è possibile mantenere la stessa versione di Kubernetes durante un evento di aggiornamento. È necessario specificare una versione più recente di Kubernetes. Per eseguire l'aggiornamento alla versione più recente di Kubernetes, è possibile [aggiornare il cluster servizio Azure Kubernetes][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Distribuire kured in un cluster servizio Azure Kubernetes

Per distribuire il `kured` DaemonSet, installare il seguente grafico Helm KURED ufficiale. Vengono creati un ruolo e un ruolo cluster, binding e un account del servizio, quindi viene distribuito il DaemonSet utilizzando `kured` .

```console
# Add the Kured Helm repository
helm repo add kured https://weaveworks.github.io/kured

# Update your local Helm chart repository cache
helm repo update

# Create a dedicated namespace where you would like to deploy kured into
kubectl create namespace kured

# Install kured in that namespace with Helm 3 (only on Linux nodes, kured is not working on Windows nodes)
helm install kured kured/kured --namespace kured --set nodeSelector."beta\.kubernetes\.io/os"=linux
```

È anche possibile configurare i parametri aggiuntivi per `kured`, ad esempio l'integrazione con Prometheus o Slack. Per ulteriori informazioni sui parametri di configurazione aggiuntivi, vedere il [grafico Helm KURED][kured-install].

## <a name="update-cluster-nodes"></a>Aggiornare i nodi del cluster

Per impostazione predefinita, i nodi Linux in AKS verificano la disponibilità di aggiornamenti ogni sera. Se non si vuole attendere, è possibile eseguire manualmente un aggiornamento per verificare che l'esecuzione di `kured` avvenga correttamente. In primo luogo, seguire la procedura per creare una [connessione SSH a uno dei nodi servizio Azure Kubernetes][aks-ssh]. Una volta stabilita una connessione SSH al nodo Linux, verificare la disponibilità di aggiornamenti e applicarli come indicato di seguito:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Se sono stati applicati gli aggiornamenti che richiedono un riavvio del nodo, viene scritto un file in */var/run/reboot-required*. `Kured` verifica la presenza di nodi che richiedono un riavvio ogni 60 minuti per impostazione predefinita.

## <a name="monitor-and-review-reboot-process"></a>Monitorare e controllare il processo di riavvio

Quando una delle repliche nel DaemonSet ha rilevato che è necessario un riavvio del nodo, viene inserito un blocco sul nodo tramite l'API di Kubernetes. Questo blocco impedisce l'aggiunta al nodo di altri pod pianificati. Inoltre, il blocco indica che deve essere riavviato solo un nodo alla volta. Con il nodo bloccato, i pod in esecuzione vengono rimossi dal nodo, che viene quindi riavviato.

È possibile monitorare lo stato dei nodi usando il comando [kubectl get nodes][kubectl-get-nodes]. L'output di esempio seguente mostra un nodo con lo stato *SchedulingDisabled* mentre si prepara per il processo di riavvio:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

Una volta completato il processo di aggiornamento, è possibile visualizzare lo stato dei nodi usando il comando [kubectl get nodes][kubectl-get-nodes] con il parametro `--output wide`. Questo output aggiuntivo consente di visualizzare una differenza in *KERNEL-VERSION* dei nodi sottostanti, come illustrato nell'output di esempio seguente. Il servizio *AKS-nodepool1-28993262-0* è stato aggiornato in un passaggio precedente e Mostra la versione del kernel *4.15.0-1039-Azure*. Il nodo *AKS-nodepool1-28993262-1* che non è stato aggiornato Mostra la versione del kernel *4.15.0-1037-Azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo illustra in modo dettagliato come usare `kured` per riavviare automaticamente i nodi Linux come parte del processo di aggiornamento della sicurezza. Per eseguire l'aggiornamento alla versione più recente di Kubernetes, è possibile [aggiornare il cluster servizio Azure Kubernetes][aks-upgrade].

Per i cluster AKS che usano i nodi di Windows Server, vedere [aggiornare un pool di nodi in AKS][nodepool-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured/tree/master/charts/kured
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
