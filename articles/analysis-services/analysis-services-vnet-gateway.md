---
title: Usare un gateway dati locale per le origini dati di Rete virtuale di Azure | Microsoft Docs
description: Informazioni su come configurare un server per l'uso di un gateway per le origini dati in una rete virtuale.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d1993996c0f982624d6976b50871fa8c4592e2f6
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301210"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Usare il gateway per le origini dati in un'istanza di Rete virtuale di Azure (VNet)

Questo articolo descrive la proprietà del server **AlwaysUseGateway** da usare quando le origini dati si trovano in un'istanza di [Rete virtuale di Azure (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Accesso del server alle origini dati della rete virtuale

Se l'accesso alle origini dati avviene tramite una rete virtuale, il server Azure Analysis Services deve connettersi a tali origini dati come se fossero nell'ambiente locale. È possibile configurare la proprietà del server **AlwaysUseGateway** per specificare il server per l'accesso a tutte le origini dati tramite un [Gateway locale](analysis-services-gateway.md). 

Istanza gestita di database SQL di Azure origini dati vengono eseguite all'interno di VNet di Azure con un indirizzo IP privato. Se l'endpoint pubblico è abilitato nell'istanza, non è necessario un gateway. Se l'endpoint pubblico non è abilitato, è necessario un gateway dati locale e la proprietà AlwaysUseGateway deve essere impostata su true.

> [!NOTE]
> Questa proprietà è valida solo quando viene installato e configurato un [gateway dati locale](analysis-services-gateway.md) . Il gateway può trovarsi nella rete virtuale.

## <a name="configure-alwaysusegateway-property"></a>Configurare la proprietà AlwaysUseGateway

1. In SSMS > server > **Proprietà** > **Generale** selezionare **Mostra proprietà avanzate**.
2. In **ASPaaS\AlwaysUseGateway** selezionare **true**.

    ![Proprietà AlwaysUseGateway](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Vedere anche
[Connessione a origini dati locali](analysis-services-gateway.md)   
[Installare e configurare un gateway dati locale](analysis-services-gateway-install.md)   
[Che cos'è Rete virtuale di Azure?](../virtual-network/virtual-networks-overview.md)   

