---
title: Usare i driver CSI (container Storage Interface) per File di Azure in Azure Kubernetes Service (AKS)
description: Informazioni su come usare i driver CSI (container Storage Interface) per File di Azure in un cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: b29f4034b12ce43e6c051e454601f196365469f3
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636981"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Usare i driver CSI (container Storage Interface) di File di Azure in Azure Kubernetes Service (AKS) (anteprima)

Il driver CSI (container Storage Interface) di File di Azure è un driver conforme a [specifiche CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md)usato da Azure Kubernetes Service (AKS) per gestire il ciclo di vita delle condivisioni di file di Azure.

CSI è uno standard per l'esposizione di sistemi di archiviazione di file e blocchi arbitrari ai carichi di lavoro in contenitori in Kubernetes. Con l'adozione e l'uso di CSI, AKS ora può scrivere, distribuire ed eseguire l'iterazione dei plug-in per esporre nuovi o migliorare i sistemi di archiviazione esistenti in Kubernetes senza dover toccare il codice Kubernetes principale e attendere i cicli di rilascio.

Per creare un cluster AKS con supporto driver CSI, vedere [Enable CSI Drivers for Azure disks and file di Azure on AKS](csi-storage-drivers.md).

>[!NOTE]
> I *driver nell'albero* fanno riferimento ai driver di archiviazione correnti che fanno parte del codice Kubernetes principale rispetto ai nuovi driver CSI, che sono plug-in.

## <a name="use-a-persistent-volume-with-azure-files"></a>Usare un volume permanente con File di Azure

Un [volume permanente (PV)](concepts-storage.md#persistent-volumes) rappresenta una parte di archiviazione di cui è stato effettuato il provisioning per l'uso con i pod Kubernetes. Un PV può essere usato da uno o più POD e può essere sottoposta a provisioning in modo dinamico o statico. Se più POD necessitano di accesso simultaneo allo stesso volume di archiviazione, è possibile usare File di Azure per connettersi usando il [protocollo SMB (Server Message Block)][smb-overview]. Questo articolo illustra come creare in modo dinamico una condivisione File di Azure per l'uso da parte di più pod in un cluster AKS. Per il provisioning statico, vedere [creare e usare manualmente un volume con una condivisione file di Azure](azure-files-volume.md).

Per altre informazioni sui volumi Kubernetes, vedere [Opzioni di archiviazione per le applicazioni nel servizio Azure Kubernetes][concepts-storage].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>Creare in modo dinamico File di Azure PVs usando le classi di archiviazione predefinite

Una classe di archiviazione viene usata per definire la modalità di creazione di una condivisione di File di Azure. Viene creato automaticamente un account di archiviazione nel [gruppo di risorse nodo][node-resource-group] da usare con la classe di archiviazione per conservare le condivisioni di file di Azure. Scegliere uno degli SKU di [ridondanza di archiviazione di Azure][storage-skus] seguenti per *SKUName* :

* **Standard_LRS** : archiviazione con ridondanza locale standard
* **Standard_GRS** : archiviazione con ridondanza geografica standard
* **Standard_ZRS** : archiviazione con ridondanza della zona standard
* **Standard_RAGRS** : archiviazione con ridondanza geografica e accesso in lettura standard
* **Premium_LRS** : archiviazione con ridondanza locale Premium

> [!NOTE]
> File di Azure supporta archiviazione Premium di Azure. La condivisione file Premium minima è di 100 GB.

Quando si usano i driver CSI di archiviazione su AKS, sono disponibili altre due funzionalità predefinite `StorageClasses` che usano i driver di archiviazione di file di Azure CSI. Le classi di archiviazione CSI aggiuntive vengono create con il cluster insieme alle classi di archiviazione predefinite nell'albero.

- `azurefile-csi`: Usa l'archiviazione standard di Azure per creare una condivisione File di Azure.
- `azurefile-csi-premium`: Usa archiviazione Premium di Azure per creare una condivisione File di Azure.

Il criterio di rimborso su entrambe le classi di archiviazione garantisce che la condivisione File di Azure sottostante venga eliminata quando viene eliminato il rispettivo PV. Anche le classi di archiviazione configurano le condivisioni file in modo che siano espandibili. è sufficiente modificare l'attestazione del volume permanente (PVC) con le nuove dimensioni.

Per usare queste classi di archiviazione, creare un [PVC](concepts-storage.md#persistent-volume-claims) e il rispettivo pod che vi faccia riferimento e li usi. Un PVC viene usato per effettuare automaticamente il provisioning dell'archiviazione in base a una classe di archiviazione. Un PVC può usare una delle classi di archiviazione create in precedenza o una classe di archiviazione definita dall'utente per creare una condivisione di File di Azure per lo SKU e le dimensioni desiderate. Quando si crea una definizione di Pod, viene specificato il PVC per richiedere l'archiviazione desiderata.

Creare un [esempio di PVC e pod che stampa la data corrente in `outfile` un oggetto](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) con il comando [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Quando il Pod si trova nello stato in esecuzione, è possibile verificare che la condivisione file sia montata correttamente eseguendo il comando seguente e verificando che l'output contenga `outfile` :

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

Il driver File di Azure CSI supporta la creazione [di snapshot dei volumi persistenti](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) e delle condivisioni file sottostanti.

Creare una [classe snapshot del volume](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) con il comando [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Creare uno [snapshot del volume](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) dal PVC [creato dinamicamente all'inizio di questa esercitazione](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes), `pvc-azurefile` .


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

## <a name="resize-a-persistent-volume"></a>Ridimensionare un volume permanente

È possibile richiedere un volume maggiore per un PVC. Modificare l'oggetto PVC e specificare una dimensione maggiore. Questa modifica attiva l'espansione del volume sottostante che esegue il backup del fotovoltaico.

> [!NOTE]
> Un nuovo PV non viene mai creato per soddisfare l'attestazione. Viene invece ridimensionato un volume esistente.

In AKS la classe di archiviazione predefinita `azurefile-csi` supporta già l'espansione, quindi usare il [PVC creato in precedenza con questa classe di archiviazione](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes). Il PVC ha richiesto una condivisione file 100Gi. È possibile confermare che eseguendo:

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

Verificare che sia il PVC che la file system all'interno del Pod mostrino le nuove dimensioni:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```


## <a name="nfs-file-shares"></a>Condivisioni file NFS
[File di Azure ora dispone del supporto per il protocollo NFS v 4.1](../storage/files/storage-files-how-to-create-nfs-shares.md). Il supporto per NFS 4,1 per File di Azure fornisce una file system NFS completamente gestita come un servizio basato su una piattaforma di archiviazione resiliente distribuita a disponibilità elevata e durevole.

 Questa opzione è ottimizzata per carichi di lavoro con accesso casuale con aggiornamenti dei dati sul posto e fornisce il supporto completo per file system POSIX. Questa sezione illustra come usare le condivisioni NFS con il driver CSI per file di Azure in un cluster AKS.

Assicurarsi di verificare le [limitazioni](../storage/files/storage-files-compare-protocols.md#limitations) e la [disponibilità dell'area](../storage/files/storage-files-compare-protocols.md#regional-availability) durante la fase di anteprima.

### <a name="register-the-allownfsfileshares-preview-feature"></a>Registrare la `AllowNfsFileShares` funzionalità di anteprima

Per creare una condivisione file che sfrutti NFS 4,1, è necessario abilitare il `AllowNfsFileShares` flag funzionalità per la sottoscrizione.

Registrare il `AllowNfsFileShares` flag funzionalità usando il comando [AZ feature Register][az-feature-register] , come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace "Microsoft.Storage" --name "AllowNfsFileShares"
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. Verificare lo stato della registrazione usando il comando [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowNfsFileShares')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft. storage* usando il comando [AZ provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.Storage
```

### <a name="create-a-storage-account-for-the-nfs-file-share"></a>Creare un account di archiviazione per la condivisione file NFS

[Creazione di un `Premium_LRS` Account di archiviazione di Azure](../storage/files/storage-how-to-create-premium-fileshare.md) con le configurazioni seguenti per supportare le condivisioni NFS:
- tipo di account: filestorage
- trasferimento sicuro obbligatorio (Abilita solo traffico HTTPS): false
- Selezionare la rete virtuale dei nodi agente in firewall e reti virtuali, in modo che sia preferibile creare l'account di archiviazione nel gruppo di risorse MC_.

### <a name="create-nfs-file-share-storage-class"></a>Crea classe di archiviazione condivisione file NFS

Salvare un `nfs-sc.yaml` file con il manifesto riportato di seguito, modificando i rispettivi segnaposto.

```yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azurefile-csi-nfs
provisioner: file.csi.azure.com
parameters:
  resourceGroup: EXISTING_RESOURCE_GROUP_NAME  # optional, required only when storage account is not in the same resource group as your agent nodes
  storageAccount: EXISTING_STORAGE_ACCOUNT_NAME
  protocol: nfs
```

Dopo aver modificato e salvato il file, creare la classe di archiviazione con il comando [kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>Creare una distribuzione con una condivisione file supportata da NFS
È possibile distribuire un [set con stato](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) di esempio che salva i timestamp in un file `data.txt` distribuendo il comando seguente con il comando [kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

Convalidare il contenuto del volume eseguendo:

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

>[!NOTE]
> Si noti che poiché la condivisione file NFS si trova in un account Premium, le dimensioni minime della condivisione file sono di 100 GB. Se si crea un PVC con dimensioni di archiviazione ridotte, è possibile che venga visualizzato un errore "Impossibile creare la condivisione file... dimensioni (5)... ".


## <a name="windows-containers"></a>Contenitori Windows

Il driver File di Azure CSI supporta anche i nodi e i contenitori di Windows. Se si vogliono usare i contenitori di Windows, seguire l' [esercitazione sui contenitori di Windows](windows-container-cli.md) per aggiungere un pool di nodi Windows.

Quando si dispone di un pool di nodi di Windows, usare le classi di archiviazione predefinite, ad esempio `azurefile-csi` o creare quelle personalizzate. È possibile distribuire un set di esempio con [stato basato su Windows](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) che salva i timestamp in un file `data.txt` distribuendo il comando seguente con il comando [kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
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

- Per informazioni su come usare i driver CSI per i dischi di Azure, vedere [usare dischi di Azure con driver CSI](azure-disk-csi.md).
- Per altre informazioni sulle procedure consigliate per l'archiviazione, vedere procedure consigliate [per l'archiviazione e i backup nel servizio Azure Kubernetes][operator-best-practices-storage].


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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register&preserve-view=true
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md
