---
title: Spostare un peering pubblico su Azure ExpressRoute nel peering Microsoft | Microsoft Docs
description: Questo articolo illustra i passaggi per spostare il peering pubblico nel peering Microsoft su ExpressRoute.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2017
ms.author: cherylmc
ms.openlocfilehash: 311e1de3200cd684bbec1329ebd5217b4fb3a2e2
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2017
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Spostare un peering pubblico nel peering Microsoft

ExpressRoute supporta ora i servizi PaaS Azure, come l'archiviazione di Azure e il database SQL di Azure, usando il peering Microsoft con i filtri di route. È ora necessario un solo dominio di routing per accedere ai servizi PaaS e SaaS Microsoft. È possibile sfruttare i filtri di route per annunciare in modo selettivo i prefissi dei servizi PaaS per le aree di Azure che si vuole usare.

Questo articolo descrive come spostare una configurazione di peering pubblico nel peering Microsoft senza tempi di inattività. Per altre informazioni sul routing di domini e i peering, vedere l'articolo relativo ai [domini di routing e i circuiti ExpressRoute](expressroute-circuit-peerings.md).

> [!IMPORTANT]
> Per usare il peering Microsoft, è necessario disporre del componente aggiuntivo Premium ExpressRoute. Per altre informazioni sul componente aggiuntivo Premium, vedere le [domande frequenti su ExpressRoute](expressroute-faqs.md#expressroute-premium).

## <a name="before-you-begin"></a>Prima di iniziare

* Per connettersi al peering Microsoft, è necessario configurare e gestire i requisiti NAT. Il provider di connettività può configurare e gestire NAT come servizio gestito. Se si intende accedere ai servizi PaaS e SaaS Azure nel peering Microsoft, è importante ridimensionare il pool di indirizzi IP NAT correttamente. Per altre informazioni su NAT per ExpressRoute, vedere i [requisiti NAT per il peering Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Se si dispone di un elenco di controllo di accesso (ACL) di rete per gli endpoint servizio PaaS di Azure quando si usa il peering pubblico Azure, è necessario assicurarsi che il pool di indirizzi IP NAT configurato con il peering Microsoft sia incluso nell'elenco di controllo di accesso configurato per l'endpoint servizio.

Per passare nel peering Microsoft senza tempi di inattività, è necessario usare i passaggi in questo articolo nell'ordine in cui sono elencati.

## <a name="1-create-microsoft-peering"></a>1. Creare il peering Microsoft

Se il peering Microsoft non è stato creato, consultare uno qualsiasi dei seguenti articoli per creare il peering Microsoft. Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di abilitare il peering Microsoft per il circuito.

  * [Creare il peering Microsoft usando il Portale di Azure](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Creare il peering Microsoft usando Azure Powershell](expressroute-howto-routing-arm.md#msft)
  * [Creare il peering Microsoft usando l'interfaccia della riga di comando di Azure](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a>2. Verificare che il peering Microsoft sia abilitato.

Verificare che il peering Microsoft sia abilitato e che i prefissi pubblici annunciati si trovino nello stato configurato.

  * [Portale di Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [Interfaccia della riga di comando di Azure](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a>3. Configurare e associare un filtro di route al circuito

Per impostazione predefinita, i nuovi peering Microsoft non annunciano i prefissi fino a quando non viene associato un filtro di route al circuito. Quando si crea una regola di filtro di route, è possibile specificare l'elenco di community di servizio per le aree di Azure che si vuole usare per i servizi PaaS Azure, come illustrato nella schermata seguente:

![Unire il peering pubblico](.\media\how-to-move-peering\public.png)

Usare uno dei seguenti articoli per configurare i filtri di route.

  * [Configurare i filtri di route per il peering Microsoft usando il Portale di Azure](how-to-routefilter-portal.md)
  * [Configurare i filtri di route per il peering Microsoft usando Azure PowerShell](how-to-routefilter-powershell.md)
  * [Configurare i filtri di route per il peering Microsoft usando l'interfaccia della riga di comando di Azure](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a>4. Eliminare il peering pubblico.

Dopo avere verificato la corretta configurazione del peering Microsoft e il corretto annuncio dei prefissi da usare nel peering Microsoft, sarà possibile eliminare il peering pubblico. Per eliminare il peering pubblico, leggere uno dei seguenti articoli:

  * [Eliminare il peering pubblico di Azure usando il Portale di Azure](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Eliminare il peering pubblico di Azure usando Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)
  * [Eliminare il peering pubblico di Azure usando l'interfaccia della riga di comando](howto-routing-cli.md#deletepublic)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).