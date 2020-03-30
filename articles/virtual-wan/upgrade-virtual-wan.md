---
title: Aggiornare una rete WAN virtuale di Azure da Basic a Standard - Portale di Azure. Documenti Microsoft
description: È possibile aggiornare il tipo di WAN virtuale per una maggiore funzionalità.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 83fd5bafb5496908403c50dc0e000fd33a836c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515811"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Aggiornare una rete WAN virtuale da Basic a Standard

Questo articolo consente di aggiornare una rete WAN di base a una rete WAN standard. Un tipo di WAN 'Basic' crea tutti gli hub al suo interno come hub SKU di base. In un hub Basic, l'utente è limitato solo alle funzionalità VPN da sito a sito. Un tipo di WAN 'Standard' crea tutti gli hub al suo interno come hub SKU Standard. Quando si usano hub Standard, è possibile abilitare ExpressRoute, la VPN da utente (da punto a sito), un hub mesh completo e il transito da VNet a VNet tramite gli hub di Azure.When you use Standard hubs, you can enable ExpressRoute, User (Point-to-site) VPN, a full mesh hub, and VNet-to-VNet transit through the Azure hubs.

Nella tabella seguente vengono illustrate le configurazioni disponibili per ogni tipo di WAN:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Per modificare il tipo di WAN virtuale

1. Nella pagina della rete WAN virtuale selezionare **Configurazione** per aprire la pagina Configurazione.

   ![Diagramma della rete WAN virtuale](./media/upgrade-virtual-wan/1.png)
2. Per il tipo di WAN virtuale, selezionare **Standard** dall'elenco a discesa.

   ![Diagramma della rete WAN virtuale](./media/upgrade-virtual-wan/2.png)
3. Tenere seguito il fatto che se si esegue l'aggiornamento a una rete WAN virtuale Standard, non è possibile ripristinare una rete WAN virtuale di base. Selezionare **Conferma** se si desidera eseguire l'aggiornamento.

   ![Diagramma della rete WAN virtuale](./media/upgrade-virtual-wan/4.png)
4. Una volta salvata la modifica, la pagina WAN virtuale è simile a questo esempio.

   ![Diagramma della rete WAN virtuale](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).