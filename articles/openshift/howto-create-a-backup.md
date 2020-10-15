---
title: Creare un backup dell'applicazione cluster Azure Red Hat OpenShift 4 con Velero
description: Informazioni su come creare un backup delle applicazioni cluster Red Hat OpenShift di Azure con Velero
ms.service: container-service
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: 49ffc33310564299131e2831b74154719b7cf7c7
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078579"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-backup"></a>Creare un backup dell'applicazione cluster Red Hat OpenShift 4 di Azure

In questo articolo si prepara l'ambiente per la creazione di un backup dell'applicazione cluster Red Hat OpenShift 4 di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Configurare i prerequisiti e installare gli strumenti necessari
> * Creare un backup dell'applicazione Azure Red Hat OpenShift 4

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure 2.6.0 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Prima di iniziare

### <a name="install-velero"></a>Installare Velero

Per [installare](https://velero.io/docs/main/basic-install/) Velero nel sistema, seguire la procedura consigliata per il sistema operativo in uso.

### <a name="set-up-azure-storage-account-and-blob-container"></a>Configurare l'account di archiviazione di Azure e il contenitore BLOB

Questo passaggio creerà un gruppo di risorse all'esterno del gruppo di risorse del cluster ARO.  Questo gruppo di risorse consentirà la permanenza dei backup e potrà ripristinare le applicazioni nei nuovi cluster.

```bash
AZURE_BACKUP_RESOURCE_GROUP=Velero_Backups
az group create -n $AZURE_BACKUP_RESOURCE_GROUP --location eastus

AZURE_STORAGE_ACCOUNT_ID="velero$(uuidgen | cut -d '-' -f5 | tr '[A-Z]' '[a-z]')"
az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_ID \
    --resource-group $AZURE_BACKUP_RESOURCE_GROUP \
    --sku Standard_GRS \
    --encryption-services blob \
    --https-only true \
    --kind BlobStorage \
    --access-tier Hot

BLOB_CONTAINER=velero
az storage container create -n $BLOB_CONTAINER --public-access off --account-name $AZURE_STORAGE_ACCOUNT_ID
```

## <a name="set-permissions-for-velero"></a>Impostare le autorizzazioni per Velero

### <a name="create-service-principal"></a>Creare un'entità servizio

Velero richiede le autorizzazioni per eseguire operazioni di backup e ripristino. Quando si crea un'entità servizio, si concede l'autorizzazione Velero per accedere al gruppo di risorse definito nel passaggio precedente. Questo passaggio otterrà il gruppo di risorse del cluster:

```bash
export AZURE_RESOURCE_GROUP=aro-$(az aro show --name <name of cluster> --resource-group <name of resource group> | jq -r '.clusterProfile.domain')
```


```bash
AZURE_SUBSCRIPTION_ID=$(az account list --query '[?isDefault].id' -o tsv)

AZURE_TENANT_ID=$(az account list --query '[?isDefault].tenantId' -o tsv)
```

```bash
AZURE_CLIENT_SECRET=$(az ad sp create-for-rbac --name "velero" --role "Contributor" --query 'password' -o tsv \
--scopes  /subscriptions/$AZURE_SUBSCRIPTION_ID)
AZURE_CLIENT_ID=$(az ad sp list --display-name "velero" --query '[0].appId' -o tsv)

```

```bash
cat << EOF  > ./credentials-velero.yaml
AZURE_SUBSCRIPTION_ID=${AZURE_SUBSCRIPTION_ID}
AZURE_TENANT_ID=${AZURE_TENANT_ID}
AZURE_CLIENT_ID=${AZURE_CLIENT_ID}
AZURE_CLIENT_SECRET=${AZURE_CLIENT_SECRET}
AZURE_RESOURCE_GROUP=${AZURE_RESOURCE_GROUP}
AZURE_CLOUD_NAME=AzurePublicCloud
EOF
```

## <a name="install-velero-on-azure-red-hat-openshift-4-cluster"></a>Installare Velero nel cluster Azure Red Hat OpenShift 4

Questo passaggio consente di installare Velero nel proprio progetto e le [definizioni di risorse personalizzate](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) necessarie per eseguire backup e ripristini con Velero. Assicurarsi di aver effettuato l'accesso a un cluster Azure Red Hat OpenShift V4.


```bash
velero install \
--provider azure \
--plugins velero/velero-plugin-for-microsoft-azure:v1.1.0 \
--bucket $BLOB_CONTAINER \
--secret-file ~/path/to/credentials-velero.yaml \
--backup-location-config resourceGroup=$AZURE_BACKUP_RESOURCE_GROUP,storageAccount=$AZURE_STORAGE_ACCOUNT_ID \
--snapshot-location-config apiTimeout=15m \
--velero-pod-cpu-limit="0" --velero-pod-mem-limit="0" \
--velero-pod-mem-request="0" --velero-pod-cpu-request="0"
```

## <a name="create-a-backup-with-velero"></a>Creare un backup con Velero

Per creare un backup dell'applicazione con Velero, è necessario includere lo spazio dei nomi in cui si trova l'applicazione.  Se si dispone di uno `nginx-example` spazio dei nomi e si desidera includere tutte le risorse nello spazio dei nomi nel backup, eseguire il comando seguente nel terminale:

```bash
velero create backup <name of backup> --include-namespaces=nginx-example
```
Per controllare lo stato del backup, è possibile eseguire:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Viene restituito un backup con esito positivo `phase:Completed` e gli oggetti si trovano nel contenitore nell'account di archiviazione.

## <a name="create-a-backup-with-velero-to-include-snapshots"></a>Creare un backup con Velero per includere gli snapshot

Per creare un backup dell'applicazione con Velero per includere i volumi persistenti dell'applicazione, è necessario includere lo spazio dei nomi in cui si trova l'applicazione e includere il `snapshot-volumes=true` flag durante la creazione del backup

```bash
velero backup create <name of backup> --include-namespaces=nginx-example --snapshot-volumes=true --include-cluster-resources=true
```

Per controllare lo stato del backup, è possibile eseguire:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Un backup con esito positivo con output `phase:Completed` e gli oggetti risiederanno nel contenitore nell'account di archiviazione.

Per altre informazioni su come creare backup e ripristini usando Velero, vedere [backup OpenShift Resources in modalità nativa](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato eseguito il backup di un'applicazione cluster Azure Red Hat OpenShift 4. Si è appreso come:

> [!div class="checklist"]
> * Creare un backup dell'applicazione cluster OpenShift V4 con Velero
> * Creare un backup dell'applicazione cluster OpenShift V4 con snapshot usando Velero


Passare all'articolo successivo per informazioni su come creare un ripristino dell'applicazione cluster Red Hat OpenShift 4 di Azure.

* [Creare un ripristino dell'applicazione cluster Red Hat OpenShift 4 di Azure](howto-create-a-restore.md)
* [Creare un ripristino dell'applicazione cluster Red Hat OpenShift 4 di Azure, inclusi gli snapshot](howto-create-a-restore.md)
