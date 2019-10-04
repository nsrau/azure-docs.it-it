---
title: Configurare il servizio notifiche Push Windows in hub di notifica di Azure | Microsoft Docs
description: Informazioni su come configurare le impostazioni del servizio notifiche Push Windows per un hub di notifica di Azure.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: a7f7734d97cd67c133ff0cedc3ef2376967bcdf4
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212421"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurare le impostazioni del servizio notifiche Push Windows (WNS) per un hub di notifica nel portale di Azure
Questo articolo illustra come configurare le impostazioni di Windows Notification Service (WNS) per un hub di notifica di Azure usando il portale di Azure.  

## <a name="prerequisites"></a>Prerequisiti
Se non è stato già creato un hub di notifica, crearne uno ora. Per altre informazioni, vedere [Creare un hub di notifica di Azure nel portale di Azure](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Configurare il servizio notifiche Push Windows (WNS)

La procedura seguente illustra i passaggi per configurare le impostazioni del servizio notifiche Push Windows (WNS) per un hub di notifica: 

1. Nella portale di Azure nella pagina Hub di **notifica** selezionare **Windows (WNS)** nel menu a sinistra.
2. Immettere i valori per **SID pacchetto** e **Chiave di sicurezza**.
3. Selezionare **Salva**.

   ![Screenshot che illustra le caselle SID pacchetto e Chiave di sicurezza](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Passaggi successivi
Per un'esercitazione con istruzioni dettagliate per eseguire il push delle notifiche ad applicazioni piattaforma UWP (Universal Windows Platform) usando hub di notifica di Azure e il servizio di notifica Push Windows (WNS), vedere [inviare notifiche alle app UWP usando la notifica di Azure Hub](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


