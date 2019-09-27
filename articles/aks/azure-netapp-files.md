---
title: Integrare Azure NetApp Files con il servizio Azure Kubernetes
description: Informazioni su come integrare Azure NetApp Files con il servizio Azure Kubernetes
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 84192a831e3b1f24e20eb07a6c8695516c28970f
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329331"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integrare Azure NetApp Files con il servizio Azure Kubernetes

[Azure NetApp files][anf] è un servizio di archiviazione di file a consumo e a prestazioni elevate di livello aziendale in esecuzione in Azure. Questo articolo illustra come integrare Azure NetApp Files con Azure Kubernetes Service (AKS).

## <a name="before-you-begin"></a>Prima di iniziare
Questo articolo presuppone che si disponga di un cluster AKS esistente. Se è necessario un cluster AKS, vedere la Guida introduttiva di AKS [usando l'interfaccia della][aks-quickstart-cli] riga di comando di Azure o [l'portale di Azure][aks-quickstart-portal].

> [!IMPORTANT]
> Il cluster AKS deve trovarsi anche [in un'area che supporta Azure NetApp files][anf-regions].

È necessaria anche l'interfaccia della riga di comando di Azure versione 2.0.59 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia][install-azure-cli]della riga di comando di Azure.

### <a name="limitations"></a>Limitazioni

Quando si utilizza Azure NetApp Files, si applicano le limitazioni seguenti:

* Azure NetApp Files è disponibile solo [nelle aree di Azure selezionate][anf-regions].
* Prima di poter usare Azure NetApp Files, è necessario disporre dell'accesso al servizio Azure NetApp Files. Per richiedere l'accesso, è possibile usare il [modulo di invio Azure NetApp files dell'attesa][anf-waitlist]. Non è possibile accedere al servizio Azure NetApp Files finché non si riceve il messaggio di posta elettronica di conferma ufficiale dal team di Azure NetApp Files.
* Il servizio di Azure NetApp Files deve essere creato nella stessa rete virtuale del cluster AKS.
* In AKS è supportato solo il provisioning statico per Azure NetApp Files.

## <a name="configure-azure-netapp-files"></a>Configurare Azure NetApp Files

> [!IMPORTANT]
> Prima di poter registrare il provider di risorse *Microsoft. NetApp* , è necessario completare il [modulo di invio Azure NetApp files Waiting][anf-waitlist] per la sottoscrizione. Non è possibile registrare la risorsa fino a quando non si riceve il messaggio di posta elettronica di conferma ufficiale dal team Azure NetApp Files.

Registrare il provider di risorse *Microsoft. NetApp* :

```azure-cli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Questa operazione può richiedere del tempo.

Quando si crea un account Azure NetApp da usare con AKS, è necessario creare l'account nel gruppo di risorse del **nodo** . Per prima cosa, ottenere il nome del gruppo di risorse con il comando [AZ AKS Show][az-aks-show] e aggiungere il parametro di query `--query nodeResourceGroup`. Nell'esempio seguente viene ottenuto il gruppo di risorse node per il cluster AKS denominato *myAKSCluster* nel nome del gruppo di risorse *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Creare un account Azure NetApp Files nel gruppo di risorse del **nodo** e nella stessa area del cluster AKS usando il comando [AZ netappfiles account create][az-netappfiles-account-create]. Nell'esempio seguente viene creato un account denominato *myaccount1* nel gruppo di risorse *MC_myResourceGroup_myAKSCluster_eastus* e nell'area *eastus* :

```azure-cli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Creare un nuovo pool di capacità usando [AZ netappfiles pool create][az-netappfiles-pool-create]. Nell'esempio seguente viene creato un nuovo pool di capacità denominato *mypool1* con 4 TB di dimensioni e il livello di servizio *Premium* :

```azure-cli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Creare una subnet da [delegare a Azure NetApp files][anf-delegate-subnet] usando [AZ Network VNET subnet create][az-network-vnet-subnet-create]. *Questa subnet deve trovarsi nella stessa rete virtuale del cluster AKS.*

```azure-cli
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

Creare un volume usando il comando [AZ netappfiles volume create][az-netappfiles-volume-create].

```azure-cli
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

## <a name="create-the-persistentvolume"></a>Creare il PersistentVolume

Elenca i dettagli del volume usando [AZ netappfiles volume show][az-netappfiles-volume-show]
```azure-cli
$ az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"

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

Creare un `pv-nfs.yaml` che definisce un PersistentVolume. Sostituire `path` con *creationToken* e `server` con *IPAddress* dal comando precedente. Esempio:

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

Aggiornare il *Server* e il *percorso* ai valori del volume NFS (Network File System) creato nel passaggio precedente. Creare il PersistentVolume con il comando [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f pv-nfs.yaml
```

Verificare che lo *stato* di PersistentVolume sia *disponibile* usando il comando [kubectl Descrivi][kubectl-describe] :

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Creare il PersistentVolumeClaim

Creare un `pvc-nfs.yaml` che definisce un PersistentVolume. Esempio:

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

Creare il PersistentVolumeClaim con il comando [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f pvc-nfs.yaml
```

Verificare che lo *stato* di PersistentVolumeClaim sia *associato* usando il comando [kubectl Descrivi][kubectl-describe] :

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Montare con un pod

Creare un `nginx-nfs.yaml` che definisce un pod che usa PersistentVolumeClaim. Esempio:

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

Creare il pod con il comando [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f nginx-nfs.yaml
```

Verificare che il POD sia *in esecuzione* usando il comando [kubectl Descrivi][kubectl-describe] :

```console
kubectl describe pod nginx-nfs
```

Verificare che il volume sia stato montato nel pod usando [kubectl Exec][kubectl-exec] per connettersi al Pod, quindi `df -h` per verificare se il volume è montato.

```console
$ kubectl exec -it nginx-nfs -- bash

root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Azure NetApp Files, vedere [che cos'è Azure NetApp files][anf]. Per altre informazioni sull'uso di NFS con AKS, vedere [creare e usare manualmente un volume del server Linux NFS (Network File System) con Azure Kubernetes Service (AKS)][aks-nfs].


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
