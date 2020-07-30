---
title: Inviare notifiche push a iOS con Hub di notifica di Azure e iOS SDK versione 3.0.0 Preview 1
description: In questa esercitazione si apprenderà come usare Hub di notifica di Azure e Apple Push Notification Service per inviare notifiche push a dispositivi iOS.
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: f43db986f70e64e55225465223b7e324450541a8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87084337"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-version-300-preview1"></a>Esercitazione: Inviare notifiche push alle app iOS con Hub di notifica di Azure (versione 3.0.0 Preview 1)

Questa esercitazione illustra come usare Hub di notifica di Azure per inviare notifiche push a un'app iOS usando l'SDK di Hub di notifica di Azure versione 2.0.4.

Questa esercitazione illustra i passaggi seguenti:

- Creare un'app iOS di esempio.
- Connettere l'app iOS a Hub di notifica di Azure.
- Inviare notifiche push di prova.
- Verificare che l'app riceva le notifiche.

Il codice completo per questa esercitazione può essere scaricato da [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione sono necessari i prerequisiti seguenti:

- Un Mac che esegue [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), insieme a un certificato dello sviluppatore valido installato nel portachiavi.
- Un iPhone o iPad con iOS versione 10 o successiva.
- Il dispositivo fisico registrato nel [portale Apple](https://developer.apple.com/) e associato al certificato.

Prima di procedere è importante seguire l'esercitazione precedente che spiega come iniziare a usare [Hub di notifica di Azure per le app iOS](ios-sdk-get-started.md) per configurare le credenziali push nell'hub di notifica. Anche se non si ha alcuna esperienza con lo sviluppo di app iOS, si dovrebbe essere in grado di seguire questa procedura.

> [!NOTE]
> Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push in un dispositivo iOS fisico (iPhone o iPad), anziché in un emulatore iOS.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Connettere l'app iOS all'hub di notifica

1. In Xcode creare un nuovo progetto iOS e selezionare il modello **Single View Application** (App visualizzazione singola).

   :::image type="content" source="media/ios-sdk/image1.png" alt-text="Selezionare il modello":::

2. Quando si configurano le opzioni per il nuovo progetto, assicurarsi di usare lo stesso **nome prodotto** e lo stesso **identificatore dell'organizzazione** usati quando è stato impostato l'identificatore del bundle nel portale Apple Developer.

3. In Project Navigator (Esplorazione progetto) selezionare il nome del progetto in **Targets** (Destinazioni), quindi selezionare la scheda **Signing & Capabilities** (Firma e funzionalità). Assicurarsi di selezionare il **Team** appropriato per l'account Apple Developer. XCode recupererà automaticamente il profilo di provisioning creato in precedenza in base all'identificatore del bundle.

   Se il profilo di provisioning creato in Xcode non viene visualizzato, provare ad aggiornare i profili per l'identità di firma. Fare clic su **Xcode** nella barra dei menu, fare clic su **Preferences** (Preferenze), selezionare la scheda **Account**, fare clic sul pulsante **View Details** (Visualizza dettagli), selezionare la propria identità di firma e infine fare clic sul pulsante di aggiornamento nell'angolo in basso a destra.

   :::image type="content" source="media/ios-sdk/image2.png" alt-text="Visualizzare i dettagli":::

4. Nella scheda **Signing & Capabilities** (Firma e funzionalità) selezionare **+ Capability** (Funzionalità). Fare doppio clic su **Push Notifications** (Notifiche push) per abilitare l'opzione.

   :::image type="content" source="media/ios-sdk/image3.png" alt-text="Funzionalità":::

5. Aggiungere i moduli dell'SDK di Hub di notifica di Azure.

   È possibile integrare l'SDK di Hub di notifica di Azure nell'app usando [Cocoapods](https://cocoapods.org/) o aggiungendo manualmente i file binari al progetto.

   - Integrazione tramite Cocoapods: Aggiungere le dipendenze seguenti al podfile per includere l'SDK di Hub di notifica di Azure nell'app:

      ```ruby
      pod 'AzureNotificationHubs-iOS'
      ```

      - Eseguire pod install per installare il pod appena definito e aprire il file con estensione .xcworkspace.

         Se viene visualizzato un errore che indica ad esempio che **non è possibile trovare una specifica per AzureNotificationHubs-iOS**, durante l'esecuzione di pod install, eseguire `pod repo update` per ottenere i pod più recenti dal repository CocoaPods, quindi eseguire pod install.

   - Integrazione tramite copia dei file binari nel progetto:

      È possibile eseguire l'integrazione copiando i file binari nel progetto, come segue:

        - Scaricare il framework dell' [SDK di Hub di notifica di Azure](https://github.com/Azure/azure-notificationhubs-iOS/releases/) fornito come file ZIP e decomprimerlo.

        - In Xcode fare clic con il pulsante destro del mouse sul progetto e scegliere l'opzione **Add Files to** (Aggiungi file a) per aggiungere la cartella **WindowsAzureMessaging.framework** al progetto Xcode. Selezionare **Options** (Opzioni) e assicurarsi che sia selezionato **Copy items if needed** (Copia elementi se necessario), quindi fare clic su **Add** (Aggiungi).

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="Aggiungere il framework":::

6. Aggiungere un nuovo file di intestazione denominato **Constants.h** al progetto. A tale scopo, fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **New File** (Nuovo file). Selezionare quindi **Header File** (File di intestazione). Questo file include le costanti per l'hub di notifica. Selezionare **Next** (Avanti). Assegnare al file il nome **Constants.h**.

7. Aggiungere il seguente codice al file Constants.h:

   ```objc
   #ifndef Constants_h
   #define Constants_h
   extern NSString* const NHInfoConnectionString;
   extern NSString* const NHInfoHubName;
   extern NSString* const NHUserDefaultTags;
   #endif /* Constants_h */
   ```

8. Aggiungere il file di implementazione per Constants.h. A tale scopo, fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **New File** (Nuovo file). Selezionare **Objective-C File** (File Objective-C) e quindi selezionare **Next** (Avanti). Assegnare al file il nome **Constants.m**.

   :::image type="content" source="media/ios-sdk/image5.png" alt-text="Aggiungere il file di implementazione":::

9. Aprire il file **Constants.m** e sostituirne il contenuto con il codice seguente. Sostituire i segnaposto dei valori letterali stringa `NotificationHubConnectionString` e `NotificationHubConnectionString` rispettivamente con il nome dell'hub e la firma **DefaultListenSharedAccessSignature** ottenuti in precedenza dal portale:

   ```objc
   #import <Foundation/Foundation.h>
   #import "Constants.h"

   NSString* const NHInfoConnectionString = @"NotificationHubConnectionString";
   NSString* const NHInfoHubName = @"NotificationHubName";NSString* const NHUserDefaultTags = @"notification_tags";
   ```

10. Nel file di progetto **AppDelegate.h** aggiungere l'istruzione `import` seguente:

    ```objc
    #import "Constants.h"
    ```

11. Nello stesso file **AppDelegate.m** sostituire tutto il codice dopo `didFinishLaunchingWithOptions` con il codice seguente:

    ```objc
    // Tells the delegate that the app successfully registered with Apple Push Notification service (APNs).


    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    NSString *hubName = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoHubName];
    NSString *connectionString = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoConnectionString];
    [MSNotificationHub initWithConnectionString:connectionString withHubName:hubName];


    NSMutableSet *tags = [[NSMutableSet alloc] init];

    // Load and parse stored tags
    NSString *unparsedTags = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
    if (unparsedTags.length > 0) {
        NSArray *tagsArray = [unparsedTags componentsSeparatedByString: @","];

        [MSNotificationHub addTags:tagsArray];
    }

    }
    - (void)showAlert:(NSString *)message withTitle:(NSString *)title {
    if (title == nil) {
        title = @"Alert";
    }
    UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:message preferredStyle:UIAlertControllerStyleAlert];
    [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil]];
    [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }

    - (void)showNotification:(NSDictionary *)userInfo {
    [self logNotificationDetails:userInfo];

    NotificationDetailViewController *notificationDetail = [[NotificationDetailViewController alloc] initWithUserInfo:userInfo];
    [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:notificationDetail animated:YES completion:nil];
    }

    @end
    ```

    Questo codice si connette all'hub di notifica usando le informazioni di connessione specificate in **Constants.h**. Fornisce quindi il token del dispositivo all'hub di notifica in modo che quest'ultimo possa inviare notifiche.

### <a name="create-notificationdetailviewcontroller-header-file"></a>Creare il file di intestazione NotificationDetailViewController

1. Analogamente alle istruzioni precedenti, aggiungere un altro file di intestazione denominato **NotificationDetailViewController.h**. Sostituire il contenuto del file di intestazione con il codice seguente:

   ```objc
   #import <UIKit/UIKit.h>

   NS_ASSUME_NONNULL_BEGIN

   @interface NotificationDetailViewController : UIViewController

   @property (strong, nonatomic) IBOutlet UILabel *titleLabel;
   @property (strong, nonatomic) IBOutlet UILabel *bodyLabel;
   @property (strong, nonatomic) IBOutlet UIButton *dismissButton;

   @property (strong, nonatomic) NSDictionary *userInfo;

   - (id)initWithUserInfo:(NSDictionary *)userInfo;

   @end

   NS_ASSUME_NONNULL_END
   ```

2. Aggiungere il file di implementazione **NotificationDetailViewController.m**. Sostituire il contenuto del file con il codice seguente, che implementa i metodi UIViewController:

   ```objc
   #import "NotificationDetailViewController.h"

   @interface NotificationDetailViewController ()

   @end

   @implementation NotificationDetailViewController

   - (id)initWithUserInfo:(NSDictionary *)userInfo {
    self = [super initWithNibName:@"NotificationDetail" bundle:nil];
    if (self) {
        _userInfo = userInfo;
    }
    return self;
   }

   - (void)viewDidLayoutSubviews {
    [self.titleLabel sizeToFit];
    [self.bodyLabel sizeToFit];
   }

   - (void)viewDidLoad {
    [super viewDidLoad];

    NSString *title = nil;
    NSString *body = nil;

    NSDictionary *aps = [_userInfo valueForKey:@"aps"];
    NSObject *alertObject = [aps valueForKey:@"alert"];
    if (alertObject != nil) {
        if ([alertObject isKindOfClass:[NSDictionary class]]) {
            NSDictionary *alertDict = (NSDictionary *)alertObject;
            title = [alertDict valueForKey:@"title"];
            body = [alertObject valueForKey:@"body"];
        } else if ([alertObject isKindOfClass:[NSString class]]) {
            body = (NSString *)alertObject;
        } else {
            NSLog(@"Unable to parse notification content. Unexpected format: %@", alertObject);
        }
    }

    if (title == nil) {
        title = @"<unset>";
    }

    if (body == nil) {
        body = @"<unset>";
    }

    self.titleLabel.text = title;
    self.bodyLabel.text = body;
   }

   - (IBAction)handleDismiss:(id)sender {
    [self dismissViewControllerAnimated:YES completion:nil];
   }

   @end
   ```

### <a name="viewcontroller"></a>ViewController

1. Nel file del progetto **ViewController.h** aggiungere le istruzioni `import` seguenti:

   ```objc
   #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
   #import <UserNotifications/UserNotifications.h>
   ```

2. In **ViewController.h** aggiungere inoltre la dichiarazione di proprietà seguente dopo la dichiarazione `@interface`:

   ```objc
   @property (strong, nonatomic) IBOutlet UITextField *tagsTextField;
   ```

3. Nel file di implementazione **ViewController.m** del progetto sostituire il contenuto del file con il codice seguente:

   ```objc
   #import "ViewController.h"
   #import "Constants.h"
   #import "AppDelegate.h"

   @interface ViewController ()

   @end

   @implementation ViewController

   // UIViewController methods

   - (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
    // Simple method to dismiss keyboard when user taps outside of the UITextField.
    [self.view endEditing:YES];
   }

   - (void)viewDidLoad {
    [super viewDidLoad];

    // Load raw tags text from storage and initialize the text field
    self.tagsTextField.text = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
   }

   - (IBAction)handleRegister:(id)sender {
    // Save raw tags text in storage
    [[NSUserDefaults standardUserDefaults] setValue:self.tagsTextField.text forKey:NHUserDefaultTags];

   // Delegate processing the register action to the app delegate.
   [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleRegister)];
   }

   - (IBAction)handleUnregister:(id)sender {
    [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleUnregister)];
   }

   @end
   ```

4. Per verificare che non vi siano errori, compilare ed eseguire l'app nel dispositivo.

## <a name="send-test-push-notifications"></a>Inviare notifiche push di prova

È possibile testare rapidamente la ricezione di notifiche nell'app con l'opzione **Invio di prova** nel [portale di Azure](https://portal.azure.com/). Verrà inviata una notifica push di prova al dispositivo.

:::image type="content" source="media/ios-sdk/image6.png" alt-text="Invio di prova":::

Le notifiche push vengono in genere inviate in un servizio back-end come App per dispositivi mobili o ASP.NET usando una libreria compatibile. Se non è disponibile una libreria per il back-end è anche possibile usare direttamente l'API REST per inviare messaggi di notifica.

Di seguito è riportato un elenco di altre esercitazioni, che è possibile esaminare per l'invio di notifiche:

- App per dispositivi mobili di Azure: per un esempio di come inviare notifiche dal back-end di App per dispositivi mobili integrato con Hub di notifica, vedere [Aggiungere notifiche push all'app iOS](/azure/app-service-mobile/app-service-mobile-ios-get-started-push).
- ASP.NET: [usare Hub di notifica per inviare notifiche push agli utenti](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).
- Hub di notifica di Azure in Java SDK: vedere [Come usare Hub di notifica da Java](notification-hubs-java-push-notification-tutorial.md) per l'invio di notifiche da Java. È stato testato in Eclipse per lo sviluppo per Android.
- PHP: [Come usare Hub di notifica da PHP](notification-hubs-php-push-notification-tutorial.md).

## <a name="verify-that-your-app-receives-push-notifications"></a>Verificare che l'app riceve le notifiche push

Per testare le notifiche push in iOS, è necessario distribuire l'app in un dispositivo fisico iOS. Non è possibile inviare notifiche push Apple con il simulatore iOS.

1. Eseguire l'app e verificare che la registrazione riesca, quindi scegliere **OK**.

   :::image type="content" source="media/ios-sdk/image7.png" alt-text="Registra":::

2. Inviare ora una notifica push di prova dal [portale di Azure](https://portal.azure.com/), come illustrato nella sezione precedente.

3. La notifica push viene inviata a tutti i dispositivi registrati per la ricezione di notifiche dall'hub di notifica specificato.

   :::image type="content" source="media/ios-sdk/image8.png" alt-text="Invio di prova":::

## <a name="next-steps"></a>Passaggi successivi

In questo semplice esempio le notifiche push vengono trasmesse a tutti i dispositivi iOS registrati. Per informazioni su come inviare notifiche push a dispositivi iOS specifici, passare all'esercitazione seguente:

[Esercitazione: Inviare notifiche push a specifici dispositivi](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Per altre informazioni, vedere gli articoli seguenti:

- [Panoramica di Hub di notifica di Azure](notification-hubs-push-notification-overview.md)
- [API REST di Hub di notifica](/rest/api/notificationhubs/)
- [SDK di Hub di notifica per le operazioni back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [SDK di Hub di notifica su GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registrazione con il back-end dell'applicazione](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Gestione delle registrazioni](notification-hubs-push-notification-registration-management.md)
- [Uso dei tag](notification-hubs-tags-segment-push-message.md)
- [Uso di modelli personalizzati](notification-hubs-templates-cross-platform-push-messages.md)
- [Controllo degli accessi del bus di servizio con firme di accesso condiviso](../service-bus-messaging/service-bus-sas.md)
- [Generare token di firma di accesso condiviso a livello di codice](/rest/api/eventhub/generate-sas-token)
- [Sicurezza di Apple: crittografia comune](https://developer.apple.com/security/)
- [Ora del periodo UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
