---
title: Usare più pool di nodi in Azure Kubernetes Service (AKS)
description: Informazioni su come creare e gestire più pool di nodi per un cluster Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/17/2019
ms.author: iainfou
ms.openlocfilehash: 48fdb251fa0302c2755281644a804c74ae80a63e
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491537"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Visualizzare in anteprima: creare e gestire più pool di nodi per un cluster Azure Kubernetes Service (AKS)

In Azure Kubernetes Service (AKS), i nodi della stessa configurazione sono raggruppati in *pool di nodi*. Questi pool di nodi contengono le macchine virtuali sottostanti che eseguono le applicazioni. Il numero iniziale di nodi e delle relative dimensioni (SKU) è definito quando si crea un cluster AKS, che crea una *predefinite pool di nodi*. Per supportare le applicazioni con esigenze di archiviazione o di calcolo diversi, è possibile creare pool di nodi aggiuntivi. Ad esempio, è possibile usare questi pool di nodi aggiuntivi per fornire le GPU per le applicazioni a elevato utilizzo di calcolo o accedere all'archiviazione SSD a prestazioni elevate.

Questo articolo illustra come creare e gestire più pool di nodi in un cluster AKS. Questa funzionalità è attualmente in anteprima.

> [!IMPORTANT]
> Funzionalità di anteprima del servizio contenitore di AZURE sono self-service, fornire il consenso esplicito. Vengono fornite per raccogliere commenti e suggerimenti e bug dalla community. In fase di anteprima, queste funzionalità non sono destinate all'uso di produzione. Le funzionalità in anteprima pubblica rientrano nel supporto "best effort". Assistenza dai team di supporto tecnico di AKS è disponibile durante le ore lavorative Pacifico (PST) solo timezone. Per altre informazioni, vedere i seguenti articoli di supporto:
>
> * [Criteri di supporto servizio contenitore di AZURE][aks-support-policies]
> * [Domande frequenti relative al supporto tecnico Azure][aks-faq]

## <a name="before-you-begin"></a>Prima di iniziare

È necessario la CLI di Azure versione 2.0.61 o versione successiva installato e configurato. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview

Per usare più nodepools, è necessario il *aks-preview* CLI versione dell'estensione 0.4.1 o versione successiva. Installare il *aks-preview* estensione di comando di Azure usando la [Aggiungi estensione az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] comando::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-multiple-node-pool-feature-provider"></a>Registrare più provider di funzionalità del pool di nodi

Per creare un cluster AKS che è possibile usare più pool di nodi, è necessario abilitare innanzitutto due flag funzionalità per la sottoscrizione. I cluster a nodi multipli pool usano un set di scalabilità di macchine virtuali (VMSS) per gestire la distribuzione e la configurazione dei nodi Kubernetes. Registrare il *MultiAgentpoolPreview* e *VMSSPreview* i flag funzionalità tramite il [register funzionalità az][az-feature-register] seguente come mostrato nell'esempio seguente:

> [!CAUTION]
> Quando si registra una funzionalità in una sottoscrizione, attualmente non è possibile annullare la registrazione tale funzionalità. Dopo aver abilitato alcune funzionalità di anteprima, potrebbero essere utilizzati i valori predefiniti per tutti i cluster AKS quindi creati nella sottoscrizione. Non abilitare la funzionalità di anteprima nella sottoscrizione di produzione. Usare una sottoscrizione separata per le funzionalità di anteprima di test e raccogliere commenti e suggerimenti.

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Tutti i cluster servizio contenitore di AZURE create dopo aver correttamente registrato il *MultiAgentpoolPreview* usano questa esperienza di cluster di anteprima. Per continuare a creare i cluster normali, completamente supportato, non abilitare la funzionalità di anteprima nella sottoscrizione di produzione. Usare un test separate o sviluppo sottoscrizione di Azure per verificare le funzionalità di anteprima.

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllarne lo stato di registrazione usando il [elenco delle funzionalità az][az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione dei *containerservice* provider di risorse usando la [register di az provider][az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Limitazioni

Le limitazioni seguenti si applicano quando si creano e gestire i cluster servizio contenitore di AZURE che supportano più pool di nodi:

* Più pool di nodi sono disponibili solo per i cluster creati dopo aver correttamente registrato il *MultiAgentpoolPreview* e *VMSSPreview* funzionalità per la sottoscrizione. Non è possibile aggiungere o gestire i pool di nodi con un cluster servizio contenitore di AZURE esistente creato prima che queste funzionalità sono state registrate correttamente.
* È possibile eliminare il primo pool di nodi.
* Impossibile utilizzare il componente di routing aggiuntivo dell'applicazione HTTP.
* Non è possibile pool di nodi di aggiunta/aggiornamento/eliminazione usando un modello di Resource Manager esistente come con la maggior parte delle operazioni. Al contrario, [usare un modello di Resource Manager separato](#manage-node-pools-using-a-resource-manager-template) apportare modifiche al pool di nodi in un cluster AKS.

Quando questa funzionalità è disponibile in anteprima, si applicano le limitazioni aggiuntive seguenti:

* Il cluster AKS può avere un massimo di otto pool di nodi.
* Il cluster AKS può avere un massimo di 400 nodi tra tali pool di otto nodi.
* Tutti i pool di nodi deve risiedere nella stessa subnet

## <a name="create-an-aks-cluster"></a>Creare un cluster del servizio Azure Container

Per iniziare, creare un cluster AKS con pool un singolo nodo. L'esempio seguente usa il [creare il gruppo di az][az-group-create] comando per creare un gruppo di risorse denominato *myResourceGroup* nel *eastus* area. Un cluster del servizio contenitore di AZURE denominato *myAKSCluster* viene creato utilizzando il [az aks create][az-aks-create] comando. Oggetto *kubernetes--version* dei *1.12.6* viene usata per mostrare come aggiornare un pool di nodi in un passaggio successivo. È possibile specificare qualsiasi [Kubernetes versione supportata][supported-versions].

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --generate-ssh-keys \
    --kubernetes-version 1.12.6
```

La creazione del cluster richiede alcuni minuti.

Quando il cluster è pronto, usare il [az aks get-credentials][az-aks-get-credentials] comando per ottenere le credenziali del cluster per l'uso con `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Aggiungere un pool di nodi

Il cluster creato nel passaggio precedente dispone di un pool singolo nodo. È possibile aggiungere un secondo nodo del pool usando il [pool di nodi di az aks aggiungere][az-aks-nodepool-add] comando. L'esempio seguente crea un pool di nodi denominato *mynodepool* che esegue *3* nodi:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

Per visualizzare lo stato dei tuoi pool di nodi, usare il [elenco dei pool di nodi di az aks][az-aks-nodepool-list] comando e specificare il nome del cluster e gruppo di risorse:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster -o table
```

L'output di esempio seguente mostra che *mynodepool* è stato creato correttamente con tre nodi nel pool di nodi. Quando il cluster AKS è stato creato nel passaggio precedente, un valore predefinito *nodepool1* è stato creato con un numero di nodi pari *1*.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

> [!TIP]
> Se nessun *OrchestratorVersion* oppure *VmSize* viene specificato quando si aggiunge un pool di nodi, i nodi vengono creati in base ai valori predefiniti per il cluster AKS. In questo esempio, che è stato versione di Kubernetes *1.12.6* e di dimensioni del nodo *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Eseguire l'aggiornamento di un pool di nodi

Quando il cluster AKS è stato creato nel primo passaggio, un `--kubernetes-version` dei *1.12.6* è stato specificato. È possibile aggiornare il *mynodepool* in Kubernetes *1.12.7*. Usare la [aggiornamento del pool az aks nodo][az-aks-nodepool-upgrade] comando per aggiornare il pool di nodi, come illustrato nell'esempio seguente:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.12.7 \
    --no-wait
```

Elenca lo stato del pool di nodi usando il [elenco dei pool di nodi di az aks][az-aks-nodepool-list] comando. Nell'esempio seguente mostra che *mynodepool* è la *Upgrading* torni allo stato *1.12.7*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.7                 100             Linux     Upgrading            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Sono necessari alcuni minuti per aggiornare i nodi per la versione specificata.

Come procedura consigliata, è consigliabile aggiornare tutti i pool di nodi in un cluster AKS per la stessa versione di Kubernetes. La possibilità di eseguire l'aggiornamento di pool di nodi individuali consente di eseguire un aggiornamento in sequenza e pianificare i POD tra pool di nodi per mantenere la disponibilità dell'applicazione.

## <a name="scale-a-node-pool"></a>Ridimensionare un pool di nodi

L'applicazione alle esigenze legate del carico di lavoro, potrebbe essere necessario aumentare il numero di nodi in un pool di nodi. Il numero di nodi può essere ridimensionato verso l'alto o verso il basso.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Per ridimensionare il numero di nodi in un pool di nodi, usare il [scalabilità del pool az aks nodo][az-aks-nodepool-scale] comando. L'esempio seguente ridimensiona il numero di nodi *mynodepool* al *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Elenca lo stato del pool di nodi usando il [elenco dei pool di nodi di az aks][az-aks-nodepool-list] comando. L'esempio seguente mostra che *mynodepool* è nel *Scaling* dello stato con un nuovo conteggio dei *5* nodi:

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Scaling              myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Sono necessari alcuni minuti per completare l'operazione di scalabilità.

## <a name="delete-a-node-pool"></a>Eliminare un pool di nodi

Se non è più necessario un pool, è possibile eliminarlo e rimuovere i nodi della macchina virtuale sottostanti. Per eliminare un pool di nodi, usare il [pool di nodi di az aks delete][az-aks-nodepool-delete] comando e specificare il nome del pool di nodi. L'esempio seguente elimina il *mynoodepool* creato nei passaggi precedenti:

> [!CAUTION]
> Non sono disponibili opzioni di ripristino per perdita di dati che possono verificarsi quando si elimina un pool di nodi. Se non è possibile pianificare i POD in altri pool di nodi, tali applicazioni non sono disponibili. Assicurarsi di che non eliminare un pool di nodi quando le applicazioni in uso non dispongono di backup dei dati o la possibilità di eseguire in altri pool di nodi nel cluster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Il seguente output di esempio dal [elenco dei pool di nodi di az aks][az-aks-nodepool-list] comando Mostra che *mynodepool* è nel *eliminazione* stato:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Deleting             myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Sono necessari alcuni minuti per eliminare i nodi e il pool di nodi.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Specificare una dimensione di macchina virtuale per un pool di nodi

Negli esempi precedenti per creare un pool di nodi, una dimensione di macchina virtuale predefinita è stata usata per i nodi creati nel cluster. Uno scenario più comune è creare pool di nodi con varie dimensioni di VM e funzionalità. Ad esempio, è possibile creare un pool di nodi contenente nodi con grandi quantità di memoria o CPU, o un pool di nodi che fornisce supporto per GPU. Nel passaggio successivo, si [usare taints e tolerations](#schedule-pods-using-taints-and-tolerations) per indicare l'utilità di pianificazione di Kubernetes come limitare l'accesso al POD eseguibili in tali nodi.

Nell'esempio seguente, creare un pool di nodi basate su GPU che usa il *Standard_NC6* dimensioni della macchina virtuale. Queste VM sono basate sulla scheda NVIDIA Tesla K80. Per informazioni sulle dimensioni VM disponibili, vedere [dimensioni delle macchine virtuali Linux in Azure][vm-sizes].

Creare un pool di nodi usando il [Aggiungi pool di nodi di az aks][az-aks-nodepool-add] nuovo il comando. Questa volta specificare il nome *gpunodepool*e utilizzare il `--node-vm-size` parametro per specificare il *Standard_NC6* dimensioni:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Il seguente output di esempio dal [elenco dei pool di nodi di az aks][az-aks-nodepool-list] comando Mostra che *gpunodepool* viene *Creating* nodi con la proprietà specificata *VmSize*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name         OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  -----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  1        110        gpunodepool  1.12.6                 100             Linux     Creating             myResourceGroupPools  Standard_NC6
VirtualMachineScaleSets  1        110        nodepool1    1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Sono necessari alcuni minuti il *gpunodepool* venga creata correttamente.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Pianificare i POD usando taints e tolerations

Sono ora due pool di nodi nel cluster - inizialmente creato il pool di nodo predefinito e il pool di nodi basate su GPU. Usare la [kubectl ottenere nodi][kubectl-get] comando per visualizzare i nodi nel cluster. L'output di esempio seguente mostra un nodo in ogni pool di nodi:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.12.6
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.12.6
```

L'utilità di pianificazione di Kubernetes può usare taint e tolleranze per limitare i carichi di lavoro che possono essere eseguiti sui nodi.

* Un **taint** viene applicato a un nodo per indicare che possono essere pianificati solo pod specifici.
* Una **tolleranza** viene quindi applicata a un pod per *tollerare* un taint di un nodo.

Per altre informazioni su come usare avanzate funzionalità di Kubernetes pianificata, vedere [procedure consigliate per le funzionalità avanzate dell'utilità di pianificazione nel servizio contenitore di AZURE][taints-tolerations]

In questo esempio, applicare un aspetto al basate su GPU a nodo usando il [kubectl aspetto nodo][kubectl-taint] comando. Specificare il nome del nodo basate su GPU dall'output del precedente `kubectl get nodes` comando. L'aspetto viene applicato come un *chiave: valore* e quindi un'opzione di pianificazione. L'esempio seguente usa il *sku = gpu* associare e definisce i POD in caso contrario, disporre la *NoSchedule* possibilità:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

Il manifesto YAML seguente esempio di base Usa Tolerance a un livello per consentire l'utilità di pianificazione di Kubernetes eseguire un pod NGINX nel nodo basate su GPU. Per un esempio più appropriato, ma richiede molto tempo eseguire un processo Tensorflow sul set di dati MNIST, vedere [utilizzo GPU per carichi di lavoro a elevato utilizzo di calcolo nel servizio contenitore di AZURE][gpu-cluster].

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

Bastano pochi secondi per pianificare il pod e il pull dell'immagine di NGINX. Usare la [kubectl descrivono pod][kubectl-describe] comando per visualizzare lo stato di pod. Esempio sintetico seguente output viene visualizzato il *sku = gpu:NoSchedule* Tolerance a livello applicazione. Nella sezione degli eventi, l'utilità di pianificazione è assegnato il pod per il *aks-gpunodepool-28993262-vmss000000* basate su GPU a nodo:

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

È possibile pianificare solo POD che hanno questo aspetto applicato in nodi *gpunodepool*. Eventuali altri pod verrà pianificata nel *nodepool1* pool di nodi. Se si creano pool di nodi aggiuntivi, è possibile usare taints aggiuntive e tali risorse nodo può essere pianificato tolerations per limitare i POD.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Gestire i pool di nodi usando un modello di Resource Manager

Quando si usa un modello Azure Resource Manager per creare e le risorse gestite, è possibile aggiornare in genere le impostazioni nel modello e ridistribuzione per aggiornare la risorsa. Con pool di nodi nel servizio contenitore di AZURE, il profilo del pool iniziale di nodi non è possibile aggiornare dopo aver creato il cluster AKS. Questo comportamento significa che non è possibile aggiornare un modello di Resource Manager esistente, apportare una modifica per il pool di nodi e ridistribuire. In alternativa, è necessario creare un modello di Resource Manager separato che aggiorna solo i pool di agenti per un cluster servizio contenitore di AZURE esistente.

Creare un modello, ad esempio `aks-agentpools.json` e incollare il seguente esempio di manifesto. Questo modello di esempio consente di configurare le impostazioni seguenti:

* Gli aggiornamenti di *Linux* pool di agenti denominato *myagentpool* per l'esecuzione di tre nodi.
* Imposta i nodi nel pool di nodi per eseguire una versione di Kubernetes *1.12.8*.
* Definisce le dimensioni del nodo come *Standard_DS2_v2*.

Modificare questi valori come necessario per l'aggiornamento, aggiunta o eliminazione di pool di nodi in base alle esigenze:

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
            "orchestratorVersion": "1.12.8"
      }
    }
  ]
}
```

Distribuire questo modello usando il [distribuzione gruppo di az creare][az-group-deployment-create] comando, come illustrato nell'esempio seguente. Richiesto per il nome del cluster servizio contenitore di AZURE e il percorso esistente:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

Potrebbero occorrere alcuni minuti per aggiornare il cluster AKS a seconda le impostazioni del pool di nodi e le operazioni definite nel modello di Resource Manager.

## <a name="clean-up-resources"></a>Pulire le risorse

In questo articolo è stato creato un cluster AKS che include i nodi basati su GPU. Per ridurre i costi non necessari, è possibile eliminare il *gpunodepool*, o l'intero cluster AKS.

Per eliminare il pool di nodi basate su GPU, usare il [nodepool di az aks delete][az-aks-nodepool-delete] seguente come mostrato nell'esempio seguente:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Per eliminare il cluster stesso, usare il [eliminazione gruppo az][az-group-delete] comando per eliminare il gruppo di risorse del servizio contenitore di AZURE:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si appreso come creare e gestire più pool di nodi in un cluster AKS. Per altre informazioni su come controllare i POD tra pool di nodi, vedere [procedure consigliate per le funzionalità avanzate dell'utilità di pianificazione in AKS][operator-best-practices-advanced-scheduler].

Per creare e usare pool di nodi contenitore di Windows Server, vedere [creare un contenitore di Windows Server in AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
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
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
