---
title: Installare il Firewall di Azure in un hub della rete WAN virtuale
titleSuffix: Azure Virtual WAN
description: Procedura per configurare il Firewall di Azure in un hub della rete WAN virtuale
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 577340e485550e84941a33d82b58aa6ff1c933d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983667"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>Configurare il Firewall di Azure in un hub della rete WAN virtuale

Un **hub protetto** è un hub della rete WAN virtuale di Azure con Firewall di Azure. Questo articolo illustra i passaggi da seguire per convertire un hub delle rete WAN virtuale in un hub protetto tramite l'installazione del Firewall di Azure direttamente dalle pagine del portale della rete WAN virtuale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Nei passaggi descritti in questo articolo si presume che sia già stata distribuita una rete WAN virtuale con uno o più hub.

Per creare una nuova rete WAN virtuale e un nuovo hub, attenersi ai passaggi descritti negli articoli seguenti:

* [Creare una rete WAN virtuale](virtual-wan-site-to-site-portal.md#openvwan)
* [Creare un hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>Visualizzare gli hub virtuali

La pagina **Panoramica**  relativa alla rete WAN virtuale riporta un elenco degli hub virtuali e degli hub protetti. Nella figura seguente è illustrata una rete WAN virtuale senza hub protetti.

[![Screenshot mostra la pagina Panoramica per una rete WAN virtuale con un elenco di hub virtuali.](./media/howto-firewall/overview.png)](./media/howto-firewall/overview.png#lightbox)

## <a name="convert-to-secured-hub"></a>Eseguire la conversione in hub protetto

1. Nella pagina **Panoramica** relativa alla rete WAN virtuale selezionare l'hub che si vuole convertire in hub protetto. Nella pagina dell'hub virtuale vengono visualizzate due opzioni per la distribuzione del Firewall di Azure in questo hub. Selezionarne una.

   [![Screenshot mostra la pagina Panoramica per la rete WAN virtuale in cui è possibile selezionare Converti in hub protetto o firewall di Azure.](./media/howto-firewall/security.png)](./media/howto-firewall/security.png#lightbox)

1. Dopo aver selezionato una delle opzioni, viene visualizzata la pagina **Converti in hub protetto**. Selezionare un hub da convertire, quindi selezionare **Next: Azure Firewall** (Avanti: Firewall di Azure) nella parte inferiore della pagina.

   [ ![seleziona hub](./media/howto-firewall/select-hub.png)](./media/howto-firewall/select-hub.png#lightbox)
1. Dopo aver completato il flusso di lavoro, selezionare **Conferma**.

   [![Screenshot mostra il riquadro Converti in hub protetto con conferma selezionata.](./media/howto-firewall/confirm.png)](./media/howto-firewall/confirm.png#lightbox)

1. Dopo aver convertito l'hub in hub protetto, è possibile visualizzarlo nella pagina **Panoramica**  della rete WAN virtuale.

   [ ![visualizza hub protetto](./media/howto-firewall/secured-hub.png)](./media/howto-firewall/secured-hub.png#lightbox)

## <a name="view-hub-resources"></a>Visualizzare le risorse dell'hub

Nella pagina **Panoramica**  della rete WAN virtuale selezionare l'hub protetto. Nella pagina dell'hub è possibile visualizzare tutte le risorse dell'hub virtuale, incluso il Firewall di Azure.

[ ![visualizza risorse dell'hub](./media/howto-firewall/view-resources.png)](./media/howto-firewall/view-resources.png#lightbox)

Per visualizzare le impostazioni del Firewall di Azure dall'hub protetto, in **Sicurezza** selezionare **Impostazioni hub virtuale protetto**.
[ ![visualizza impostazioni hub](./media/howto-firewall/hub-settings.png)](./media/howto-firewall/hub-settings.png#lightbox)

## <a name="configure-additional-settings"></a>Configurare impostazioni aggiuntive

Per configurare altre impostazioni del firewall di Azure per l'hub virtuale, selezionare il collegamento a **Gestione firewall di Azure**. Per informazioni sui criteri firewall, vedere [Gestione firewall di Azure](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub).

[ ![impostazioni aggiuntive](./media/howto-firewall/additional-settings.png)](./media/howto-firewall/additional-settings.png#lightbox)

Per tornare alla pagina **Panoramica**  dell'hub, è possibile andare indietro facendo clic sul percorso, come illustrato nella figura seguente.

[ ![ritorno alla panoramica](./media/howto-firewall/arrow.png)](./media/howto-firewall/arrow.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
