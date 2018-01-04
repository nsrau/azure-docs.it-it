---
title: Distribuire OpenShift Container Platform in Azure | Microsoft Docs
description: Distribuire OpenShift Container Platform in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 81d1e2a92a24d43c6324b4fe026680c379e656da
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Distribuire OpenShift Container Platform in Azure

Esistono diversi modi di distribuire OpenShift Container Platform in Azure:

- È possibile distribuire manualmente i componenti necessari dell'infrastruttura di Azure e quindi seguire la [documentazione](https://docs.openshift.com/container-platform/3.6/welcome/index.html) di OpenShift Container Platform.
- È anche possibile usare un [modello di Resource Manager](https://github.com/Microsoft/openshift-container-platform/) esistente che semplifica la distribuzione del cluster OpenShift Container Platform.
- Un'altra opzione consiste nell'usare l'[offerta di Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Per tutte le opzioni è necessaria una sottoscrizione di Red Hat. Durante la distribuzione, l'istanza Red Hat Enterprise Linux viene registrata nella sottoscrizione di Red Hat e allegata all'ID del pool che contiene i diritti per OpenShift Container Platform.
Verificare di avere un nome utente, una password e un ID pool validi per Red Hat Subscription Manager (RHSM). È possibile verificare le informazioni effettuando l'accesso a https://access.redhat.com.

## <a name="deploy-by-using-the-openshift-container-platform-resource-manager-template"></a>Distribuire usando il modello di Resource Manager di OpenShift Container Platform

Per distribuire usando il modello di Resource Manager, viene usato un file di parametri per specificare tutti i parametri di input. Per personalizzare un elemento della distribuzione che non vengono trattati se si usano i parametri di input, creare un fork del repository GitHub e modificare gli elementi appropriati.

Tra le opzioni di personalizzazione comuni sono incluse, ad esempio:

- CIDR rete virtuale (variabile in azuredeploy.json)
- Dimensioni macchina virtuale bastion (variabile in azuredeploy.json)
- Convenzioni di denominazione (variabile in azuredeploy.json)
- Specifiche del cluster OpenShift - modificate usando il file hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Configurare il file dei parametri

Usare il valore `appId` dall'entità servizio creata in precedenza per il parametro `aadClientId`. 

L'esempio seguente crea un file di parametri denominato azuredeploy.parameters.json con tutti gli input necessari.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "true"
        },
        "enableCockpit": {
            "value": "false"
        },
        "rhsmUsernamePasswordOrActivationKey": {
            "value": "usernamepassword"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Sostituire gli elementi tra parentesi con le proprie informazioni.

### <a name="deploy-by-using-azure-cli"></a>Eseguire la distribuzione usando l'interfaccia della riga di comando di Azure

> [!NOTE] 
> Il comando seguente richiede l'interfaccia della riga di comando di Azure 2.0.8 o versione successiva. È possibile verificare la versione dell'interfaccia della riga di comando di Azure con il comando `az --version`. Per aggiornare la versione della CLI, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

L'esempio seguente consente di distribuire il cluster OpenShift e tutte le risorse correlate in un gruppo di risorse denominato myResourceGroup con nome di distribuzione myOpenShiftCluster. Viene fatto riferimento al modello direttamente dal repository GitHub e viene usato un file di parametri locale denominato azuredeploy.parameters.json.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

La distribuzione richiede almeno 30 minuti, a seconda del numero totale di nodi distribuiti. Al termine della distribuzione, l'URL della console di OpenShift e il nome DNS del master di OpenShift vengono visualizzati sul terminale.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-by-using-the-openshift-container-platform-azure-marketplace-offer"></a>Eseguire la distribuzione usando l'offerta di OpenShift Container Platform in Azure Marketplace

Il modo più semplice per distribuire OpenShift Container Platform in Azure è usare l'[offerta di Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Questa opzione è la più semplice, ma è limitata in termini di funzionalità di personalizzazione. L'offerta include tre opzioni di configurazione:

- **Small**: distribuisce un cluster non a disponibilità elevata con un nodo master, un nodo di infrastruttura, due nodi di applicazione e un nodo bastion. Tutti i nodi sono macchine virtuali DS2v2 Standard. Questo cluster richiede 10 core totali ed è ideale per il testing su scala ridotta.
- **Medium**: distribuisce un cluster a disponibilità elevata con tre nodi master, due nodi di infrastruttura, quattro nodi di applicazione e un nodo bastion. Tutti i nodi tranne il nodo bastion sono macchine virtuali DS3v2 Standard. La dimensione del nodo bastion è DS2v2 Standard. Questo cluster richiede 38 core.
- **Large**: distribuisce un cluster a disponibilità elevata con tre nodi master, due nodi di infrastruttura, sei nodi di applicazione e un nodo bastion. I nodi master e di infrastruttura sono macchine virtuali DS3v2 Standard. I nodi di applicazione sono macchine virtuali DS4v2 Standard e il nodo bastion è di tipo DS2v2 Standard. Questo cluster richiede 70 core.

La configurazione del provider di soluzioni cloud di Azure è facoltativa per le dimensioni Medium e Large del cluster. La dimensione Small del cluster non offre un'opzione per la configurazione del provider di soluzioni cloud di Azure.

## <a name="connect-to-the-openshift-cluster"></a>Eseguire la connessione al cluster OpenShift

Al termine della distribuzione, eseguire la connessione alla console di OpenShift tramite il browser immettendo `OpenShift Console Uri`. In alternativa, è possibile connettersi al master OpenShift usando il comando seguente:

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile rimuovere il gruppo di risorse, il cluster OpenShift e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group#delete).

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

- [Attività di post-distribuzione](./openshift-post-deployment.md)
- [Risoluzione dei problemi relativi alla distribuzione di OpenShift in Azure](./openshift-troubleshooting.md)
- [Getting started with OpenShift Container Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html) (Introduzione a OpenShift Container Platform)
