---
title: Configurare Azure Analysis Services per le origini dati della rete virtuale - Documenti Microsoft
description: Informazioni su come configurare un server Azure Analysis Services per usare un gateway per le origini dati in Rete virtuale di Azure.Learn how to configure an Azure Analysis Services server to use a gateway for data sources on Azure Virtual Network (VNet).
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572269"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Usare il gateway per le origini dati in un'istanza di Rete virtuale di Azure (VNet)

Questo articolo descrive la proprietà del server **AlwaysUseGateway** di Azure Analysis Services da usare quando le origini dati si trovano in [una rete virtuale di Azure.](../virtual-network/virtual-networks-overview.md)

## <a name="server-access-to-vnet-data-sources"></a>Accesso del server alle origini dati della rete virtuale

Se l'accesso alle origini dati avviene tramite una rete virtuale, il server Azure Analysis Services deve connettersi a tali origini dati come se fossero nell'ambiente locale. È possibile configurare la proprietà del server **AlwaysUseGateway** per specificare il server per accedere a tutte le origini dati tramite un [gateway locale.](analysis-services-gateway.md) 

Le origini dati dell'istanza gestita del database SQL di Azure vengono eseguite all'interno di una rete virtuale di Azure con un indirizzo IP privato. Se nell'istanza è abilitato un endpoint pubblico, non è necessario un gateway. Se l'endpoint pubblico non è abilitato, è necessario un gateway dati locale e la proprietà AlwaysUseGateway deve essere impostata su true.

> [!NOTE]
> Questa proprietà è valida solo quando viene installato e configurato un [gateway dati locale.](analysis-services-gateway.md) Il gateway può trovarsi nella rete virtuale.

## <a name="configure-alwaysusegateway-property"></a>Configurare la proprietà AlwaysUseGateway

1. In SSMS > server > **Proprietà** > **generali**selezionare Mostra proprietà **avanzate (tutte)**.
2. In **ASPaaS\AlwaysUseGateway** selezionare **true**.

    ![Proprietà AlwaysUseGateway](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Vedere anche
[Connessione a origini dati localiConnecting to on-premises data sources](analysis-services-gateway.md)   
[Installare e configurare un gateway dati locale](analysis-services-gateway-install.md)   
[Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)   

