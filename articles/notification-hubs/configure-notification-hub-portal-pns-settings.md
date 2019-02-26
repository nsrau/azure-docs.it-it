---
title: Configurare un hub di notifica di Azure con le impostazioni PNS | Microsoft Docs
description: Questa guida introduttiva illustra come configurare un hub di notifica nel portale di Azure con le impostazioni PNS (Platform Notification System).
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: 7f7e4a4d75a8e118da6f026817bc4ecfcc7a60db
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313966"
---
# <a name="configure-an-azure-notification-hub-with-platform-notification-system-settings-in-the-azure-portal"></a>Configurare un hub di notifica di Azure con le impostazioni PNS (Platform Notification System) nel portale di Azure 
Hub di notifica di Azure offre un motore di push di facile uso e con scalabilità orizzontale che consente di inviare notifiche a qualsiasi piattaforma (iOS, Android, Windows, Kindle, Baidu e così via) da qualsiasi back-end (cloud o locale). Per altre informazioni sul servizio, vedere [Informazioni su Hub di notifica](notification-hubs-push-notification-overview.md).

[Creare un hub di notifica di Azure tramite il portale di Azure](create-notification-hub-portal.md) se non lo si è già fatto. Questa guida introduttiva illustra come configurare un hub di notifica nel portale di Azure con le impostazioni PNS (Platform Notification System).

## <a name="apple-push-notification-service-apns"></a>Apple Push Notification Service (APNS)
1. Nella pagina **Hub di notifica** nel portale di Azure selezionare **Apple (servizio APN)** in **Impostazioni** nel menu a sinistra.
2. Se si seleziona **Certificato**, eseguire le operazioni seguenti:
    1. Selezionare l'**icona del file** e selezionare il file **.p12** da caricare. 
    2. Specificare la **password**.
    3. Selezionare la modalità **Sandbox**. Usare la modalità **Produzione** solo se si vuole inviare notifiche push agli utenti che hanno acquistato l'app dallo Store.

        ![Configurare il certificato APN nel portale di Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)
3. Se si seleziona **Token**, seguire questa procedura: 
    1. Immettere i valori per **ID chiave**, **ID bundle**, **ID team** e **token**.
    2. Selezionare la modalità **Sandbox**. Usare la modalità **Produzione** solo se si vuole inviare notifiche push agli utenti che hanno acquistato l'app dallo Store.

        ![Configurare il token del servizio APN nel portale di Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

Per un'esercitazione completa sull'invio di notifiche push a dispositivi iOS con Hub di notifica di Azure e Apple Push Notification Service (servizio APN), vedere [questa esercitazione](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)
1. Nella pagina **Hub di notifica** nel portale di Azure selezionare **Google (GCM/FCM)** in **Impostazioni** nel menu a sinistra. 
2. Incollare la **chiave del server** per il progetto FCM salvato in precedenza. 
3. Sulla barra degli strumenti selezionare **Salva**. 

    ![Hub di notifica di Azure - Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Negli avvisi viene visualizzato un messaggio che indica che gli hub di notifica sono stati aggiornati correttamente. Il pulsante **Save** (Salva) è disabilitato. 

Per un'esercitazione completa sull'invio di notifiche push a dispositivi Android con Hub di notifica di Azure e Google Firebase Cloud Messaging, vedere [questa esercitazione](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service-wns"></a>Servizio notifica Push Windows (WNS)
1. Nella pagina **Hub di notifica** nel portale di Azure selezionare **Windows (WNS)** in **Impostazioni** nel menu a sinistra.
2. Immettere i valori per **SID pacchetto** e **Chiave di sicurezza**.
3. Sulla barra degli strumenti selezionare **Salva**.

    ![Caselle SID pacchetto e Chiave di sicurezza](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)


Per un'esercitazione completa sull'invio di notifiche push a un'app per la piattaforma UWP (Universal Windows Platform) in esecuzione in un dispositivo Windows, vedere [questa esercitazione](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="windows-phone---microsoft-push-notification-service"></a>Windows Phone - Servizio notifica push Microsoft
1. Nella pagina **Hub di notifica** nel portale di Azure selezionare **Windows Phone (MPNS)** in **Impostazioni**.
2. Se si vogliono abilitare le notifiche push non autenticate, selezionare **Abilita notifiche push non autenticate** e selezionare **Salva** nella barra degli strumenti.

    ![Portale di Azure - Abilita notifiche push non autenticate](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)
3. Se si vogliono usare le notifiche push **autenticate**, seguire questa procedura:
    1. Selezionare **Carica certificato** nella barra degli strumenti.
    2. Selezionare l'**icona del file** e selezionare il file del certificato.
    3. Immettere la **password** per il certificato. 
    4. Selezionare **OK** per chiudere la pagina **Carica certificato**. 
    5. Nella pagina **Windows Phone(MPNS)** selezionare **Salva** nella barra degli strumenti.

Per un'esercitazione completa sull'invio di notifiche push a un'app per Windows Phone 8 con il Servizio di notifica push Microsoft (MPNS), vedere [questa esercitazione](notification-hubs-windows-mobile-push-notifications-mpns.md).
      
## <a name="amazon-device-messaging-adm"></a>Amazon Device Messaging (ADM)
1. Nella pagina **Hub di notifica** nel portale di Azure selezionare **Amazon (ADM)** in **Impostazioni** nel menu a sinistra.
2. Immettere i valori per **ID client** e **Segreto client**.
3. Sulla barra degli strumenti selezionare **Salva**.
    
    ![Hub di notifica di Azure - Impostazioni ADM](./media/notification-hubs-kindle-get-started/notification-hub-adm-settings.png)

Per un'esercitazione completa sull'uso di Hub di notifica di Azure per l'invio di notifiche push a un'applicazione Kindle, vedere [questa esercitazione](notification-hubs-kindle-amazon-adm-push-notification.md).

## <a name="baidu-android-china"></a>Baidu (Android China)
1. Nella pagina **Hub di notifica** nel portale di Azure selezionare **Baidu (Android China)** in **Impostazioni** nel menu a sinistra. 
2. Immettere la **chiave API** ottenuta dalla console di Baidu nel progetto push cloud Baidu. 
3. Immettere la **chiave privata** ottenuta dalla console di Baidu nel progetto push cloud Baidu. 
4. Sulla barra degli strumenti selezionare **Salva**. 

    ![Hub di notifica di Azure - Baidu (Android China)](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)
4. Negli avvisi viene visualizzato un messaggio che indica che gli hub di notifica sono stati aggiornati correttamente. Il pulsante **Save** (Salva) è disabilitato. 

Per un'esercitazione completa sull'invio di notifiche push con Hub di notifica di Azure e push cloud Baidu, vedere [questa esercitazione](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva è stato illustrato come configurare diversi sistemi PNS (Platform Notification System) per un hub di notifica nel portale di Azure. 

Per istruzioni dettagliate complete per l'invio di notifiche push a queste diverse piattaforme, vedere le esercitazioni presenti nella sezione **Esercitazioni**.

- [Inviare notifiche push a dispositivi iOS con Hub di notifica di Azure e Apple Push Notification Service (servizio APN)](notification-hubs-ios-apple-push-notification-apns-get-started.md)
- [Effettuare il push di notifiche ai dispositivi Android con Hub di notifica di Azure e Google Firebase Cloud Messaging](notification-hubs-android-push-notification-google-fcm-get-started.md)
- [Inviare notifiche push a un'app della piattaforma UWP (Universal Windows Platform) in esecuzione in un dispositivo Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
- [Inviare notifiche push a un'app per Windows Phone 8 con il Servizio di notifica push Microsoft (MPNS)](notification-hubs-windows-mobile-push-notifications-mpns.md)
- [Inviare notifiche push a un'applicazione Kindle](notification-hubs-kindle-amazon-adm-push-notification.md)
- [Inviare notifiche push con Hub di notifica di Azure e push cloud Baidu](notification-hubs-baidu-china-android-notifications-get-started.md)
