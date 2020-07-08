---
title: 'Scenario: firewall di Azure-routing Interhub'
titleSuffix: Azure Virtual WAN
description: Scenari per il routing del routing tra più hub con firewall di Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 1057dc28682c90374b2f8e8e0297f3d769c08bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567624"
---
# <a name="scenario-azure-firewall---interhub-preview"></a>Scenario: firewall di Azure-Interhub (anteprima)

Quando si usa il routing dell'hub virtuale WAN virtuale, esistono molti scenari disponibili. In questo scenario, l'obiettivo è eseguire il routing tra più hub che contengono il firewall di Azure. Per informazioni sul routing degli hub virtuali, vedere [About Virtual Hub routing](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Architettura dello scenario

In questo scenario si presuppone la configurazione seguente:

* Sono presenti più hub con il firewall di Azure in ogni hub.
* Si usa l'hub virtuale protetto.
* Sono stati configurati i criteri appropriati per il traffico privato (VNet), Internet e ramo.
* Da VNet a Internet (V2I), da VNet a Branch (V2B), da ramo a VNet (B2V), tutti passano attraverso il firewall di Azure in ogni hub.

Considerazioni aggiuntive:

* Per uno scenario tra hub con il firewall di Azure, il presupposto è che spoke reti virtuali non è associato a tabelle di route separate. (ad esempio, **VNET 1** associato alla **tabella di route a**e **VNET 2** associato alla **tabella di Route B**). Tutte le reti virtuali associate alla stessa tabella di route in cui risiedono le route per il firewall di Azure.
* La protezione tramite il firewall di Azure è attualmente limitata a **Branch <->** solo traffico **Internet** e VNet. Il flusso da ramo a ramo tramite il firewall di Azure attualmente non è supportato.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/architecture.png" alt-text="architettura":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Flusso di lavoro dello scenario

Per configurare questo scenario, prendere in considerazione i passaggi seguenti:

### <a name="step-1"></a><a name="step-1"></a>Step 1

Supponendo che siano già state protette le connessioni tramite Azure Firewall Manager, il primo passaggio consiste nel garantire che tutte le connessioni Branch e VNet propaghino **Nessuna**. Questa operazione è necessaria per garantire che il traffico venga impostato tramite il firewall di Azure.

1. Selezionare l'hub e modificare la tabella di route **None** .
1. Aggiornamento della **propagazione** per selezionare tutti i rami e tutti reti virtuali.

### <a name="step-2"></a><a name="step-2"></a>Passaggio 2

Configurare una tabella di route personalizzata per hub.

1. Per **hub 1**, creare una tabella di route **RT_Hub1**.

    * Se è stato usato gestione firewall di Azure, viene creata automaticamente una route per 0.0.0.0/0 con hop successivo **AZFW1** nella tabella di route predefinita dell'hub. Questa impostazione verrà modificata nel passaggio 3. Per **RT_Hub1**, creare in modo esplicito una voce per 0.0.0.0/0 con hop successivo **AZFW1**. Questa impostazione attiverà V2V, B2V e V2I tramite AZFW1.
    * Poiché si vuole che i rami **Hub 2** e reti virtuali siano raggiungibili tramite **AZFW2** dall' **hub 1** (anziché tramite AZFW1), è necessario aggiungere altre 2 Route aggregate per i rami **hub 2** (10.5.0.0/16->AZFW2) e reti virtuali (10.2.0.0/16->AZFW2).

1. Per **Hub 2**creare **RT_Hub2**tabella di route.

    * Se è stato usato gestione firewall di Azure, viene creata automaticamente una route per 0.0.0.0/0 con hop successivo **AZFW2** nella tabella di route predefinita dell'hub. Questa impostazione verrà modificata nel passaggio 3. Per **RT_Hub2**, creare in modo esplicito una voce per 0.0.0.0/0 con hop successivo **AZFW2**. Questa impostazione attiverà V2V, B2V e V2I tramite **AZFW2**.
    * Poiché si vuole che il traffico tra hub sia protetto da **AZFW2**di hub 2, non è necessario aggiungere in modo esplicito un passaggio simile al secondo punto elenco nel passaggio precedente dell'hub 1.

### <a name="step-3"></a><a name="step-3"></a>Passaggio 3

Modificare la **tabella di route predefinita** in ogni hub per aggiungere una route statica per il **ramo a VNet** (B2V) e i flussi **da ramo a Internet** (B2I) tramite il firewall di Azure. Il ramo al ramo non è attualmente supportato tramite il firewall di Azure.

1. Per la **tabella di route predefinita dell'hub 1**:

    * **Ramo a hub 2 rami tramite AZFW2**: 10.5.0.0/16->AZFW2. Questa configurazione configura una route per il firewall Hub 2.
    * **Da ramo a hub 2 reti virtuali tramite AZFW2**: 10.2.0.0/16->AZFW2. Questa configurazione configura una route per il firewall Hub 2.
    * Da **ramo a ramo (locale)/ramo a VNet (locale)/ramo a Internet**: 0.0.0.0/0->AZFW1.

2. Per la **tabella di route predefinita Hub 2**:

    * Ramo a ramo (locale e remoto)/ramo in VNet (locale e remota)/ramo a Internet: 0.0.0.0/0->AZFW2.

Questo comporterà la modifica della configurazione di routing, come illustrato nella figura seguente.

**Figura 2**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/workflow.png" alt-text="workflow":::

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
* Per altre informazioni su come configurare il routing dell'hub virtuale, vedere [How to Configure Virtual Hub routing](how-to-virtual-hub-routing.md).
