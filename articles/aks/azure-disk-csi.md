---
title: Usare i driver CSI (container Storage Interface) per i dischi di Azure in Azure Kubernetes Service (AKS)
description: Informazioni su come usare i driver CSI (container Storage Interface) per i dischi di Azure in un cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 260631e36d113b6ccd190f66ce61caa7ba1b187b
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900892"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Usare i driver CSI (disk container Storage Interface) di Azure in Azure Kubernetes Service (AKS) (anteprima)
Il driver CSI (disk container Storage Interface) di Azure è un driver conforme a [specifiche CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md)usato da Azure Kubernetes Service (AKS) per gestire il ciclo di vita dei dischi di Azure.

CSI è uno standard per l'esposizione di sistemi di archiviazione di file e blocchi arbitrari ai carichi di lavoro in contenitori in Kubernetes. Adottando e usando CSI, AKS può scrivere, distribuire ed eseguire l'iterazione dei plug-in per esporre nuovi sistemi di archiviazione esistenti in Kubernetes senza dover toccare il codice Kubernetes principale e attendere i cicli di rilascio.

Per creare un cluster AKS con supporto driver CSI, vedere [Enable CSI Drivers for Azure disks and file di Azure on AKS](csi-storage-drivers.md).

>[!NOTE]
> I *driver nell'albero* fanno riferimento ai driver di archiviazione correnti che fanno parte del codice Kubernetes principale rispetto ai nuovi driver CSI, che sono plug-in.

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>Usare volumi persistenti CSI con dischi di Azure

Un [volume permanente](concepts-storage.md#persistent-volumes) (PV) rappresenta una parte di archiviazione di cui è stato effettuato il provisioning per l'uso con i pod Kubernetes. Un PV può essere usato da uno o più POD e può essere sottoposta a provisioning in modo dinamico o statico. Questo articolo illustra come creare in modo dinamico PVs con dischi di Azure per l'uso da un singolo pod in un cluster AKS. Per il provisioning statico, vedere [creare e usare manualmente un volume con dischi di Azure](azure-disk-volume.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Per altre informazioni sui volumi Kubernetes, vedere [Opzioni di archiviazione per le applicazioni nel servizio Azure Kubernetes][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>Creare dinamicamente il disco di Azure PVs usando le classi di archiviazione predefinite

Una classe di archiviazione viene usata per definire la creazione dinamica di un'unità di archiviazione con un volume permanente. Per altre informazioni sulle classi di archiviazione Kubernetes, vedere [classi di archiviazione Kubernetes][kubernetes-storage-classes]. Quando si usano i driver CSI di archiviazione su AKS, sono disponibili altre due funzionalità predefinite `StorageClasses` che usano i driver di archiviazione di Azure disk CSI. Le classi di archiviazione CSI aggiuntive vengono create con il cluster insieme alle classi di archiviazione predefinite nell'albero.

- `managed-csi`: USA Azure SDD Standard archiviazione con ridondanza locale (con ridondanza locale) per creare un disco gestito.
- `managed-csi-premium`: USA Azure Premium con ridondanza locale per creare un disco gestito.

Il criterio di rimborso in entrambe le classi di archiviazione garantisce che il disco di Azure sottostante venga eliminato quando viene eliminato il rispettivo PV. Anche le classi di archiviazione configurano il PVs in modo che sia espandibile. È sufficiente modificare l'attestazione del volume permanente (PVC) con le nuove dimensioni.

Per sfruttare queste classi di archiviazione, creare un [PVC](concepts-storage.md#persistent-volume-claims) e il rispettivo pod che vi faccia riferimento e li usi. Un PVC viene usato per effettuare automaticamente il provisioning dell'archiviazione in base a una classe di archiviazione. Un PVC può usare una delle classi di archiviazione create in precedenza o una classe di archiviazione definita dall'utente per creare un disco gestito da Azure per lo SKU e le dimensioni desiderate. Quando si crea una definizione di Pod, viene specificato il PVC per richiedere l'archiviazione desiderata.

Creare un pod di esempio e il rispettivo PVC con il comando [kubectl Apply][kubectl-apply] :

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

È ora possibile verificare che il disco sia montato correttamente eseguendo il comando seguente e verificando che il file sia visualizzato `test.txt` nell'output:

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Creare una classe di archiviazione personalizzata

Le classi di archiviazione predefinite soddisfano gli scenari più comuni, ma non tutte. In alcuni casi, potrebbe essere necessario personalizzare la propria classe di archiviazione con i propri parametri. Ad esempio, è presente uno scenario in cui potrebbe essere necessario modificare la `volumeBindingMode` classe.

Le classi di archiviazione predefinite usano una `volumeBindingMode: Immediate` classe che garantisce che si verifichi immediatamente dopo la creazione del PVC. Nei casi in cui i pool di nodi sono vincolati alla topologia, ad esempio usando le zone di disponibilità, PVs viene associato o sottoposto a provisioning senza conoscere i requisiti di pianificazione del Pod, in questo caso in una zona specifica.

Per risolvere questo scenario, è possibile usare `volumeBindingMode: WaitForFirstConsumer` , che ritarda l'associazione e il provisioning di un PV fino a quando non viene creato un pod che usa il PVC. In questo modo, il PV sarà conforme e verrà eseguito il provisioning nella zona di disponibilità (o in un'altra topologia) specificata dai vincoli di pianificazione del Pod.

Creare un file denominato `sc-azuredisk-csi-waitforfirstconsumer.yaml` e incollare il manifesto seguente.
La classe di archiviazione è identica alla `managed-csi` classe di archiviazione, ma con una `volumeBindingMode` classe diversa.

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

Il driver CSI per dischi di Azure supporta la creazione [di snapshot di volumi permanenti](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html). Come parte di questa funzionalità, il driver può eseguire snapshot *completi* o [ *incrementali*](../virtual-machines/windows/disks-incremental-snapshots.md) a seconda del valore impostato nel `incremental` parametro (per impostazione predefinita, è true).

Per informazioni dettagliate su tutti i parametri, vedere [parametri della classe snapshot del volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass).

### <a name="create-a-volume-snapshot"></a>Creazione di uno snapshot del volume

Per un esempio di questa funzionalità, creare una [classe snapshot del volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) con il comando [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

A questo punto è possibile creare uno [snapshot del volume](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) dal PVC [creato dinamicamente all'inizio di questa esercitazione](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `pvc-azuredisk` .


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

Il driver CSI per i dischi di Azure supporta la clonazione dei volumi. Per illustrare, creare un [volume clonato](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) del [creato in precedenza](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `azuredisk-pvc` e di [un nuovo pod per utilizzarlo](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml).


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

È ora possibile controllare il contenuto del volume clonato eseguendo il comando seguente e confermare che il file creato è ancora visibile `test.txt` .

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>Ridimensionare un volume permanente

È invece possibile richiedere un volume maggiore per un PVC. Modificare l'oggetto PVC e specificare una dimensione maggiore. Questa modifica attiva l'espansione del volume sottostante che esegue il backup del fotovoltaico.

> [!NOTE]
> Un nuovo PV non viene mai creato per soddisfare l'attestazione. Viene invece ridimensionato un volume esistente.

In AKS la classe di archiviazione predefinita `managed-csi` consente già l'espansione, quindi usare il [PVC creato in precedenza con questa classe di archiviazione](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes). Il PVC ha richiesto un volume permanente di 10 gi. È possibile confermare che eseguendo:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> Attualmente, il driver CSI per dischi di Azure supporta solo il ridimensionamento di PVC senza Pod associato (e il volume non montato in un nodo specifico).

Di conseguenza, eliminare il Pod creato in precedenza:

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

Infine, confermare le dimensioni del PVC e all'interno del Pod:
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>Disco condiviso

[Azure Shared](../virtual-machines/windows/disks-shared.md) disks è una funzionalità di Azure Managed disks che consente di allegare simultaneamente un disco di Azure ai nodi degli agenti. Il fissaggio di un disco gestito a più nodi dell'agente consente, ad esempio, di distribuire nuove applicazioni in cluster o di eseguirne la migrazione in Azure.

> [!IMPORTANT] 
> Attualmente, `volumeMode: Block` il driver CSI per dischi di Azure supporta solo i dispositivi Block Raw (). Le applicazioni devono gestire il coordinamento e il controllo di Scritture, letture, blocchi, cache, montaggi e schermatura sul disco condiviso, esposto come dispositivo a blocchi non elaborato.

Creare un file denominato `shared-disk.yaml` copiando il comando seguente che contiene la classe di archiviazione su disco condiviso e il PVC:

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

Creare la classe di archiviazione con il comando [kubectl Apply][kubectl-apply] e specificare il `shared-disk.yaml` file:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

A questo punto, creare un file denominato `deployment-shared.yml` copiando il comando seguente:

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
          image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Creare la distribuzione con il comando [kubectl Apply][kubectl-apply] e specificare il `deployment-shared.yml` file:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Infine, controllare il dispositivo a blocchi all'interno del Pod:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Contenitori Windows

Il driver CSI per dischi di Azure supporta anche i nodi e i contenitori di Windows. Se si vogliono usare i contenitori di Windows, seguire l' [esercitazione sui contenitori di Windows](windows-container-cli.md) per aggiungere un pool di nodi Windows.

Dopo aver creato un pool di nodi di Windows, è ora possibile usare le classi di archiviazione predefinite, ad esempio `managed-csi` . È possibile distribuire un set di esempio con [stato basato su Windows](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) che salva i timestamp nel file `data.txt` distribuendo il comando seguente con il comando [kubectl Apply][kubectl-apply] :

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

- Per informazioni su come usare i driver CSI per File di Azure, vedere [usare file di Azure con i driver CSI](azure-files-csi.md).
- Per altre informazioni sulle procedure consigliate per l'archiviazione, vedere procedure consigliate [per l'archiviazione e i backup nel servizio Azure Kubernetes][operator-best-practices-storage].


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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register