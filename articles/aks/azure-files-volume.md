---
title: Creazione manuale di File di Azure condivisione
titleSuffix: Azure Kubernetes Service
description: Informazioni su come creare manualmente un volume con i File di Azure per l'uso con pod multipli contemporaneamente nel servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 89976211763f5d4729718c4e4c6503650f27f7cc
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126274"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Creare manualmente e usare un volume con la condivisione di File di Azure nel servizio Azure Kubernetes

Le applicazioni basate su contenitore hanno spesso necessità di accedere e salvare in modo permanente i dati in un volume di dati esterno. Se più pod necessitano di accesso simultaneo allo stesso volume di archiviazione, è possibile usare File di Azure per connettersi usando il [protocollo Server Message Block (SMB)][smb-overview]. Questo articolo illustra come creare manualmente una condivisione di File di Azure e collegarla a un pod nel servizio Azure Kubernetes.

Per altre informazioni sui volumi Kubernetes, vedere [Opzioni di archiviazione per le applicazioni nel servizio Azure Kubernetes][concepts-storage].

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

È anche necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.59 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure

Prima di poter usare i File di Azure come volume Kubernetes, è necessario creare un account di archiviazione di Azure e la condivisione file. I comandi seguenti creano un gruppo di risorse denominato *myAKSShare* , un account di archiviazione e una condivisione file denominata *aksshare* :

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Prendere nota del nome dell'account di archiviazione e della chiave mostrato alla fine dell'output dello script. Questi valori sono necessari quando si crea il volume Kubernetes in uno dei passaggi seguenti.

## <a name="create-a-kubernetes-secret"></a>Creare un segreto Kubernetes

Kubernetes necessita di credenziali per accedere alla condivisione file creata nel passaggio precedente. Queste credenziali sono archiviate in un [segreto Kubernetes][kubernetes-secret], cui viene fatto riferimento durante la creazione di un pod Kubernetes.

Usare il comando `kubectl create secret` per creare il segreto. L'esempio seguente crea un oggetto condiviso denominato *azure-secret* e popola *azurestorageaccountname* e *azurestorageaccountkey* del passaggio precedente. Per usare l'account di archiviazione di Azure indicare il nome e la chiave dell'account.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>Montare la condivisione file come un volume

Per montare la condivisione File di Azure nel pod, configurare il volume nella specifica del contenitore. Creare un nuovo file denominato `azure-files-pod.yaml` con il contenuto seguente. Se è stato modificato il nome della condivisione file o nome del segreto, aggiornare il *shareName* e *secretName* . Se lo si desidera, annotare `mountPath`, che è il percorso in cui la condivisione file viene montata nel pod. Per i contenitori Windows Server specificare un valore per *mountPath* usando la convenzione di percorso di Windows, ad esempio *'D:'* .

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
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

Usare il comando `kubectl` per creare il pod.

```console
kubectl apply -f azure-files-pod.yaml
```

A questo punto è disponibile un pod in esecuzione con una condivisione File di Azure montata in */mnt/azure* . È possibile usare `kubectl describe pod mypod` per verificare che la condivisione sia montata correttamente. L'esempio sintetico di output seguente illustra il volume montato nel contenitore:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-options"></a>Opzioni di montaggio

Il valore predefinito per *FileMode* e *dirMode* è *0755* per Kubernetes versione 1.9.1 e successive. Se si usa un cluster con Kubernetes versione 1.8.5 o successiva e si crea in modo statico l'oggetto volume permanente, è necessario specificare le opzioni di montaggio nell'oggetto *PersistentVolume* . L'esempio seguente imposta *0777* :

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Se si usa un cluster di una versione da 1.8.0 a 1.8.4, è possibile specificare un contesto di protezione con il valore *runAsUser* impostato su *0* . Per altre informazioni sul contesto di sicurezza Pod, vedere [Configure a Security Context][kubernetes-security-context] (Configurazione di un contesto di protezione).

Per aggiornare le opzioni di montaggio, creare un file *azurefile-Mount-Options-PV. YAML* con una *PersistentVolume* . Ad esempio:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Creare un file *azurefile-Mount-Options-PVC. YAML* con un *PersistentVolumeClaim* che usa *PersistentVolume* . Ad esempio:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Usare i `kubectl` comandi per creare *PersistentVolume* e *PersistentVolumeClaim* .

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Verificare che *PersistentVolumeClaim* sia stato creato e associato a *PersistentVolume* .

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Aggiornare le specifiche del contenitore in modo che facciano riferimento al *PersistentVolumeClaim* e aggiornino il pod. Ad esempio:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>Passaggi successivi

Per le procedure consigliate associate, vedere [Procedure consigliate per archiviazione e backup nel servizio Azure Kubernetes][operator-best-practices-storage].

Per altre informazioni su come i cluster del servizio Azure Kubernetes interagiscono con i File di Azure, vedere l'argomento relativo ai [plug-in Kubernetes per i File di Azure][kubernetes-files].

Per i parametri della classe di archiviazione, vedere [provisioning statico (Bring your own file share)](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#static-provisionbring-your-own-file-share).

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
