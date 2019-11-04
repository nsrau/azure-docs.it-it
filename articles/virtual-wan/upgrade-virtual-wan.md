---
title: Aggiornare una rete WAN virtuale di Azure da Basic a portale di Azure standard | Microsoft Docs
description: È possibile aggiornare il tipo di WAN virtuale per ottenere maggiori funzionalità.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 83fd5bafb5496908403c50dc0e000fd33a836c95
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515811"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Aggiornare una rete WAN virtuale da Basic a standard

Questo articolo consente di aggiornare una rete WAN di base a una rete WAN standard. Un tipo WAN ' Basic ' Crea tutti gli hub al suo interno come hub SKU di base. In un hub di base si è limitati esclusivamente alla funzionalità VPN da sito a sito. Un tipo WAN ' standard ' Crea tutti gli hub al suo interno come hub SKU standard. Quando si usano hub standard, è possibile abilitare ExpressRoute, una VPN utente (da punto a sito), un hub mesh completo e un transito da VNet a VNet attraverso gli hub di Azure.

La tabella seguente illustra le configurazioni disponibili per ogni tipo di WAN:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Per modificare il tipo di WAN virtuale

1. Nella pagina della rete WAN virtuale selezionare **configurazione** per aprire la pagina di configurazione.

   ![Diagramma della rete WAN virtuale](./media/upgrade-virtual-wan/1.png)
2. Per il tipo di rete WAN virtuale selezionare **standard** nell'elenco a discesa.

   ![Diagramma della rete WAN virtuale](./media/upgrade-virtual-wan/2.png)
3. Tenere presente che se si esegue l'aggiornamento a una rete WAN virtuale standard, non è possibile ripristinare una rete WAN virtuale di base. Selezionare **conferma** se si desidera eseguire l'aggiornamento.

   ![Diagramma della rete WAN virtuale](./media/upgrade-virtual-wan/4.png)
4. Una volta salvata la modifica, la pagina WAN virtuale avrà un aspetto simile a questo esempio.

   ![Diagramma della rete WAN virtuale](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).