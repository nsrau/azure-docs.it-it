---
title: Effettuare il push di notifiche alle app iOS con Hub di notifica di Azure | Microsoft Docs
description: In questa esercitazione si apprende come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione iOS.
services: notification-hubs
documentationcenter: ios
keywords: notifica push,notifiche push,notifiche push ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 083b0c956055ab5b54a4af2eec57f096613cbe65
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38681520"
---
# <a name="tutorial-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Esercitazione: Effettuare il push di notifiche alle app iOS con Hub di notifica di Azure
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

In questa esercitazione verranno usati Hub di notifica di Azure per effettuare il push di notifiche a un'applicazione iOS. Si crea un'app iOS vuota che riceve notifiche push tramite [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1). 

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Generare il file della richiesta di firma del certificato
> * Richiedere le notifiche push all'app
> * Creare un profilo di provisioning per l'app
> * Configurare l'hub di notifica per l'invio di notifiche push di iOS
> * Connettere l'app iOS all'hub di notifica
> * Inviare notifiche push di prova
> * Verificare che l'app riceve le notifiche

Il codice completo per questa esercitazione è disponibile in [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

## <a name="prerequisites"></a>prerequisiti

- Un account Azure attivo. Se non si dispone di un account, è possibile creare un [account di valutazione gratuita](https://azure.microsoft.com/free) in pochi minuti. 
- [Framework di messaggistica di Windows Azure]
- Ultima versione di [Xcode]
- Dispositivo compatibile con iOS 10 o versione successiva
- [Apple Developer Program](https://developer.apple.com/programs/) .
  
  > [!NOTE]
  > Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push in un dispositivo fisico con iOS, iPhone o iPad, anziché in un simulatore iOS.
  
Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Configurare l'hub di notifica per l'invio di notifiche push di iOS
Questa sezione viene creato un hub di notifica e configurare l'autenticazione con il servizio APNS usando il certificato push **.p12** creato in precedenza. Se si vuole usare un hub di notifica che è già stato creato, è possibile ignorare il passaggio 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-your-notification-hub-with-apns-information"></a>Configurare l'hub di notifica con le informazioni APNS

1. In **Servizi di notifica** selezionare **Apple (APNS)**. 
2. Selezionare **Certificate**.
3. Selezionare l'**icona del file**.
4. Selezionare il file **p12** esportato in precedenza.
5. Specificare la **password** corretta.
6. Selezionare la modalità **Sandbox**. Usare la modalità **Produzione** solo se si vuole inviare notifiche push agli utenti che hanno acquistato l'app dallo Store.

    ![Configurare il certificato APN nel portale di Azure][7]

L'hub di notifica è ora configurato per l'uso con il servizio APN e sono disponibili le stringhe di connessione per la registrazione dell'app e l'invio di notifiche push.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Connettere l'app iOS all'hub di notifica
1. In Xcode creare un nuovo progetto iOS e selezionare il modello **Single View Application** .
   
    ![Xcode - Single View Application][8]
    
2. Quando si configurano le opzioni per il nuovo progetto, assicurarsi di usare lo stesso **nome prodotto** e lo stesso **identificatore organizzazione** usati quando è stato impostato l'identificatore del bundle nel portale Apple Developer.
   
    ![Xcode - Opzioni di progetto][11]
    
3. In Project Navigator fare clic sul nome del progetto, quindi sulla scheda **General** (Generale) e trovare **Signing** (Firma). Selezionare il team corretto per l'account Apple Developer. XCode recupererà automaticamente il profilo di provisioning creato in precedenza in base all'identificatore del bundle.
   
    Se il profilo di provisioning creato in Xcode non viene visualizzato, provare ad aggiornare i profili per l'identità di firma. Fare clic su **Xcode** nella barra dei menu, fare clic su **Preferences** (Preferenze), selezionare la scheda **Account**, fare clic sul pulsante **View Details** (Visualizza dettagli), selezionare la propria identità di firma e infine fare clic sul pulsante di aggiornamento nell'angolo in basso a destra.

    ![Xcode - Profilo di provisioning][9]

4. Selezionare la scheda **Capabilities** (Funzionalità) e assicurarsi di abilitare le notifiche push

    ![Xcode - Funzionalità push][12]
   
5. Scaricare il [framework di messaggistica di Windows Azure] e decomprimere il file. In Xcode fare clic con il pulsante destro del mouse sul progetto e scegliere l'opzione **Add Files to** (Aggiungi file a) per aggiungere la cartella **WindowsAzureMessaging.framework** al progetto Xcode. Selezionare **Options** (Opzioni) e assicurarsi che sia selezionato **Copy items if needed** (Copia elementi se necessario), quindi fare clic su **Add** (Aggiungi).

    ![Decomprimere Azure SDK][10]

6. Aggiungere un nuovo file di intestazione al progetto denominato **HubInfo.h**. Questo file include le costanti per l'hub di notifica. Aggiungere le definizioni seguenti e sostituire i segnaposto dei valori letterali stringa con il *nome dell'hub* e il valore *DefaultListenSharedAccessSignature* annotati in precedenza.

    ```obj-c
        #ifndef HubInfo_h
        #define HubInfo_h
   
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
   
        #endif /* HubInfo_h */
    ```
    
7. Aprire il file **AppDelegate.h** e aggiungere le direttive import seguenti:

    ```obj-c
        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
        #import <UserNotifications/UserNotifications.h> 
        #import "HubInfo.h"
    ```
8. Nel file **AppDelegate.m** aggiungere il codice seguente nel metodo **didFinishLaunchingWithOptions** in base alla versione di iOS. Questo codice registra l'handle di dispositivo nel servizio APNS:

    ```obj-c
        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
            UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];
   
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```
   
9. Nello stesso file aggiungere i metodi seguenti:

    ```obj-c
         - (void) application:(UIApplication *) application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
           SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];
   
            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
               if (error != nil) {
                   NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                   [self MessageBox:@"Registration Status" message:@"Registered"];
              }
          }];
         }
   
        -(void)MessageBox:(NSString *) title message:(NSString *)messageText
        {
         UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }
    ```

    Questo codice si connette all'hub di notifica usando le informazioni di connessione specificate in HubInfo.h. Fornisce il token del dispositivo all'hub di notifica in modo che quest'ultimo possa inviare notifiche.

10. Nello stesso file aggiungere il metodo seguente per visualizzare un avviso **UIAlert** se la notifica viene ricevuta mentre l'app è attiva:

    ```obj-c
            - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
               NSLog(@"%@", userInfo);
               [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
           }
    ```

11. Per verificare che non vi siano errori, compilare ed eseguire l'app nel dispositivo.

## <a name="send-test-push-notifications"></a>Inviare notifiche push di prova
È possibile testare rapidamente la ricezione di notifiche nell'app con l'opzione *Invio di prova* nel [portale di Azure]. Verrà inviata una notifica push di prova al dispositivo.

![Portale di Azure - Invio di prova][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="verify-that-your-app-receives-push-notifications"></a>Verificare che l'app riceve le notifiche push
Per testare le notifiche push in iOS, è necessario distribuire l'app in un dispositivo fisico iOS. Non è possibile inviare notifiche push Apple con il simulatore iOS.

1. Eseguire l'app e verificare che la registrazione riesca e quindi scegliere **OK**.
   
    ![Test di registrazione notifica push per app iOS][33]
2. Ora viene inviata una notifica push di prova dal [portale di Azure], come illustrato nella sezione precedente. 

3. La notifica push viene inviata a tutti i dispositivi registrati per la ricezione di notifiche dall'hub di notifica specifico.
   
    ![Test di ricezione notifica push per app iOS][35]

## <a name="next-steps"></a>Passaggi successivi
In questo semplice esempio le notifiche push sono state trasmesse a tutti i dispositivi iOS registrati. Per informazioni sulle procedure per eseguire il push di notifiche a dispositivi iOS specifici, passare all'esercitazione seguente: 

> [!div class="nextstepaction"]
>[Eseguire il push di notifiche a dispositivi specifici](notification-hubs-ios-xplat-segmented-apns-push-notification.md)


<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Framework di messaggistica di Windows Azure]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Portale di Azure]: https://portal.azure.com
