---
title: Creare e configurare un piano di protezione DDoS di Azure con l'interfaccia della riga di comando
description: Informazioni su come creare un piano di protezione DDoS usando l'interfaccia della riga di comando di Azure
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: e2f5528fde977520dc0aa0215a480a40ef8f1e7d
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989616"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-cli"></a>Guida introduttiva: creare e configurare protezione DDoS di Azure standard usando l'interfaccia della riga di comando

Introduzione a protezione DDoS di Azure standard usando l'interfaccia della riga di comando di Azure. 

Un piano di protezione DDoS definisce un set di reti virtuali in cui è abilitata la protezione DDoS standard per le sottoscrizioni. È possibile configurare un piano di protezione DDoS standard per l'organizzazione e collegare reti virtuali da più sottoscrizioni allo stesso piano. 

In questa Guida introduttiva si creerà un piano di protezione DDoS che verrà collegato a una rete virtuale. 

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Interfaccia della riga di comando di Azure installata in locale o Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-ddos-protection-plan"></a>Creare un piano di protezione DDoS

In Azure, si allocano le risorse correlate a un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno nuovo.

Per creare un gruppo di risorse usare [az group create](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-create). In questo esempio si denominano il gruppo di risorse _MyResourceGroup_ e si usa la località _Stati Uniti orientali_ :

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus
```

A questo punto, creare un piano di protezione DDoS denominato _MyDdosProtectionPlan_:

```azurecli-interactive
az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Abilitare la protezione DDoS per una rete virtuale

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Abilitare la protezione DDoS per una nuova rete virtuale

È possibile abilitare la protezione DDoS quando si crea una rete virtuale. In questo esempio il nome della rete virtuale _MyVnet_: 

```azurecli-interactive
az network vnet create \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --location eastus \
    --ddos-protection true
```

Non è possibile spostare una rete virtuale in un altro gruppo di risorse o in un'altra sottoscrizione quando la protezione DDoS standard è abilitata per la rete virtuale. Se si vuole spostare una rete virtuale con la protezione DDoS standard abilitata, disabilitare innanzitutto la protezione, spostare la rete virtuale e quindi abilitare la protezione DDoS standard. Dopo lo spostamento, vengono reimpostate le soglie dei criteri ottimizzati automaticamente per tutti gli indirizzi IP pubblici protetti nella rete virtuale.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Abilitare la protezione DDoS per una rete virtuale esistente

Quando si [Crea un piano di protezione DDoS](#create-a-ddos-protection-plan), è possibile associare una o più reti virtuali al piano. Per aggiungere più di una rete virtuale, è sufficiente elencare i nomi o gli ID, separati da spazi. In questo esempio si aggiungerà _MyVnet_:

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
    --vnet MyVnet
```

In alternativa, è possibile abilitare la protezione DDoS per una determinata rete virtuale:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --vnet MyVnet \
    --ddos-protection true
```

## <a name="validate-and-test"></a>Convalida e verifica

Per prima cosa, verificare i dettagli del piano di protezione DDoS:

```azurecli-interactive
az network ddos-protection show \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

Verificare che il comando restituisca i dettagli corretti del piano di protezione DDoS.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile salvare le risorse per l'esercitazione successiva. Se non è più necessario, eliminare il gruppo di risorse _MyResourceGroup_ . Quando si elimina il gruppo di risorse, viene eliminato anche il piano di protezione DDoS e tutte le risorse correlate. 

Per eliminare il gruppo di risorse, usare [az group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az_group_delete):

```azurecli-interactive
az group delete \
--name MyResourceGroup 
```

Aggiornare una determinata rete virtuale per disabilitare la protezione DDoS:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --vnet MyVnet \
    --ddos-protection false
```

Se si vuole eliminare un piano di protezione DDoS, è necessario innanzitutto annullare l'associazione di tutte le reti virtuali. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare e configurare i dati di telemetria per il piano di protezione DDoS, continuare con le esercitazioni.

> [!div class="nextstepaction"]
> [Visualizzare e configurare i dati di telemetria della protezione DDoS](telemetry-monitoring-alerting.md)