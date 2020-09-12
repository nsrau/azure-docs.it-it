---
title: Abilitare i driver CSI (container Storage Interface) in Azure Kubernetes Service (AKS)
description: Informazioni su come abilitare i driver CSI (container Storage Interface) per i dischi di Azure e File di Azure in un cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: bd5706d20496e1ff00843f761443d183cf7fcae3
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422042"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>Abilitare i driver CSI (container Storage Interface) per i dischi di Azure e File di Azure in Azure Kubernetes Service (AKS) (anteprima)

L'interfaccia CSI (container Storage Interface) è uno standard per l'esposizione di sistemi di archiviazione di blocchi e file arbitrari ai carichi di lavoro in contenitori in Kubernetes. Grazie all'adozione e all'uso di CSI, Azure Kubernetes Service (AKS) può scrivere, distribuire ed eseguire l'iterazione di plug-in che espongono nuovi o migliorati sistemi di archiviazione esistenti in Kubernetes senza dover toccare il codice Kubernetes principale e attendere i cicli di rilascio.

Il supporto del driver di archiviazione CSI su AKS consente di sfruttare in modo nativo:
- [*Dischi di Azure*](azure-disk-csi.md) : è possibile usare per creare una risorsa Kubernetes *DataDisk* . I dischi possono usare l'archiviazione Premium di Azure, supportata da unità SSD a prestazioni elevate o archiviazione standard di Azure, supportata da HDD normali o da unità SSD standard. Per la maggior parte dei carichi di lavoro di produzione e di sviluppo, usare l'archiviazione Premium. I dischi di Azure sono montati come *ReadWriteOnce*, quindi sono disponibili solo per un singolo POD. Per i volumi di archiviazione a cui è possibile accedere simultaneamente da più POD, utilizzare File di Azure.
- [*File di Azure*](azure-files-csi.md) può essere usato per montare una condivisione SMB 3,0 supportata da un account di archiviazione di Azure per i pod. I file consentono di condividere dati tra più nodi e pod. I file possono usare l'archiviazione standard di Azure supportata da HDD normali o archiviazione Premium di Azure, supportata da unità SSD a prestazioni elevate.

> [!IMPORTANT]
> A partire da Kubernetes versione 1,21, Kubernetes utilizzerà solo driver CSI e per impostazione predefinita. Questo è il futuro del supporto per l'archiviazione in Kubernetes.
>
> *"Driver all'interno dell'albero"* si riferisce ai driver di archiviazione correnti che fanno parte del codice kubernetes principale rispetto ai nuovi driver CSI che sono plug-in.

## <a name="limitations"></a>Limitazioni

- Questa funzionalità può essere impostata solo al momento della creazione del cluster.
- La versione minima di kubernetes secondaria che supporta i driver CSI è la v 1.17.
- Durante l'anteprima, la classe di archiviazione predefinita sarà ancora la [stessa classe di archiviazione nell'albero](concepts-storage.md#storage-classes). Quando questa funzionalità è disponibile a livello generale, la classe di archiviazione predefinita sarà la `managed-csi` classe di archiviazione e le classi di archiviazione nell'albero verranno rimosse.
- Durante la prima fase di anteprima, è supportata solo l'interfaccia della riga di comando di Azure.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>Registrare la `EnableAzureDiskFileCSIDriver` funzionalità di anteprima

Per creare un cluster AKS in grado di sfruttare i driver CSI per i dischi di Azure e File di Azure, è necessario abilitare il `EnableAzureDiskFileCSIDriver` flag funzionalità per la sottoscrizione.

Registrare il `EnableAzureDiskFileCSIDriver` flag feature usando il comando [AZ feature Register][az-feature-register] , come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. Verificare lo stato della registrazione usando il comando [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft.ContainerService* usando il comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando aks-preview

Per creare un cluster AKS o un pool di nodi in grado di usare i driver di archiviazione CSI, è necessaria la versione più recente dell'estensione dell'interfaccia della riga di comando *AKS-Preview* . Installare l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* usando il comando [AZ Extension Add][az-extension-add] oppure installare gli aggiornamenti disponibili usando il comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>Creare un nuovo cluster in grado di usare i driver di archiviazione CSI

Creare un nuovo cluster in grado di sfruttare i driver di archiviazione CSI per i dischi di Azure e File di Azure usando i comandi dell'interfaccia della riga di comando seguenti. Usare il `--aks-custom-headers` flag per impostare la `EnableAzureDiskFileCSIDriver` funzionalità.

Creare un gruppo di risorse di Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Creare il cluster AKS con supporto per i driver di archiviazione CSI.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure -k 1.17.9 --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Se si desidera creare cluster di driver di archiviazione ad albero anziché driver di archiviazione CSI, è possibile omettere il `--aks-custom-headers` parametro personalizzato.


Verificare il numero di volumi basati su disco di Azure che è possibile aggiungere a questo nodo eseguendo:

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>Passaggi successivi

- Usare l'unità CSI per i dischi di Azure, vedere [usare dischi di Azure con driver CSI](azure-disk-csi.md).
- Usare l'unità CSI per file di Azure. vedere [usare file di Azure con driver CSI](azure-files-csi.md).
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