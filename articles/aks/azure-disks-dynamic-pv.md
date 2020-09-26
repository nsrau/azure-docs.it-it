---
title: Creazione dinamica di volumi di dischi di Azure
titleSuffix: Azure Kubernetes Service
description: Informazioni su come creare dinamicamente un volume permanente con dischi di Azure in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: fd2bc698a107599dccf8f142b0d318400b40aaf3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91299324"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Creare dinamicamente e usare un volume persistente con i dischi di Azure nel servizio Azure Kubernetes

Un volume permanente rappresenta una parte di risorsa di archiviazione di cui è stato eseguito il provisioning per l'uso con pod Kubernetes. Un volume permanente può essere usato da uno o più pod e se ne può eseguire il provisioning in modo dinamico o in modo statico. Questo articolo mostra come creare in modo dinamico volumi persistenti con i dischi di Azure per l'uso con un singolo pod in un cluster del servizio Azure Kubernetes.

> [!NOTE]
> Un disco di Azure può essere montato solo con la *modalità di accesso* di tipo *ReadWriteOnce*, che lo rende disponibile per un nodo in AKS. Se è necessario condividere un volume permanente tra più nodi, utilizzare [file di Azure][azure-files-pvc].

Per altre informazioni sui volumi Kubernetes, vedere [Opzioni di archiviazione per le applicazioni nel servizio Azure Kubernetes][concepts-storage].

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

È anche necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.59 o versione successiva. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="built-in-storage-classes"></a>Classi di archiviazione predefinite

Una classe di archiviazione viene usata per definire la creazione dinamica di un'unità di archiviazione con un volume permanente. Per altre informazioni sulle classi di archiviazione Kubernetes, vedere [Kubernetes Storage Classes][kubernetes-storage-classes] (Classi di archiviazione Kubernetes).

Ogni cluster AKS include quattro classi di archiviazione create in precedenza, due delle quali sono configurate per l'uso con i dischi di Azure:

* La classe di archiviazione *predefinita* effettua il provisioning di un disco di Azure SSD standard.
    * L'archiviazione standard è supportata da SSD standard e offre una soluzione di archiviazione economicamente conveniente, garantendo allo stesso tempo prestazioni affidabili. 
* La classe di archiviazione *gestita Premium* esegue il provisioning di un disco di Azure premium.
    * I dischi premium sono supportati da un disco a bassa latenza e ad alte prestazioni basato su SSD. Ideale per le macchine virtuali che eseguono il carico di lavoro della produzione. Se i nodi del servizio Azure Container nel cluster usano l'archiviazione premium, selezionare la classe *gestita Premium*.
    
Se si usa una delle classi di archiviazione predefinite, non è possibile aggiornare la dimensione del volume dopo la creazione della classe di archiviazione. Per poter aggiornare le dimensioni del volume dopo la creazione di una classe di archiviazione, aggiungere la riga `allowVolumeExpansion: true` a una delle classi di archiviazione predefinite oppure è possibile creare una classe di archiviazione personalizzata. Si noti che non è supportato per ridurre le dimensioni di un PVC (per evitare la perdita di dati). È possibile modificare una classe di archiviazione esistente usando il `kubectl edit sc` comando. 

Se ad esempio si vuole usare un disco di dimensioni 4 TiB, è necessario creare una classe di archiviazione che definisce `cachingmode: None` perché la [memorizzazione nella cache del disco non è supportata per i dischi 4 TIB e superiori](../virtual-machines/premium-storage-performance.md#disk-caching).

Per altre informazioni sulle classi di archiviazione e sulla creazione di una classe di archiviazione personalizzata, vedere [Opzioni di archiviazione per le applicazioni in AKS][storage-class-concepts].

Usare il comando [kubectl get sc][kubectl-get] per visualizzare le classi di archiviazione create in precedenza. L'esempio seguente illustra la creazione preliminare di classi di archiviazione disponibile all'interno di un cluster servizio Azure Kubernetes:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Le attestazioni di volumi permanenti sono specificate in GiB, ma i dischi gestiti di Azure vengono fatturati in base al codice SKU per una dimensione specifica. Questi SKU variano da 32GiB per i dischi S4 o P4 a 32TiB per S80 o P80 Disks (in anteprima). La velocità effettiva e le prestazioni delle operazioni di I/O al secondo per un disco gestito Premium dipendono sia dallo SKU sia dalla dimensione dell'istanza dei nodi nel cluster servizio Azure Kubernetes. Per altre informazioni, vedere [Prezzi e prestazioni dei dischi gestiti][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Creare un'attestazione di volume permanente

Un'attestazione di volume permanente viene usata per il provisioning automatico dell'archiviazione in una classe di archiviazione. In tal caso un'attestazione di volume permanente può usare una delle classi di archiviazione create in precedenza per creare un disco gestito di Azure standard o premium.

Creare un file denominato `azure-premium.yaml` e copiarlo nel manifesto seguente. L'attestazione richiede un disco denominato `azure-managed-disk` con dimensione di *5 GB* e accesso *ReadWriteOnce*. Come classe di archiviazione è specificata *managed-premium*.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Per creare un disco con archiviazione standard, usare `storageClassName: default` anziché *managed-premium*.

Creare l'attestazione di volume permanente con il comando [kubectl apply][kubectl-apply] e specificare il file *azure-premium.yaml*:

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Usare il volume permanente

Dopo aver creato l'attestazione di volume persistente e aver effettuato il provisioning del disco, è possibile creare un pod con accesso al disco. Il manifesto seguente crea un pod NGINX di base che usa l'attestazione di volume persistente denominata *azure-managed-disk* per montare il disco di Azure nel percorso `/mnt/azure`. Per i contenitori Windows Server specificare un valore per *mountPath* usando la convenzione di percorso di Windows, ad esempio *'D:'* .

Creare un file denominato `azure-pvc-disk.yaml` e copiarlo nel manifesto seguente.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Creare il pod con il comando [kubectl apply][kubectl-apply], come illustrato nell'esempio seguente:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

A questo punto è disponibile un pod in esecuzione con il disco di Azure montato nella directory `/mnt/azure`. Questa configurazione può essere visualizzata quando si controlla il pod tramite `kubectl describe pod mypod`, come illustrato nell'esempio sintetico seguente:

```console
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="use-ultra-disks"></a>Usare dischi ultra
Per sfruttare il disco Ultra, vedere [usare dischi ultra in Azure Kubernetes Service (AKS)](use-ultra-disks.md).

## <a name="back-up-a-persistent-volume"></a>Eseguire il backup di un volume persistente

Per eseguire il backup dei dati nel volume persistente, creare uno snapshot del disco gestito per il volume. È quindi possibile usare questo snapshot per creare un disco ripristinato e collegarlo ai pod come soluzione di ripristino dei dati.

Prima di tutto, ottenere il nome del volume con il comando `kubectl get pvc`, ad esempio per l'attestazione di volume persistente denominata *azure-managed-disk*:

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Questo nome di volume costituisce il nome del disco di Azure sottostante. Eseguire una query per ottenere l'ID del disco con il comando [az disk list][az-disk-list] e specificare il nome di volume dell'attestazione di volume persistente, come illustrato nell'esempio seguente:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Usare l'ID del disco per creare lo snapshot di un disco con [az snapshot create][az-snapshot-create]. L'esempio seguente crea uno snapshot denominato *pvcSnapshot* nello stesso gruppo di risorse del cluster servizio Azure Kubernetes (*MC_myResourceGroup_myservizio Azure KubernetesCluster_eastus*). Se si creano snapshot e si ripristinano dischi in gruppi di risorse a cui il cluster servizio Azure Kubernetes non ha accesso, è possibile che si verifichino problemi di autorizzazione.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

A seconda della quantità di dati sul disco, possono essere necessari alcuni minuti per creare lo snapshot.

## <a name="restore-and-use-a-snapshot"></a>Ripristinare e usare uno snapshot

Per ripristinare il disco e usarlo con un pod Kubernetes, usare lo snapshot come origine quando si crea un disco con [az disk create][az-disk-create]. Questa operazione consente di mantenere la risorsa originale se in un secondo momento è necessario accedere allo snapshot di dati originale. L'esempio seguente crea un disco denominato *pvcRestored* a partire dallo snapshot *pvcSnapshot*:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Per usare il disco ripristinato con un pod, specificare l'ID del disco nel manifesto. Ottenere l'ID del disco con il comando [az disk show][az-disk-show]. L'esempio seguente ottiene l'ID relativo al disco *pvcRestored* creato nel passaggio precedente:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Creare un manifesto di pod denominato `azure-restored.yaml` e specificare l'URI del disco ottenuto nel passaggio precedente. L'esempio seguente crea un server Web NGINX di base, con il disco ripristinato montato come volume in */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Creare il pod con il comando [kubectl apply][kubectl-apply], come illustrato nell'esempio seguente:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

È possibile usare `kubectl describe pod mypodrestored` per visualizzare i dettagli del pod, come nell'esempio sintetico seguente che mostra le informazioni sul volume:

```console
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>Passaggi successivi

Per le procedure consigliate associate, vedere [Procedure consigliate per archiviazione e backup nel servizio Azure Kubernetes][operator-best-practices-storage].

Altre informazioni sui volumi permanenti Kubernetes che usano i dischi di Azure.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes per dischi di Azure][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
