---
title: Eseguire il push di notifiche localizzate ai dispositivi iOS con Hub di notifica di Azure | Microsoft Docs
description: Imparare a usare le notifiche push localizzate per i dispositivi iOS tramite Hub di notifica di Azure.
services: notification-hubs
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: b3d74086ee233da50138aff00d8da78aa0243a75
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776499"
---
# <a name="tutorial-push-localized-notifications-to-ios-devices-using-azure-notification-hubs"></a>Esercitazione: eseguire il push di notifiche localizzate ai dispositivi iOS con Hub di notifica di Azure 
> [!div class="op_single_selector"]
> * [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 

In questa esercitazione viene illustrato come usare la funzionalità relativa ai [modelli](notification-hubs-templates-cross-platform-push-messages.md) di Hub di notifica di Azure per trasmettere notifiche relative alle ultime notizie localizzate in base alla lingua e al dispositivo. In questa esercitazione verrà usata l'app di iOS creata in [Utilizzo di Hub di notifica per inviare le ultime notizie]. Al termine sarà possibile effettuare la registrazione per le categorie di proprio interesse, specificare la lingua in cui ricevere le notifiche e ricevere solo notifiche push per le categorie selezionate nella lingua scelta.

Lo scenario è composto da due parti:

* l'app per iOS consente ai dispositivi client di specificare una lingua e di sottoscrivere categorie diverse di ultime notizie;
* il back-end trasmette le notifiche usando le funzionalità relative ai **tag** e ai **modelli** di Hub di notifica di Azure.

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Aggiornare l'interfaccia utente dell'app
> * Compilare l'app iOS
> * Inviare notifiche modello localizzate dall'app console .NET
> * Inviare notifiche modello localizzate dal dispositivo


## <a name="overview"></a>Panoramica
In [Utilizzo di Hub di notifica per inviare le ultime notizie] è stata creata un'app che usa i **tag** per sottoscrivere le notifiche per diverse categorie di notizie. Molte app, tuttavia, sono destinate a più mercati ed è necessario localizzarle. Questo significa che il contenuto delle notifiche stesse deve essere localizzato e inviato al set di dispositivi corretto. In questa esercitazione viene usata la funzionalità relativa ai **modelli** di Hub di notifica per inviare facilmente notifiche relative alle ultime notizie localizzate.

> [!NOTE]
> Un possibile modo per inviare notifiche localizzate consiste nel creare più versioni di ogni tag. Per supportare l'inglese, il francese e il mandarino, ad esempio, sono necessari tre tag diversi per le ultime notizie internazionali: "world_en", "world_fr" e "world_ch". È quindi necessario inviare una versione localizzata delle ultime notizie internazionali a ogni tag. In questo argomento verranno usati i modelli per evitare la proliferazione di tag e la necessità di inviare più messaggi.

In linea generale, i modelli consentono di specificare in che modo uno specifico dispositivo deve ricevere una notifica. Il modello definisce lo specifico formato di payload da utilizzare, facendo riferimento alle proprietà del messaggio inviato dal back-end dell'app. In questo caso verrà inviato un messaggio indipendente dalle impostazioni locali, che contiene tutte le lingue supportate:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Si procede quindi a verificare che i dispositivi effettuino la registrazione con un modello che faccia riferimento alla proprietà corretta. Per un'app per iOS che deve essere registrata per le notizie in francese, ad esempio, si effettuerà la registrazione con la sintassi seguente:

    {
        aps:{
            alert: "$(News_French)"
        }
    }

Per altre informazioni sui modelli, vedere l'articolo [Modelli](notification-hubs-templates-cross-platform-push-messages.md).

## <a name="prerequisites"></a>prerequisiti

- Completare l'esercitazione [Eseguire il push di notifiche a dispositivi iOS specifici](notification-hubs-ios-xplat-segmented-apns-push-notification.md) e conservare il codice, perché questa esercitazione si basa direttamente su di esso.
- Visual Studio 2012 o versione successiva è facoltativo.

## <a name="update-the-app-user-interface"></a>Aggiornare l'interfaccia utente dell'app
In questa sezione l'app Breaking News creata nell'argomento [Utilizzo di Hub di notifica per inviare le ultime notizie] viene modificata in modo da inviare notizie localizzate mediante modelli.

In MainStoryboard_iPhone.storyboard aggiungere un controllo Segmented con le tre lingue: inglese, francese e mandarino.

![][13]

Assicurarsi quindi di aggiungere un elemento IBOutlet in ViewController.h come illustrato nell'immagine seguente:

![][14]

## <a name="build-the-ios-app"></a>Compilare l'app iOS
1. In Notification.h aggiungere il metodo *retrieveLocale*, quindi modificare i metodi store e subscribe come illustrato nel codice seguente:
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;
   
        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;
   
        - (NSSet*) retrieveCategories;
   
        - (int) retrieveLocale;
   
    ```
    In Notification.m modificare il metodo *storeCategoriesAndSubscribe* aggiungendo il parametro relativo alle impostazioni locali e archiviandolo nelle impostazioni predefinite dell'utente:
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];
   
            [self subscribeWithLocale: locale categories:categories completion:completion];
        }
    ````
    Modificare quindi il metodo *subscribe* in modo da includere le impostazioni locali:
   
    ```obj-c
        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];
   
            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }
   
            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];
   
            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }
       ```
    You use the method *registerTemplateWithDeviceToken*, instead of *registerNativeWithDeviceToken*. When you register for a template, you have to provide the json template and also a name for the template (as the app might want to register different templates). Make sure to register your categories as tags, as you want to make sure to receive the notifciations for those news.
   
    Add a method to retrieve the locale from the user default settings:
   
    ```obj-c
        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
   
            int locale = [defaults integerForKey:@"BreakingNewsLocale"];
   
            return locale < 0?0:locale;
        }
    ```
2. Ora che è stata modificata la classe Notifications, è necessario assicurarsi che ViewController usi il nuovo UISegmentControl. Aggiungere la riga seguente nel metodo *viewDidLoad* per assicurarsi che vengano mostrate le impostazioni locali attualmente selezionate:
   
    ```obj-c
        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
     ```  
    Nel metodo *subscribe* modificare quindi la chiamata a *storeCategoriesAndSubscribe* nel codice seguente:
   
    ```obj-c
        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];
    ```
3. Aggiornare infine il metodo *didRegisterForRemoteNotificationsWithDeviceToken* in AppDelegate.m, affinché sia possibile aggiornare correttamente la registrazione all'avvio dell'applicazione. Modificare la chiamata al metodo *subscribe* delle notifiche con il codice seguente:
   
    ```obj-c
        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(facoltativo) Invio di notifiche modello localizzato dall'app console .NET
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(facoltativo) Invio di notifiche modello localizzato dal dispositivo
Se non si ha accesso a Visual Studio o si desidera solo l'invio di notifiche modello localizzato direttamente dall'app sul dispositivo. È possibile aggiungere i parametri di modello localizzato per il metodo `SendNotificationRESTAPI` definito nell'esercitazione precedente.

    ```obj-c
        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }
    ```


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione le notifiche localizzate sono state inviate ai dispositivi iOS. Per informazioni sulle procedure per eseguire il push di notifiche a utenti specifici di app iOS, passare all'esercitazione seguente: 

> [!div class="nextstepaction"]
>[Eseguire il push di notifiche a utenti specifici](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Utilizzo di Hub di notifica per inviare le ultime notizie]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
