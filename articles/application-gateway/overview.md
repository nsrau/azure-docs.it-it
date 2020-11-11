---
title: Cos'è il gateway applicazione di Azure
description: Informazioni su come usare un gateway applicazione di Azure per gestire il traffico Web verso l'applicazione.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 52bc2a20be0d7113583b7f23cc569dbb9503d364
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397247"
---
# <a name="what-is-azure-application-gateway"></a>Cos'è il gateway applicazione di Azure?

Il gateway applicazione di Azure è un servizio di bilanciamento del carico del traffico Web che consente di gestire il traffico verso le applicazioni Web. I servizi di bilanciamento del carico tradizionali operano a livello di trasporto (OSI livello 4 - TCP e UDP) ed eseguono il routing del traffico da un indirizzo IP e una porta di origine verso un indirizzo IP e una porta di destinazione.

Il gateway applicazione consente di prendere decisioni relative al routing basate su altri attributi di una richiesta HTTP, ad esempio il percorso dell'URI o le intestazioni host. Ad esempio, è possibile eseguire il rounting del traffico in base all'URL in ingresso. Pertanto, se `/images` è nell'URL in ingresso, è possibile eseguire il rounting del traffico verso un set specifico di server, detto pool, configurato per le immagini. Se `/video` è incluso nell'URL, il routing del traffico corrispondente viene instradato a un altro pool ottimizzato per i video.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Questo tipo di routing è detto bilanciamento del carico a livello di applicazione (OSI livello 7). Il gateway applicazione di Azure può eseguire il routing basato su URL e molto altro.

>[!NOTE]
> Azure offre una suite di soluzioni di bilanciamento del carico completamente gestite per tutti gli scenari. Se è necessario un bilanciamento del carico di livello 4 a elevate prestazioni e bassa latenza, vedere [Informazioni su Azure Load Balancer](../load-balancer/load-balancer-overview.md). Per il bilanciamento del carico DNS globale, vedere [Informazioni su Gestione traffico](../traffic-manager/traffic-manager-overview.md). Gli scenari end-to-end possono trarre vantaggio dalla combinazione di queste soluzioni in base alle esigenze.
>
> Per un confronto tra le opzioni di bilanciamento del carico di Azure, vedere [Panoramica delle opzioni di bilanciamento del carico in Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="features"></a>Funzionalità

Per informazioni sulle funzionalità del gateway applicazione, vedere [Funzionalità del gateway applicazione di Azure](features.md).

## <a name="pricing-and-sla"></a>Prezzi e contratto di servizio

Per informazioni sui prezzi del gateway applicazione, vedere [Prezzi del gateway applicazione](https://azure.microsoft.com/pricing/details/application-gateway/).

Per informazioni sul contratto di servizio del gateway applicazione, vedere [Contratto di servizio del gateway applicazione](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/).

## <a name="whats-new"></a>Novità

Per informazioni sulle novità del gateway applicazione di Azure, vedere gli [aggiornamenti di Azure](https://azure.microsoft.com/updates/?category=networking&query=Application%20Gateway).

## <a name="next-steps"></a>Passaggi successivi

A seconda dei requisiti e dell'ambiente, è possibile creare un'applicazione gateway di test usando il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure.

- [Avvio rapido: Indirizzare il traffico Web con il gateway applicazione di Azure - Portale di Azure](quick-create-portal.md)
- [Guida introduttiva: Indirizzare il traffico Web con un gateway applicazione di Azure - Azure PowerShell](quick-create-powershell.md)
- [Avvio rapido: Indirizzare il traffico Web con un gateway applicazione Azure - Interfaccia della riga di comando di Azure](quick-create-cli.md)