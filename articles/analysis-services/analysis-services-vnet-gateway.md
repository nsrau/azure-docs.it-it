---
title: Configurare Azure Analysis Services per le origini dati della rete virtuale | Microsoft Docs
description: Informazioni su come configurare un server di Azure Analysis Services per l'uso di un gateway per le origini dati in un'istanza di Rete virtuale di Azure (VNet).
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 912dab16cd854ae940b7bbdfe88a8da7adf1c5e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84197203"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Usare il gateway per le origini dati in un'istanza di Rete virtuale di Azure (VNet)

Questo articolo descrive la proprietà del server **AlwaysUseGateway** di Azure Analysis Services da usare quando le origini dati si trovano in un'istanza di [Rete virtuale di Azure (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Accesso del server alle origini dati della rete virtuale

Se l'accesso alle origini dati avviene tramite una rete virtuale, il server Azure Analysis Services deve connettersi a tali origini dati come se fossero nell'ambiente locale. È possibile configurare la proprietà del server **AlwaysUseGateway** per specificare che il server deve accedere a tutte le origini dati tramite un [gateway locale](analysis-services-gateway.md). 

Le origini dati di Azure SQL Istanza gestita vengono eseguite all'interno di Azure VNet con un indirizzo IP privato. Se nell'istanza è abilitato l'endpoint pubblico, non è necessario un gateway. Se l'endpoint pubblico non è abilitato, è necessario un gateway dati locale e la proprietà AlwaysUseGateway deve essere impostata su true.

> [!NOTE]
> Questa proprietà è valida solo quando è installato e configurato un [gateway dati locale](analysis-services-gateway.md). Il gateway può trovarsi nella rete virtuale.

## <a name="configure-alwaysusegateway-property"></a>Configurare la proprietà AlwaysUseGateway

1. In SSMS > server > **Proprietà** > **Generale** selezionare **Mostra proprietà avanzate**.
2. In **ASPaaS\AlwaysUseGateway** selezionare **true**.

    ![Proprietà AlwaysUseGateway](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Vedere anche
[Connessione a origini dati locali](analysis-services-gateway.md)   
[Installare e configurare un gateway dati locale](analysis-services-gateway-install.md)   
[Che cos'è Rete virtuale di Azure?](../virtual-network/virtual-networks-overview.md)   

