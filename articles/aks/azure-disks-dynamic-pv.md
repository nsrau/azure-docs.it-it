---
title: Usare un disco di Azure con AKS
description: Usare Dischi di Azure con il servizio contenitore di Azure
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/12/2018
ms.author: nepeters
ms.openlocfilehash: a4e4ce6a23f9f8a99d8ae5f9e4e2084e3b749017
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="persistent-volumes-with-azure-disks---dynamic-provisioning"></a>Volumi permanenti con i dischi di Azure: provisioning dinamico

Un volume permanente rappresenta una parte di risorsa di archiviazione di cui è stato eseguito il provisioning per l'uso in un cluster Kubernetes. Un volume permanente può essere usato da uno o più pod e se ne può eseguire il provisioning in modo dinamico o in modo statico. Questo documento illustra in dettaglio il provisioning dinamico di un disco di Azure come volume permanente Kubernetes in un cluster AKS. 

Per altre informazioni sui volumi Kubernetes permanenti, vedere [Kubernetes persistent volumes][kubernetes-volumes] (Volumi Kubernetes permanenti).

## <a name="built-in-storage-classes"></a>Classi di archiviazione predefinite

Una classe di archiviazione viene usata per definire la configurazione di un volume permanente creato dinamicamente. Per altre informazioni sulle classi di archiviazione Kubernetes, vedere [Kubernetes Storage Classes][kubernetes-storage-classes] (Classi di archiviazione Kubernetes).

Ogni cluster AKS include due classi di archiviazione predefinite, entrambe configurate per essere usate con i dischi di Azure. Per vederle, usare il comando `kubectl get storageclass`.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

Se queste classi di archiviazione soddisfano le esigenze, non è necessario crearne di nuove.

## <a name="create-storage-class"></a>Creazione della classe di archiviazione

Se si vuole creare una nuova classe di archiviazione configurata per i dischi di Azure, è possibile usare il manifesto di esempio seguente. 

Il valore `storageaccounttype` di `Standard_LRS` indica che viene creato un disco standard. Questo valore può essere modificato in `Premium_LRS` per creare un [disco Premium][premium-storage]. Per usare i dischi Premium, la macchina virtuale del nodo AKS deve avere una dimensione compatibile con i dischi Premium. Per un elenco delle dimensioni compatibili, vedere [questo documento][premium-storage].

```yaml
apiVersion: storage.k8s.io/v1beta1
kind: StorageClass
metadata:
  name: azure-managed-disk
provisioner: kubernetes.io/azure-disk
parameters:
  kind: Managed
  storageaccounttype: Standard_LRS
```



## <a name="create-persistent-volume-claim"></a>Creare un'attestazione di volume permanente

Un'attestazione di volume permanente usa un oggetto classe di archiviazione per il provisioning dinamico di una parte di risorsa di archiviazione. Quando si usa un disco di Azure, questo viene creato nello stesso gruppo di risorse del servizio contenitore di Azure.

Questo manifesto di esempio crea un'attestazione di volume permanente usando la classe di archiviazione `azure-managed-disk` per creare un disco da `5GB` con accesso `ReadWriteOnce`. Per altre informazioni sulle modalità di accesso PVC, vedere [Access Modes][access-modes] (Modalità di accesso).

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

## <a name="using-the-persistent-volume"></a>Uso del volume permanente

Dopo aver creato l'attestazione di volume permanente e aver eseguito il provisioning del disco, è possibile creare un pod con accesso al disco. Il manifesto seguente crea un pod che usa l'attestazione di volume permanente `azure-managed-disk` per montare il disco di Azure nel percorso `/var/www/html`. 

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui volumi permanenti Kubernetes che usano i dischi di Azure.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes per i dischi di Azure][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[premium-storage]: ../virtual-machines/windows/premium-storage.md