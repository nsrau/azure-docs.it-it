---
title: Abilitare il supporto del disco ultra in Azure Kubernetes Service (AKS)
description: Informazioni su come abilitare e configurare dischi ultra in un cluster Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 46be67a415f67e260262e5b80e5a1dad534aea79
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531602"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>Usare i dischi di Azure ultra in Azure Kubernetes Service (anteprima)

I [dischi di Azure ultra](../virtual-machines/linux/disks-enable-ultra-ssd.md) offrono velocità effettiva elevata, IOPS elevate e archiviazione su disco a bassa latenza coerente per le applicazioni con stato. Uno dei vantaggi principali di ultra disks è la possibilità di modificare dinamicamente le prestazioni dell'unità SSD insieme ai carichi di lavoro senza dover riavviare i nodi dell'agente. I dischi Ultra sono adatti per carichi di lavoro con utilizzo intensivo di dati.

## <a name="before-you-begin"></a>Prima di iniziare

Questa funzionalità può essere impostata solo in fase di creazione del cluster o di creazione del pool di nodi.

> [!IMPORTANT]
> Azure ultra disks richiede la distribuzione di nodepools in zone di disponibilità e aree che supportano questi dischi, oltre a specifiche serie di VM. Vedere [**ambito e limitazioni di ultra disks GA**](../virtual-machines/linux/disks-enable-ultra-ssd.md#ga-scope-and-limitations).

### <a name="prerequisites"></a>Prerequisiti

- Assicurarsi di avere `EnableUltraSSD` abilitato il flag funzionalità.
- Assicurarsi che sia installata la versione più recente dell' `aks-preview` [estensione CLI][az-extension-add] .

### <a name="register-the-enableultrassd-preview-feature"></a>Registrare la `EnableUltraSSD` funzionalità di anteprima

Per creare un cluster AKS o un pool di nodi in grado di sfruttare i dischi Ultra, è necessario abilitare il `EnableUltraSSD` flag funzionalità per la sottoscrizione.

Registrare il `EnableUltraSSD` flag feature usando il comando [AZ feature Register][az-feature-register] , come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableUltraSSD"
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato di registrazione usando il comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableUltraSSD')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft.ContainerService* usando il comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono il consenso esplicito self-service. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico per il massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, consultare gli articoli di supporto seguenti:
>
> - [Criteri di supporto del servizio Azure Kubernetes](support-policies.md)
> - [Domande frequenti relative al supporto tecnico Azure](faq.md)

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando aks-preview

Per creare un cluster AKS o un pool di nodi che può usare dischi Ultra, è necessaria la versione più recente dell'estensione dell'interfaccia della riga di comando *AKS-Preview* . Installare l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* usando il comando [AZ Extension Add][az-extension-add] oppure installare gli aggiornamenti disponibili usando il comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>Limitazioni
- Vedere [ **ambito e limitazioni di ultra DISKs GA**](../virtual-machines/linux/disks-enable-ultra-ssd.md#ga-scope-and-limitations)
- L'intervallo di dimensioni supportato per un disco Ultra è compreso tra 100 e 1500

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>Creare un nuovo cluster che può usare dischi ultra

Creare un cluster AKS in grado di sfruttare i dischi Ultra usando i comandi dell'interfaccia della riga di comando seguenti. Usare il `--aks-custom-headers` flag per impostare la `EnableUltraSSD` funzionalità.

Creare un gruppo di risorse di Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Creare il cluster del servizio contenitore di Azure con l'integrazione Azure AD gestita e il controllo degli accessi in base al ruolo

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Se si desidera creare cluster senza supporto per dischi Ultra, è possibile omettere il `--aks-custom-headers` parametro personalizzato.

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>Abilitare i dischi ultra in un cluster esistente

È possibile abilitare i dischi Ultra nei cluster esistenti aggiungendo un nuovo pool di nodi al cluster che supporta dischi Ultra. Configurare un nuovo pool di nodi per usare la crittografia basata su host usando il `--aks-custom-headers` flag.


```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableEncryptionAtHost=true
```

Se si vuole creare nuovi pool di nodi senza supporto per i dischi Ultra, è possibile omettere il `--aks-custom-headers` parametro personalizzato.

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>Usare dischi Ultra dinamici con una classe di archiviazione

Per usare i dischi Ultra nelle distribuzioni o nei set con stato, è possibile usare una [classe di archiviazione per il provisioning dinamico](azure-disks-dynamic-pv.md).

### <a name="create-the-storage-class"></a>Creare la classe di archiviazione

Una classe di archiviazione viene usata per definire la creazione dinamica di un'unità di archiviazione con un volume permanente. Per altre informazioni sulle classi di archiviazione Kubernetes, vedere [Kubernetes Storage Classes][kubernetes-storage-classes] (Classi di archiviazione Kubernetes).

In questo caso, verrà creata una classe di archiviazione che fa riferimento a dischi Ultra. Creare un file denominato `azure-ultra-disk-sc.yaml` e copiarlo nel manifesto seguente.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: ultra-disk-sc
provisioner: kubernetes.io/azure-disk
volumeBindingMode: WaitForFirstConsumer # optional, but recommended if you want to wait until the pod that will use this disk is created 
parameters:
  skuname: UltraSSD_LRS
  kind: managed
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB 
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
```

Creare la classe di archiviazione con il comando [kubectl Apply][kubectl-apply] e specificare il file *Azure-ultra-disk-SC. YAML* :

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>Creare un'attestazione di volume permanente

Un'attestazione di volume permanente viene usata per il provisioning automatico dell'archiviazione in una classe di archiviazione. In tal caso un'attestazione di volume permanente può usare una delle classi di archiviazione create in precedenza per creare un disco gestito di Azure standard o premium.

Creare un file denominato `azure-ultra-disk-pvc.yaml` e copiarlo nel manifesto seguente. L'attestazione richiede un disco denominato `ultra-disk` con dimensioni di *1000 GB* con accesso a *ReadWriteOnce* . La classe di archiviazione *ultra-disk-SC* è specificata come classe di archiviazione.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ultra-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: ultra-disk-sc
  resources:
    requests:
      storage: 1000Gi
```

Creare l'attestazione del volume permanente con il comando [kubectl Apply][kubectl-apply] e specificare il file *Azure-ultra-disk-PVC. YAML* :

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>Usare il volume permanente

Dopo aver creato l'attestazione di volume persistente e aver effettuato il provisioning del disco, è possibile creare un pod con accesso al disco. Il manifesto seguente crea un pod NGINX di base che usa l'attestazione del volume permanente denominato *ultra-disk* per montare il disco di Azure nel percorso `/mnt/azure` .

Creare un file denominato `nginx-ultra.yaml` e copiarlo nel manifesto seguente.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-ultra
spec:
  containers:
  - name: nginx-ultra
    image: nginx
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: ultra-disk
```

Creare il pod con il comando [kubectl apply][kubectl-apply], come illustrato nell'esempio seguente:

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

A questo punto è disponibile un pod in esecuzione con il disco di Azure montato nella directory `/mnt/azure`. Questa configurazione può essere visualizzata quando si controlla il pod tramite `kubectl describe pod nginx-ultra`, come illustrato nell'esempio sintetico seguente:

```console
$ kubectl describe pod nginx-ultra

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```


## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui dischi Ultra, vedere [uso di dischi ultra di Azure](../virtual-machines/linux/disks-enable-ultra-ssd.md).
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
