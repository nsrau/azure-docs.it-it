---
title: Usare un gateway dati locale per le origini dati di Rete virtuale di Azure | Microsoft Docs
description: Informazioni su come configurare un server per l'uso di un gateway per le origini dati in una rete virtuale.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f1cf447f27509bc62cc71a9c64237ff7dc3d7b30
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446085"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Usare il gateway per le origini dati in un'istanza di Rete virtuale di Azure (VNet)

Questo articolo descrive la proprietà del server **AlwaysUseGateway** da usare quando le origini dati si trovano in un'istanza di [Rete virtuale di Azure (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Accesso del server alle origini dati della rete virtuale

Se l'accesso alle origini dati avviene tramite una rete virtuale, il server Azure Analysis Services deve connettersi a tali origini dati come se fossero nell'ambiente locale. È possibile configurare la proprietà del server **AlwaysUseGateway** per specificare che il server deve accedere a tutti i dati delle origini dati tramite un [gateway locale](analysis-services-gateway.md). 

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

