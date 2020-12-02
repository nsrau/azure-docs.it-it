---
title: Creare un'istanza di StorageClass di File di Azure in Azure Red Hat OpenShift 4
description: Informazioni su come creare un File di Azure StorageClass in Azure Red Hat OpenShift
ms.service: container-service
ms.topic: article
ms.date: 10/16/2020
author: grantomation
ms.author: b-grodel
keywords: Aro, OpenShift, AZ Aro, Red Hat, CLI, file di Azure
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: db87e61166da87300a5e1fb8433a42f3f47cf832
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493825"
---
# <a name="create-an-azure-files-storageclass-on-azure-red-hat-openshift-4"></a>Creare un'istanza di StorageClass di File di Azure in Azure Red Hat OpenShift 4

In questo articolo si creerà un StorageClass per Azure Red Hat OpenShift 4 che esegue dinamicamente il provisioning di ReadWriteMany (RWX) storage usando File di Azure. Verrà descritto come:

> [!div class="checklist"]
> * Configurare i prerequisiti e installare gli strumenti necessari
> * Creare un StorageClass di Azure Red Hat OpenShift 4 con il provisioning di file di Azure

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure 2.6.0 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Prima di iniziare

Distribuire un cluster Azure Red Hat OpenShift 4 nella sottoscrizione, vedere [creare un cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md)


### <a name="set-up-azure-storage-account"></a>Configurare l'account di archiviazione di Azure

Questo passaggio creerà un gruppo di risorse all'esterno del gruppo di risorse del cluster Azure Red Hat OpenShift (ARO). Questo gruppo di risorse conterrà le condivisioni di File di Azure create dal provisioning dinamico di Azure Red Hat OpenShift.

```bash
AZURE_FILES_RESOURCE_GROUP=aro_azure_files
LOCATION=eastus

az group create -l $LOCATION -n $AZURE_FILES_RESOURCE_GROUP

AZURE_STORAGE_ACCOUNT_NAME=aroazurefilessa

az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_NAME \
    --resource-group $AZURE_FILES_RESOURCE_GROUP \
    --kind StorageV2 \
    --sku Standard_LRS
```

## <a name="set-permissions"></a>Impostare le autorizzazioni
### <a name="set-resource-group-permissions"></a>Impostare le autorizzazioni per il gruppo di risorse

L'entità servizio ARO richiede l'autorizzazione ' listKeys ' per il nuovo gruppo di risorse dell'account di archiviazione di Azure. Assegnare il ruolo ' collaboratore ' per ottenere questo risultato. 

```bash
ARO_RESOURCE_GROUP=aro-rg
CLUSTER=cluster
ARO_SERVICE_PRINCIPAL_ID=$(az aro show -g $ARO_RESOURCE_GROUP -n $CLUSTER –-query servicePrincipalProfile.clientId -o tsv)

az role assignment create –-role Contributor -–assignee $ARO_SERVICE_PRINCIPAL_ID -g $AZURE_FILES_RESOURCE_GROUP
```

### <a name="set-aro-cluster-permissions"></a>Impostazione delle autorizzazioni del cluster ARO

L'account del servizio Binder del volume permanente OpenShift richiede la possibilità di leggere i segreti. Creare e assegnare un ruolo cluster OpenShift per ottenere questo risultato.
```bash
ARO_API_SERVER=$(az aro list --query "[?contains(name,'$CLUSTER')].[apiserverProfile.url]" -o tsv)

oc login -u kubeadmin -p $(az aro list-credentials -g $ARO_RESOURCE_GROUP -n $CLUSTER --query=kubeadminPassword -o tsv) $APISERVER

oc create clusterrole azure-secret-reader \
    --verb=create,get \
    --resource=secrets

oc adm policy add-cluster-role-to-user azure-secret-reader system:serviceaccount:kube-system:persistent-volume-binder
```

## <a name="create-storageclass-with-azure-files-provisioner"></a>Creazione di StorageClass con File di Azure provisioner

Questo passaggio creerà una StorageClass con un provisioner File di Azure. All'interno del manifesto StorageClass, i dettagli dell'account di archiviazione sono necessari, in modo che il cluster ARO sappia di esaminare un account di archiviazione all'esterno del gruppo di risorse corrente.

```bash
cat << EOF >> azure-storageclass-azure-file.yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azure-file
provisioner: kubernetes.io/azure-file
parameters:
  location: $LOCATION
  skuName: Standard_LRS 
  storageAccount: $AZURE_STORAGE_ACCOUNT_NAME
  resourceGroup: $AZURE_FILES_RESOURCE_GROUP
reclaimPolicy: Delete
volumeBindingMode: Immediate
EOF

oc create -f azure-storageclass-azure-file.yaml
```

## <a name="change-the-default-storageclass-optional"></a>Modificare il valore predefinito di StorageClass (facoltativo)

Il valore predefinito di StorageClass in ARO è denominato Managed-Premium e usa il provisioning di Azure-disk. Per modificare questa impostazione, emettere i comandi patch sui manifesti di StorageClass.

```bash
oc patch storageclass managed-premium -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'

oc patch storageclass azure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## <a name="verify-azure-file-storageclass-optional"></a>Verificare StorageClass file di Azure (facoltativo)

Creare una nuova applicazione e assegnarvi lo spazio di archiviazione.

```bash
oc new-project azfiletest
oc new-app –template httpd-example

#Wait for the pod to become Ready
curl $(oc get route httpd-example -n azfiletest -o jsonpath={.spec.host})

oc set volume dc/httpd-example --add --name=v1 -t pvc --claim-size=1G -m /data

#Wait for the new deployment to rollout
export POD=$(oc get pods --field-selector=status.phase==Running -o jsonpath={.items[].metadata.name})
oc exec $POD -- bash -c "mkdir ./data"
oc exec $POD -- bash -c "echo 'azure file storage' >> /data/test.txt"

oc exec $POD -- bash -c "cat /data/test.txt"
azure file storage
```
Il file di test.txt sarà anche visibile tramite il Storage Explorer nel portale di Azure. 

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una risorsa di archiviazione permanente dinamica usando file di Microsoft Azure e Azure Red Hat OpenShift 4. Si è appreso come:

> [!div class="checklist"]
> * Creare un account di archiviazione
> * Configurare una StorageClass in un cluster Azure Red Hat OpenShift 4 usando il File di Azure provisioner

Passare all'articolo successivo per informazioni sulle risorse supportate da Azure Red Hat OpenShift 4.

* [Criteri di supporto di Azure Red Hat OpenShift](support-policies-v4.md)