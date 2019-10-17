---
title: Aggiungere l'autenticazione in iOS con le app per dispositivi mobili di Azure
description: Informazioni su come usare le app per dispositivi mobili di Azure per autenticare gli utenti dell'app iOS tramite vari provider di identità, tra cui AAD, Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 800d86750f091404ee7f940d7cf8f6631e3fbbeb
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388689"
---
# <a name="add-authentication-to-your-ios-app"></a>Aggiungere l'autenticazione all'app iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center supporta servizi end-to-end e integrati fondamentali per lo sviluppo di app per dispositivi mobili. Gli sviluppatori possono usare i servizi **Build**, **Test** e **Distribute** per configurare una pipeline di integrazione e distribuzione continue. Dopo la distribuzione dell'app, gli sviluppatori possono monitorarne lo stato e l'utilizzo tramite i servizi **Analytics** e **Diagnostics** e interagire con gli utenti tramite il servizio **Push**. Gli sviluppatori possono anche usare il servizio **Auth** per autenticare gli utenti e il servizio **Data** per salvare e sincronizzare i dati dell'app nel cloud.
>
> Per integrare i servizi cloud nelle applicazioni per dispositivi mobili, iscriversi ad [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

Questa esercitazione illustra come aggiungere l'autenticazione al progetto di [avvio rapido di iOS] tramite un provider di identità supportato. Questa esercitazione è basata sull'esercitazione relativa all' [avvio rapido di iOS] , che deve essere completata per prima.

## <a name="register"></a>Registrare l'app per l'autenticazione e configurare il servizio app
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Aggiungere l'app agli URL di reindirizzamento esterni consentiti

L'autenticazione sicura richiede la definizione di un nuovo schema URL per l'app.  In questo modo il sistema di autenticazione reindirizza all'app al termine del processo di autenticazione.  In questa esercitazione si usa lo schema URL _appname_.  È tuttavia possibile usare QUALSIASI schema URL.  Lo schema deve essere univoco per l'applicazione per dispositivi mobili.  Per abilitare il reindirizzamento sul lato server:

1. Nel [Azure portal] selezionare il servizio app.

2. Fare clic sull'opzione di menu **Autenticazione/Autorizzazione**.

3. Fare clic su **Azure Active Directory** nella sezione **Provider di autenticazione**.

4. Impostare **Modalità di gestione** su **Avanzata**.

5. In **URL di reindirizzamento esterni consentiti** specificare `appname://easyauth.callback`.  Il valore _appname_ in questa stringa è lo schema URL per l'applicazione per dispositivi mobili.  Deve seguire le normale specifica URL per un protocollo, ovvero usare solo lettere e numeri e iniziare con una lettera.  È opportuno prendere nota della stringa scelta perché sarà necessario modificare il codice dell'applicazione per dispositivi mobili con lo schema URL in diverse posizioni.

6. Fare clic su **OK**.

7. Fare clic su **Salva**

## <a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

In Xcode fare clic su **Esegui** per avviare l'app. Viene generata un'eccezione perché l'app prova ad accedere al back-end come utente non autenticato, mentre ora la tabella *TodoItem* richiede l'autenticazione.

## <a name="add-authentication"></a>Aggiungere l'autenticazione all'app
**Objective-C**:

1. In Mac aprire *QSTodoListViewController.m* in Xcode e aggiungere il metodo seguente:

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    Modificare *google* in *microsoftaccount*, *twitter*, *facebook* o *windowsazureactivedirectory* se non si usa Google come provider di identità. Se si usa Facebook, è necessario aggiungere i [domini Facebook][1] nell'app.

    Sostituire **urlScheme** con un nome univoco per l'applicazione.  Il valore di urlScheme deve corrispondere a quello del protocollo dello schema URL specificato nel campo **URL di reindirizzamento esterni consentiti** nel portale di Azure. urlScheme viene usato dal callback di autenticazione per tornare all'applicazione dopo aver completato la richiesta di autenticazione.

2. Sostituire `[self refresh]` in `viewDidLoad` in *QSTodoListViewController.m* con il codice seguente:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Aprire il file `QSAppDelegate.h` e aggiungere il codice seguente:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Aprire il file `QSAppDelegate.m` e aggiungere il codice seguente:

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   Aggiungere questo codice direttamente prima della riga `#pragma mark - Core Data stack`.  Sostituire _appname_ con il valore urlScheme usato nel passaggio 1.

5. Aprire il file `AppName-Info.plist` (sostituendo AppName con il nome dell'app) e aggiungere il codice seguente:

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Questo codice deve essere inserito all'interno dell'elemento `<dict>`.  Sostituire la stringa _appname_ (all'interno della matrice per **CFBundleURLSchemes**) con il nome app scelto nel passaggio 1.  È possibile apportare queste modifiche anche nell'editor Plist. Fare clic sul file `AppName-Info.plist` in XCode per aprire l'editor Plist.

    Sostituire la stringa `com.microsoft.azure.zumo` per **CFBundleURLName** con l'identificatore del bundle Apple.

6. Fare clic su *Run* (Esegui) per avviare l'app e quindi accedere. Una volta eseguito l'accesso, dovrebbe essere possibile visualizzare l'elenco Todo e apportare modifiche.

**Swift**:

1. In Mac aprire *ToDoTableViewController.swift* in Xcode e aggiungere il metodo seguente:

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    Modificare *google* in *microsoftaccount*, *twitter*, *facebook* o *windowsazureactivedirectory* se non si usa Google come provider di identità. Se si usa Facebook, è necessario aggiungere i [domini Facebook][1] nell'app.

    Sostituire **urlScheme** con un nome univoco per l'applicazione.  Il valore di urlScheme deve corrispondere a quello del protocollo dello schema URL specificato nel campo **URL di reindirizzamento esterni consentiti** nel portale di Azure. urlScheme viene usato dal callback di autenticazione per tornare all'applicazione dopo aver completato la richiesta di autenticazione.

2. Rimuovere le righe `self.refreshControl?.beginRefreshing()` e `self.onRefresh(self.refreshControl)` alla fine di `viewDidLoad()` in *ToDoTableViewController.swift*. Aggiungere una chiamata a `loginAndGetData()` al posto di tali righe:

    ```swift
    loginAndGetData()
    ```

3. Aprire il file `AppDelegate.swift` e aggiungere la riga seguente alla classe `AppDelegate`:

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    Sostituire _appname_ con il valore urlScheme usato nel passaggio 1.

4. Aprire il file `AppName-Info.plist` (sostituendo AppName con il nome dell'app) e aggiungere il codice seguente:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Questo codice deve essere inserito all'interno dell'elemento `<dict>`.  Sostituire la stringa _appname_ (all'interno della matrice per **CFBundleURLSchemes**) con il nome app scelto nel passaggio 1.  È possibile apportare queste modifiche anche nell'editor Plist. Fare clic sul file `AppName-Info.plist` in XCode per aprire l'editor Plist.

    Sostituire la stringa `com.microsoft.azure.zumo` per **CFBundleURLName** con l'identificatore del bundle Apple.

5. Fare clic su *Run* (Esegui) per avviare l'app e quindi accedere. Una volta eseguito l'accesso, dovrebbe essere possibile visualizzare l'elenco Todo e apportare modifiche.

L'autenticazione del servizio app usa la comunicazione tra app di Apple.  Per altri dettagli su questo argomento, vedere la [documentazione di Apple][2] .
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure portal]: https://portal.azure.com

[avvio rapido di iOS]: app-service-mobile-ios-get-started.md

