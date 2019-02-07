---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Gestire il traffico Web | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Gestire il traffico Web con un gateway applicazione e un set di scalabilità di macchine virtuali.
services: application-gateway
documentationcenter: networking
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: b2485b65c54bdd5dcd523226e17c180bf1eb86a6
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734199"
---
# <a name="manage-web-traffic-using-the-azure-cli"></a>Gestire il traffico Web con l'interfaccia della riga di comando di Azure

Questo script crea un gateway applicazione che usa un set di scalabilità di macchine virtuali per server back-end. Il gateway applicazione può quindi essere configurato per gestire il traffico Web. Dopo aver eseguito lo script, è possibile testare il gateway applicazione tramite il relativo indirizzo IP pubblico.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire il comando seguente per rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Crea una rete virtuale. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnetwork) | Crea una subnet in una rete virtuale. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest) | Crea l'indirizzo IP pubblico per il gateway applicazione. |
| [az network application-gateway create](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest) | Creare un gateway applicazione. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az-vmss-create) | Crea un set di scalabilità di macchine virtuali. |
| [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip) | Ottiene l'indirizzo IP pubblico del gateway applicazione. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando per il gateway applicazione sono reperibili nella [documentazione della macchina virtuale Windows Azure](../cli-samples.md).
