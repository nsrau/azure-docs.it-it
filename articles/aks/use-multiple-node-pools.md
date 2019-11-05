---
title: Usare più pool di nodi in Azure Kubernetes Service (AKS)
description: Informazioni su come creare e gestire pool di nodi multipli per un cluster in Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/9/2019
ms.author: mlearned
ms.openlocfilehash: 8a78c854e9c842915700d4a20c1a57e4f1594a2e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472446"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Creare e gestire più pool di nodi per un cluster in Azure Kubernetes Service (AKS)

In Azure Kubernetes Service (AKS) i nodi della stessa configurazione sono raggruppati in pool di *nodi*. Questi pool di nodi contengono le macchine virtuali sottostanti che eseguono le applicazioni. Il numero iniziale di nodi e le relative dimensioni (SKU) vengono definiti quando si crea un cluster AKS, che crea un *pool di nodi predefinito*. Per supportare applicazioni con esigenze di calcolo o di archiviazione diverse, è possibile creare pool di nodi aggiuntivi. Usare, ad esempio, questi pool di nodi aggiuntivi per fornire GPU per applicazioni a elevato utilizzo di calcolo o per l'accesso a un'archiviazione SSD a prestazioni elevate.

> [!NOTE]
> Questa funzionalità consente un maggiore controllo sulla creazione e la gestione di più pool di nodi. Di conseguenza, per create/Update/Delete sono necessari comandi distinti. In precedenza le operazioni del cluster tramite `az aks create` o `az aks update` usavano l'API managedCluster ed erano l'unica opzione per modificare il piano di controllo e un pool a nodo singolo. Questa funzionalità espone un set di operazioni separato per i pool di agenti tramite l'API agentPool e richiede l'uso del `az aks nodepool` set di comandi per eseguire operazioni in un singolo pool di nodi.

Questo articolo illustra come creare e gestire più pool di nodi in un cluster AKS.

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.76 o successiva installata e configurata. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono cluster AKS che supportano più pool di nodi, si applicano le limitazioni seguenti:

* Non è possibile eliminare il pool di nodi predefinito (primo).
* Non è possibile usare il componente aggiuntivo routing applicazione HTTP.
* Non è possibile aggiungere o eliminare pool di nodi usando un modello di Gestione risorse esistente come per la maggior parte delle operazioni. Usare invece [un modello di gestione risorse separato](#manage-node-pools-using-a-resource-manager-template) per apportare modifiche ai pool di nodi in un cluster AKS.
* Il nome di un pool di nodi deve iniziare con una lettera minuscola e può contenere solo caratteri alfanumerici. Per i pool di nodi Linux la lunghezza deve essere compresa tra 1 e 12 caratteri, per i pool di nodi Windows la lunghezza deve essere compresa tra 1 e 6 caratteri.
* Il cluster AKS può avere un massimo di otto pool di nodi.
* Il cluster AKS può avere un massimo di 400 nodi tra questi otto pool di nodi.
* Tutti i pool di nodi devono trovarsi nella stessa subnet.
* Il cluster AKS deve usare i set di scalabilità di macchine virtuali per i nodi.

## <a name="create-an-aks-cluster"></a>Creare un cluster del servizio Azure Container

Per iniziare, creare un cluster AKS con un pool a nodo singolo. L'esempio seguente usa il comando [AZ Group create][az-group-create] per creare un gruppo di risorse denominato *myResourceGroup* nell'area *eastus* . Un cluster AKS denominato *myAKSCluster* viene quindi creato usando il comando [AZ AKS create][az-aks-create] . Viene usata una *versione--kubernetes-Version* di *1.13.10* per illustrare come aggiornare un pool di nodi in un passaggio successivo. È possibile specificare qualsiasi [versione di Kubernetes supportata][supported-versions].

> [!NOTE]
> Lo SKU *Basic* Load balanacer non è supportato quando si usano più pool di nodi. Per impostazione predefinita, i cluster AKS vengono creati con lo SKU loadbalacer *standard* .

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
    --kubernetes-version 1.13.10 \
    --load-balancer-sku standard
```

La creazione del cluster richiede alcuni minuti.

> [!NOTE]
> Per garantire un funzionamento affidabile del cluster, è consigliabile eseguire almeno 2 (due) nodi nel pool di nodi predefinito, perché i servizi di sistema essenziali sono in esecuzione in questo pool di nodi.

Quando il cluster è pronto, usare il comando [AZ AKS Get-credentials][az-aks-get-credentials] per ottenere le credenziali del cluster da usare con `kubectl`:

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
    --node-count 3 \
    --kubernetes-version 1.12.7
```

> [!NOTE]
> Il nome di un pool di nodi deve iniziare con una lettera minuscola e può contenere solo caratteri alfanumerici. Per i pool di nodi Linux la lunghezza deve essere compresa tra 1 e 12 caratteri, per i pool di nodi Windows la lunghezza deve essere compresa tra 1 e 6 caratteri.

Per visualizzare lo stato dei pool di nodi, usare il comando [AZ AKS node pool list][az-aks-nodepool-list] e specificare il gruppo di risorse e il nome del cluster:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

L'output di esempio seguente mostra che *mynodepool* è stato creato correttamente con tre nodi nel pool di nodi. Quando il cluster AKS è stato creato nel passaggio precedente, è stato creato un *nodepool1* predefinito con un numero di nodi pari a *2*.

```console
$ az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.12.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Se non si specifica *OrchestratorVersion* o *VmSize* quando si aggiunge un pool di nodi, i nodi vengono creati in base alle impostazioni predefinite del cluster AKS. In questo esempio è stato Kubernetes versione *1.13.10* e dimensioni nodo di *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Aggiornare un pool di nodi
 
> [!NOTE]
> Le operazioni di aggiornamento e ridimensionamento in un cluster o in un pool di nodi non possono essere eseguite simultaneamente, se si tenta di restituire un errore. Al contrario, ogni tipo di operazione deve essere completato sulla risorsa di destinazione prima della richiesta successiva sulla stessa risorsa. Per altre informazioni, vedere la [Guida alla risoluzione dei problemi](https://aka.ms/aks-pending-upgrade).

Quando il cluster AKS è stato creato inizialmente nel primo passaggio, è stato specificato un `--kubernetes-version` di *1.13.10* . Questo imposta la versione di Kubernetes sia per il piano di controllo che per il pool di nodi predefinito. I comandi in questa sezione illustrano come aggiornare un singolo pool di nodi specifico.

La relazione tra l'aggiornamento della versione Kubernetes del piano di controllo e il pool di nodi è illustrata nella [sezione seguente](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> La versione dell'immagine del sistema operativo del pool di nodi è associata alla versione Kubernetes del cluster. Si otterranno solo gli aggiornamenti delle immagini del sistema operativo, in seguito a un aggiornamento del cluster.

Poiché in questo esempio sono presenti due pool di nodi, è necessario usare [AZ AKS nodepool upgrade][az-aks-nodepool-upgrade] per aggiornare un pool di nodi. Aggiornare *mynodepool* a Kubernetes *1.13.10*. Usare il comando [AZ AKS nodepool upgrade][az-aks-nodepool-upgrade] per aggiornare il pool di nodi, come illustrato nell'esempio seguente:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.13.10 \
    --no-wait
```

Elencare nuovamente lo stato dei pool di nodi usando il comando [AZ AKS node pool list][az-aks-nodepool-list] . L'esempio seguente mostra che *mynodepool* è in stato di *aggiornamento* a *1.13.10*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
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
> Kubernetes usa lo schema di controllo delle versioni [semantico](https://semver.org/) standard. Il numero di versione è espresso come *x. y. z*, dove *x* è la versione principale, *y* è la versione secondaria e *z* è la versione della patch. Ad esempio, nella versione *1.12.6*, 1 è la versione principale, 12 è la versione secondaria e 6 è la versione della patch. La versione Kubernetes del piano di controllo e del pool di nodi iniziale viene impostata durante la creazione del cluster. Tutti i pool di nodi aggiuntivi hanno la versione Kubernetes impostata quando vengono aggiunti al cluster. Le versioni di Kubernetes possono essere diverse tra i pool di nodi, nonché tra un pool di nodi e il piano di controllo, ma sono valide le restrizioni seguenti:
> 
> * La versione del pool di nodi deve avere la stessa versione principale del piano di controllo.
> * La versione del pool di nodi può essere una versione secondaria minore della versione del piano di controllo.
> * La versione del pool di nodi può essere qualsiasi versione patch, purché vengano seguiti gli altri due vincoli.

Un cluster AKS ha due oggetti risorsa cluster con le versioni Kubernetes associate. Il primo è una versione del piano di controllo Kubernetes. Il secondo è un pool di agenti con una versione di Kubernetes. Un piano di controllo è mappato a uno o più pool di nodi. Il comportamento di un'operazione di aggiornamento dipende dal comando dell'interfaccia della riga di comando di Azure usato.

* Per l'aggiornamento del piano di controllo è necessario usare `az aks upgrade`
   * Questo Aggiorna la versione del piano di controllo e tutti i pool di nodi nel cluster
   * Passando `az aks upgrade` con il flag di `--control-plane-only` solo il piano di controllo cluster viene aggiornato e nessuno dei pool di nodi associati viene modificato.
* L'aggiornamento di singoli pool di nodi richiede l'uso di `az aks nodepool upgrade`
   * Questo Aggiorna solo il pool di nodi di destinazione con la versione specificata di Kubernetes

La relazione tra le versioni di Kubernetes contenute nei pool di nodi deve anche seguire un set di regole.

* Non è possibile effettuare il downgrade del piano di controllo né della versione Kubernetes del pool di nodi.
* Se non viene specificata una versione Kubernetes del pool di nodi, il comportamento dipende dal client utilizzato. Per la dichiarazione nel modello di Gestione risorse viene utilizzata la versione esistente definita per il pool di nodi, se non è impostata alcuna versione del piano di controllo.
* È possibile aggiornare o ridimensionare un piano di controllo o un pool di nodi in un determinato momento, non è possibile inviare entrambe le operazioni simultaneamente.
* Una versione Kubernetes del pool di nodi deve essere la stessa versione principale del piano di controllo.
* Una versione Kubernetes del pool di nodi può essere al massimo due (2) versioni secondarie minori del piano di controllo, mai maggiore.
* Un pool di nodi può essere qualsiasi versione patch Kubernetes inferiore o uguale al piano di controllo, mai maggiore.

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

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
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

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
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

Creare un pool di nodi usando di nuovo il comando [AZ AKS node pool Add][az-aks-nodepool-add] . Questa volta specificare il nome *gpunodepool*e usare il parametro `--node-vm-size` per specificare le dimensioni di *Standard_NC6* :

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

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
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
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.13.10
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.13.10
```

L'utilità di pianificazione di Kubernetes può usare taint e tolleranze per limitare i carichi di lavoro che possono essere eseguiti sui nodi.

* Un **taint** viene applicato a un nodo per indicare che possono essere pianificati solo pod specifici.
* Una **tolleranza** viene quindi applicata a un pod per *tollerare* un taint di un nodo.

Per altre informazioni su come usare le funzionalità pianificate di Kubernetes avanzate, vedere [procedure consigliate per le funzionalità avanzate dell'utilità di pianificazione in AKS][taints-tolerations] .

In questo esempio, applicare un oggetto Tain al nodo basato su GPU usando il comando [kubectl Tain node][kubectl-taint] . Specificare il nome del nodo basato su GPU dall'output del comando `kubectl get nodes` precedente. Il Taint viene applicato come *chiave: valore* e quindi un'opzione di pianificazione. Nell'esempio seguente viene usata la coppia *SKU = GPU* e vengono definiti i pod in caso contrario la capacità *NoSchedule* :

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
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

Pianificare il pod usando il comando `kubectl apply -f gpu-toleration.yaml`:

```console
kubectl apply -f gpu-toleration.yaml
```

Sono necessari alcuni secondi per pianificare il Pod ed eseguire il pull dell'immagine NGINX. Usare il comando [Pod kubectl descrivere][kubectl-describe] per visualizzare lo stato del Pod. Il seguente output di esempio ridotto Mostra lo *SKU = GPU: NoSchedule* tolleration è applicato. Nella sezione Events l'utilità di pianificazione ha assegnato il pod al nodo basato su GPU *AKS-gpunodepool-28993262-vmss000000* :

```console
$ kubectl describe pod mypod

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

Solo i pod con questo Taint applicato possono essere pianificati nei nodi di *gpunodepool*. Qualsiasi altro Pod verrà pianificato nel pool di nodi *nodepool1* . Se si creano pool di nodi aggiuntivi, è possibile usare ulteriori macchie e tolleranze per limitare i pod che possono essere pianificati in tali risorse del nodo.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Gestire i pool di nodi usando un modello di Gestione risorse

Quando si usa un modello di Azure Resource Manager per creare e risorse gestite, è in genere possibile aggiornare le impostazioni nel modello e ridistribuirle per aggiornare la risorsa. Con i pool di nodi in AKS, il profilo del pool iniziale del nodo non può essere aggiornato dopo la creazione del cluster AKS. Questo comportamento significa che non è possibile aggiornare un modello di Gestione risorse esistente, apportare una modifica ai pool di nodi e ridistribuirlo. Al contrario, è necessario creare un modello di Gestione risorse separato che aggiorni solo i pool di agenti per un cluster AKS esistente.

Creare un modello, ad esempio `aks-agentpools.json` e incollare il manifesto di esempio seguente. Questo modello di esempio configura le impostazioni seguenti:

* Aggiorna il pool di agenti *Linux* denominato *myagentpool* per eseguire tre nodi.
* Imposta i nodi nel pool di nodi per eseguire Kubernetes versione *1.13.10*.
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
            "aks": "2019-04-01"
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
            "apiVersion": "2019-04-01",
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
                "orchestratorVersion": "1.13.10"
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

L'aggiornamento del cluster AKS potrebbe richiedere alcuni minuti in base alle impostazioni del pool di nodi e alle operazioni definite nel modello di Gestione risorse.

## <a name="assign-a-public-ip-per-node-in-a-node-pool"></a>Assegnare un indirizzo IP pubblico per nodo in un pool di nodi

> [!WARNING]
> Durante l'anteprima dell'assegnazione di un indirizzo IP pubblico per nodo, non può essere usato con lo *SKU Load Balancer standard in AKS* a causa di possibili regole del servizio di bilanciamento del carico in conflitto con il provisioning delle macchine virtuali. Durante l'anteprima è necessario usare lo *SKU Basic Load Balancer* se è necessario assegnare un indirizzo IP pubblico per ogni nodo.

I nodi AKS non richiedono indirizzi IP pubblici per la comunicazione. Tuttavia, in alcuni scenari potrebbe essere necessario che i nodi in un pool di nodi dispongano di indirizzi IP pubblici. Un esempio è il gioco, in cui una console deve effettuare una connessione diretta a una macchina virtuale cloud per ridurre al minimo i luppoli. Per ottenere questo risultato, è possibile registrarsi per una funzionalità di anteprima separata, ovvero l'indirizzo IP pubblico del nodo (anteprima).

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

Al termine della registrazione, distribuire un modello di Azure Resource Manager seguendo le stesse istruzioni riportate [sopra](#manage-node-pools-using-a-resource-manager-template) e aggiungendo la seguente proprietà del valore booleano "enableNodePublicIP" in agentPoolProfiles. Impostarlo su `true` come per impostazione predefinita viene impostato come `false` se non è specificato. Si tratta di una proprietà solo in fase di creazione e richiede una versione API minima di 2019-06-01. Questo può essere applicato ai pool di nodi di Linux e Windows.

```
"agentPoolProfiles":[  
    {  
      "maxPods": 30,
      "osDiskSizeGB": 0,
      "agentCount": 3,
      "agentVmSize": "Standard_DS2_v2",
      "osType": "Linux",
      "vnetSubnetId": "[parameters('vnetSubnetId')]",
      "enableNodePublicIP":true
    }
```

## <a name="clean-up-resources"></a>Pulire le risorse

In questo articolo è stato creato un cluster AKS che include nodi basati su GPU. Per ridurre i costi non necessari, è consigliabile eliminare il *gpunodepool*o l'intero cluster AKS.

Per eliminare il pool di nodi basato su GPU, usare il comando [AZ AKS nodepool Delete][az-aks-nodepool-delete] , come illustrato nell'esempio seguente:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Per eliminare il cluster, usare il comando [AZ Group Delete][az-group-delete] per eliminare il gruppo di risorse AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come creare e gestire più pool di nodi in un cluster AKS. Per altre informazioni su come controllare i pod nei pool di nodi, vedere [procedure consigliate per le funzionalità dell'utilità di pianificazione avanzate in AKS][operator-best-practices-advanced-scheduler].

Per creare e usare pool di nodi contenitore di Windows Server, vedere [creare un contenitore di Windows Server in AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
