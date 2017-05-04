---
title: Configurare un gateway di rete virtuale per ExpressRoute con PowerShell:versione classica:Azure | Documentazione Microsoft
description: Configurare un gateway VNet per una rete virtuale con modello di distribuzione classico usando PowerShell per una configurazione ExpressRoute.
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 85ee0bc1-55be-4760-bfb4-34d9f2c96f30
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: charwen
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: aa9b7e3ac07d4da035afd549fbeebaec17b56426
ms.lasthandoff: 04/27/2017


---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Configurare un gateway di rete virtuale per ExpressRoute usando PowerShell (versione classica)
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classica: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Portale di Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Questo articolo illustra le procedure di aggiunta,ridimensionamento e rimozione di un gateway di rete virtuale (VNet) per una rete virtuale già esistente. I passaggi di questa configurazione sono specifici per le reti virtuali create usando il **modello di distribuzione classico** e che verranno usate in una configurazione ExpressRoute. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Informazioni sui modelli di distribuzione di AzureAbout Azure deployment models**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Prima di iniziare
Verificare di avere installato i cmdlet di Azure PowerShell richiesti per questa configurazione (1.0.2 o versione successiva). Se i cmdlet non sono stati installati, è necessario effettuare l’operazione prima di iniziare i passaggi di configurazione. Per altre informazioni sull'installazione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere creato il gateway VNet, è possibile collegare la rete virtuale a un circuito ExpressRoute. Vedere [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md).


