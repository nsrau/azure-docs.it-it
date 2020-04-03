---
title: Usare pool di più nodi nel servizio Azure Kubernetes (AKS)Use multiple node pools in Azure Kubernetes Service (AKS)
description: Informazioni su come creare e gestire pool di più nodi per un cluster nel servizio Azure Kubernetes (AKS)Learn how to create and manage multiple node pools for a cluster in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 87f066ed17e5274439082956803d269bdd5853f5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616510"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Creare e gestire pool di più nodi per un cluster nel servizio Azure Kubernetes (AKS)Create and manage multiple node pools for a cluster in Azure Kubernetes Service (AKS)

Nel servizio Azure Kubernetes (AKS), i nodi della stessa configurazione vengono raggruppati in pool di *nodi.* Questi pool di nodi contengono le macchine virtuali sottostanti che eseguono le applicazioni. Il numero iniziale di nodi e le relative dimensioni (SKU) viene definito quando si crea un cluster AKS, che crea un *pool di nodi predefinito.* Per supportare applicazioni con esigenze di archiviazione o di calcolo diverse, è possibile creare pool di nodi aggiuntivi. Ad esempio, usare questi pool di nodi aggiuntivi per fornire GPU per applicazioni a elevato utilizzo di elaborazione o l'accesso a storage SSD ad alte prestazioni.

> [!NOTE]
> Questa funzionalità consente un maggiore controllo su come creare e gestire pool di più nodi. Di conseguenza, sono necessari comandi separati per la creazione/aggiornamento/eliminazione. In precedenza `az aks create` `az aks update` le operazioni del cluster tramite o utilizzavano l'API managedCluster e erano l'unica opzione per modificare il piano di controllo e un pool di nodi a nodo singolo. Questa funzionalità espone un set di operazioni separato per i pool `az aks nodepool` di agenti tramite l'API agentPool e richiede l'utilizzo del set di comandi per eseguire operazioni in un pool di singoli nodi.

In questo articolo viene illustrato come creare e gestire più pool di nodi in un cluster AKS.

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure versione 2.2.0 o successiva installata e configurata. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][install-azure-cli]you need to install or upgrade, see Install Azure CLI.

## <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono cluster AKS che supportano pool di più nodi, si applicano le limitazioni seguenti:The following limitations apply when you create and manage AKS clusters that support multiple node pools:

* Vedere Quote, restrizioni sulle dimensioni delle [macchine virtuali e disponibilità dell'area nel servizio Azure Kubernetes (AKS).][quotas-skus-regions]
* Non è possibile eliminare il pool di nodi di sistema, per impostazione predefinita il primo pool di nodi.
* Il cluster AKS deve usare il servizio di bilanciamento del carico SKU Standard per usare pool di più nodi, la funzionalità non è supportata con i servizi di bilanciamento del carico SKU di base.
* Il cluster AKS deve usare set di scalabilità di macchine virtuali per i nodi.
* Il nome di un pool di nodi può contenere solo caratteri alfanumerici minuscoli e deve iniziare con una lettera minuscola. Per i pool di nodi Linux la lunghezza deve essere compresa tra 1 e 12 caratteri, per i pool di nodi di Windows la lunghezza deve essere compresa tra 1 e 6 caratteri.
* Tutti i pool di nodi devono risiedere nella stessa rete virtuale.
* Quando si creano pool di più nodi in fase di creazione del cluster, tutte le versioni di Kubernetes utilizzate dai pool di nodi devono corrispondere alla versione impostata per il piano di controllo. Questo può essere aggiornato dopo il provisioning del cluster utilizzando le operazioni per pool di nodi.

## <a name="create-an-aks-cluster"></a>Creare un cluster AKS

Per iniziare, creare un cluster AKS con un pool a nodo singolo. L'esempio seguente usa il comando az group create per creare un gruppo di risorse denominato myResourceGroup nell'area eastus.The following example uses the [az group create][az-group-create] command to create a resource group named *myResourceGroup* in the *eastus* region. Viene quindi creato un cluster AKS denominato *myAKSCluster* usando il comando [az aks create.][az-aks-create] Una *versione --kubernetes* di *1.15.7* viene utilizzata per mostrare come aggiornare un pool di nodi in un passaggio successivo. È possibile specificare qualsiasi [versione Kubernetes supportata.][supported-versions]

> [!NOTE]
> Lo SKU del servizio di bilanciamento del carico *di base* non è **supportato** quando si usano più pool di nodi. Per impostazione predefinita, i cluster AKS vengono creati con lo SKU del servizio di bilanciamento del carico Standard dalla CLI di Azure e dal portale di Azure.By default, AKS clusters are created with the *Standard* load balancer SKU from the Azure CLI and Azure portal.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --kubernetes-version 1.15.7 \
    --load-balancer-sku standard
```

La creazione del cluster richiede alcuni minuti.

> [!NOTE]
> Per garantire che il cluster funzioni in modo affidabile, è consigliabile eseguire almeno 2 (due) nodi nel pool di nodi predefinito, poiché i servizi di sistema essenziali sono in esecuzione in questo pool di nodi.

Quando il cluster è pronto, usare il comando [az aks get-credentials][az-aks-get-credentials] per ottenere le credenziali del cluster per l'utilizzo con `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Aggiungere un pool di nodiAdd a node pool

Il cluster creato nel passaggio precedente dispone di un pool a nodo singolo. Aggiungiamo un secondo pool di nodi usando il comando [az aks nodepool add.][az-aks-nodepool-add] Nell'esempio seguente viene creato un pool di nodi denominato mynodepool che esegue 3 nodi:The following example creates a node pool named *mynodepool* that runs *3* nodes:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
```

> [!NOTE]
> Il nome di un pool di nodi deve iniziare con una lettera minuscola e può contenere solo caratteri alfanumerici. Per i pool di nodi Linux la lunghezza deve essere compresa tra 1 e 12 caratteri, per i pool di nodi di Windows la lunghezza deve essere compresa tra 1 e 6 caratteri.

Per visualizzare lo stato dei pool di nodi, usare il comando [az aks node pool list][az-aks-nodepool-list] e specificare il gruppo di risorse e il nome del cluster:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

L'output di esempio seguente mostra che *mynodepool* è stato creato correttamente con tre nodi nel pool di nodi. Quando il cluster AKS è stato creato nel passaggio precedente, è stato creato un *nodepool1* predefinito con un numero di nodi pari a *2*.

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.5",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Se non viene specificato alcun VmSize quando si aggiunge un pool di nodi, la dimensione predefinita è Standard_DS2_v3 per i pool di nodi di Windows e Standard_DS2_v2 per i pool di nodi Linux.If no *VmSize* is specified when you add a node pool, the default size is *Standard_DS2_v3* for Windows node pools and *Standard_DS2_v2* for Linux node pools. Se non viene specificato alcun *OrchestratorVersion,* per impostazione predefinita viene la stessa versione del piano di controllo.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Aggiungere un pool di nodi con una subnet univoca (anteprima)Add a node pool with a unique subnet (preview)

Un carico di lavoro può richiedere la suddivisione dei nodi di un cluster in pool separati per l'isolamento logico. Questo isolamento può essere supportato con subnet separate dedicate a ogni pool di nodi nel cluster. Questo può soddisfare requisiti come avere uno spazio di indirizzi di rete virtuale non contiguo per dividere tra i pool di nodi.

#### <a name="limitations"></a>Limitazioni

* Tutte le subnet assegnate anodepools devono appartenere alla stessa rete virtuale.
* I pod di sistema devono avere accesso a tutti i nodi del cluster per fornire funzionalità critiche, ad esempio la risoluzione DNS tramite coreDNS.
* L'assegnazione di una subnet univoca per pool di nodi è limitata a CNI di Azure durante l'anteprima.
* L'utilizzo di criteri di rete con una subnet univoca per pool di nodi non è supportato durante l'anteprima.

Per creare un pool di nodi con una subnet dedicata, passare l'ID della risorsa subnet come parametro aggiuntivo durante la creazione di un pool di nodi.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Aggiornare un pool di nodiUpgrade a node pool

> [!NOTE]
> Le operazioni di aggiornamento e scalabilità in un cluster o in un pool di nodi non possono essere eseguite contemporaneamente, se viene restituito un tentativo di errore. Al contrario, ogni tipo di operazione deve essere completato nella risorsa di destinazione prima della richiesta successiva sulla stessa risorsa. Per saperne di più su questo nella nostra [guida alla risoluzione dei problemi](https://aka.ms/aks-pending-upgrade).

Quando il cluster AKS è stato inizialmente `--kubernetes-version` creato nel primo passaggio, è stato specificato un di *1.15.7.* In questo modo viene impostata la versione di Kubernetes sia per il piano di controllo che per il pool di nodi predefinito. I comandi in questa sezione illustrano come aggiornare un singolo pool di nodi specifico.

La relazione tra l'aggiornamento della versione Kubernetes del piano di controllo e il pool di nodi è illustrata nella [sezione seguente.](#upgrade-a-cluster-control-plane-with-multiple-node-pools)

> [!NOTE]
> La versione dell'immagine del sistema operativo del pool di nodi è legata alla versione Kubernetes del cluster. Si otterrà solo gli aggiornamenti delle immagini del sistema operativo, dopo un aggiornamento del cluster.

Poiché in questo esempio sono presenti due pool di nodi, è necessario usare [az aks nodepool upgrade][az-aks-nodepool-upgrade] per aggiornare un pool di nodi. Aggiorniamo *mynodepool* a Kubernetes *1.15.7*. Utilizzare il comando az aks nodepool upgrade per aggiornare il pool di nodi, come illustrato nell'esempio seguente:Use the [az aks nodepool upgrade][az-aks-nodepool-upgrade] command to upgrade the node pool, as shown in the following example:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

Elencare nuovamente lo stato dei pool di nodi utilizzando il comando [az aks node pool list.][az-aks-nodepool-list] L'esempio seguente mostra che *mynodepool* è nello stato *Upgrading* a *1.15.7*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

L'aggiornamento dei nodi alla versione specificata richiede alcuni minuti.

È consigliabile aggiornare tutti i pool di nodi in un cluster AKS alla stessa versione Kubernetes. Il comportamento `az aks upgrade` predefinito di consiste nell'aggiornare tutti i pool di nodi insieme al piano di controllo per ottenere questo allineamento. La possibilità di aggiornare pool di singoli nodi consente di eseguire un aggiornamento in sequenza e pianificare i pod tra i pool di nodi per mantenere il tempo di attività dell'applicazione entro i vincoli indicati sopra.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Aggiornare un piano di controllo del cluster con pool di più nodiUpgrade a cluster control plane with multiple node pools

> [!NOTE]
> Kubernetes utilizza lo schema di controllo delle versioni [delle versioni semantico](https://semver.org/) standard. Il numero di versione è espresso come *x.y.z*, dove *x* è la versione principale, *y* è la versione secondaria e *z* è la versione della patch. Ad esempio, nella versione *1.12.6*, 1 è la versione principale, 12 è la versione secondaria e 6 è la versione di patch. La versione Kubernetes del piano di controllo e del pool di nodi iniziale vengono impostati durante la creazione del cluster. Tutti i pool di nodi aggiuntivi hanno la versione Kubernetes impostata quando vengono aggiunti al cluster. Le versioni Kubernetes possono differire tra i pool di nodi e tra un pool di nodi e il piano di controllo.

Un cluster AKS dispone di due oggetti risorsa cluster con le versioni Kubernetes associate.

1. Una versione Kubernetes del piano di controllo del cluster.
2. Un pool di nodi con una versione Kubernetes.

Un piano di controllo esegue il mapping a uno o più pool di nodi. Il comportamento di un'operazione di aggiornamento dipende dal comando dell'interfaccia della riga di comando di Azure.The behavior of an upgrade operation depends on which Azure CLI command is used.

L'aggiornamento di un `az aks upgrade`piano di controllo AKS richiede l'utilizzo di . Questo comando aggiorna la versione del piano di controllo e tutti i pool di nodi nel cluster.

L'emissione `az aks upgrade` del `--control-plane-only` comando con il flag aggiorna solo il piano di controllo del cluster. Nessuno dei pool di nodi associati nel cluster viene modificato.

L'aggiornamento di `az aks nodepool upgrade`pool di singoli nodi richiede l'utilizzo di . Questo comando aggiorna solo il pool di nodi di destinazione con la versione Kubernetes specificata

### <a name="validation-rules-for-upgrades"></a>Regole di convalida per gli aggiornamenti

Gli aggiornamenti validi di Kubernetes per il piano di controllo e i pool di nodi di un cluster vengono convalidati dai set di regole seguenti.

* Regole per le versioni valide per l'aggiornamento dei pool di nodi:Rules for valid versions to upgrade node pools:
   * La versione del pool di nodi deve avere la stessa versione *principale* del piano di controllo.
   * La versione *secondaria* del pool di nodi deve essere compresa in due versioni *secondarie* della versione piano di controllo.
   * La versione del pool di `major.minor.patch` nodi non può essere maggiore della versione del controllo.

* Regole per l'invio di un'operazione di aggiornamento:Rules for submitting an upgrade operation:
   * Non è possibile eseguire il downgrade del piano di controllo o di una versione Kubernetes del pool di nodi.
   * Se non viene specificata una versione di Kubernetes del pool di nodi, il comportamento dipende dal client in uso. Dichiarazione nei modelli di Resource Manager esegue il fallback alla versione esistente definita per il pool di nodi, se utilizzato, se non è impostata la versione del piano di controllo viene utilizzata per eseguire il fallback.
   * È possibile aggiornare o ridimensionare un piano di controllo o un pool di nodi in un determinato momento, non è possibile inviare più operazioni su un singolo piano di controllo o una risorsa del pool di nodi contemporaneamente.

## <a name="scale-a-node-pool-manually"></a>Ridimensionare manualmente un pool di nodiScale a node pool manually

Man mano che le richieste del carico di lavoro dell'applicazione cambiano, potrebbe essere necessario ridimensionare il numero di nodi in un pool di nodi. Il numero di nodi può essere scalato verso l'alto o verso il basso.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Per ridimensionare il numero di nodi in un pool di nodi, usare il comando [az aks node scale scale.][az-aks-nodepool-scale] L'esempio seguente ridimensiona il numero di nodi in *mynodepool* a *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Elencare nuovamente lo stato dei pool di nodi utilizzando il comando [az aks node pool list.][az-aks-nodepool-list] L'esempio seguente mostra che mynodepool è nello stato Scaling con un nuovo conteggio di 5 nodi:The following example shows that *mynodepool* is in the *Scaling* state with a new count of *5* nodes:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Il completamento dell'operazione di ridimensionamento richiede alcuni minuti.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Ridimensionare automaticamente un pool di nodi specifico abilitando il cluster autoscaler

AKS offre una funzionalità separata per ridimensionare automaticamente i pool di nodi con una funzionalità denominata [cluster autoscaler](cluster-autoscaler.md). Questa funzionalità può essere abilitata per ogni pool di nodi con conteggi di scalabilità minimo e massimo univoci per pool di nodi. Informazioni su come [usare la scalabilità automatica del cluster per ogni pool di nodi.](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)

## <a name="delete-a-node-pool"></a>Eliminare un pool di nodiDelete a node pool

Se un pool non è più necessario, è possibile eliminarlo e rimuovere i nodi VM sottostanti. Per eliminare un pool di nodi, utilizzare il comando [az aks node pool delete][az-aks-nodepool-delete] e specificare il nome del pool di nodi. L'esempio seguente elimina il *mynoodepool* creato nei passaggi precedenti:

> [!CAUTION]
> Non sono disponibili opzioni di ripristino per la perdita di dati che possono verificarsi quando si elimina un pool di nodi. Se i pod non possono essere pianificati in altri pool di nodi, tali applicazioni non sono disponibili. Assicurarsi di non eliminare un pool di nodi quando le applicazioni in uso non dispongono di backup dei dati o la possibilità di eseguire in altri pool di nodi nel cluster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

L'output di esempio seguente dal comando [az aks node pool list][az-aks-nodepool-list] mostra che *mynodepool* è nello stato *Deleting:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

L'eliminazione dei nodi e del pool di nodi richiede alcuni minuti.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Specificare una dimensione della macchina virtuale per un pool di nodiSpecify a VM size for a node pool

Negli esempi precedenti per creare un pool di nodi, è stata usata una dimensione di macchina virtuale predefinita per i nodi creati nel cluster. Uno scenario più comune prevede la creazione di pool di nodi con dimensioni e funzionalità di MACCHINE virtuali diverse. Ad esempio, è possibile creare un pool di nodi che contiene nodi con grandi quantità di CPU o memoria o un pool di nodi che fornisce il supporto GPU. Nel passaggio successivo, si [utilizzano taints e tolarazioni](#schedule-pods-using-taints-and-tolerations) per indicare all'utilità di pianificazione Kubernetes come limitare l'accesso ai pod che possono essere eseguiti su questi nodi.

Nell'esempio seguente creare un pool di nodi basato su GPU che usa la *Standard_NC6* dimensioni della macchina virtuale. Queste macchine virtuali sono alimentate dalla scheda NVIDIA Tesla K80. Per informazioni sulle dimensioni delle macchine virtuali disponibili, vedere Dimensioni per le macchine virtuali Linux in Azure.For information on available VM sizes, see [Sizes for Linux virtual machines in Azure.][vm-sizes]

Creare nuovamente un pool di nodi utilizzando il comando [az aks node pool add.][az-aks-nodepool-add] In questa volta, specificare il nome `--node-vm-size` *gpunodepool*e utilizzare il parametro per specificare la dimensione del *Standard_NC6:*

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

L'output di esempio seguente dal comando [az aks node pool list][az-aks-nodepool-list] mostra che *gpunodepool* *crea* nodi con il *VmSize*specificato:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

La creazione del *gpunodepool* richiede alcuni minuti.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Pianificare i baccelli con taint e tolarazioni

Nel cluster sono ora disponibili due pool di nodi: il pool di nodi predefinito inizialmente creato e il pool di nodi basato su GPU. Usare il comando [kubectl get nodes][kubectl-get] per visualizzare i nodi nel cluster. L'output di esempio seguente mostra i nodi:The following example output shows the nodes:

```console
kubectl get nodes
```

```output
NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

L'utilità di pianificazione di Kubernetes può usare taint e tolleranze per limitare i carichi di lavoro che possono essere eseguiti sui nodi.

* Un **taint** viene applicato a un nodo per indicare che possono essere pianificati solo pod specifici.
* Una **tolleranza** viene quindi applicata a un pod per *tollerare* un taint di un nodo.

Per ulteriori informazioni su come utilizzare le funzionalità pianificate avanzate Kubernetes, vedere [Procedure consigliate per le funzionalità avanzate dell'utilità di pianificazione in AKSFor][taints-tolerations] more information on how to use advanced Kubernetes scheduled features, see Best practices for advanced scheduler features in AKS

In questo esempio, applicare una macchia al nodo basato su GPU utilizzando il comando --node-taints. Specificare il nome del nodo basato su `kubectl get nodes` GPU dall'output del comando precedente. La macchia viene applicata come coppia *chiave-valore* e quindi come opzione di pianificazione. L'esempio seguente usa la coppia *sku-gpu* e definisce i pod in caso contrario l'abilità *NoSchedule:*

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

L'esempio di base seguente il manifesto YAML usa una torazione per consentire all'utilità di pianificazione Kubernetes di eseguire un pod NGINX nel nodo basato su GPU. Per un esempio più appropriato, ma dispendioso in termini di tempo per eseguire un processo Tensorflow sul set di dati MNIST, vedere [Usare GPU per carichi di lavoro ad alta intensità di calcolo in AKS][gpu-cluster].

Creare un file denominato `gpu-toleration.yaml` e copiarlo nell'esempio YAML seguente:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Pianificate il `kubectl apply -f gpu-toleration.yaml` contenitore con il comando:

```console
kubectl apply -f gpu-toleration.yaml
```

Ci vogliono alcuni secondi per pianificare il pod e tirare l'immagine NGINX. Utilizzate il comando [pod kubectl describe][kubectl-describe] per visualizzare lo stato del contenitore. Il seguente output di esempio condensato mostra la torazione *sku-gpu:NoSchedule* applicata. Nella sezione eventi, l'utilità di pianificazione ha assegnato il pod al nodo basato su GPU *aks-gpunodepool-28993262-vms000000:*

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

Solo i pod a cui è applicata questa torazione possono essere pianificati su nodi in *gpunodepool*. Qualsiasi altro pod verrà pianificato nel pool di nodi *nodepool1.* Se si creano pool di nodi aggiuntivi, è possibile utilizzare ulteriori taint e tolarazioni per limitare quali pod possono essere pianificati in tali risorse del nodo.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Specificare una macchia, un'etichetta o un'etichetta per un pool di nodiSpecify a taint, label, or tag for a node pool

Quando si crea un pool di nodi, è possibile aggiungere taint, etichette o tag a tale pool di nodi. Quando si aggiunge una macchia, un'etichetta o un'etichetta, anche tutti i nodi all'interno del pool di nodi ottengono tale fascia, etichetta o etichetta.

Per creare un pool di nodi con una macchia, utilizzare [az aks nodepool add][az-aks-nodepool-add]. Specificare il nome *taintnp* e utilizzare il `--node-taints` parametro per specificare *sku-gpu:NoSchedule* per la macchia.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

L'output di esempio seguente dal comando [az aks nodepoollist][az-aks-nodepool-list] mostra che *taintnp* *crea* nodi con i *nodi*specificati:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  {
      "sku": "gpu:NoSchedule"
    },
    ...
  },
 ...
]
```

Le informazioni contenute sono visibili in Kubernetes per la gestione delle regole di pianificazione per i nodi.

È inoltre possibile aggiungere etichette a un pool di nodi durante la creazione del pool di nodi. Le etichette impostate nel pool di nodi vengono aggiunte a ogni nodo del pool di nodi. Queste [etichette sono visibili in Kubernetes][kubernetes-labels] per la gestione delle regole di pianificazione per i nodi.

Per creare un pool di nodi con un'etichetta, utilizzare [az aks nodepool add][az-aks-nodepool-add]. Specificare il nome *labelnp* e utilizzare il `--labels` parametro per specificare *dept, IT* e *costcenter, 9999* per le etichette.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> L'etichetta può essere impostata solo per i pool di nodi durante la creazione del pool di nodi. Le etichette devono inoltre essere una coppia chiave/valore e avere una [sintassi valida.][kubernetes-label-syntax]

L'output di esempio seguente del comando [az aks nodepoollist][az-aks-nodepool-list] mostra che *labelnp* sta *creando* nodi con il *nodoEtichette*specificato:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

È possibile applicare un tag di Azure ai pool di nodi nel cluster AKS. I tag applicati a un pool di nodi vengono applicati a ogni nodo all'interno del pool di nodi e vengono mantenuti tramite aggiornamenti. I tag vengono applicati anche ai nuovi nodi aggiunti a un pool di nodi durante le operazioni di scalabilità orizzontale. L'aggiunta di un tag può essere utile per attività quali il rilevamento dei criteri o la stima dei costi.

Creare un pool di nodi utilizzando l'aggiunta di [nodepool az aks][az-aks-nodepool-add]. Specificare il nome *tagnodepool* e utilizzare il `--tag` parametro per specificare *dept, IT* e *costcenter, 9999* per i tag.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> È inoltre possibile `--tags` utilizzare il parametro quando si utilizza il comando [az aks nodepool update][az-aks-nodepool-update] e durante la creazione del cluster. Durante la creazione `--tags` del cluster, il parametro applica il tag al pool di nodi iniziale creato con il cluster. Tutti i nomi dei tag devono rispettare le limitazioni in Usare i tag per organizzare le risorse di [Azure.][tag-limitation] L'aggiornamento di `--tags` un pool di nodi con il parametro aggiorna tutti i valori di tag esistenti e aggiunge eventuali nuovi tag. Se, ad esempio, il pool di nodi dispone di *dept, IT* e *costcenter, 9999* per i tag e di averlo aggiornato con i tag *team,dev* e *costcenter,111,* il nodo è *stato deto-IT,* *costcenter'111*e *team-dev* per i tag.

L'output di esempio seguente dal comando [az aks nodepoollist][az-aks-nodepool-list] mostra che *tagnodepool* sta *creando* nodi con il *tag*specificato :

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Gestire i pool di nodi usando un modello di Resource ManagerManage node pools using a Resource Manager template

Quando si usa un modello di Azure Resource Manager per creare e gestire le risorse, è in genere possibile aggiornare le impostazioni nel modello e ridistribuire per aggiornare la risorsa. Con i pool di nodi in AKS, il profilo del pool di nodi iniziale non può essere aggiornato dopo la creazione del cluster AKS. Questo comportamento significa che non è possibile aggiornare un modello di Resource Manager esistente, apportare una modifica ai pool di nodi e ridistribuire. È invece necessario creare un modello di Resource Manager separato che aggiorni solo i pool di nodi per un cluster AKS esistente.

Creare un modello, ad `aks-agentpools.json` esempio e incollare il manifesto di esempio seguente. Questo modello di esempio configura le impostazioni seguenti:This example template configures the following settings:

* Aggiorna il pool di nodi *Linux* denominato *myagentpool* per eseguire tre nodi.
* Imposta i nodi nel pool di nodi per eseguire Kubernetes versione *1.15.7*.
* Definisce la dimensione del nodo come *Standard_DS2_v2*.

Modificare questi valori in base alle esigenze per aggiornare, aggiungere o eliminare pool di nodi in base alle esigenze:Edit these values as need to update, add, or delete node pools as needed:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

Distribuire questo modello usando il comando [az group deployment create,][az-group-deployment-create] come illustrato nell'esempio seguente. Viene richiesto il nome e il percorso del cluster AKS esistente:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> È possibile aggiungere un tag al pool di nodi aggiungendo la proprietà *tag* nel modello, come illustrato nell'esempio seguente.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

L'aggiornamento del cluster AKS potrebbe richiedere alcuni minuti a seconda delle impostazioni e delle operazioni del pool di nodi definite nel modello di Resource Manager.

## <a name="assign-a-public-ip-per-node-for-a-node-pool-preview"></a>Assegnare un indirizzo IP pubblico per nodo per un pool di nodi (anteprima)Assign a public IP per node for a node pool (preview)

> [!WARNING]
> Durante l'anteprima dell'assegnazione di un IP pubblico per nodo, non può essere usato con lo SKU di *Load Balancer Standard in AKS* a causa di possibili regole di bilanciamento del carico in conflitto con il provisioning delle macchine virtuali. Come risultato di questa limitazione, i pool di agenti di Windows non sono supportati con questa funzionalità di anteprima. Mentre nell'anteprima è necessario usare lo *SKU* di Basic Load Balancer se è necessario assegnare un indirizzo IP pubblico per nodo.

I nodi AKS non richiedono i propri indirizzi IP pubblici per la comunicazione. Tuttavia, gli scenari possono richiedere che i nodi in un pool di nodi ricevano i propri indirizzi IP pubblici dedicati. Uno scenario comune è per i carichi di lavoro di gioco, in cui una console deve effettuare una connessione diretta a una macchina virtuale cloud per ridurre al minimo gli hop. Questo scenario può essere ottenuto su AKS registrandosi per una funzionalità di anteprima, Node Public IP (anteprima).

Eseguire la registrazione per la funzionalità IP pubblico del nodo eseguendo il comando dell'interfaccia della riga di comando di Azure seguente.

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

Dopo la registrazione, distribuire un modello di Azure Resource Manager `enableNodePublicIP` seguendo le stesse istruzioni precedenti e aggiungere la proprietà booleana a agentPoolProfiles.After successful registration, deploy an Azure Resource Manager template following the same instructions as [above](#manage-node-pools-using-a-resource-manager-template) and add the boolean property to agentPoolProfiles. Impostare il `true` valore su come `false` predefinito è impostato come se non specificato. 

Questa proprietà è una proprietà solo in fase di creazione e richiede una versione API minima di 2019-06-01.This property is a create-time only property and requires a minimum API version of 2019-06-01. Questo può essere applicato a entrambi i pool di nodi Linux e Windows.This can be applied to both Linux and Windows node pools.

## <a name="clean-up-resources"></a>Pulire le risorse

In questo articolo è stato creato un cluster AKS che include nodi basati su GPU. Per ridurre i costi non necessari, è possibile eliminare il *gpunodepool*o l'intero cluster AKS.

Per eliminare il pool di nodi basato su GPU, utilizzare il comando [az aks nodepool delete][az-aks-nodepool-delete] come illustrato nell'esempio seguente:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Per eliminare il cluster stesso, usare il comando az group delete per eliminare il gruppo di risorse AKS:To delete the cluster itself, use the [az group delete][az-group-delete] command to delete the AKS resource group:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come creare e gestire più pool di nodi in un cluster AKS. Per ulteriori informazioni su come controllare i pod tra i pool di nodi, consultate [Procedure consigliate per le funzionalità avanzate dell'utilità di pianificazione in AKS.][operator-best-practices-advanced-scheduler]

Per creare e utilizzare pool di nodi contenitore di Windows Server, vedere [Creare un contenitore di Windows Server in AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md