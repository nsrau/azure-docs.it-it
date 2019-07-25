---
title: Creare un volume statico per i pod nel servizio Azure Kubernetes
description: Informazioni su come creare manualmente un volume con i dischi di Azure per l'uso con un pod nel servizio Azure Kubernetes
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 9017c8cf721fbb9c493dc18da769b9d6e83ddf05
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "67616127"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Creare manualmente e usare un volume con i dischi di Azure nel servizio Azure Kubernetes

Le applicazioni basate su contenitore hanno spesso necessità di accedere e salvare in modo permanente i dati in un volume di dati esterno. Se un singolo pod deve accedere all'archiviazione, è possibile usare i dischi di Azure per presentare un volume nativo per l'uso dell'applicazione. Questo articolo illustra come creare un manualmente un disco di Azure e collegarlo a un pod nel servizio Azure Kubernetes.

> [!NOTE]
> Un disco di Azure può essere installato solo su un singolo pod per volta. Se è necessario condividere un volume permanente tra più POD, usare [file di Azure][azure-files-volume].

Per altre informazioni sui volumi Kubernetes, vedere [Opzioni di archiviazione per le applicazioni in AKS][concepts-storage].

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster AKS esistente. Se è necessario un cluster AKS, vedere la Guida introduttiva di AKS [usando l'interfaccia della][aks-quickstart-cli] riga di comando di Azure o [l'portale di Azure][aks-quickstart-portal].

È necessaria anche l'interfaccia della riga di comando di Azure versione 2.0.59 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia][install-azure-cli]della riga di comando di Azure.

## <a name="create-an-azure-disk"></a>Creare un disco di Azure

Quando si crea un disco di Azure da usare con il servizio Kubernetes di Azure, è possibile creare la risorsa disco nel gruppo di risorse del **nodo**. Questo approccio consente al cluster del servizio Azure Kubernetes di accedere e gestire la risorsa disco. Se invece si crea il disco in un gruppo di risorse separato, è necessario fornire all'entità servizio del servizio Azure Kubernetes per il cluster il ruolo `Contributor` al gruppo di risorse del disco.

Per questo articolo, creare il disco nel gruppo di risorse del nodo. Per prima cosa, ottenere il nome del gruppo di risorse con il comando [AZ AKS Show][az-aks-show] e aggiungere il `--query nodeResourceGroup` parametro di query. L'esempio seguente ottiene il gruppo di risorse del nodo per il nome del cluster servizio Azure Kubernetes *myservizio Azure KubernetesCluster* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

A questo punto, creare un disco usando il comando [AZ disk create][az-disk-create] . Specificare il nome del gruppo di risorse del nodo ottenuto nel comando precedente e quindi un nome per la risorsa disco, ad esempio *myAKSDisk*. Nell'esempio seguente viene creato un disco da *20*GiB e viene restituito l'ID del disco dopo la creazione. Se è necessario creare un disco da usare con i contenitori di Windows Server (attualmente in anteprima in AKS), aggiungere `--os-type windows` il parametro per formattare correttamente il disco.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> I dischi di Azure vengono fatturati per SKU in base alle dimensioni specifiche. Questi SKU variano da 32GiB per i dischi S4 o P4 a 32TiB per S80 o P80 Disks (in anteprima). La velocità effettiva e le prestazioni delle operazioni di I/O al secondo per un disco gestito Premium dipendono sia dallo SKU sia dalla dimensione dell'istanza dei nodi nel cluster servizio Azure Kubernetes. Vedi [i prezzi e le prestazioni dei Managed disks][managed-disk-pricing-performance].

L'ID risorsa del disco viene visualizzato dopo aver completato correttamente il comando, come illustrato nell'output di esempio seguente. Questo ID del disco viene usato per montare il disco nel passaggio successivo.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Montare il disco come volume

Per montare il disco di Azure nel pod, configurare il volume nella specifica del contenitore. Creare un nuovo file denominato `azure-disk-pod.yaml` con il contenuto seguente. Aggiornare `diskName` con il nome del disco creato nel passaggio precedente e `diskURI` con l'ID del disco visualizzato nell'output del comando di creazione del disco. Se lo si desidera, annotare `mountPath`, che è il percorso in cui il disco di Azure viene montato nel pod. Per i contenitori di Windows Server (attualmente in anteprima in AKS), specificare un *mountPath* usando la convenzione di percorso di Windows, ad esempio *' d:'* .

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Usare il comando `kubectl` per creare il pod.

```console
kubectl apply -f azure-disk-pod.yaml
```

A questo punto è disponibile un pod in esecuzione con un disco di Azure montato in `/mnt/azure`. È possibile usare `kubectl describe pod mypod` per verificare che il disco sia montato correttamente. L'esempio sintetico di output seguente illustra il volume montato nel contenitore:

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>Passaggi successivi

Per le procedure consigliate associate, vedere procedure consigliate [per l'archiviazione e i backup in AKS][operator-best-practices-storage].

Per altre informazioni sui cluster AKS interagire con i dischi di Azure, vedere il plug-in [Kubernetes per i dischi di Azure][kubernetes-disks].

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
