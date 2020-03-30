---
title: Che cos'è un hub virtuale protetto?
description: Informazioni sugli hub virtuali protetti
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518437"
---
# <a name="what-is-a-secured-virtual-hub"></a>Che cos'è un hub virtuale protetto?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Un hub virtuale è una rete virtuale gestita da Microsoft che consente la connettività da altre risorse. Quando un hub virtuale viene creato da una rete WAN virtuale nel portale di Azure, una rete virtuale dell'hub virtuale e i gateway (facoltativo) vengono creati come componenti.

Un hub virtuale *protetto* è un hub WAN virtuale di Azure con criteri di sicurezza e routing associati configurati da Gestione firewall di Azure.A secured virtual hub is an [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) with associated security and routing policy configured by Azure Firewall Manager. Usa hub virtuali protetti per creare facilmente architetture hub-and-spoke e transitive con servizi di sicurezza nativi per la governance e la protezione del traffico. 

È possibile utilizzare un hub virtuale protetto come rete virtuale centrale gestita senza connettività locale. Sostituisce la rete virtuale centrale precedentemente necessaria per una distribuzione di Firewall di Azure.It replaces the central VNet that was previously required for an Azure Firewall deployment. Poiché l'hub virtuale protetto fornisce il routing automatico, non è necessario configurare i propri UDR (route definite dall'utente) per instradare il traffico attraverso il firewall.

È anche possibile usare hub virtuali protetti come parte di un'architettura WAN virtuale completa. Questa architettura offre connettività di succursale protetta, ottimizzata e automatizzata verso e tramite Azure.This architecture provides secured, optimized, and automated branch connectivity to and through Azure. È possibile scegliere i servizi per proteggere e gestire il traffico di rete, inclusi Firewall di Azure e altri provider di sicurezza di terze parti come servizio (SECaaS).

## <a name="create-a-secured-virtual-hub"></a>Creare un hub virtuale protettoCreate a secured virtual hub

Using Firewall Manager in the Azure portal, you can either create a new secured virtual hub, or convert an existing virtual hub that you previously created using Azure Virtual WAN.

## <a name="next-steps"></a>Passaggi successivi

Per creare un hub virtuale protetto e usarlo per proteggere e gestire una rete hub e spoke, vedere [Esercitazione: Proteggere la rete cloud con Azure Firewall Manager tramite il portale](secure-cloud-network.md)di Azure.