---
title: Usare i driver CSI (container Storage Interface) per i dischi di Azure in Azure Kubernetes Service (AKS)
description: Informazioni su come usare i driver CSI (container Storage Interface) per i dischi di Azure in un cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 4b5ccd2712a95f5f020daa0161f1b5908a38a62e
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422053"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Usare i driver CSI (disk container Storage Interface) di Azure in Azure Kubernetes Service (AKS) (anteprima)
Il driver CSI per dischi di Azure è un driver conforme a [specifiche CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md) usato da AKS per gestire il ciclo di vita dei dischi di Azure. 

L'interfaccia CSI (container Storage Interface) è uno standard per l'esposizione di sistemi di archiviazione di blocchi e file arbitrari ai carichi di lavoro in contenitori in Kubernetes. Grazie all'adozione e all'uso di CSI, Azure Kubernetes Service (AKS) può scrivere, distribuire ed eseguire l'iterazione di plug-in che espongono nuovi o migliorati sistemi di archiviazione esistenti in Kubernetes senza dover toccare il codice Kubernetes principale e attendere i cicli di rilascio.

Per creare un cluster AKS con supporto driver CSI, vedere [Enable CSI Drivers for Azure disks and file di Azure on AKS](csi-storage-drivers.md).

>[!NOTE]
> *"Driver all'interno dell'albero"* si riferisce ai driver di archiviazione correnti che fanno parte del codice kubernetes principale rispetto ai nuovi driver CSI che sono plug-in.

## <a name="use-csi-persistent-volumes-pv-with-azure-disks"></a>Usare i volumi con dischi permanenti CSI (PV) con dischi di Azure 

Un [volume permanente](concepts-storage.md#persistent-volumes) rappresenta una parte di archiviazione di cui è stato effettuato il provisioning per l'uso con Pod Kubernetes. Un volume permanente può essere usato da uno o più pod e se ne può eseguire il provisioning in modo dinamico o in modo statico. Questo articolo mostra come creare in modo dinamico volumi persistenti con i dischi di Azure per l'uso con un singolo pod in un cluster del servizio Azure Kubernetes. Per il provisioning statico, vedere [creare e usare manualmente un volume con dischi di Azure](azure-disk-volume.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Per altre informazioni sui volumi Kubernetes, vedere [Opzioni di archiviazione per le applicazioni nel servizio Azure Kubernetes][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes"></a>Creare in modo dinamico il disco di Azure PVs usando le classi di archiviazione predefinite

Una classe di archiviazione viene usata per definire la creazione dinamica di un'unità di archiviazione con un volume permanente. Per altre informazioni sulle classi di archiviazione Kubernetes, vedere [Kubernetes Storage Classes][kubernetes-storage-classes] (Classi di archiviazione Kubernetes). Quando si usano i driver CSI di archiviazione nel servizio contenitore di Azure, sono disponibili due componenti aggiuntivi incorporati `StorageClasses` che sfruttano i **driver di archiviazione di Azure disk CSI**. Le classi di archiviazione CSI aggiuntive vengono create con il cluster insieme alle classi di archiviazione predefinite nell'albero.

>[!NOTE]
> *"Driver nell'albero"* si riferisce ai driver di archiviazione correnti che fanno parte del codice kubernetes principale e dei driver CSI che sono plug-in.

- `managed-csi` : Usa l'archiviazione con ridondanza locale di Azure StandardSSD (con ridondanza locale) per creare un disco gestito.
- `managed-csi-premium` : Usa l'archiviazione con ridondanza locale di Azure Premium (con ridondanza locale) per creare un disco gestito. 

Il criterio di rimborso in entrambe le classi di archiviazione garantisce che il disco di Azure sottostante venga eliminato quando viene eliminato il rispettivo volume permanente. Anche le classi di archiviazione configurano i volumi permanenti in modo che siano espandibili. è sufficiente modificare l'attestazione del volume permanente con le nuove dimensioni.

Per sfruttare queste classi di archiviazione, è sufficiente creare un' [attestazione di volume permanente (PVC)](concepts-storage.md#persistent-volume-claims) e il rispettivo pod che vi fa riferimento e li utilizza. Un'attestazione di volume permanente viene usata per il provisioning automatico dell'archiviazione in una classe di archiviazione. Un PVC può usare una delle classi di archiviazione create in precedenza o una classe di archiviazione definita dall'utente per creare un disco gestito da Azure per lo SKU e le dimensioni desiderate. Quando si crea una definizione di pod, viene specificata l'attestazione di volume permanente per richiedere le risorse di archiviazione desiderate.

Creare un pod di esempio e l'attestazione del volume permanente corrispondente con il comando [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

Quando il Pod si trova nello stato in esecuzione, creare un nuovo file denominato `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

È ora possibile convalidare che il disco sia montato correttamente eseguendo il comando seguente e verificando che il file sia visualizzato `test.txt` nell'output: 

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Creare una classe di archiviazione personalizzata

Le classi di archiviazione predefinite soddisfano gli scenari più comuni, ma non tutte. In alcuni casi, potrebbe essere necessario personalizzare la propria classe di archiviazione con i propri parametri. Per esemplificare, esiste uno scenario in cui potrebbe essere necessario modificare `volumeBindingMode` . 

Le classi di archiviazione predefinite utilizzano un oggetto `volumeBindingMode: Immediate` che garantisce che si verifichi immediatamente dopo la creazione del PersistentVolumeClaim. Nei casi in cui i pool di nodi sono vincolati alla topologia, ad esempio usando zone di disponibilità, i volumi permanenti verrebbero associati o sottoposti a provisioning senza conoscere i requisiti di pianificazione del Pod, in questo caso in una zona specifica.

Per risolvere questo scenario, è possibile usare `volumeBindingMode: WaitForFirstConsumer` , che ritarderà l'associazione e il provisioning di un PersistentVolume fino a quando non viene creato un pod che usa il PersistentVolumeClaim. In questo modo, il PV sarà conforme e verrà eseguito il provisioning nella zona di disponibilità (o in un'altra topologia) specificata dai vincoli di pianificazione del Pod. 

Creare un file denominato `sc-azuredisk-csi-waitforfirstconsumer.yaml` e incollare il manifesto riportato di seguito.
La classe di archiviazione corrisponde alla `managed-csi` classe di archiviazione, ma con un diverso `volumeBindingMode` . 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

Creare la classe di archiviazione con il comando [kubectl Apply][kubectl-apply] e specificare il `sc-azuredisk-csi-waitforfirstconsumer.yaml` file:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Snapshot del volume

Il driver CSI per dischi di Azure supporta la creazione [di snapshot di volumi permanenti](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html). Come parte di questa funzionalità, il driver può eseguire snapshot *completi* o [ *incrementali* ](../virtual-machines/windows/disks-incremental-snapshots.md) a seconda del valore impostato nel `incremental` parametro (per impostazione predefinita è true). 

Per informazioni dettagliate su tutti i parametri, vedere [parametri della classe snapshot del volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass).

### <a name="create-a-volume-snapshot"></a>Creazione di uno snapshot del volume

Per esemplificare questa funzionalità, creare una [classe snapshot del volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) con il comando [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

A questo punto è possibile creare uno [snapshot del volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) dal PVC [creato dinamicamente all'inizio di questa esercitazione](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes) `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Verificare che lo snapshot sia stato creato correttamente:

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Creare un nuovo PVC basato su uno snapshot del volume

È possibile creare un nuovo PVC basato su uno snapshot del volume. Usare lo snapshot creato nel passaggio precedente e creare un [nuovo PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) e un [nuovo pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) per utilizzarlo.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Infine, verificare che sia lo stesso PVC creato prima controllando il contenuto.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Come previsto, è ancora possibile visualizzare il file creato in precedenza `test.txt` .

## <a name="clone-volumes"></a>Clona volumi

Un volume clonato viene definito come duplicato di un volume Kubernetes esistente. Per ulteriori informazioni sulla clonazione dei volumi in Kubernetes, vedere la documentazione concettuale per la [clonazione](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)dei volumi.

Il driver CSI per i dischi di Azure supporta la clonazione dei volumi. Per illustrare, creare un [volume clonato](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) del [creato in precedenza](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes) `azuredisk-pvc` e di [un nuovo pod per utilizzarlo](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml).



```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

È ora possibile controllare il contenuto del volume clonato eseguendo il seguente e confermando che il file creato è ancora visibile `test.txt` .

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume-pv"></a>Ridimensionare un volume permanente (PV)

È invece possibile richiedere un volume maggiore per un PVC. Modificare l'oggetto PVC e specificare una dimensione maggiore. Questa modifica attiva l'espansione del volume sottostante che esegue il backup del PersistentVolume. 

> [!NOTE] 
> Un nuovo PersistentVolume non viene mai creato per soddisfare l'attestazione. Viene invece ridimensionato un volume esistente.

In AKS la classe di archiviazione predefinita `managed-csi` consente già l'espansione, quindi sfruttare il [PVC creato in precedenza con questa classe di archiviazione](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes). Il PVC ha richiesto un volume permanente 10Gi, è possibile verificare che esegua:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```
> [!IMPORTANT]
> Attualmente, il driver CSI per dischi di Azure supporta solo il ridimensionamento di PVC senza Pod associato (e il volume non montato in un nodo specifico).

Di conseguenza, è possibile eliminare il Pod creato in precedenza:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

Espandere il PVC aumentando il `spec.resources.requests.storage` campo:

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

Verificare che il volume sia ora più grande:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE] 
> Il PVC non rifletterà le nuove dimensioni fino a quando non sarà ancora associato un pod.

Viene ora creato un nuovo pod:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

E infine verificare le dimensioni del PVC e all'interno del Pod: 
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

<!--- ## Shared disk

[Azure shared disks](../virtual-machines/windows/disks-shared.md) is an Azure managed disks feature that enables attaching an Azure disk to agent nodes simultaneously. Attaching a managed disk to multiple agent nodes allows you, for example, to deploy new or migrate existing clustered applications to Azure.

> [!IMPORTANT] Currently, only raw block device (`volumeMode: Block`) is supported by the Azure disk CSI driver. Applications should manage the coordination and control of writes, reads, locks, caches, mounts and fencing on the shared disk which is exposed as raw block device.

Let's create file called `shared-disk.yaml` by copying the below that contains the shared disk storage class and PVC:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

Create the storage class with the [kubectl apply][kubectl-apply] command and specify your `shared-disk.yaml` file:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Now let's create a file called `deployment-shared.yml` by copying the below:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: nginx
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Create the deployment with the [kubectl apply][kubectl-apply] command and specify your `deployment-shared.yml` file:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Finally, let's check the block device inside the pod:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp bash
root@deployment-sharedisk-7454978bc6-xh7jp:/# dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out
104857600 bytes (105 MB, 100 MiB) copied, 0.0502999 s, 2.1 GB/s
```
-->

## <a name="windows-containers"></a>Contenitori di Windows

Il driver CSI per dischi di Azure supporta anche i nodi e i contenitori di Windows. Se si desidera utilizzare i contenitori di Windows, seguire l' [esercitazione sui contenitori di Windows](windows-container-cli.md) per aggiungere un pool di nodi Windows.

Quando si dispone di un pool di nodi di Windows, è ora possibile sfruttare solo le classi di archiviazione predefinite, ad esempio `managed-csi` . È possibile distribuire un set di esempio con [stato basato su Windows](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) che salva i timestamp in un file `data.txt` distribuendo il comando seguente con il comando [kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

È ora possibile convalidare il contenuto del volume eseguendo:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come usare il driver CSI per file di Azure, vedere [usare file di Azure con driver CSI](azure-files-csi.md).
- Per altre informazioni sulle procedure consigliate per l'archiviazione, vedere procedure consigliate [per l'archiviazione e i backup in Azure Kubernetes Service (AKS)][operator-best-practices-storage]


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
[premium-storage]: ../virtual-machines/windows/disks-types.md
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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register