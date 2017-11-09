---
title: Contenuto dell'SDK per app di Windows universali
description: Informazioni sul contenuto di Azure Mobile Engagement SDK per app di Windows universali
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8fa1b701-1c2b-4aec-940c-06c974ef5405
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: b28d525ab16487b963772e23fdecd11f94dcabd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-apps-sdk-content"></a>Contenuto dell'SDK per app di Windows universali
Questo documento elenca e descrive il contenuto distribuito dall'SDK nell'applicazione.

## <a name="the-resources-folder"></a>Cartella `/Resources`
Questa cartella contiene tutte le risorse richieste da Mobile Engagement. È inoltre possibile personalizzarle in modo da adattarle all'app.

* `EngagementConfiguration.xml` : il file di configurazione di Mobile Engagement, in cui è possibile personalizzare le impostazioni di Mobile Engagement (stringa di connessione di Mobile Engagement, segnalazione di arresto anomalo e così via).

### <a name="html-folder"></a>Cartella /html
* `EngagementNotification.html` : la progettazione HTML per la visualizzazione Web di `Notification` in banner in-app.
* `EngagementAnnouncement.html` : la progettazione HTML per la visualizzazione Web di `Announcement` in visualizzazioni intermedie.

### <a name="images-folder"></a>Cartella /images
* `EngagementIconNotification.png`: l'icona del marchio visualizzata a sinistra di una notifica. Sostituirla con un'icona personalizzata.
* `EngagementIconOk.png`: l'icona `Ok` delle pagine di contenuto Reach per il pulsante di azione o convalida.
* `EngagementIconNOK.png`: l'icona `NOK` usata quando il pulsante di convalida delle pagine di contenuto Reach è disabilitato.
* `EngagementIconClose.png`: l'icona `Close` per il pulsante che consente di ignorare il contenuto e le notifiche Reach.

### <a name="overlay-folder"></a>Cartella /overlay
* `EngagementPageOverlay.cs` : la pagina di sovrimpressione che determina l'aggiunta dell'interfaccia utente in-app Reach di Engagement al figlio.

