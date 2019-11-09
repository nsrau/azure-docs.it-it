---
title: Usare il componente di scalabilità automatica del cluster nel servizio Azure Kubernetes (AKS)
description: Informazioni su come usare il componente di scalabilità automatica per ridimensionare automaticamente il cluster e soddisfare le richieste delle applicazioni in un cluster del servizio Azure Kubernetes (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: 8ce5d2965d0127eec01620c702d7d83bd0b39416
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885769"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Ridimensionare automaticamente un cluster per soddisfare le richieste delle applicazioni nel servizio Azure Kubernetes (AKS)

Per tenere il passo delle richieste delle applicazioni nel servizio Azure Kubernetes (AKS), potrebbe essere necessario regolare il numero di nodi che eseguono i carichi di lavoro. Il componente di scalabilità automatica del cluster può cercare i pod del cluster che non possono essere pianificati a causa di vincoli delle risorse. Quando vengono rilevati problemi, il numero di nodi in un pool di nodi viene aumentato per soddisfare la richiesta dell'applicazione. Inoltre i nodi vengono controllati regolarmente per rilevare la mancanza di pod in esecuzione, con la riduzione del numero di nodi in base alle esigenze. Questa capacità di incrementare o ridurre il numero di nodi nel cluster del servizio Azure Kubernetes permette di gestire il cluster in modo efficace e conveniente.

Questo articolo illustra come abilitare e gestire il componente di scalabilità automatica in un cluster del servizio Azure Kubernetes. 

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede la versione 2.0.76 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono i cluster AKS che usano il ridimensionamento automatico del cluster, si applicano le limitazioni seguenti:

* Non è possibile usare il componente aggiuntivo routing applicazione HTTP.

## <a name="about-the-cluster-autoscaler"></a>Informazioni sul componente di scalabilità automatica

Per adattarsi alle variazioni delle richieste delle applicazioni, ad esempio tra l’orario lavorativo diurno e la sera o il fine settimana, è spesso necessario trovare il modo di adattare automaticamente i cluster. La scalabilità dei cluster del servizio Azure Kubernetes può essere ottenuta in uno dei due modi seguenti:

* Il **componente di scalabilità automatica** cerca i pod che non possono essere pianificati nei nodi a causa di vincoli delle risorse. Il cluster aumenta automaticamente il numero di nodi.
* Il **componente di scalabilità automatica orizzontale dei pod** usa il server delle metriche in un cluster Kubernetes per monitorare la richiesta di risorse dei pod. Se un'applicazione necessita di più risorse, il numero di Pod viene aumentato automaticamente per soddisfare la richiesta.

![Il componente di scalabilità automatica del cluster e il componente di scalabilità automatica orizzontale dei pod spesso interagiscono per soddisfare le esigenze delle applicazioni obbligatorie](media/autoscaler/cluster-autoscaler.png)

Sia il ridimensionamento automatico del Pod orizzontale che il ridimensionamento automatico del cluster possono anche ridurre il numero di Pod e nodi in base alle esigenze. Il componente di scalabilità automatica del cluster riduce il numero di nodi quando una certa quantità di capacità è rimasta inutilizzata per un periodo di tempo. I pod in un nodo che devono essere rimossi dal componente di scalabilità automatica del cluster sono pianificati in modo sicuro in un'altra posizione nel cluster. Il componente di scalabilità automatica del cluster potrebbe non essere in grado di effettuare la riduzione qualora non fosse possibile spostare i pod, ad esempio nelle situazioni seguenti:

* Un pod creato direttamente e non supportato da un oggetto controller, come un set di distribuzioni o di repliche.
* Un budget di interruzione dei pod (PDB) troppo restrittivo e che non permette la riduzione del numero di pod sotto una determinata soglia.
* Un pod che utilizza selettori di nodo o anti-affinità impossibili da rispettare se pianificati in un nodo diverso.

Per altre informazioni su come il ridimensionamento automatico del cluster potrebbe non essere in grado di ridurre, vedere [quali tipi di Pod possono impedire al cluster di scalabilità automatica di rimuovere un nodo?][autoscaler-scaledown]

Il componente di scalabilità automatica del cluster usa i parametri di avvio per elementi come gli intervalli di tempo tra gli eventi di scalabilità e le soglie delle risorse. Questi parametri vengono definiti dalla piattaforma Azure e non sono attualmente esposti per la modifica da parte dell’utente. Per ulteriori informazioni sui parametri utilizzati dal servizio di scalabilità automatica del cluster, vedere informazioni sui [parametri del servizio di scalabilità automatica del cluster][autoscaler-parameters].

Il cluster e il servizio di scalabilità automatica dei Pod orizzontali possono interagire insieme e spesso vengono distribuiti in un cluster. Nell’uso combinato, il componente di scalabilità automatica orizzontale dei pod è dedicato all’esecuzione del numero di pod necessari per soddisfare le richieste delle applicazioni. Il componente di scalabilità automatica del cluster è dedicato all’esecuzione del numero di nodi richiesto per supportare i pod pianificati.

> [!NOTE]
> Il ridimensionamento manuale è disabilitato quando si usa il componente di scalabilità automatica del cluster. È il componente di scalabilità automatica del cluster che determina il numero di nodi richiesto. Se si desidera ridimensionare manualmente il cluster [disabilitare il componente di scalabilità automatica del cluster](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Creare un cluster del servizio Azure Kubernetes e abilitare il componente di scalabilità automatica del cluster

Se è necessario creare un cluster AKS, usare il comando [AZ AKS create][az-aks-create] . Per abilitare e configurare il servizio di scalabilità automatica del cluster nel pool di nodi per il cluster, usare il parametro *--Enable-cluster-AutoScaler* e specificare un nodo *--min-count* e *--Max-count*.

> [!IMPORTANT]
> Il ridimensionamento automatico del cluster è un componente di Kubernetes. Anche se il cluster del servizio Azure Kubernetes usa un set di scalabilità per i nodi di macchine virtuali, non abilitare o modificare manualmente le impostazioni di scalabilità per il ridimensionamento automatico nel portale di Azure o tramite l'interfaccia della riga di comando di Azure. Consentire il ridimensionamento automatico del cluster Kubernetes di gestire le impostazioni di scalabilità necessaria. Per altre informazioni, vedere è [possibile modificare le risorse AKS nel gruppo di risorse del nodo?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

L'esempio seguente crea un cluster AKS con un pool a nodo singolo supportato da un set di scalabilità di macchine virtuali. Abilita anche il ridimensionamento automatico del cluster nel pool di nodi per il cluster e imposta un minimo di *1* e un massimo di *3* nodi:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Sono necessari alcuni minuti per creare il cluster e configurare le impostazioni del componente di scalabilità automatica del cluster.

## <a name="change-the-cluster-autoscaler-settings"></a>Modificare le impostazioni del componente di scalabilità automatica del cluster

> [!IMPORTANT]
> Se nel cluster AKS sono presenti più pool di nodi, passare alla [sezione scalabilità automatica con più pool di agenti](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled). I cluster con più pool di agenti richiedono l'uso del `az aks nodepool` set di comandi per modificare le proprietà specifiche del pool di nodi anziché `az aks`.

Nel passaggio precedente per creare un cluster AKS o aggiornare un pool di nodi esistente, il numero minimo di nodi del cluster AutoScaler è stato impostato su *1*e il numero massimo di nodi è stato impostato su *3*. Se le richieste delle applicazioni cambiano, potrebbe essere necessario regolare il numero di nodi del componente di scalabilità automatica del cluster.

Per modificare il numero di nodi, usare il comando [AZ AKS Update][az-aks-update] .

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

L'esempio precedente aggiorna la scalabilità automatica del cluster nel pool a nodo singolo in *myAKSCluster* con un minimo di *1* e un massimo di *5* nodi.

> [!NOTE]
> Non è possibile impostare un numero di nodi minimo superiore a quello attualmente impostato per il pool di nodi. Ad esempio, se il numero minimo è attualmente impostato su *1*, non è possibile aggiornarlo a *3*.

Monitorare le prestazioni delle applicazioni e dei servizi e modificare il numero di nodi del componente di scalabilità automatica del cluster per ottenere le prestazioni necessarie.

## <a name="disable-the-cluster-autoscaler"></a>Disabilitare il componente di scalabilità automatica del cluster

Se non si vuole più usare il servizio di scalabilità automatica del cluster, è possibile disabilitarlo usando il comando [AZ AKS Update][az-aks-update] , specificando il parametro *--Disable-cluster-AutoScaler* . I nodi non vengono rimossi quando il componente di scalabilità automatica del cluster è disabilitato.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

È possibile ridimensionare manualmente il cluster dopo aver disabilitato il ridimensionamento automatico del cluster usando il comando [AZ AKS scale][az-aks-scale] . Se si usa il servizio di scalabilità automatica di Pod orizzontali, la funzionalità continua a essere eseguita con il ridimensionamento automatico del cluster disabilitato, ma i pod potrebbero non essere pianificati se sono in uso tutte le risorse del nodo.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Abilitare di nuovo il ridimensionamento automatico di un cluster disabilitato

Se si vuole abilitare di nuovo il ridimensionamento automatico del cluster in un cluster esistente, è possibile riabilitarlo usando il comando [AZ AKS Update][az-aks-update] , specificando i parametri-- *Enable-cluster-AutoScaler*, *--min-count*e *--Max-count* .

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Recuperare i log e lo stato di scalabilità automatica del cluster

Per eseguire la diagnosi e il debug degli eventi di scalabilità automatica, è possibile recuperare i log e lo stato dal componente aggiuntivo di scalabilità automatica.

AKS gestisce il ridimensionamento automatico del cluster per conto dell'utente e lo esegue nel piano di controllo gestito. I log dei nodi master devono essere configurati per essere visualizzati come risultato.

Per configurare i log da inserire dal ridimensionatore automatico del cluster in Log Analytics seguire questa procedura.

1. Configurare una regola per i log di diagnostica per eseguire il push dei log del cluster di scalabilità automatica in Log Analytics. Per [istruzioni dettagliate](https://docs.microsoft.com/azure/aks/view-master-logs#enable-diagnostics-logs), assicurarsi di selezionare la casella per `cluster-autoscaler` quando si selezionano le opzioni per "log".
1. Fare clic sulla sezione "log" del cluster tramite il portale di Azure.
1. Immettere la query di esempio seguente in Log Analytics:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Verranno visualizzati log simili ai seguenti restituiti, purché siano presenti log da recuperare.

![Log di Log Analytics](media/autoscaler/autoscaler-logs.png)

Il servizio di scalabilità automatica del cluster scriverà anche lo stato di integrità in un configMap denominato `cluster-autoscaler-status`. Per recuperare questi log, eseguire il comando di `kubectl` seguente. Viene segnalato uno stato di integrità per ogni pool di nodi configurato con il ridimensionatore automatico del cluster.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Per altre informazioni sugli elementi registrati da scalabilità automatica, vedere le domande frequenti sul [progetto Kubernetes/AutoScaler GitHub](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why).

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Usare il ridimensionamento automatico del cluster con più pool di nodi abilitati

Il servizio di scalabilità automatica del cluster può essere usato insieme a [più pool di nodi](use-multiple-node-pools.md) abilitati. Seguire questo documento per informazioni su come abilitare più pool di nodi e aggiungere altri pool di nodi a un cluster esistente. Quando si usano entrambe le funzionalità insieme, si Abilita il ridimensionamento automatico del cluster in ogni singolo pool di nodi del cluster e si possono passare regole di scalabilità automatica univoche a ciascuna.

Il comando seguente presuppone che siano state seguite le [istruzioni iniziali](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) riportate in precedenza in questo documento e si desideri aggiornare un numero massimo di pool di nodi esistente da *3* a *5*. Usare il comando [AZ AKS nodepool Update][az-aks-nodepool-update] per aggiornare le impostazioni di un pool di nodi esistente.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Il servizio di scalabilità automatica del cluster può essere disabilitato con [AZ AKS nodepool Update][az-aks-nodepool-update] e passando il parametro `--disable-cluster-autoscaler`.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Se si vuole abilitare di nuovo il ridimensionamento automatico del cluster in un cluster esistente, è possibile riabilitarlo usando il comando [AZ AKS nodepool Update][az-aks-nodepool-update] , specificando i parametri-- *Enable-cluster-AutoScaler*, *--min-count*e *--Max-count* .

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha descritto come ridimensionare automaticamente il numero di nodi del servizio Azure Kubernetes. È anche possibile usare il componente di scalabilità automatica orizzontale dei pod per regolare automaticamente il numero di pod che eseguono l'applicazione. Per i passaggi relativi all'uso del ridimensionamento automatico del Pod orizzontale, vedere [ridimensionare le applicazioni in AKS][aks-scale-apps].

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
