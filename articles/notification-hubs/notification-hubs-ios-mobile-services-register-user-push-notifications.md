<properties 
	pageTitle="Registrare l'utente corrente per le notifiche push mediante un servizio mobile - Hub di notifica" 
	description="Informazioni su come chiedere la registrazione per le notifiche push in un'app per iOS con Hub di notifica di Azure quando la registrazione viene eseguita mediante Servizi mobili di Azure." 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="07/28/2015" 
	ms.author="yuaxu"/>

# Registrare l'utente corrente per le notifiche push mediante un servizio mobile

> [AZURE.SELECTOR]
[Windows Store C#](notification-hubs-windows-store-mobile-services-register-user-push-notifications.md)
[Windows Store C#](notification-hubs-ios-mobile-services-register-user-push-notifications.md)

In questo argomento viene illustrato come richiedere la registrazione per le notifiche push con Hub di notifica di Azure quando la registrazione viene eseguita mediante Servizi mobili di Azure. In questo argomento viene estesa l'esercitazione [Utilizzo di Hub di notifica per inviare notifiche agli utenti]. Per creare il servizio mobile autenticato è necessario aver già completato i passaggi richiesti in tale esercitazione. Per ulteriori informazioni sullo scenario di notifica agli utenti, vedere [Utilizzo di Hub di notifica per inviare notifiche agli utenti].

1. In Xcode, aprire il file QSTodoService.h nel progetto creato dopo avere completato l'esercitazione sui prerequisiti [Introduzione all'autenticazione], quindi aggiungere la proprietà **deviceToken** seguente:

		@property (nonatomic) NSData* deviceToken;

 	Nella proprietà viene archiviato il token del dispositivo.

2. Nel file QSTodoService.m file aggiungere il metodo **getDeviceTokenInHex** seguente:

			- (NSString*)getDeviceTokenInHex {
			    const unsigned *tokenBytes = [[self deviceToken] bytes];
			    NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
			                          ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
			                          ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
			                          ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
			    return hexToken;
			}

	Il metodo converte il token del dispositivo in un valore stringa esadecimale.

3. Nel file QSAppDelegate.m aggiungere le righe di codice seguenti al metodo **didFinishLaunchingWithOptions**:

			[[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

	Ciò consente di abilitare le notifiche push nell'app.

4. 	Nel file QSAppDelegate.m aggiungere il metodo seguente:

			- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
			    [QSTodoService defaultService].deviceToken = deviceToken;
			}

	Ciò consente l'aggiornamento della proprietà **deviceToken**.

	> [AZURE.NOTE]A questo punto, il metodo non dovrebbe contenere altro codice. Se è già presente una chiamata al metodo **registerNativeWithDeviceToken** aggiunto durante l'esercitazione [Introduzione ad Hub di notifica](/manage/services/notification-hubs/get-started-notification-hubs-ios/"%20target="_blank"), è necessario impostare la chiamata come commento oppure rimuoverla.

5.  (Facoltativo): nel file QSAppDelegate.m aggiungere il metodo del gestore seguente:

			- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
			    NSLog(@"%@", userInfo);
			    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
			                          [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
			                          @"OK" otherButtonTitles:nil, nil];
			    [alert show];
			}

 	Questo metodo visualizza un avviso nell'interfaccia utente quando l'app riceve notifiche mentre è in esecuzione.

6. Nel file QSTodoListViewController.m file aggiungere il metodo **registerForNotificationsWithBackEnd**:

			- (void)registerForNotificationsWithBackEnd {
			    NSString* json = [NSString  stringWithFormat:@"{"platform":"ios", "deviceToken":"%@"}", [self.todoService getDeviceTokenInHex] ];

			    [self.todoService.client invokeAPI:@"register_notifications" data:[json dataUsingEncoding:NSUTF8StringEncoding] HTTPMethod:@"POST" parameters:nil headers:nil completion:^(id result, NSHTTPURLResponse *response, NSError *error) {
			        if (error != nil) {
			            NSLog(@"Registration failed: %@", error);
			        } else {
			            // display UIAlert with successful login
			            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
			            [alert show];
			        }
			    }];
			}

	Questo metodo costruisce un payload json che contiene il token del dispositivo, quindi chiama l'API personalizzata nel servizio mobile per effettuare la registrazione per le notifiche. Questo metodo crea un token dispositivo per le notifiche push e lo invia, insieme al tipo di dispositivo, al metodo dell'API personalizzata che crea una registrazione in Hub di notifica. Questa API personalizzata è stata definita in [Utilizzo di Hub di notifica per inviare notifiche agli utenti].

7.	Nel metodo **viewDidAppear**, infine, aggiungere una chiamata al nuovo metodo **registerForNotificationsWithBackEnd** dopo la riuscita dell'autenticazione dell'utente, come nell'esempio seguente:

			- (void)viewDidAppear:(BOOL)animated
			{
			    MSClient *client = self.todoService.client;

			    if (client.currentUser != nil) {
			        return;
			    }

			    [client loginWithProvider:@"microsoftaccount" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
			        [self refresh];
			        [self registerForNotificationsWithBackEnd];
			    }];
			}

	> [AZURE.NOTE]In questo modo la registrazione verrà richiesta ogni volta che viene caricata la pagina. Nell'app dell'utente può essere preferibile eseguire la registrazione solo su base periodica, per verificare che sia aggiornata.
	
Ora che l'app client è stata aggiornata, tornare a [Utilizzo di Hub di notifica per inviare notifiche agli utenti] e aggiornare il servizio mobile per l'invio di notifiche con Hub di notifica.

<!-- Anchors. -->

<!-- Images. -->


<!-- URLs. -->
[Utilizzo di Hub di notifica per inviare notifiche agli utenti]: /manage/services/notification-hubs/notify-users
[Introduzione all'autenticazione]: /develop/mobile/tutorials/get-started-with-users-ios/

[Azure Management Portal]: https://manage.windowsazure.com/
[Get Started with Notification Hubs]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
 

<!---HONumber=August15_HO6-->