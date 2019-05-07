---
title: Aggiornare e riavviare i nodi Linux con kured in Azure Kubernetes Service (AKS)
description: Informazioni su come aggiornare i nodi Linux e riavvia automaticamente con kured in Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: b426399f73375618a2084eff82abba5d4934b914
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074216"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Applicare sicurezza e gli aggiornamenti del kernel a nodi Linux in Azure Kubernetes Service (AKS)

Per proteggere i cluster, gli aggiornamenti della sicurezza vengono applicati automaticamente ai nodi di Linux nel servizio contenitore di AZURE. Questi aggiornamenti includono correzioni della sicurezza del sistema operativo o gli aggiornamenti del kernel. Alcuni di questi aggiornamenti richiedono il riavvio di un nodo per completare il processo. Servizio contenitore di AZURE non riavvia automaticamente i nodi Linux per completare il processo di aggiornamento.

Il processo per mantenere aggiornati i nodi di Windows Server, attualmente in anteprima nel servizio contenitore di AZURE, è leggermente diverso. Windows Server e non ricevono gli aggiornamenti giornalieri. In alternativa, è eseguire un aggiornamento sul servizio contenitore di AZURE che consente di distribuire nuovi nodi con l'ultima immagine di base di Window Server e le patch. Per i cluster servizio contenitore di AZURE che usano i nodi di Windows Server, vedere [esegue l'aggiornamento di un pool di nodi in AKS][nodepool-upgrade].

Questo articolo illustra come usare open source [(Daemon di riavvio KUbernetes) kured] [ kured] da controllare per nodi Linux che richiedono un riavvio, quindi di gestire automaticamente la ripianificazione dell'esecuzione di POD e il nodo Riavvia processo.

> [!NOTE]
> `Kured` è un progetto open source di Weaveworks. L'assistenza per questo progetto in servizio Azure Kubernetes è fornita con il massimo impegno possibile. Supporto aggiuntivo è reperibile nel canale slack weave # della community.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster AKS esistente. Se è necessario un cluster servizio Azure Kubernetes, vedere la Guida introduttiva su servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

Anche necessario la CLI di Azure versione 2.0.59 o versione successiva installato e configurato. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Comprendere l'esperienza di aggiornamento del nodo AKS

In un cluster servizio Azure Kubernetes, i nodi Kubernetes vengono eseguiti come macchine virtuali di Azure (VM). Queste macchine virtuali, basate su Linux, usano un'immagine di Ubuntu con il sistema operativo configurato per cercare automaticamente gli aggiornamenti tutte le notti. Se sono disponibili aggiornamenti di sicurezza o del kernel, questi vengono scaricati e installati automaticamente.

![Processo di aggiornamento e riavvio del nodo servizio Azure Kubernetes con kured](media/node-updates-kured/node-reboot-process.png)

Alcuni aggiornamenti di sicurezza, ad esempio gli aggiornamenti del kernel, richiedono un riavvio del nodo per finalizzare il processo. Un nodo Linux che richiede il riavvio consente di creare un file denominato */var/run/reboot-required*. Questo processo di riavvio non avviene automaticamente.

È possibile usare i propri flussi di lavoro e processi per gestire i riavvii del nodo, oppure utilizzare `kured` per dirigere il processo. Con `kured`, una [DaemonSet] [ DaemonSet] viene distribuito un pod in esecuzione su ogni nodo Linux nel cluster. Questi pod nel DaemonSet verificano la presenza del file */var/run/reboot-required*, quindi avviano un processo di riavvio dei nodi.

### <a name="node-upgrades"></a>Aggiornamenti del nodo

Esiste un processo aggiuntivo nel servizio Azure Kubernetes che permette di *aggiornare* un cluster. In genere, un aggiornamento è un passaggio a una versione più recente di Kubernetes, non solo l'applicazione di aggiornamenti di sicurezza del nodo. Un aggiornamento di servizio Azure Kubernetes esegue le azioni seguenti:

* Un nuovo nodo viene distribuito con gli aggiornamenti di sicurezza e la versione di Kubernetes applicati più recenti.
* Un nodo precedente viene chiuso e svuotato.
* I pod vengono pianificati sul nuovo nodo.
* Il nodo precedente viene eliminato.

Non è possibile mantenere la stessa versione di Kubernetes durante un evento di aggiornamento. È necessario specificare una versione più recente di Kubernetes. Per eseguire l'aggiornamento alla versione più recente di Kubernetes, è possibile [aggiornare il cluster servizio Azure Kubernetes][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Distribuire kured in un cluster servizio Azure Kubernetes

Per distribuire il DaemonSet `kured`, applicare l'esempio di manifesto YAML seguente dalla loro pagina dei progetti di GitHub. Questo manifesto crea un ruolo e un ruolo del cluster, associazioni e un account del servizio, quindi distribuisce il DaemonSet usando la versione 1.1.1.0 di `kured` che supporta i cluster di servizio Azure Kubernetes versione 1.9 o successiva.

```console
kubectl apply -f https://github.com/weaveworks/kured/releases/download/1.1.0/kured-1.1.0.yaml
```

È anche possibile configurare i parametri aggiuntivi per `kured`, ad esempio l'integrazione con Prometheus o Slack. Per altre informazioni sui parametri di configurazione aggiuntivi, consultare la [documentazione di installazione di kured][kured-install].

## <a name="update-cluster-nodes"></a>Aggiornare i nodi del cluster

Per impostazione predefinita, i nodi di Linux nel servizio contenitore di AZURE controllare gli aggiornamenti ogni sera. Se non si vuole attendere, è possibile eseguire manualmente un aggiornamento per verificare che l'esecuzione di `kured` avvenga correttamente. In primo luogo, seguire la procedura per creare una [connessione SSH a uno dei nodi servizio Azure Kubernetes][aks-ssh]. Dopo aver creato una connessione SSH al nodo Linux, verificare la presenza di aggiornamenti e applicarli come indicato di seguito:

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

Una volta completato il processo di aggiornamento, è possibile visualizzare lo stato dei nodi usando il comando [kubectl get nodes][kubectl-get-nodes] con il parametro `--output wide`. Questo output aggiuntivo consente di visualizzare una differenza in *KERNEL-VERSION* dei nodi sottostanti, come illustrato nell'output di esempio seguente. Il *aks-nodepool1-28993262-0* è stata aggiornata in un passaggio precedente e la versione kernel *4.15.0-1039-azure*. Il nodo *aks-nodepool1-28993262-1* che non è stato aggiornato versione kernel *4.15.0-1037-azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo dettagliato come usare `kured` per riavviare automaticamente come parte del processo di aggiornamento della sicurezza i nodi di Linux. Per eseguire l'aggiornamento alla versione più recente di Kubernetes, è possibile [aggiornare il cluster servizio Azure Kubernetes][aks-upgrade].

Per i cluster servizio contenitore di AZURE che usano i nodi di Windows Server, vedere [esegue l'aggiornamento di un pool di nodi in AKS][nodepool-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured#installation
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
