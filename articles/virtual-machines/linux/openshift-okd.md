---
title: Distribuire OKD in Azure | Microsoft Docs
description: Distribuire OKD in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/02/2019
ms.author: haroldw
ms.openlocfilehash: 7db50007dd32c84a360eaec25bf860709272437b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542510"
---
# <a name="deploy-okd-in-azure"></a>Distribuire OKD in Azure

La distribuzione di OKD (in precedenza OpenShift Origin) in Azure può essere eseguita in uno dei due modi seguenti:

- È possibile distribuire manualmente tutti i componenti necessari dell'infrastruttura di Azure e quindi seguire la [documentazione di OKD](https://docs.okd.io).
- È anche possibile usare un [modello di Resource Manager](https://github.com/Microsoft/openshift-origin) esistente che semplifica la distribuzione del cluster OKD.

## <a name="deploy-using-the-okd-template"></a>Distribuire usando il modello OKD

Per distribuire usando il modello di Resource Manager, viene usato un file di parametri per specificare tutti i parametri di input. Per personalizzare ulteriormente la distribuzione, creare una copia tramite fork dell'archivio GitHub e modificare gli elementi appropriati.

Tra le opzioni di personalizzazione comuni sono incluse, ad esempio:

- Dimensioni macchina virtuale bastion (variabile in azuredeploy.json)
- Convenzioni di denominazione (variabile in azuredeploy.json)
- Specifiche del cluster OpenShift - modificate usando il file hosts (deployOpenShift.sh)

Il [modello OKD](https://github.com/Microsoft/openshift-origin) dispone di più rami disponibili per versioni diverse di OKD.  In base alle esigenze, è possibile distribuire direttamente dal repository oppure è possibile dividere il repository e apportare modifiche personalizzate prima della distribuzione.

Usare il valore `appId` dall'entità servizio creata in precedenza per il parametro `aadClientId`.

L'esempio seguente mostra un file di parametri denominato azuredeploy.parameters.json con tutti gli input necessari.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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
        "storageKind": {
            "value": "managed"
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
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
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

Sostituire i parametri con le informazioni specifiche.

Versioni diverse possono avere parametri diversi, pertanto occorre verificare i parametri necessari per il ramo utilizzato.

### <a name="deploy-using-azure-cli"></a>Eseguire la distribuzione usando l'interfaccia della riga di comando di Azure


> [!NOTE] 
> Il comando seguente richiede l'interfaccia della riga di comando di Azure 2.0.8 o versione successiva. È possibile verificare la versione dell'interfaccia della riga di comando di Azure con il comando `az --version`. Per aggiornare la versione della CLI, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

L'esempio seguente consente di distribuire il cluster ODK e tutte le risorse correlate in un gruppo di risorse denominato openshiftrg, con nome di distribuzione myOpenShiftCluster. Viene fatto riferimento al modello direttamente dal repository GitHub e viene usato un file di parametri locale denominato azuredeploy.parameters.json.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

La distribuzione richiede almeno 30 minuti, a seconda del numero totale di nodi distribuiti. Al termine della distribuzione, l'URL della console di OpenShift e il nome DNS del master di OpenShift vengono visualizzati sul terminale. In alternativa, è possibile visualizzare la sezione di output della distribuzione dal portale di Azure.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Se non si vuole bloccare la riga di comando in attesa del completamento della distribuzione, aggiungere `--no-wait` come una delle opzioni per la distribuzione del gruppo. L'output dalla distribuzione può essere recuperato dal portale di Azure nella sezione distribuzione del gruppo di risorse.

## <a name="connect-to-the-okd-cluster"></a>Connettersi al cluster OKD

Al termine della distribuzione, eseguire la connessione alla console di OpenShift tramite il browser immettendo `OpenShift Console Url`. In alternativa, è possibile SSH al master OKD. Ecco un esempio che usa l'output della distribuzione:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile rimuovere il gruppo di risorse, il cluster OpenShift e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group).

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Passaggi successivi

- [Attività di post-distribuzione](./openshift-post-deployment.md)
- [Risoluzione dei problemi relativi alla distribuzione di OpenShift](./openshift-troubleshooting.md)
- [Introduzione a OKD](https://docs.okd.io)
