---
title: Esempio di distribuzione di script dell'interfaccia della riga di comando di Azure
description: Creare un cluster Linux di Service Fabric protetto in Azure usando l'interfaccia della riga di comando di Azure.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 1386be8093cac4f5271886ff736b24acd483d64e
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69032821"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Creare un cluster Linux di Service Fabric protetto in Azure

Questo comando crea un certificato autofirmato, lo aggiunge a un insieme di credenziali delle chiavi e lo scarica in locale.  Il nuovo certificato viene usato per proteggere il cluster in fase di distribuzione.  È possibile anche usare un certificato esistente anziché crearne uno nuovo.  In ogni caso il nome del soggetto del certificato deve corrispondere al dominio usato per accedere al cluster di Service Fabric. Questa corrispondenza è necessaria per fornire un certificato SSL per gli endpoint di gestione HTTPS del cluster e Service Fabric Explorer. Non è possibile ottenere un certificato SSL da una CA per il dominio `.cloudapp.azure.com`. È necessario ottenere un nome di dominio personalizzato per il cluster. Quando si richiede un certificato da una CA, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato usato per il cluster.

Se necessario, installare l'[interfaccia della riga di comando di Azure](/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sample-script"></a>Script di esempio

[!code-sh[main](../../../cli_scripts/service-fabric/create-cluster/create-cluster.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo avere eseguito lo script di esempio, usare il comando seguente per rimuovere il gruppo di risorse, il cluster e tutte le risorse correlate.

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az sf cluster create](https://docs.microsoft.com/cli/azure/sf/cluster?view=azure-cli-latest) | Crea un nuovo cluster di Service Fabric.  |

## <a name="next-steps"></a>Passaggi successivi

Altri esempi dell'interfaccia della riga di comando di Service Fabric per Azure Service Fabric sono disponibili negli [esempi dell'interfaccia della riga di comando di Service Fabric](../samples-cli.md).
