---
title: Connettersi a un sinapsi studio usando collegamenti privati
description: Questo articolo illustra come connettersi ad Azure sinapsi studio usando i collegamenti privati
author: NanditaV
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 2613a4fd931ad49a4f40a4221ea20e8c25f185fe
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501586"
---
# <a name="connect-to-azure-synapse-studio-using-azure-private-link-hubs"></a>Connettersi ad Azure sinapsi Studio tramite hub di collegamento privato di Azure 

Questo articolo illustra come vengono usati i collegamenti privati degli hub di collegamento privato di Azure sinapsi Analytics per connettersi in modo sicuro a sinapsi Studio. 

## <a name="azure-private-link-hubs"></a>Hub di collegamento privato di Azure 
È possibile connettersi in modo sicuro ad Azure sinapsi studio dalla rete virtuale di Azure usando collegamenti privati. Gli hub di collegamento privato di Azure sinapsi Analytics sono risorse di Azure che fungono da connettori tra la rete protetta e l'esperienza Web di sinapsi Studio. 

Ci sono due passaggi per connettersi a sinapsi studio usando i collegamenti privati. In primo luogo, è necessario creare una risorsa Hub di collegamento privato. In secondo luogo, è necessario creare un endpoint privato dalla rete virtuale di Azure a questo hub di collegamento privato. È quindi possibile usare endpoint privati per comunicare in modo sicuro con sinapsi Studio. È necessario integrare gli endpoint privati con la soluzione DNS, ovvero la soluzione locale o Azure DNS privato. 

## <a name="azure-private-links-hubs-and-azure-synapse-studio"></a>Hub collegamenti privati di Azure e Azure sinapsi Studio
È possibile usare una singola risorsa Hub di collegamento privato di Azure sinapsi per connettersi privatamente a tutte le aree di lavoro di Azure sinapsi Analytics usando Azure sinapsi Studio. Le aree di lavoro non devono trovarsi nella stessa area dell'hub di collegamento privato della sinapsi di Azure. La risorsa Hub di collegamento privato di Azure sinapsi può essere usata anche per le connessioni alle aree di lavoro sinapsi in sottoscrizioni diverse o Azure AD tenant.

È possibile creare l'hub di collegamento privato cercando Hub di collegamento *privato sinapsi* nel portale di Azure e selezionando **Azure sinapsi Analytics (hub di collegamento privato)** dai servizi. Per informazioni dettagliate, attenersi alla procedura illustrata nella Guida per la [connessione alle risorse dell'area di lavoro da una rete con restrizioni](./how-to-connect-to-workspace-from-restricted-network.md) .

>[!NOTE]
>Gli hub di collegamento privato sono destinati a caricare in modo sicuro il contenuto statico sinapsi studio su collegamenti privati. È necessario creare **endpoint privati separati** per le risorse a cui si desidera connettersi all'interno dell'area di lavoro, ad esempio i pool SQL con provisioning/dedicato o i pool Spark. 

## <a name="azure-private-links-hubs-and-azure-virtual-network"></a>Hub collegamenti privati di Azure e rete virtuale di Azure
È necessario connettere la rete virtuale di Azure alla risorsa dell'hub di collegamento privato sinapsi per proteggere la connessione end-to-end a sinapsi Studio. A tale proposito, è necessario creare un endpoint privato dalla rete virtuale all'hub di collegamento privato creato. È possibile usare la portale di Azure per l'hub di collegamento privato e passare alla sezione endpoint privato. Selezionare "+ endpoint privato" per creare un nuovo endpoint privato che si connette all'hub di collegamento privato.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-private-endpoint.png" alt-text="Creare un endpoint privato per l'hub di collegamento privato":::

Assicurarsi di scegliere il tipo di risorsa "Microsoft. sinapsi/privateLinkHubs" nella scheda "risorsa". :::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-resource-type.png" alt-text="creare un endpoint privato per l'hub di collegamento privato":::

Nella scheda "configurazione" selezionare "privatelink.azuresynapse.net" per le zone DNS privato durante l'integrazione con la rete virtuale e la zona DNS privata.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-dns-zones.png" alt-text="Creare un endpoint privato per l'hub di collegamento privato":::

## <a name="next-steps"></a>Passaggi successivi

[Connettersi alle risorse dell'area di lavoro da una rete con restrizioni](./how-to-connect-to-workspace-from-restricted-network.md)

Altre informazioni sulla [rete virtuale dell'area di lavoro gestita](./synapse-workspace-managed-vnet.md)

Altre informazioni sugli [endpoint privati gestiti](./synapse-workspace-managed-private-endpoints.md)

[Creare endpoint privati gestiti per le origini dati](./how-to-create-managed-private-endpoints.md)

[Connettersi all'area di lavoro sinapsi usando endpoint privati](./how-to-connect-to-workspace-with-private-links.md)

