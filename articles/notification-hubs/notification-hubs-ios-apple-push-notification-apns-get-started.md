---
title: Introduzione ad Hub di notifica di Azure per le app per iOS | Microsoft Docs
description: In questa esercitazione si apprende come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione iOS.
services: notification-hubs
documentationcenter: ios
keywords: notifica push,notifiche push,notifiche push ios
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 0e9e7ab196eef790b74074be319cd8122cf3ff5c
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-ios-apps"></a>Introduzione ad Hub di notifica di Azure per le app per iOS
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Panoramica
> [!NOTE]
> Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).
> 
> 

Questa esercitazione illustra come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per iOS. Si creerà un'app iOS vuota che riceve notifiche push tramite [Apple Push Notification Service (servizio APN)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1). 

Al termine sarà possibile usare l'hub di notifica per trasmettere le notifiche push a tutti i dispositivi che eseguono l'app.

## <a name="before-you-begin"></a>Prima di iniziare
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Il codice completo per questa esercitazione è disponibile in [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

## <a name="prerequisites"></a>prerequisiti
Per completare questa esercitazione, è necessario disporre di:

* [framework di messaggistica di Windows Azure]
* Ultima versione di [Xcode]
* Dispositivo compatibile con iOS 10 o versione successiva
* [Apple Developer Program](https://developer.apple.com/programs/) .
  
  > [!NOTE]
  > Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push in un dispositivo fisico con iOS, iPhone o iPad, anziché in un simulatore iOS.
  > 
  > 

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Configurare l'hub di notifica per l'invio di notifiche push di iOS
Questa sezione illustra come creare un nuovo hub di notifica e configurare l'autenticazione con il servizio APN usando il certificato push **.p12** creato in precedenza. Se si vuole usare un hub di notifica che è già stato creato, è possibile ignorare il passaggio 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>In <b>Servizi di notifica</b> selezionare <b>Apple (servizio APN)</b>. Selezionare <b>Certificato</b>, fare clic sull'icona del file e quindi selezionare il file con estensione <b>p12</b> esportato in precedenza. Assicurarsi di specificare anche la password corretta.</p>

<p>Assicurarsi di selezionare la modalità <b>Sandbox</b>, destinata allo sviluppo. Usare la modalità <b>Produzione</b> solo se si vuole inviare notifiche push agli utenti che hanno acquistato l'app dallo Store.</p>
</li>
</ol>
&emsp;&emsp;&emsp;&emsp;![Configurare il servizio APN nel portale di Azure][6]

&emsp;&emsp;&emsp;&emsp;![Configurare il certificato APN nel portale di Azure][7]

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

6. Aggiungere un nuovo file di intestazione al progetto denominato **HubInfo.h**. Questo file includerà le costanti per l'hub di notifica. Aggiungere le definizioni seguenti e sostituire i segnaposto dei valori letterali stringa con il *nome dell'hub* e il valore *DefaultListenSharedAccessSignature* annotati in precedenza.

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
È possibile testare rapidamente la ricezione di notifiche nell'app con l'opzione *Invio di prova* nel [portale di Azure]. Verrà così inviata una notifica push di prova al dispositivo.

![Portale di Azure - Invio di prova][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="checking-if-your-app-can-receive-push-notifications"></a>Verifica della ricezione di notifiche push nell'app
Per testare le notifiche push in iOS, è necessario distribuire l'app in un dispositivo fisico iOS. Non è possibile inviare notifiche push Apple con il simulatore iOS.

1. Eseguire l'app e verificare che la registrazione riesca e quindi scegliere **OK**.
   
    ![Test di registrazione notifica push per app iOS][33]
2. Ora verrà inviata una notifica push di prova dal [portale di Azure], come illustrato in precedenza. 

3. La notifica push viene inviata a tutti i dispositivi registrati per la ricezione di notifiche dall'hub di notifica specifico.
   
    ![Test di ricezione notifica push per app iOS][35]

## <a name="next-steps"></a>Passaggi successivi
In questo semplice esempio le notifiche push sono state trasmesse a tutti i dispositivi iOS registrati. Il passaggio successivo consigliato per l'apprendimento è l'esercitazione [Uso di Hub di notifica di Azure per inviare notifiche agli utenti per iOS con back-end .NET]. Questa guida illustra la creazione di un back-end per l'invio di notifiche push con tag. 

Per segmentare gli utenti per gruppi di interesse, è possibile vedere anche l'esercitazione [Usare Hub di notifica per inviare le ultime notizie] . 

Per informazioni generali su Hub di notifica, vedere [Panoramica dell'Hub di notifica].

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
[framework di messaggistica di Windows Azure]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Panoramica dell'Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Uso di Hub di notifica di Azure per inviare notifiche agli utenti per iOS con back-end .NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Usare Hub di notifica per inviare le ultime notizie]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[portale di Azure]: https://portal.azure.com
