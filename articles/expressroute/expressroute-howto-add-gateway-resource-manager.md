---
title: 'Aggiungere un gateway di rete virtuale a una rete virtuale per ExpressRoute: PowerShell: Azure | Documentazione Microsoft'
description: Questo articolo illustra come aggiungere un gateway di rete virtuale a una rete virtuale di Resource Manager già creata per ExpressRoute.
documentationcenter: na
services: expressroute
author: charwen
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 63e0bd60-abad-4963-8e27-3aa973e0d968
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2017
ms.author: charwen
ms.openlocfilehash: 32e49a11b02afedf69e5aa61ca2f626ffe5a125e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239577"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Configurare un gateway di rete virtuale per ExpressRoute usando PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classica: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Portale di Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Questo articolo illustra le procedure per l'aggiunta, il ridimensionamento e la rimozione di un gateway di rete virtuale per una rete virtuale già esistente. I passaggi di questa configurazione sono specifici per le reti virtuali create usando il modello di distribuzione di Resource Manager che viene usato in una configurazione ExpressRoute. Per altre informazioni sui gateway di rete virtuale e sulle impostazioni di configurazione dei gateway per ExpressRoute, vedere [Informazioni sui gateway di rete virtuale per ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Prima di iniziare
Assicurarsi che sia installata la versione più recente dei cmdlet Azure PowerShell. Se i cmdlet più recenti non sono stati installati, è necessario eseguire l'operazione prima di iniziare i passaggi di configurazione. Per altre informazioni, vedere [Installare e configurare Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere creato il gateway VNet, è possibile collegare la rete virtuale a un circuito ExpressRoute. Vedere [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

