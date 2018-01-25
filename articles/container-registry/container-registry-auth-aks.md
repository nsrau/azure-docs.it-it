---
title: Eseguire l'autenticazione con Registro contenitori di Azure dal servizio contenitore di Azure
description: "Informazioni su come fornire l'accesso alle immagini nel registro del contenitore privato dal servizio contenitore di Azure usando un'entità del servizio Azure Active Directory."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/12/2018
ms.author: nepeters
ms.openlocfilehash: d6f6688011ddebe2b486bb6ae00f1f3e095a931d
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-service"></a>Eseguire l'autenticazione con Registro contenitori di Azure dal servizio contenitore di Azure

Quando si usa Registro contenitori di Azure con un servizio contenitore di Azure, è necessario definire un meccanismo di autenticazione. Questo documento illustra in dettaglio le configurazioni consigliate per l'autenticazione tra questi due servizi di Azure.

## <a name="grant-aks-access-to-acr"></a>Concedere al servizio contenitore di Azure l'accesso a Registro contenitori di Azure

Quando viene creato un cluster del servizio contenitore di Azure, viene creata anche un'entità servizio per gestire il funzionamento del cluster con le risorse di Azure. Questa entità servizio può essere usata anche per l'autenticazione con un registro del servizio Registro contenitori di Azure. A tale scopo, è necessario creare un'assegnazione di ruolo per concedere all'entità servizio l'accesso in lettura alla risorsa di Registro contenitori di Azure. 

Per completare questa operazione, usare l'esempio seguente.

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Accedere mediante un segreto Kubernetes

In alcuni casi, l'entità servizio usato dal servizio contenitore di Azure non può essere definito nel Registro contenitori di Azure. In questi casi è possibile creare un'entità servizio univoca e definirne l'ambito solo nel Registro contenitori di Azure.

Lo script seguente può essere usato per creare l'entità servizio. 

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id. 
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a contributor role assignment with a scope of the ACR resource. 
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principle client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Le credenziali dell'entità servizio possono ora essere archiviate in un [segreto per il pull delle immagini][image-pull-secret] Kubernetes a cui fare riferimento durante l'esecuzione di contenitori in un cluster AKS. 

Il comando seguente crea il segreto Kubernetes. Sostituire il nome del server con il server di accesso di Registro contenitori di Azure, il nome utente con l'ID dell'entità servizio e la password con la password dell'entità servizio.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> 
```

Il segreto Kubernetes può essere usato in una distribuzione di pod tramite il parametro `ImagePullSecrets`. 

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
