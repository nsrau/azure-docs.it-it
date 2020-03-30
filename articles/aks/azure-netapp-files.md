---
title: Integrare i file NetApp di Azure con il servizio Azure Kubernetes
description: Informazioni su come integrare i file NetApp di Azure con il servizio Azure Kubernetes
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 1c4996df66d475c63110e3d2797f55598fd85b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273759"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integrare i file NetApp di Azure con il servizio Azure Kubernetes

[File NetApp][anf] di Azure è un servizio di archiviazione file a livello di utilizzo a consumo di classe enterprise, ad alte prestazioni e di classe Enterprise.Azure NetApp Files is an enterprise-class, high-performance, metered file storage service running on Azure. Questo articolo illustra come integrare i file NetApp di Azure con il servizio Azure Kubernetes (AKS).

## <a name="before-you-begin"></a>Prima di iniziare
Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster servizio Azure Kubernetes, vedere la Guida introduttiva su servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

> [!IMPORTANT]
> Il cluster AKS deve trovarsi anche [in un'area che supporta i file NetApp][anf-regions]di Azure.

È inoltre necessaria l'interfaccia della riga di comando di Azure versione 2.0.59 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

### <a name="limitations"></a>Limitazioni

Quando si usano file NetApp di Azure, si applicano le limitazioni seguenti:The following limitations apply when you use Azure NetApp Files:

* I file NetApp di Azure sono disponibili solo [nelle aree di Azure selezionate.][anf-regions]
* Prima di poter usare i file NetApp di Azure, è necessario disporre dell'accesso al servizio File NetApp di Azure.Before you can use Azure NetApp Files, you must be granted access to the Azure NetApp Files service. Per richiedere l'accesso, è possibile usare il modulo di [invio dell'elenco di attesa][anf-waitlist]file di Azure NetApp . Non è possibile accedere al servizio File NetApp di Azure finché non si riceve l'e-mail di conferma ufficiale dal team File netApp di Azure.You can't access the Azure NetApp Files service until you receive the official confirmation email from the Azure NetApp Files team.
* Il servizio File NetApp di Azure deve essere creato nella stessa rete virtuale del cluster AKS.
* Dopo la distribuzione iniziale di un cluster AKS, è supportato solo il provisioning statico per i file NetApp di Azure.After the initial deployment of an AKS cluster, only static provisioning for Azure NetApp Files is supported.
* Per usare il provisioning dinamico con i file NetApp di Azure, installare e configurare [NetApp Trident](https://netapp-trident.readthedocs.io/) versione 19.07 o successiva.

## <a name="configure-azure-netapp-files"></a>Configurare i file NetApp di AzureConfigure Azure NetApp Files

> [!IMPORTANT]
> Prima di registrare il provider di risorse *Microsoft.NetApp,* è necessario compilare il modulo di [invio dell'elenco di attesa][anf-waitlist] dei file NetApp di Azure per la sottoscrizione. Non è possibile registrare la risorsa fornita fino a quando non si riceve l'e-mail di conferma ufficiale dal team file NetApp di Azure.You can can register the resource provide until you receive the official confirmation email from the Azure NetApp Files team.

Registrare il provider di risorse *Microsoft.NetApp:*

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Questa operazione può richiedere del tempo.

Quando si crea un account Di Azure NetApp da usare con AKS, è necessario creare l'account nel gruppo di risorse del **nodo.** Per prima cosa, ottenere il nome del gruppo di risorse con il comando [az servizio Azure Kubernetes show][az-aks-show] e aggiungere il parametro di query `--query nodeResourceGroup`. L'esempio seguente ottiene il gruppo di risorse del nodo per il cluster AKS denominato *myAKSCluster* nel nome del gruppo di risorse *myResourceGroup*:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Creare un account File NetApp di Azure nel gruppo di risorse del **nodo** e nella stessa area del cluster AKS usando [l'account netappfiles az create][az-netappfiles-account-create]. Nell'esempio seguente viene creato un account denominato myaccount1 nel gruppo di risorse MC_myResourceGroup_myAKSCluster_eastus e nell'area *eastus:The* following example creates an account named *myaccount1* in the *MC_myResourceGroup_myAKSCluster_eastus* resource group and eastus region:

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Creare un nuovo pool di capacità utilizzando [il pool az netappfiles create][az-netappfiles-pool-create]. Nell'esempio seguente viene creato un nuovo pool di capacità denominato mypool1 con 4 TB di dimensioni e livello di servizio *Premium:The* following example creates a new capacity pool named *mypool1* with 4 TB in size and Premium service level:

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Creare una subnet da [delegare ai][anf-delegate-subnet] file NetApp di Azure usando [la subnet vnet][az-network-vnet-subnet-create]di rete az create . *Questa subnet deve trovarsi nella stessa rete virtuale del cluster AKS.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Creare un volume utilizzando [az netappfiles volume create][az-netappfiles-volume-create].

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --creation-token $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>Creare il volume persistenteCreate the PersistentVolume

Elencare i dettagli del volume utilizzando [az netappfiles volume show][az-netappfiles-volume-show]

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Creare `pv-nfs.yaml` una definizione di un PersistentVolume.Create a defining a PersistentVolume. Sostituire `path` con *creationToken* e `server` con *ipAddress* del comando precedente. Ad esempio:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Aggiornare il *server* e il *percorso* ai valori del volume NFS (Network File System) creato nel passaggio precedente. Create il PersistentVolume con il comando [kubectl apply:][kubectl-apply]

```console
kubectl apply -f pv-nfs.yaml
```

Verificare che *lo stato* di PersistentVolume sia *Disponibile* utilizzando il comando [kubectl describe:][kubectl-describe]

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Creare PersistentVolumeClaimCreate the PersistentVolumeClaim

Creare `pvc-nfs.yaml` una definizione di un PersistentVolume.Create a defining a PersistentVolume. Ad esempio:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

Creare il PersistentVolumeClaim con il comando [kubectl apply:][kubectl-apply]

```console
kubectl apply -f pvc-nfs.yaml
```

Verificare che *lo stato* di PersistentVolumeClaim sia *Bound* utilizzando il comando [kubectl describe:][kubectl-describe]

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Montare con un baccello

Creare `nginx-nfs.yaml` un pod di definizione che utilizza PersistentVolumeClaim.Create a defining a pod that uses the PersistentVolumeClaim. Ad esempio:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: nginx
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Create il contenitore con il comando [kubectl apply:][kubectl-apply]

```console
kubectl apply -f nginx-nfs.yaml
```

Verificate che il contenitore sia *in esecuzione* utilizzando il comando [kubectl describe:][kubectl-describe]

```console
kubectl describe pod nginx-nfs
```

Verificare che il volume sia stato montato nel contenitore utilizzando `df -h` [kubectl exec][kubectl-exec] per connettersi al pod, quindi per verificare se il volume è montato.

```console
$ kubectl exec -it nginx-nfs -- bash
```

```output
root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui file NetApp di Azure, vedere [Che cos'è][anf]File NetApp di Azure. Per altre informazioni sull'uso di NFS con [AKS, vedere Creare e usare manualmente un volume di server Linux NFS (Network File System) con il servizio Azure Kubernetes (AKS)][aks-nfs].


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
