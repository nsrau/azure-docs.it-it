---
title: Configurare un gateway di rete virtuale per ExpressRoute con PowerShell:versione classica:Azure | Documentazione Microsoft
description: Configurare un gateway VNet per una rete virtuale con modello di distribuzione classico usando PowerShell per una configurazione ExpressRoute.
documentationcenter: na
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 11/05/2018
ms.author: charwen
ms.openlocfilehash: d0425b68f1b241bde4b2100d13d60165e5a1f1fe
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51255173"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Configurare un gateway di rete virtuale per ExpressRoute usando PowerShell (versione classica)
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classica: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Portale di Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
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

