<properties 
	pageTitle="Inviare notifiche multipiattaforma a un utente specifico in iOS" 
	description="Informazioni su come inviare notifiche push a tutti i dispositivi di un utente specifico."
	services="app-service\mobile,notification-hubs" 
	documentationCenter="ios" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-dotnet" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="11/17/2015"
	ms.author="yuaxu"/>

# Inviare notifiche multipiattaforma a un utente specifico

[AZURE.INCLUDE [app-service-mobile-selector-push-users](../../includes/app-service-mobile-selector-push-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Questo argomento descrive come inviare notifiche a tutti i dispositivi registrati di un utente specifico dal proprio back-end mobile. È stato introdotto il concetto di [modelli], che offre alle applicazioni client la libertà di specificare i formati di payload e segnaposto di variabili al momento della registrazione. L'operazione di invio raggiunge tutte le piattaforme grazie a questi segnaposto, abilitando le notifiche multipiattaforma.

> [AZURE.NOTE]Affinché il push funzioni con i client multipiattaforma, sarà necessario completare questa esercitazione per ogni piattaforma che si vuole abilitare. Sarà necessario effettuare l'[aggiornamento del back-end mobile](#backend) solo una volta per i client che condividono lo stesso back-end mobile.
 
##Prerequisiti 

Prima di iniziare questa esercitazione, è necessario aver già completato le esercitazioni seguenti sul servizio app per ogni piattaforma client con cui si vuole lavorare:

+ [Introduzione all'autenticazione]<br/>Aggiunge un requisito di accesso all'app di esempio TodoList.

+ [Introduzione alle notifiche push]<br/>Configura l'app di esempio TodoList per le notifiche push.

##<a name="client"></a>Aggiornare il client per registrare i modelli per la gestione di notifiche push multipiattaforma

1. Spostare i frammenti di codice di registrazione del servizio APN in **application:didFinishLaunchingWithOptions** di **QSAppDelegate.m** nella chiamata a **loginWithProvider** in **QSTodoListViewController.m** in modo che questa avvenga dopo il completamento dell'autenticazione:

        [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
            [self refresh];
            
            // register iOS8 or previous devices for notifications
            if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
                [[UIApplication sharedApplication] registerForRemoteNotifications];
            }
            else {
                // Register for remote notifications
                [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
                 UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            }
        }];

2. Sostituire la chiamata **registerDeviceToken** in **application:didRegisterForRemoteNotificationsWithDeviceToken** con il codice seguente, in modo da poter lavorare con i modelli:

        NSDictionary *templates = @{
                               @"testNotificationTemplate": @{ @"body" : @{ @"aps" : @{ @"alert": @"$(message)" } } }
                               };
    
        // register with templates
        [client.push registerDeviceToken:deviceToken template:templates completion:^(NSError *error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

	È importante autenticare l'utente prima di eseguirne la registrazione per le notifiche push. Se si registra un utente autenticato, infatti, viene automaticamente aggiunto un tag con l'ID utente.

##<a name="backend"></a>Aggiornare il back-end del servizio per inviare notifiche a un utente specifico

[AZURE.INCLUDE [app-service-mobile-push-notifications-to-users](../../includes/app-service-mobile-push-notifications-to-users.md)]

##<a name="test"></a>Test dell'app

Pubblicare di nuovo il progetto di back-end mobile ed eseguire una qualsiasi delle app client impostate. Quando viene inserito l'elemento, il back-end invierà le notifiche a tutte le app client alle quali l'utente è connesso.

<!-- URLs. -->
[Introduzione all'autenticazione]: app-service-mobile-ios-get-started-users.md
[Introduzione alle notifiche push]: app-service-mobile-ios-get-started-push.md
[modelli]: https://msdn.microsoft.com/library/dn530748.aspx
 

<!---HONumber=AcomDC_1210_2015--->