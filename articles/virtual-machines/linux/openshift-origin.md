---
title: Distribuire OKD in Azure | Microsoft Docs
description: Distribuire OKD in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: e525bf0a5aa9bda7fdbbcefc4cb5b683c7fabc3b
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426227"
---
# <a name="deploy-okd-in-azure"></a>Distribuire OKD in Azure

La distribuzione di OKD (in precedenza OpenShift Origin) in Azure può essere eseguita in uno dei due modi seguenti:

- È possibile distribuire manualmente tutti i componenti necessari dell'infrastruttura di Azure e quindi seguire la [documentazione](https://docs.okd.io/3.10/welcome/index.html) di OKD.
- È anche possibile usare un [modello di Resource Manager](https://github.com/Microsoft/openshift-origin) esistente che semplifica la distribuzione del cluster OKD.

## <a name="deploy-by-using-the-okd-template"></a>Distribuire usando il modello OKD

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

### <a name="deploy-by-using-azure-cli"></a>Eseguire la distribuzione usando l'interfaccia della riga di comando di Azure


> [!NOTE] 
> Il comando seguente richiede l'interfaccia della riga di comando di Azure 2.0.8 o versione successiva. È possibile verificare la versione dell'interfaccia della riga di comando di Azure con il comando `az --version`. Per aggiornare la versione della CLI, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

L'esempio seguente consente di distribuire il cluster ODK e tutte le risorse correlate in un gruppo di risorse denominato myResourceGroup con nome di distribuzione myOpenShiftCluster. Viene fatto riferimento al modello direttamente dal repository GitHub e viene usato un file di parametri locale denominato azuredeploy.parameters.json.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

La distribuzione richiede almeno 25 minuti, a seconda del numero totale di nodi distribuiti. Al termine della distribuzione, l'URL della console di ODK e il nome DNS del master di OpenShift vengono visualizzati sul terminale.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-okd-cluster"></a>Connettersi al cluster OKD

Al termine della distribuzione, eseguire la connessione alla console di ODK tramite il browser immettendo `OpenShift Console Uri`. In alternativa, è possibile connettersi al master ODK usando il comando seguente:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile rimuovere il gruppo di risorse, il cluster OpenShift e tutte le risorse correlate tramite il comando [az group delete](/cli/azure/group#az_group_delete).

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

- [Attività di post-distribuzione](./openshift-post-deployment.md)
- [Risoluzione dei problemi relativi alla distribuzione di OpenShift](./openshift-troubleshooting.md)
- [Introduzione a OKD](https://docs.okd.io/latest/getting_started/index.html)
