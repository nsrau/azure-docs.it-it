---
title: Usare i driver CSI (container Storage Interface) per File di Azure in Azure Kubernetes Service (AKS)
description: Informazioni su come usare i driver CSI (container Storage Interface) per File di Azure in un cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 018275b6db4c2d2d1059f35077f74a6f45ec3ba9
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422050"
---
# <a name="use-the-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Usare i driver CSI (container Storage Interface) di File di Azure in Azure Kubernetes Service (AKS) (anteprima)
Il driver File di Azure CSI è un driver conforme alla [specifica CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md) usato da AKS per gestire il ciclo di vita delle condivisioni di file di Azure. 

L'interfaccia CSI (container Storage Interface) è uno standard per l'esposizione di sistemi di archiviazione di blocchi e file arbitrari ai carichi di lavoro in contenitori in Kubernetes. Grazie all'adozione e all'uso di CSI, Azure Kubernetes Service (AKS) ora è in grado di scrivere, distribuire ed eseguire l'iterazione di plug-in che espongono nuovi o migliorati sistemi di archiviazione esistenti in Kubernetes senza dover toccare il codice Kubernetes principale e attendere i cicli di rilascio.

Per creare un cluster AKS con supporto driver CSI, vedere [Enable CSI Drivers for Azure disks and file di Azure on AKS](csi-storage-drivers.md).

>[!NOTE]
> *"Driver all'interno dell'albero"* si riferisce ai driver di archiviazione correnti che fanno parte del codice kubernetes principale rispetto ai nuovi driver CSI che sono plug-in.

## <a name="use-a-persistent-volume-pv-with-azure-files"></a>Usare un volume permanente (PV) con File di Azure

Un [volume permanente](concepts-storage.md#persistent-volumes) rappresenta una parte di archiviazione di cui è stato effettuato il provisioning per l'uso con Pod Kubernetes. Un volume permanente può essere usato da uno o più pod e se ne può eseguire il provisioning in modo dinamico o in modo statico. Se più pod necessitano di accesso simultaneo allo stesso volume di archiviazione, è possibile usare File di Azure per connettersi usando il [protocollo Server Message Block (SMB)][smb-overview]. Questo articolo illustra come creare in modo dinamico una condivisione di file di Azure per l'uso da più POD in un cluster del servizio Azure Kubernetes. Per il provisioning statico, vedere [creare e usare manualmente un volume con file di Azure condivisione](azure-files-volume.md).

Per altre informazioni sui volumi Kubernetes, vedere [Opzioni di archiviazione per le applicazioni nel servizio Azure Kubernetes][concepts-storage].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-using-the-built-in-storage-classes"></a>Crea dinamicamente File di Azure PVs usando le classi di archiviazione predefinite
Una classe di archiviazione permette di definire come creare una condivisione file di Azure. Nel [gruppo di risorse nodo][node-resource-group] viene creato automaticamente un account di archiviazione da usare insieme alla classe di archiviazione per contenere le condivisioni file di Azure. Scegliere la [ridondanza dell'archiviazione di Azure][storage-skus] seguente per *skuName*:

* Archiviazione con ridondanza locale *Standard_LRS* standard
* Archiviazione con ridondanza geografica standard *Standard_GRS*
* *Standard_ZRS* -archiviazione con ridondanza della zona standard
* Archiviazione con ridondanza geografica e accesso in lettura standard *Standard_RAGRS*
* Archiviazione con ridondanza locale di *Premium_LRS* Premium

> [!NOTE]
> File di Azure supportano archiviazione Premium, la condivisione file Premium minima è 100 GB.

Quando si usano i driver CSI di archiviazione su AKS, sono disponibili 2 componenti aggiuntivi `StorageClasses` che sfruttano i **driver di archiviazione di file di Azure CSI**. Le classi di archiviazione CSI aggiuntive vengono create con il cluster insieme alle classi di archiviazione predefinite nell'albero.

- `azurefile-csi` : Usa l'archiviazione standard di Azure per creare una condivisione file di Azure. 
- `azurefile-csi-premium` -Usa archiviazione Premium di Azure per creare una condivisione file di Azure. 

Il criterio di rimborso su entrambe le classi di archiviazione garantisce che la condivisione file di Azure sottostante venga eliminata quando viene eliminato il rispettivo volume permanente. Anche le classi di archiviazione configurano le condivisioni file in modo che siano espandibili. è sufficiente modificare l'attestazione del volume permanente con le nuove dimensioni.

Per sfruttare queste classi di archiviazione, creare un' [attestazione di volume permanente (PVC)](concepts-storage.md#persistent-volume-claims) e il rispettivo pod che vi fa riferimento e li utilizza. Un'attestazione di volume permanente viene usata per il provisioning automatico dell'archiviazione in una classe di archiviazione. Un PVC può usare una delle classi di archiviazione create in precedenza o una classe di archiviazione definita dall'utente per creare una condivisione di File di Azure per lo SKU e le dimensioni desiderate. Quando si crea una definizione di pod, viene specificata l'attestazione di volume permanente per richiedere le risorse di archiviazione desiderate.

Creare un' [attestazione di volume permanente di esempio e un pod che stampa la `outfile` data corrente in un oggetto](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) con il comando [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Quando il Pod si trova nello stato in esecuzione, è possibile convalidare che la condivisione file sia montata correttamente eseguendo il comando seguente e verificando che l'output contenga `outfile` : 

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Creare una classe di archiviazione personalizzata

Le classi di archiviazione predefinite soddisfano gli scenari più comuni, ma non tutte. In alcuni casi, potrebbe essere necessario personalizzare la propria classe di archiviazione con i propri parametri. Usare, ad esempio, il manifesto seguente per configurare la `mountOptions` della condivisione file.

Il valore predefinito per *FileMode* e *dirMode* è *0777* per le condivisioni file montate in Kubernetes. È possibile specificare le diverse opzioni di montaggio nell'oggetto classe di archiviazione.

Creare un file denominato `azure-file-sc.yaml` e incollare il manifesto di esempio seguente: 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

Creare la classe di archiviazione con il comando [kubectl apply][kubectl-apply]:

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Il driver CSI per file di Azure supporta la creazione [di snapshot dei volumi persistenti](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) e delle condivisioni file sottostanti. 

Creare una [classe snapshot del volume](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) con il comando [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Creare uno [snapshot del volume](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) dal PVC [creato dinamicamente all'inizio di questa esercitazione](#dynamically-create-azure-files-pvs-using-the-built-in-storage-classes), `pvc-azurefile` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Verificare che lo snapshot sia stato creato correttamente:

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume-pv"></a>Ridimensionare un volume permanente (PV)

È possibile richiedere un volume maggiore per un PVC. Modificare l'oggetto PVC e specificare una dimensione maggiore. Questa modifica attiva l'espansione del volume sottostante che esegue il backup del PersistentVolume. 

> [!NOTE] 
> Un nuovo PersistentVolume non viene mai creato per soddisfare l'attestazione. Viene invece ridimensionato un volume esistente.

In AKS la classe di archiviazione predefinita `azurefile-csi` supporta già l'espansione, quindi è necessario sfruttare il [PVC creato in precedenza con questa classe di archiviazione](#dynamically-create-azure-files-pvs-using-the-built-in-storage-classes). Il PVC ha richiesto una condivisione file 100Gi, è possibile verificare che esegua:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Espandere il PVC aumentando il `spec.resources.requests.storage` campo:

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Verificare che il PVC e il file System all'interno del Pod mostrino le nuove dimensioni:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```

## <a name="windows-containers"></a>Contenitori di Windows

Il driver CSI per file di Azure supporta anche i nodi e i contenitori di Windows, se si vuole usare i contenitori di Windows, seguire l' [esercitazione sui contenitori](windows-container-cli.md) di Windows per aggiungere un pool di nodi Windows.

Quando si dispone di un pool di nodi di Windows, è possibile sfruttare le classi di archiviazione predefinite, ad esempio `azurefile-csi` o creare quelle personalizzate. È possibile distribuire un set di esempio con [stato basato su Windows](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) che salva i timestamp in un file `data.txt` distribuendo il comando seguente con il comando [kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

Convalidare il contenuto del volume eseguendo:

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come usare il driver CSI per i dischi di Azure, vedere [usare dischi di Azure con driver CSI](azure-disk-csi.md).
- Per altre informazioni sulle procedure consigliate per l'archiviazione, vedere procedure consigliate [per l'archiviazione e i backup in Azure Kubernetes Service (AKS)][operator-best-practices-storage]


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md