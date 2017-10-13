---
title: Creare un cluster Linux di Service Fabric in Azure | Microsoft Docs
description: Informazioni su come distribuire un cluster Linux di Service Fabric in una rete virtuale di Azure esistente tramite l'interfaccia della riga di comando di Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: 1c493a3fa00d5185f8210fe25e3065bd7b32a41f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>Distribuire un cluster Linux di Service Fabric in una rete virtuale di Azure
Questa è la prima di una serie di esercitazioni. Si apprenderà come distribuire un cluster Linux di Service Fabric in una rete virtuale e in una subnet di Azure esistente tramite l'interfaccia della riga di comando di Azure. Al termine, si ottiene un cluster in esecuzione nel cloud nel quale è possibile distribuire applicazioni. Per creare un cluster Windows tramite PowerShell, vedere [Creare un cluster sicuro di Windows in Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una rete virtuale in Azure usando l'interfaccia della riga di comando di Azure
> * Creare un cluster sicuro di Service Fabric in Azure usando l'interfaccia della riga di comando di Azure
> * Proteggere il cluster con un certificato X.509
> * Connettersi al cluster con l'interfaccia della riga di comando di Service Fabric
> * Rimuovere un cluster

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * Creare un cluster sicuro in Azure
> * [Distribuire Gestione API e Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Installare l'[interfaccia della riga di comando di Service Fabric](service-fabric-cli.md)
- Installare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli)

Le procedure seguenti creano un cluster di Service Fabric a cinque nodi. Per calcolare i costi sostenuti per l'esecuzione di un cluster di Service Fabric in Azure, usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Accedere ad Azure e selezionare la sottoscrizione
Questa guida usa l'interfaccia della riga di comando di Azure. Quando si avvia una nuova sessione accedere al proprio account Azure e selezionare la sottoscrizione prima di eseguire i comandi di Azure.
 
Eseguire questo script per accedere al proprio account Azure e selezionare la sottoscrizione:

```azurecli
az login
az account set --subscription <guid>
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un nuovo gruppo di risorse per la distribuzione e assegnargli un nome e un percorso.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
az group create --name $ResourceGroupName --location $Location
```

## <a name="deploy-the-network-topology"></a>Distribuire la topologia di rete
Configurare quindi la topologia di rete nella quale verrà distribuito il cluster di Gestione API e Service Fabric. Il modello di Gestione risorse [network.json][network-arm] è configurato per creare una rete virtuale (VNET), nonché una subnet e un gruppo di sicurezza di rete (NSG) per Service Fabric e una subnet e un gruppo di sicurezza di rete (NSG) per Gestione API. Altre informazioni sulle reti virtuali, le subnet e NSG sono reperibili [qui](../virtual-network/virtual-networks-overview.md).

Il file dei parametri [network.parameters.json][network-parameters-arm] contiene i nomi delle subnet e dei gruppi di sicurezza di rete in cui verranno distribuiti Service Fabric e Gestione API.  Gestione API viene distribuito nell'[esercitazione seguente](service-fabric-tutorial-deploy-api-management.md). In questa guida non è necessario modificare i valori dei parametri. I modelli di Gestione risorse di Service Fabric usano questi valori.  Se i valori vengono modificati qui, è necessario modificarli negli altri modelli di Gestione risorse usati in questa esercitazione e nell'[esercitazione di distribuzione di Gestione API](service-fabric-tutorial-deploy-api-management.md). 

Scaricare il modello e il file di parametri di Resource Manager seguenti:
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

Per distribuire il modello e i file dei parametri di Gestione risorse per la configurazione di rete, usare lo script seguente:

```azurecli
az group deployment create \
    --name VnetDeployment \
    --resource-group $ResourceGroupName \
    --template-file network.json \
    --parameters @network.parameters.json
```
<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Distribuire il cluster di Service Fabric
Una volta terminata la distribuzione delle risorse di rete, il passaggio successivo consiste nel distribuire un cluster Service Fabric per la rete virtuale nella subnet e nel gruppo NSG designato per tale cluster. La distribuzione di un cluster in una rete virtuale esistente e una subnet (distribuito in precedenza in questo articolo) richiede un modello di Gestione risorse.  Per altre informazioni, vedere [Creare un cluster usando Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Per questa serie di esercitazioni, il modello è preconfigurato per usare i nomi di rete virtuale, subnet e gruppo di sicurezza di rete configurati in un passaggio precedente.  Scaricare il modello e il file di parametri di Resource Manager seguenti:
- [linuxcluster.json][cluster-arm]
- [linuxcluster.Parameters.json][cluster-parameters-arm]

Compilare i parametri **clusterName**, **adminUserName** e **adminPassword** vuoti nel file *linuxcluster.parameters.json* per la distribuzione.  Lasciare vuoti i parametri **certificateThumbprint**, **certificateUrlValue** e **sourceVaultValue** se si desidera creare un certificato autofirmato.  Se è disponibile un certificato esistente caricato in precedenza in un insieme di credenziali delle chiavi, immettere quei valori di parametro.

Per distribuire il cluster usando il modello e il file dei parametri di Gestione risorse, usare lo script seguente.  Un certificato autofirmato viene creato nell'insieme di credenziali delle chiavi specificato e viene usato per proteggere il cluster.  Il certificato viene anche scaricato localmente.

```azurecli
Password="q6D7nN%6ck@6"
Subject="aztestcluster.southcentralus.cloudapp.azure.com"
VaultName="linuxclusterkeyvault"
az group create --name $ResourceGroupName --location $Location

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file linuxcluster.json --parameter-file linuxcluster.parameters.json

```

## <a name="connect-to-the-secure-cluster"></a>Connettersi al cluster sicuro
Connettersi al cluster tramite l'interfaccia della riga di comando di Service Fabric `sfctl cluster select` usando la chiave.  Usare solo l'opzione **--no-verify** per un certificato autofirmato.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Verificare di essere connessi e che il cluster sia integro usando il comando `sfctl cluster health`.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Pulire le risorse
Gli altri articoli in questa serie di esercitazioni usano il cluster appena creato. Se non si intende passare subito all'articolo successivo, è opportuno eliminare il cluster per evitare di sostenere costi. Il modo più semplice per eliminare il cluster e tutte le risorse che utilizza consiste nell'eliminare il gruppo di risorse.

Accedere ad Azure e selezionare l'ID della sottoscrizione da usare per rimuovere il cluster.  È possibile trovare l'ID della sottoscrizione accedendo al [portale di Azure](http://portal.azure.com). Eliminare il gruppo di risorse e tutte le risorse del cluster con il comando [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete).

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare una rete virtuale in Azure usando l'interfaccia della riga di comando di Azure
> * Creare un cluster sicuro di Service Fabric in Azure usando l'interfaccia della riga di comando di Azure
> * Proteggere il cluster con un certificato X.509
> * Connettersi al cluster con l'interfaccia della riga di comando di Service Fabric
> * Rimuovere un cluster

Procedere con l'esercitazione seguente per scoprire come distribuire Gestione API con Service Fabric.
> [!div class="nextstepaction"]
> [Distribuire Gestione API](service-fabric-tutorial-deploy-api-management.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/linuxcluster.parameters.json
