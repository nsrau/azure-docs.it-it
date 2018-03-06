---
title: Integrazione con i servizi gestiti di Azure usando Open Service Broker for Azure (OSBA)
description: Integrazione con i servizi gestiti di Azure usando Open Service Broker for Azure (OSBA)
services: container-service
author: sozercan
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 12/05/2017
ms.author: seozerca
ms.openlocfilehash: 594cb0afbdb0a44e9f092b9afc5af13b21e763a4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="integrate-with-azure-managed-services-using-open-service-broker-for-azure-osba"></a>Integrazione con i servizi gestiti di Azure usando Open Service Broker for Azure (OSBA)

Insieme al [catalogo di servizi di Kubernetes][kubernetes-service-catalog], Open Service Broker consente agli sviluppatori di usare i servizi gestiti di Azure in Kubernetes. Questa guida descrive la distribuzione del catalogo di servizi di Kubernetes, di Open Service Broker for Azure (OSBA) e delle applicazioni che usano i servizi gestiti di Azure con Kubernetes.

## <a name="prerequisites"></a>prerequisiti
* Una sottoscrizione di Azure.

* Interfaccia della riga di comando di Azure versione 2.0: è possibile [installarla localmente][azure-cli-install] o usarla in [Azure Cloud Shell][azure-cloud-shell].

* Interfaccia della riga di comando di Helm versione 2.7 e successive: è possibile [installarla localmente][helm-cli-install] o usarla in [Azure Cloud Shell][azure-cloud-shell].

* Autorizzazioni per creare un'entità servizio con il ruolo Collaboratore nella sottoscrizione di Azure

* Un cluster esistente del servizio contenitore di Azure (AKS). Se è necessario un cluster del servizio contenitore di Azure consultare la guida introduttiva [Creare un cluster del servizio contenitore di Azure][create-aks-cluster].

## <a name="install-service-catalog"></a>Installare il catalogo di servizi

Il primo passaggio consiste nell'installare il catalogo di servizi nel cluster di Kubernetes usando un grafico Helm. Aggiornare l'installazione di Tiller (server Helm) nel cluster con:

```azurecli-interactive
helm init --upgrade
```

A questo punto aggiungere il grafico del catalogo di servizi al repository Helm:

```azurecli-interactive
helm repo add svc-cat https://svc-catalog-charts.storage.googleapis.com
```

Infine installare il catalogo di servizi con il grafico Helm:

```azurecli-interactive
helm install svc-cat/catalog --name catalog --namespace catalog --set rbacEnable=false
```

Dopo l'esecuzione del grafico Helm verificare che `servicecatalog` venga visualizzato nell'output del comando seguente:

```azurecli-interactive
kubectl get apiservice
```

Ad esempio, l'output dovrebbe essere simile al seguente (troncato):

```
NAME                                 AGE
v1.                                  10m
v1.authentication.k8s.io             10m
...
v1beta1.servicecatalog.k8s.io        34s
v1beta1.storage.k8s.io               10
```

## <a name="install-open-service-broker-for-azure"></a>Installare Open Service Broker for Azure

Il passaggio successivo consiste nell'installare [Open Service Broker for Azure][open-service-broker-azure], che include il catalogo dei servizi gestiti di Azure. Esempi di servizi di Azure disponibili sono Database di Azure per PostgreSQL, Cache Redis di Azure, Database di Azure per MySQL, Azure Cosmos DB, Database SQL di Azure e altri.

Iniziare aggiungendo Open Service Broker per il repository Helm di Azure:

```azurecli-interactive
helm repo add azure https://kubernetescharts.blob.core.windows.net/azure
```

Usare quindi lo script seguente per creare un'[entità servizio][create-service-principal] e popolare alcune variabili. Queste variabili vengono usate durante l'esecuzione del grafico Helm per installare il Service Broker.

```azurecli-interactive
SERVICE_PRINCIPAL=$(az ad sp create-for-rbac)
AZURE_CLIENT_ID=$(echo $SERVICE_PRINCIPAL | cut -d '"' -f 4)
AZURE_CLIENT_SECRET=$(echo $SERVICE_PRINCIPAL | cut -d '"' -f 16)
AZURE_TENANT_ID=$(echo $SERVICE_PRINCIPAL | cut -d '"' -f 20)
AZURE_SUBSCRIPTION_ID=$(az account show --query id --output tsv)
```

Ora che queste variabili di ambiente sono state popolate, eseguire il comando seguente per installare il Service Broker.

```azurecli-interactive
helm install azure/open-service-broker-azure --name osba --namespace osba \
    --set azure.subscriptionId=$AZURE_SUBSCRIPTION_ID \
    --set azure.tenantId=$AZURE_TENANT_ID \
    --set azure.clientId=$AZURE_CLIENT_ID \
    --set azure.clientSecret=$AZURE_CLIENT_SECRET
```

Dopo avere completato la distribuzione di Open Service Broker for Azure installare l'[interfaccia della riga di comando del catalogo di servizi][service-catalog-cli], un'interfaccia della riga di comando di facile utilizzo per l'interrogazione di service broker, classi di servizio, piani di servizio e molto altro.

Eseguire i comandi seguenti per installare il binario dell'interfaccia della riga di comando del catalogo di servizi:

```azurecli-interactive
curl -sLO https://servicecatalogcli.blob.core.windows.net/cli/latest/$(uname -s)/$(uname -m)/svcat
chmod +x ./svcat
```

A questo punto elencare i Service Broker installati:

```azurecli-interactive
./svcat get brokers
```

L'output dovrebbe essere simile al seguente:

```
  NAME                               URL                                STATUS
+------+--------------------------------------------------------------+--------+
  osba   http://osba-open-service-broker-azure.osba.svc.cluster.local   Ready
```

Successivamente elencare le classi di servizio disponibili. Le classi di servizio visualizzate sono i servizi gestiti di Azure disponibili che possono essere sottoposti a provisioning tramite Open Service Broker for Azure.

```azurecli-interactive
./svcat get classes
```

Infine elencare tutti i piani di servizio disponibili. I piani di servizio sono i livelli di servizio dei servizi gestiti di Azure. Ad esempio, per il Database di Azure per MySQL, i piani vanno da `basic50` per il livello Basic con 50 unità di transazione database (DTU), a `standard800` per il livello Standard con 800 DTU.

```azurecli-interactive
./svcat get plans
```

## <a name="install-wordpress-from-helm-chart-using-azure-database-for-mysql"></a>Installare WordPress dal grafico Helm usando il Database di Azure per MySQL

In questo passaggio usare Helm per installare un grafico Helm aggiornato per WordPress. Il grafico esegue il provisioning di un'istanza del Database di Azure per MySQL che WordPress può usare. Il processo potrebbe richiedere alcuni minuti.

```azurecli-interactive
helm install azure/wordpress --name wordpress --namespace wordpress --set resources.requests.cpu=0
```

Per verificare che l'installazione abbia eseguito il provisioning delle risorse corrette elencare le istanze e i collegamenti del servizio installato:

```azurecli-interactive
./svcat get instances -n wordpress
./svcat get bindings -n wordpress
```

Elencare i segreti installati:

```azurecli-interactive
kubectl get secrets -n wordpress -o yaml
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha descritto come distribuire il catalogo di servizi in un cluster del servizio contenitore di Azure (AKS). Con Open Service Broker for Azure è stato possibile distribuire un'installazione di WordPress che usa i servizi gestiti di Azure, in questo caso il Database di Azure per MySQL.

Consultare il repository [Azure/helm-charts][helm-charts] per accedere ad altri grafici Helm basati su OSBA aggiornati. Per creare grafici che funzionino con OSBA, consultare [Creating a New Chart][helm-create-new-chart] (Creazione di un nuovo grafico).

<!-- LINKS - external -->
[helm-charts]: https://github.com/Azure/helm-charts
[helm-cli-install]: kubernetes-helm.md#install-helm-cli
[helm-create-new-chart]: https://github.com/Azure/helm-charts#creating-a-new-chart
[kubernetes-service-catalog]: https://github.com/kubernetes-incubator/service-catalog
[open-service-broker-azure]: https://github.com/Azure/open-service-broker-azure
[service-catalog-cli]: https://github.com/Azure/service-catalog-cli

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[create-aks-cluster]: ./kubernetes-walkthrough.md
[create-service-principal]: ./kubernetes-service-principal.md
