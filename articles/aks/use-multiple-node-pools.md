---
title: Usare più pool di nodi in Azure Kubernetes Service (AKS)
description: Informazioni su come creare e gestire pool di nodi multipli per un cluster in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: 400e595d51f08428b01337e63f6c6e8ba5836794
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133096"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Creare e gestire più pool di nodi per un cluster nel servizio Azure Kubernetes (AKS)

In Azure Kubernetes Service (AKS) i nodi della stessa configurazione sono raggruppati in pool di *nodi*. Questi pool di nodi contengono le macchine virtuali sottostanti che eseguono le applicazioni. Il numero iniziale di nodi e la relativa dimensione (SKU) viene definito quando si crea un cluster AKS, che crea un [pool di nodi di sistema][use-system-pool]. Per supportare applicazioni con esigenze di calcolo o di archiviazione diverse, è possibile creare *pool di nodi utente*aggiuntivi. I pool di nodi di sistema servono lo scopo principale di ospitare i pod di sistema critici, ad esempio CoreDNS e tunnelfront. I pool di nodi utente servono lo scopo principale di ospitare i pod dell'applicazione. Tuttavia, i pod dell'applicazione possono essere pianificati nei pool di nodi di sistema se si vuole avere un solo pool nel cluster AKS. I pool di nodi utente sono i punti in cui inserire i pod specifici dell'applicazione. Usare, ad esempio, questi pool di nodi utente aggiuntivi per fornire GPU per applicazioni a elevato utilizzo di calcolo o per l'accesso a un'archiviazione SSD a prestazioni elevate.

> [!NOTE]
> Questa funzionalità consente un maggiore controllo sulla creazione e la gestione di più pool di nodi. Di conseguenza, per create/Update/Delete sono necessari comandi distinti. In precedenza le operazioni `az aks create` del cluster tramite o `az aks update` USAVANO l'API managedCluster ed erano l'unica opzione per modificare il piano di controllo e un pool a nodo singolo. Questa funzionalità espone un set di operazioni separato per i pool di agenti tramite l'API agentPool e richiede l'uso del `az aks nodepool` set di comandi per eseguire operazioni in un singolo pool di nodi.

Questo articolo illustra come creare e gestire più pool di nodi in un cluster AKS.

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure versione 2.2.0 o successiva installata e configurata. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono cluster del servizio Azure Kubernetes che supportano pool di nodi multipli, si applicano le limitazioni seguenti:

* Vedere [quote, restrizioni sulle dimensioni delle macchine virtuali e disponibilità di aree in Azure Kubernetes Service (AKS)][quotas-skus-regions].
* È possibile eliminare i pool di nodi di sistema, purché si disponga di un altro pool di nodi di sistema per sostituirlo nel cluster AKS.
* I pool di sistema devono contenere almeno un nodo e i pool di nodi utente possono contenere zero o più nodi.
* Il cluster AKS deve usare il servizio di bilanciamento del carico SKU standard per usare più pool di nodi. la funzionalità non è supportata con i bilanciamenti del carico SKU Basic.
* Il cluster AKS deve usare i set di scalabilità di macchine virtuali per i nodi.
* Il nome di un pool di nodi può contenere solo caratteri alfanumerici minuscoli e deve iniziare con una lettera minuscola. Per i pool di nodi Linux la lunghezza deve essere compresa tra 1 e 12 caratteri, per i pool di nodi Windows la lunghezza deve essere compresa tra 1 e 6 caratteri.
* Tutti i pool di nodi devono trovarsi nella stessa rete virtuale.
* Quando si creano più pool di nodi in fase di creazione del cluster, tutte le versioni di Kubernetes utilizzate dai pool di nodi devono corrispondere al set di versioni per il piano di controllo. Questa operazione può essere aggiornata dopo il provisioning del cluster tramite operazioni del pool per nodo.

## <a name="create-an-aks-cluster"></a>Creare un cluster AKS

> [!Important]
> Se si esegue un singolo pool di nodi di sistema per il cluster AKS in un ambiente di produzione, è consigliabile usare almeno tre nodi per il pool di nodi.

Per iniziare, creare un cluster AKS con un pool a nodo singolo. L'esempio seguente usa il comando [AZ Group create][az-group-create] per creare un gruppo di risorse denominato *myResourceGroup* nell'area *eastus* . Un cluster AKS denominato *myAKSCluster* viene quindi creato usando il comando [AZ AKS create][az-aks-create] .

> [!NOTE]
> Lo SKU del servizio di bilanciamento del carico di *base* **non è supportato** quando si usano più pool di nodi. Per impostazione predefinita, i cluster AKS vengono creati con lo SKU del servizio di bilanciamento del carico *standard* dall'interfaccia della riga di comando di Azure e portale di Azure.

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
    --load-balancer-sku standard
```

La creazione del cluster richiede alcuni minuti.

> [!NOTE]
> Per garantire un funzionamento affidabile del cluster, è consigliabile eseguire almeno 2 (due) nodi nel pool di nodi predefinito, perché i servizi di sistema essenziali sono in esecuzione in questo pool di nodi.

Quando il cluster è pronto, usare il comando [AZ AKS Get-credentials][az-aks-get-credentials] per ottenere le credenziali del cluster da usare con `kubectl` :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Aggiungere un pool di nodi

Il cluster creato nel passaggio precedente include un pool a nodo singolo. Si aggiungerà un secondo pool di nodi usando il comando [AZ AKS nodepool Add][az-aks-nodepool-add] . Nell'esempio seguente viene creato un pool di nodi denominato *mynodepool* che esegue *3* nodi:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> Il nome di un pool di nodi deve iniziare con una lettera minuscola e può contenere solo caratteri alfanumerici. Per i pool di nodi Linux la lunghezza deve essere compresa tra 1 e 12 caratteri, per i pool di nodi Windows la lunghezza deve essere compresa tra 1 e 6 caratteri.

Per visualizzare lo stato dei pool di nodi, usare il comando [AZ AKS node pool list][az-aks-nodepool-list] e specificare il gruppo di risorse e il nome del cluster:

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
    "orchestratorVersion": "1.15.7",
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
> Se non viene specificato alcun *VmSize* quando si aggiunge un pool di nodi, le dimensioni predefinite vengono *Standard_D2s_v3* per i pool di nodi di Windows e *Standard_DS2_v2* per i pool di nodi Linux. Se non viene specificato alcun *OrchestratorVersion* , il valore predefinito è la stessa versione del piano di controllo.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Aggiungere un pool di nodi con una subnet univoca (anteprima)

Un carico di lavoro potrebbe richiedere la suddivisione dei nodi di un cluster in pool distinti per l'isolamento logico. Questo isolamento può essere supportato con subnet separate dedicate a ogni pool di nodi del cluster. Questo può soddisfare i requisiti, ad esempio lo spazio degli indirizzi della rete virtuale non contiguo per suddividere i pool di nodi.

#### <a name="limitations"></a>Limitazioni

* Tutte le subnet assegnate a nodepools devono appartenere alla stessa rete virtuale.
* I pod di sistema devono avere accesso a tutti i nodi del cluster per fornire funzionalità critiche, ad esempio la risoluzione DNS tramite coreDNS.
* L'assegnazione di una subnet univoca per ogni pool di nodi è limitata ad Azure CNI durante l'anteprima.
* L'uso di criteri di rete con una subnet univoca per ogni pool di nodi non è supportato durante l'anteprima.

Per creare un pool di nodi con una subnet dedicata, passare l'ID risorsa della subnet come parametro aggiuntivo quando si crea un pool di nodi.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Aggiornare un pool di nodi

> [!NOTE]
> Le operazioni di aggiornamento e ridimensionamento in un cluster o in un pool di nodi non possono essere eseguite simultaneamente, se si tenta di restituire un errore. Al contrario, ogni tipo di operazione deve essere completato sulla risorsa di destinazione prima della richiesta successiva sulla stessa risorsa. Per altre informazioni, vedere la [Guida alla risoluzione dei problemi](https://aka.ms/aks-pending-upgrade).

I comandi in questa sezione illustrano come aggiornare un singolo pool di nodi specifico. La relazione tra l'aggiornamento della versione Kubernetes del piano di controllo e il pool di nodi è illustrata nella [sezione seguente](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> La versione dell'immagine del sistema operativo del pool di nodi è associata alla versione Kubernetes del cluster. Si otterranno solo gli aggiornamenti delle immagini del sistema operativo, in seguito a un aggiornamento del cluster.

Poiché in questo esempio sono presenti due pool di nodi, è necessario usare [AZ AKS nodepool upgrade][az-aks-nodepool-upgrade] per aggiornare un pool di nodi. Per visualizzare gli aggiornamenti disponibili [, usare AZ AKS Get-upgrades][az-aks-get-upgrades]

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Aggiornare il *mynodepool*. Usare il comando [AZ AKS nodepool upgrade][az-aks-nodepool-upgrade] per aggiornare il pool di nodi, come illustrato nell'esempio seguente:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

Elencare nuovamente lo stato dei pool di nodi usando il comando [AZ AKS node pool list][az-aks-nodepool-list] . L'esempio seguente mostra che *mynodepool* è in stato di *aggiornamento* per *KUBERNETES_VERSION*:

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
    "orchestratorVersion": "KUBERNETES_VERSION",
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

Sono necessari alcuni minuti per aggiornare i nodi alla versione specificata.

Come procedura consigliata, è consigliabile aggiornare tutti i pool di nodi in un cluster AKS alla stessa versione di Kubernetes. Il comportamento predefinito di `az aks upgrade` consiste nell'aggiornare tutti i pool di nodi insieme al piano di controllo per ottenere questo allineamento. La possibilità di aggiornare singoli pool di nodi consente di eseguire un aggiornamento in sequenza e pianificare i pod tra i pool di nodi per mantenere i tempi di esecuzione dell'applicazione nei vincoli indicati in precedenza.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Aggiornare un piano di controllo cluster con più pool di nodi

> [!NOTE]
> Kubernetes usa lo schema di controllo delle versioni [semantico](https://semver.org/) standard. Il numero di versione è espresso come *x. y. z*, dove *x* è la versione principale, *y* è la versione secondaria e *z* è la versione della patch. Ad esempio, nella versione *1.12.6*, 1 è la versione principale, 12 è la versione secondaria e 6 è la versione patch. La versione Kubernetes del piano di controllo e il pool di nodi iniziale vengono impostati durante la creazione del cluster. Tutti i pool di nodi aggiuntivi hanno la versione Kubernetes impostata quando vengono aggiunti al cluster. Le versioni di Kubernetes possono essere diverse tra i pool di nodi, nonché tra un pool di nodi e il piano di controllo.

Un cluster AKS ha due oggetti risorsa cluster con le versioni Kubernetes associate.

1. Versione Kubernetes del piano di controllo cluster.
2. Un pool di nodi con una versione di Kubernetes.

Un piano di controllo è mappato a uno o più pool di nodi. Il comportamento di un'operazione di aggiornamento dipende dal comando dell'interfaccia della riga di comando di Azure usato.

Per l'aggiornamento di un piano di controllo AKS è necessario usare `az aks upgrade` . Questo comando Aggiorna la versione del piano di controllo e tutti i pool di nodi nel cluster.

Se si invia il `az aks upgrade` comando con il `--control-plane-only` flag, viene aggiornato solo il piano di controllo cluster. Nessuno dei pool di nodi associati nel cluster viene modificato.

L'aggiornamento di singoli pool di nodi richiede l'uso di `az aks nodepool upgrade` . Questo comando Aggiorna solo il pool di nodi di destinazione con la versione specificata di Kubernetes

### <a name="validation-rules-for-upgrades"></a>Regole di convalida per gli aggiornamenti

Gli aggiornamenti Kubernetes validi per il piano di controllo e i pool di nodi di un cluster vengono convalidati in base ai set di regole seguenti.

* Regole per le versioni valide per aggiornare i pool di nodi:
   * La versione del pool di nodi deve avere la stessa versione *principale* del piano di controllo.
   * La versione *secondaria* del pool di nodi deve essere all'interno di due versioni *secondarie* della versione del piano di controllo.
   * La versione del pool di nodi non può essere maggiore della versione del controllo `major.minor.patch` .

* Regole per l'invio di un'operazione di aggiornamento:
   * Non è possibile effettuare il downgrade del piano di controllo o della versione Kubernetes del pool di nodi.
   * Se non viene specificata una versione Kubernetes del pool di nodi, il comportamento dipende dal client utilizzato. La dichiarazione nei modelli Gestione risorse esegue il fallback alla versione esistente definita per il pool di nodi, se utilizzata, se non è impostata, viene utilizzata la versione del piano di controllo per eseguire il fallback.
   * È possibile aggiornare o ridimensionare un piano di controllo o un pool di nodi in un determinato momento, non è possibile inviare simultaneamente più operazioni su un singolo piano di controllo o una risorsa del pool di nodi.

## <a name="scale-a-node-pool-manually"></a>Ridimensionare manualmente un pool di nodi

Con la necessità di modificare il carico di lavoro dell'applicazione, potrebbe essere necessario ridimensionare il numero di nodi in un pool di nodi. Il numero di nodi può essere ridimensionato verso l'alto o verso il basso.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Per ridimensionare il numero di nodi in un pool di nodi, usare il comando [AZ AKS node pool scale][az-aks-nodepool-scale] . L'esempio seguente ridimensiona il numero di nodi in *mynodepool* a *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Elencare nuovamente lo stato dei pool di nodi usando il comando [AZ AKS node pool list][az-aks-nodepool-list] . L'esempio seguente mostra che *mynodepool* è nello stato di *ridimensionamento* con un nuovo conteggio di *5* nodi:

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

Sono necessari alcuni minuti per il completamento dell'operazione di ridimensionamento.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Ridimensionare automaticamente un pool di nodi specifico abilitando il ridimensionamento automatico del cluster

AKS offre una funzionalità separata per la scalabilità automatica dei pool di nodi con una funzionalità denominata [ridimensionatore](cluster-autoscaler.md)automatico del cluster. Questa funzionalità può essere abilitata per ogni pool di nodi con conteggi di scala minimi e massimi univoci per ogni pool di nodi. Informazioni su come [usare il ridimensionamento automatico del cluster per ogni pool di nodi](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Eliminare un pool di nodi

Se un pool non è più necessario, è possibile eliminarlo e rimuovere i nodi VM sottostanti. Per eliminare un pool di nodi, usare il comando [AZ AKS node pool Delete][az-aks-nodepool-delete] e specificare il nome del pool di nodi. Nell'esempio seguente viene eliminato il *mynoodepool* creato nei passaggi precedenti:

> [!CAUTION]
> Non sono disponibili opzioni di ripristino per la perdita di dati che possono verificarsi quando si elimina un pool di nodi. Se non è possibile pianificare i pod in altri pool di nodi, tali applicazioni non sono disponibili. Assicurarsi di non eliminare un pool di nodi quando le applicazioni in uso non hanno backup dei dati o la possibilità di esecuzione in altri pool di nodi del cluster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

L'output di esempio seguente del comando [AZ AKS node pool list][az-aks-nodepool-list] Mostra che *mynodepool* è in stato di *eliminazione* :

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

Sono necessari alcuni minuti per eliminare i nodi e il pool di nodi.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Specificare le dimensioni della macchina virtuale per un pool di nodi

Negli esempi precedenti per creare un pool di nodi, è stata usata una dimensione di macchina virtuale predefinita per i nodi creati nel cluster. Uno scenario più comune è creare pool di nodi con diverse dimensioni e funzionalità di VM. Ad esempio, è possibile creare un pool di nodi che contiene nodi con grandi quantità di CPU o memoria oppure un pool di nodi che fornisce supporto per la GPU. Nel passaggio successivo si [useranno tains e tollerations](#schedule-pods-using-taints-and-tolerations) per indicare all'utilità di pianificazione Kubernetes come limitare l'accesso ai pod che possono essere eseguiti in questi nodi.

Nell'esempio seguente creare un pool di nodi basato su GPU che usa le dimensioni della macchina virtuale *Standard_NC6* . Queste VM sono basate sulla scheda NVIDIA Tesla K80. Per informazioni sulle dimensioni delle VM disponibili, vedere [dimensioni per le macchine virtuali Linux in Azure][vm-sizes].

Creare un pool di nodi usando di nuovo il comando [AZ AKS node pool Add][az-aks-nodepool-add] . Questa volta specificare il nome *gpunodepool*e utilizzare il `--node-vm-size` parametro per specificare le dimensioni del *Standard_NC6* :

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

L'output di esempio seguente del comando [AZ AKS node pool list][az-aks-nodepool-list] Mostra che *gpunodepool* sta *creando* nodi con il *VmSize*specificato:

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

Sono necessari alcuni minuti prima che il *gpunodepool* venga creato correttamente.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Pianificare i pod usando macchie e tolleranze

Sono ora disponibili due pool di nodi nel cluster, ovvero il pool di nodi predefinito creato inizialmente e il pool di nodi basato su GPU. Usare il comando [kubectl Get nodes][kubectl-get] per visualizzare i nodi nel cluster. L'output di esempio seguente mostra i nodi:

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

Per altre informazioni su come usare le funzionalità pianificate di Kubernetes avanzate, vedere [procedure consigliate per le funzionalità avanzate dell'utilità di pianificazione in AKS][taints-tolerations] .

In questo esempio, applicare un oggetto Tain al nodo basato su GPU usando il comando--node-tains. Specificare il nome del nodo basato su GPU dall'output del `kubectl get nodes` comando precedente. Il Taint viene applicato come coppia *chiave = valore* e quindi come opzione di pianificazione. Nell'esempio seguente viene usata la coppia *SKU = GPU* e vengono definiti i pod in caso contrario la capacità *NoSchedule* :

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

Il manifesto YAML di esempio di base seguente usa una tolleranza per consentire all'utilità di pianificazione di Kubernetes di eseguire un pod NGINX nel nodo basato su GPU. Per un esempio più appropriato, ma con un uso intensivo del tempo per eseguire un processo Tensorflow sul set di dati MNIST, vedere [usare GPU per carichi di lavoro a elevato utilizzo di calcolo in AKS][gpu-cluster].

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

Pianificare il pod usando il `kubectl apply -f gpu-toleration.yaml` comando:

```console
kubectl apply -f gpu-toleration.yaml
```

Sono necessari alcuni secondi per pianificare il Pod ed eseguire il pull dell'immagine NGINX. Usare il comando [Pod kubectl descrivere][kubectl-describe] per visualizzare lo stato del Pod. Il seguente output di esempio ridotto Mostra lo *SKU = GPU: NoSchedule* tolleration è applicato. Nella sezione Events l'utilità di pianificazione ha assegnato il pod al nodo basato su GPU *AKS-gpunodepool-28993262-vmss000000* :

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

Solo i pod a cui è applicata questa tolleranza possono essere pianificati nei nodi di *gpunodepool*. Qualsiasi altro Pod verrà pianificato nel pool di nodi *nodepool1* . Se si creano pool di nodi aggiuntivi, è possibile usare ulteriori macchie e tolleranze per limitare i pod che possono essere pianificati in tali risorse del nodo.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Specificare un Taint, un'etichetta o un tag per un pool di nodi

Quando si crea un pool di nodi, è possibile aggiungere macchie, etichette o tag al pool di nodi. Quando si aggiunge un Taint, un'etichetta o un tag, tutti i nodi all'interno del pool di nodi ottengono anche tale macchia, etichetta o tag.

Per creare un pool di nodi con un Taint, usare [AZ AKS nodepool Add][az-aks-nodepool-add]. Specificare il nome *taintnp* e usare il `--node-taints` parametro per specificare *SKU = GPU: NoSchedule* per il Taint.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

> [!NOTE]
> Un Taint può essere impostato solo per i pool di nodi durante la creazione del pool di nodi.

L'output di esempio seguente del comando [AZ AKS nodepool list][az-aks-nodepool-list] Mostra che *taintnp* sta *creando* nodi con i *notains*specificati:

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
    "nodeTaints":  [
      "sku=gpu:NoSchedule"
    ],
    ...
  },
 ...
]
```

Le informazioni sulle macchie sono visibili in Kubernetes per la gestione delle regole di pianificazione per i nodi.

È inoltre possibile aggiungere etichette a un pool di nodi durante la creazione del pool di nodi. Le etichette impostate nel pool di nodi vengono aggiunte a ogni nodo nel pool di nodi. Queste [etichette sono visibili in Kubernetes][kubernetes-labels] per la gestione delle regole di pianificazione per i nodi.

Per creare un pool di nodi con un'etichetta, usare [AZ AKS nodepool Add][az-aks-nodepool-add]. Specificare il nome *labelnp* e utilizzare il `--labels` parametro per specificare *Dept = it* e *CostCenter = 9999* per le etichette.

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
> L'etichetta può essere impostata solo per i pool di nodi durante la creazione del pool di nodi. Le etichette devono essere anche una coppia chiave/valore e avere una [sintassi valida][kubernetes-label-syntax].

L'output di esempio seguente del comando [AZ AKS nodepool list][az-aks-nodepool-list] Mostra che *labelnp* sta *creando* nodi con il *nodeLabels*specificato:

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

È possibile applicare un tag di Azure ai pool di nodi nel cluster AKS. I tag applicati a un pool di nodi vengono applicati a ogni nodo all'interno del pool di nodi e vengono salvati in modo permanente tramite aggiornamenti. I tag vengono applicati anche ai nuovi nodi aggiunti a un pool di nodi durante le operazioni di scalabilità orizzontale. L'aggiunta di un tag può essere utile per attività quali il rilevamento dei criteri o la stima dei costi.

Creare un pool di nodi usando [AZ AKS nodepool Add][az-aks-nodepool-add]. Specificare il nome *tagnodepool* e usare il `--tag` parametro per specificare *Dept = it* e *CostCenter = 9999* per i tag.

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
> È anche possibile usare il `--tags` parametro quando si usa il comando [AZ AKS nodepool Update][az-aks-nodepool-update] e durante la creazione del cluster. Durante la creazione del cluster, il `--tags` parametro applica il tag al pool di nodi iniziale creato con il cluster. Tutti i nomi di tag devono rispettare le limitazioni nei [tag use per organizzare le risorse di Azure][tag-limitation]. L'aggiornamento di un pool di nodi con il `--tags` parametro aggiorna tutti i valori di tag esistenti e aggiunge eventuali nuovi tag. Se, ad esempio, nel pool di nodi sono presenti *Dept = it* e *CostCenter = 9999* per i tag e l'aggiornamento è stato eseguito con *Team = Dev* e *CostCenter = 111* per i tag, nodepool avrebbe *Dept = it*, *CostCenter = 111*e *Team = Dev* per i tag.

L'output di esempio seguente del comando [AZ AKS nodepool list][az-aks-nodepool-list] Mostra che *tagnodepool* sta *creando* nodi con il *tag*specificato:

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

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Gestire i pool di nodi usando un modello di Gestione risorse

Quando si usa un modello di Azure Resource Manager per creare e risorse gestite, è in genere possibile aggiornare le impostazioni nel modello e ridistribuirle per aggiornare la risorsa. Con i pool di nodi in AKS, il profilo del pool iniziale del nodo non può essere aggiornato dopo la creazione del cluster AKS. Questo comportamento significa che non è possibile aggiornare un modello di Gestione risorse esistente, apportare una modifica ai pool di nodi e ridistribuirlo. Al contrario, è necessario creare un modello di Gestione risorse separato che aggiorni solo i pool di nodi per un cluster AKS esistente.

Creare un modello, ad esempio `aks-agentpools.json` , e incollare il manifesto di esempio seguente. Questo modello di esempio configura le impostazioni seguenti:

* Aggiorna il pool di nodi *Linux* denominato *myagentpool* per eseguire tre nodi.
* Imposta i nodi nel pool di nodi per eseguire Kubernetes versione *1.15.7*.
* Definisce le dimensioni del nodo come *Standard_DS2_v2*.

Modificare questi valori come necessario per aggiornare, aggiungere o eliminare i pool di nodi in base alle esigenze:

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

Distribuire questo modello usando il comando [AZ Group Deployment create][az-group-deployment-create] , come illustrato nell'esempio seguente. Viene richiesto il nome e il percorso del cluster AKS esistenti:

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

L'aggiornamento del cluster AKS potrebbe richiedere alcuni minuti in base alle impostazioni del pool di nodi e alle operazioni definite nel modello di Gestione risorse.

## <a name="assign-a-public-ip-per-node-for-your-node-pools-preview"></a>Assegnare un indirizzo IP pubblico per nodo per i pool di nodi (anteprima)

> [!WARNING]
> Per usare la funzionalità IP pubblico per nodo, è necessario installare l'estensione di anteprima dell'interfaccia della riga di comando 0.4.43 o versione successiva.

I nodi AKS non richiedono indirizzi IP pubblici per la comunicazione. Tuttavia, gli scenari possono richiedere che i nodi in un pool di nodi ricevano indirizzi IP pubblici dedicati. Uno scenario comune è per i carichi di lavoro di gioco, in cui una console deve effettuare una connessione diretta a una macchina virtuale cloud per ridurre al minimo gli hop. Questo scenario può essere eseguito su AKS registrandosi per una funzionalità di anteprima, IP pubblico del nodo (anteprima).

Per installare e aggiornare la versione più recente dell'estensione AKS-Preview, usare i seguenti comandi dell'interfaccia della riga di comando di Azure:

```azurecli
az extension add --name aks-preview
az extension update --name aks-preview
az extension list
```

Eseguire la registrazione per la funzionalità IP pubblico node con il comando dell'interfaccia della riga di comando di Azure seguente:

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```
Potrebbero essere necessari alcuni minuti prima che la funzionalità venga registrata.  È possibile controllare lo stato con il comando seguente:

```azurecli-interactive
 az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodePublicIPPreview')].{Name:name,State:properties.state}"
```

Una volta completata la registrazione, creare un nuovo gruppo di risorse.

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

Creare un nuovo cluster AKS e alleghi un IP pubblico per i nodi. Ognuno dei nodi nel pool di nodi riceve un indirizzo IP pubblico univoco. È possibile verificarlo esaminando le istanze del set di scalabilità di macchine virtuali.

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

Per i cluster AKS esistenti, è anche possibile aggiungere un nuovo pool di nodi e alleghi un IP pubblico per i nodi.

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

> [!Important]
> Durante l'anteprima, il servizio metadati dell'istanza di Azure non supporta attualmente il recupero di indirizzi IP pubblici per lo SKU di VM di livello standard. A causa di questa limitazione, non è possibile usare i comandi kubectl per visualizzare gli indirizzi IP pubblici assegnati ai nodi. Tuttavia, gli indirizzi IP vengono assegnati e funzionano come previsto. Gli indirizzi IP pubblici per i nodi sono collegati alle istanze nel set di scalabilità di macchine virtuali.

È possibile individuare gli indirizzi IP pubblici per i nodi in diversi modi:

* Usare il comando dell'interfaccia della riga di comando di Azure [AZ vmss list-instance-public-IPS][az-list-ips]
* Usare i [comandi di PowerShell o bash][vmss-commands]. 
* È anche possibile visualizzare gli indirizzi IP pubblici nel portale di Azure visualizzando le istanze nel set di scalabilità di macchine virtuali.

> [!Important]
> Il [gruppo di risorse del nodo][node-resource-group] contiene i nodi e i relativi indirizzi IP pubblici. Usare il gruppo di risorse nodo quando si eseguono i comandi per trovare gli indirizzi IP pubblici per i nodi.

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>Eseguire la pulizia delle risorse

In questo articolo è stato creato un cluster AKS che include nodi basati su GPU. Per ridurre i costi non necessari, è consigliabile eliminare il *gpunodepool*o l'intero cluster AKS.

Per eliminare il pool di nodi basato su GPU, usare il comando [AZ AKS nodepool Delete][az-aks-nodepool-delete] , come illustrato nell'esempio seguente:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Per eliminare il cluster, usare il comando [AZ Group Delete][az-group-delete] per eliminare il gruppo di risorse AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

È anche possibile eliminare il cluster aggiuntivo creato per lo scenario IP pubblico per i pool di nodi.

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui [pool di nodi di sistema][use-system-pool].

In questo articolo si è appreso come creare e gestire più pool di nodi in un cluster AKS. Per altre informazioni su come controllare i pod nei pool di nodi, vedere [procedure consigliate per le funzionalità dell'utilità di pianificazione avanzate in AKS][operator-best-practices-advanced-scheduler].

Per creare e usare pool di nodi contenitore di Windows Server, vedere [creare un contenitore di Windows Server in AKS][aks-windows].

Usare i [gruppi di posizionamento di prossimità][reduce-latency-ppg] per ridurre la latenza per le applicazioni AKS.

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
[az-aks-get-upgrades]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-upgrades
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
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-system-pool]: use-system-pools.md
[ip-limitations]: ../virtual-network/virtual-network-ip-addresses-overview-arm#standard
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[vmss-commands]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine
[az-list-ips]: /cli/azure/vmss?view=azure-cli-latest.md#az-vmss-list-instance-public-ips
[reduce-latency-ppg]: reduce-latency-ppg.md
