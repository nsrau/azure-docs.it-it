---
title: Contenuto dell'SDK per app di Windows universali
description: Informazioni sul contenuto di Azure Mobile Engagement SDK per app di Windows universali
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8fa1b701-1c2b-4aec-940c-06c974ef5405
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 7b520adcc6af24f7b092580ea82a787a120668bf
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-sdk-content"></a>Contenuto dell'SDK per app di Windows universali
> [!IMPORTANT]
> Azure Mobile Engagement verrà ritirato il 31/03/2018. Questa pagina verrà eliminata subito dopo.
> 

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

