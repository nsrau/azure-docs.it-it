---
title: Scaricare i profili VPN globali o basati su hub della rete WAN virtuale di Azure. Documenti Microsoft
description: Informazioni sulla connettività scalabile da ramo a ramo, sulle aree disponibili e sui partner.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 4/20/2020
ms.author: alzam
ms.openlocfilehash: b63bb861f4df087f852bb1bf599d32100d063f7e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733176"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Scaricare un profilo globale o basato su hub per i client VPN utente

La rete WAN virtuale di Azure offre due tipi di connettività per gli utenti remoti: globale e basata su hub. Utilizzare le sezioni seguenti per ottenere informazioni e scaricare un profilo. 

> [!IMPORTANT]
> L'autenticazione RADIUS supporta solo il profilo basato su Hub.

## <a name="global-profile"></a>Profilo globale

Il profilo punta a un servizio di bilanciamento del carico che include tutti gli hub VPN utente attivi. L'utente viene indirizzato all'hub più vicino alla posizione geografica dell'utente. Questo tipo di connettività è utile quando gli utenti si recano frequentemente in luoghi diversi. Per scaricare il profilo **globale:**

1. Passare alla rete WAN virtuale.
2. Fare clic su **Configurazione VPN utente**.
3. Evidenziare la configurazione per la quale si desidera scaricare il profilo.
4. Fare clic su **Scarica profilo VPN utente WAN virtuale**.

   ![Profilo globale](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Profilo basato su hub

Il profilo punta a un singolo hub. L'utente può connettersi solo all'hub specifico utilizzando questo profilo. Per scaricare il profilo **basato su hub:**

1. Passare alla rete WAN virtuale.
2. Fare clic su **Hub** nella pagina Panoramica.

    ![Profilo hub 1](./media/global-hub-profile/hub1.png)
3. Fare clic su **VPN utente (punto al sito)**.
4. Fare clic su **Scarica profilo VPN utente hub virtuale**.

   ![Profilo hub 2](./media/global-hub-profile/hub2.png)
5. Controllare **EAPTLS**.
6. Fare clic su **Genera e scarica profilo**.

   ![Profilo hub 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).
