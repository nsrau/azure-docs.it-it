---
title: Creare un volume statico per i pod nel servizio Azure Kubernetes
description: Informazioni su come creare manualmente un volume con i dischi di Azure per l'uso con un pod nel servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: d44c8a7241308c26a3f1148ec70a7a5730dd0c89
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900845"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Creare manualmente e usare un volume con i dischi di Azure nel servizio Azure Kubernetes

Le applicazioni basate su contenitore hanno spesso necessità di accedere e salvare in modo permanente i dati in un volume di dati esterno. Se un singolo pod deve accedere all'archiviazione, è possibile usare i dischi di Azure per presentare un volume nativo per l'uso dell'applicazione. Questo articolo illustra come creare un manualmente un disco di Azure e collegarlo a un pod nel servizio Azure Kubernetes.

> [!NOTE]
> Un disco di Azure può essere installato solo su un singolo pod per volta. Se è necessario condividere un volume permanente tra più pod, usare i [File di Azure][azure-files-volume].

Per altre informazioni sui volumi Kubernetes, vedere [Opzioni di archiviazione per le applicazioni nel servizio Azure Kubernetes][concepts-storage].

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

È anche necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.59 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="create-an-azure-disk"></a>Creare un disco di Azure

Quando si crea un disco di Azure da usare con il servizio Azure Kubernetes, è possibile creare la risorsa disco nel gruppo di risorse del **nodo** . Questo approccio consente al cluster del servizio Azure Kubernetes di accedere e gestire la risorsa disco. Se invece si crea il disco in un gruppo di risorse separato, è necessario fornire all'entità servizio del servizio Azure Kubernetes per il cluster il ruolo `Contributor` al gruppo di risorse del disco. In alternativa, è possibile usare l'identità gestita assegnata dal sistema per le autorizzazioni anziché l'entità servizio. Per altre informazioni, vedere [Usare le identità gestite](use-managed-identity.md).

Per questo articolo, creare il disco nel gruppo di risorse del nodo. Per prima cosa, ottenere il nome del gruppo di risorse con il comando [az servizio Azure Kubernetes show][az-aks-show] e aggiungere il parametro di query `--query nodeResourceGroup`. L'esempio seguente ottiene il gruppo di risorse del nodo per il nome del cluster servizio Azure Kubernetes *myservizio Azure KubernetesCluster* nel gruppo di risorse denominato *myResourceGroup* :

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

A questo punto creare un disco con il comando [az disk create][az-disk-create]. Specificare il nome del gruppo di risorse del nodo ottenuto nel comando precedente e quindi un nome per la risorsa disco, ad esempio *myAKSDisk* . Nell'esempio seguente viene creato un disco da *20* GiB e viene restituito l'ID del disco dopo la creazione. Se è necessario creare un disco da usare con i contenitori di Windows Server, aggiungere il `--os-type windows` parametro per formattare correttamente il disco.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> I dischi di Azure vengono fatturati per SKU in base alle dimensioni specifiche. Questi SKU variano da 32GiB per i dischi S4 o P4 a 32TiB per S80 o P80 Disks (in anteprima). La velocità effettiva e le prestazioni delle operazioni di I/O al secondo per un disco gestito Premium dipendono sia dallo SKU sia dalla dimensione dell'istanza dei nodi nel cluster del servizio Azure Kubernetes. Vedere [Prezzi per Managed Disks][managed-disk-pricing-performance].

L'ID risorsa del disco viene visualizzato dopo aver completato correttamente il comando, come illustrato nell'output di esempio seguente. Questo ID del disco viene usato per montare il disco nel passaggio successivo.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Montare il disco come volume

Per montare il disco di Azure nel pod, configurare il volume nella specifica del contenitore. Creare un nuovo file denominato `azure-disk-pod.yaml` con il contenuto seguente. Aggiornare `diskName` con il nome del disco creato nel passaggio precedente e `diskURI` con l'ID del disco visualizzato nell'output del comando di creazione del disco. Se lo si desidera, annotare `mountPath`, che è il percorso in cui il disco di Azure viene montato nel pod. Per i contenitori Windows Server specificare un valore per *mountPath* usando la convenzione di percorso di Windows, ad esempio *'D:'* .

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

Per le procedure consigliate associate, vedere [Procedure consigliate per archiviazione e backup nel servizio Azure Kubernetes][operator-best-practices-storage].

Per altre informazioni su come il cluster del servizio Azure Kubernetes interagisce con i dischi di Azure, vedere l'argomento relativo ai [plug-in Kubernetes per i dischi di Azure][kubernetes-disks].

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
