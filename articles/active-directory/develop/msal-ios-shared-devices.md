---
title: Modalità dispositivo condiviso per dispositivi iOSShared device mode for iOS devices
titleSuffix: Microsoft identity platform | Azure
description: Informazioni su come abilitare la modalità dispositivo condiviso per consentire a Firstline Worker di condividere un dispositivo iOS
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7cecbc48eb362c2c0f1741352e6f7f5f6ad40c9e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550253"
---
# <a name="shared-device-mode-for-ios-devices"></a>Modalità dispositivo condiviso per dispositivi iOSShared device mode for iOS devices

> [!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I lavoratori di prima linea, come i dipendenti al dettaglio, i membri dell'equipaggio di volo e i lavoratori dei servizi sul campo, spesso utilizzano un dispositivo mobile condiviso per svolgere il proprio lavoro. Questi dispositivi condivisi possono presentare rischi per la sicurezza se gli utenti condividono le proprie password o PIN, intenzionalmente o meno, per accedere ai dati dei clienti e aziendali sul dispositivo condiviso.

La modalità dispositivo condiviso consente di configurare un dispositivo iOS 13 o versione successiva in modo che sia condiviso in modo più semplice e sicuro dai dipendenti. I dipendenti possono accedere e accedere rapidamente alle informazioni sui clienti. Quando hanno finito con il loro turno o attività, possono disconnettersi dal dispositivo ed è immediatamente pronto per l'uso da parte del dipendente successivo.

La modalità dispositivo condiviso fornisce anche la gestione supportata dall'identità Microsoft del dispositivo.

Questa funzionalità utilizza [l'app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) per gestire gli utenti nel dispositivo e per distribuire il [plug-in Microsoft Enterprise SSO per i dispositivi Apple.](apple-sso-plugin.md)

## <a name="create-a-shared-device-mode-app"></a>Creare un'app in modalità dispositivo condivisoCreate a shared device mode app

Per creare un'app in modalità dispositivo condivisa, gli sviluppatori e gli amministratori dei dispositivi cloud collaborano:To create a shared device mode app, developers and cloud device admins work together:

1. **Gli sviluppatori di** applicazioni scrivono un'app con un solo account (le app con più account non sono supportate in modalità dispositivo condiviso) e scrivono codice per gestire elementi come la disconnessione dei dispositivi condivisi.

1. **Gli amministratori di** dispositivo preparano il dispositivo da condividere tramite un provider di gestione dei dispositivi mobili (MDM) come Microsoft Intune per gestire i dispositivi nell'organizzazione. L'MDM esegue il push dell'app Microsoft Authenticator nei dispositivi e attiva la "Modalità condivisa" per ogni dispositivo tramite un aggiornamento del profilo per il dispositivo. Questa impostazione Modalità condivisa consente di modificare il comportamento delle app supportate nel dispositivo. Questa configurazione del provider MDM imposta la modalità del dispositivo condiviso per il dispositivo e abilita il [plug-in Microsoft Enterprise SSO per](apple-sso-plugin.md) i dispositivi Apple necessario per la modalità del dispositivo condiviso.

1. [**Obbligatorio solo durante l'anteprima pubblica**] Un utente con ruolo [Cloud Device Administrator](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator) deve quindi avviare [l'app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) e aggiungere il dispositivo all'organizzazione.

    Per configurare l'appartenenza dei ruoli organizzativi nel portale di Azure: Ruoli di Azure Active Directory e Amministratore dei dispositivi cloud**di amministratoriTo** > configure the membership of your organizational roles in the Azure portal: Azure **Active Directory** > Roles and Administrators**Cloud Device Administrator**

Le sezioni seguenti consentono di aggiornare l'applicazione per supportare la modalità dispositivo condiviso.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Usare Intune per abilitare la modalità dispositivo condiviso & estensione SSOUse Intune to enable shared device mode & SSO extension

> [!NOTE]
> Il passaggio seguente è necessario solo durante l'anteprima pubblica.

Il dispositivo deve essere configurato per supportare la modalità del dispositivo condiviso. Deve avere iOS 13 e deve essere registrato mdm. La configurazione MDM deve inoltre abilitare il [plug-in Microsoft Enterprise SSO per i dispositivi Apple.](apple-sso-plugin.md) Per ulteriori informazioni sulle estensioni SSO, vedere il [video Apple](https://developer.apple.com/videos/play/tech-talks/301/).

1. Nel portale di configurazione di Intune indicare al dispositivo di abilitare il [plug-in Microsoft Enterprise SSO per](apple-sso-plugin.md) i dispositivi Apple con la configurazione seguente:

    - **Tipo**: Reindirizza
    - **ID estensione**: com.microsoft.azureauthenticator.ssoextension
    - **ID team**: SGGM6D27TK
    - **URL**:https://login.microsoftonline.com
    - Dati aggiuntivi da configurare:
      - Chiave: sharedDeviceModeKey: sharedDeviceMode
      - Tipo: Boolean
      - Valore: True

    Per altre informazioni sulla configurazione con Intune, vedere la documentazione sulla configurazione di Intune.For more information about configuring with Intune, see the [Intune configuration documentation.](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)

1. Configurare quindi il MDM in modo che esetrai l'app Microsoft Authenticator al dispositivo tramite un profilo MDM.

    Impostare le seguenti opzioni di configurazione per attivare la modalità Dispositivo condiviso:

    - Configurazione 1:
      - Chiave: sharedDeviceModeKey: sharedDeviceMode
      - Tipo: Boolean
      - Valore: True

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>Modificare l'applicazione iOS per supportare la modalità dispositivo condivisoModify your iOS application to support shared device mode

Gli utenti dipendono da te per garantire che i loro dati non siano trapelati a un altro utente. Nelle sezioni seguenti vengono forniti segnali utili per indicare all'applicazione che si è verificata una modifica e deve essere gestita.

L'utente è responsabile del controllo dello stato dell'utente nel dispositivo ogni volta che viene usata l'app e quindi della cancellazione dei dati dell'utente precedente. Ciò include se viene ricaricato dallo sfondo in multi-tasking.

In caso di modifica dell'utente, è necessario assicurarsi che i dati dell'utente precedente vengano cancellati e che tutti i dati memorizzati nella cache visualizzati nell'applicazione vengano rimossi. Consigliamo vivamente a te e alla tua azienda di eseguire un processo di revisione della sicurezza dopo aver aggiornato l'app per supportare la modalità del dispositivo condiviso.

### <a name="detect-shared-device-mode"></a>Rileva modalità dispositivo condiviso

Il rilevamento della modalità dispositivo condiviso è importante per l'applicazione. Molte applicazioni richiedono una modifica nell'esperienza utente (UX) quando l'applicazione viene utilizzata in un dispositivo condiviso. Ad esempio, l'applicazione potrebbe avere una funzionalità "Iscriviti", che non è appropriata per un primo lavoratore perché probabilmente hanno già un account. È anche possibile aggiungere ulteriore sicurezza alla gestione dei dati da parte dell'applicazione se è in modalità dispositivo condiviso.

Usare `getDeviceInformationWithParameters:completionBlock:` l'API `MSALPublicClientApplication` in per determinare se un'app è in esecuzione in un dispositivo in modalità dispositivo condiviso.

I frammenti di codice seguenti `getDeviceInformationWithParameters:completionBlock:` illustrano esempi di utilizzo dell'API.

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Ottenere l'utente connesso e determinare se un utente è stato modificato nel dispositivoGet the signed-in user and determine if a user has changed on the device

Un'altra parte importante del supporto della modalità di dispositivo condiviso è determinare lo stato dell'utente nel dispositivo e cancellare i dati dell'applicazione se un utente è stato modificato o se non è presente alcun utente nel dispositivo. L'utente è responsabile di garantire che i dati non vengano trapelati a un altro utente.

È possibile `getCurrentAccountWithParameters:completionBlock:` utilizzare l'API per eseguire una query sull'account attualmente connesso nel dispositivo.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>Connettere un utente a livello globale

Quando un dispositivo è configurato come dispositivo condiviso, l'applicazione può chiamare l'API `acquireTokenWithParameters:completionBlock:` per accedere all'account. L'account sarà disponibile a livello globale per tutte le app idonee sul dispositivo dopo l'accesso della prima app all'account.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Disconnettere un utente a livello globale

Il codice seguente rimuove l'account di accesso e cancella i token memorizzati nella cache non solo dall'app, ma anche dal dispositivo in modalità dispositivo condiviso. Tuttavia, non cancella i *dati* dall'applicazione. È necessario cancellare i dati dall'applicazione, nonché cancellare tutti i dati memorizzati nella cache che l'applicazione potrebbe visualizzare all'utente.

#### <a name="clear-browser-state"></a>Cancella stato del browser

> [!NOTE]
> Il passaggio seguente è necessario solo durante l'anteprima pubblica.

In questa versione di anteprima pubblica, il [plug-in Microsoft Enterprise SSO per](apple-sso-plugin.md) i dispositivi Apple cancella lo stato solo per le applicazioni. Non cancella lo stato sul browser Safari. Si consiglia di cancellare manualmente la sessione del browser per assicurarsi che nessuna traccia dello stato utente venga lasciata indietro. È possibile utilizzare `signoutFromBrowser` la proprietà opzionale mostrata di seguito per cancellare eventuali cookie. In questo modo il browser per avviare brevemente sul dispositivo.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>Passaggi successivi

Per vedere in azione la modalità dispositivo condiviso, l'esempio di codice seguente in GitHub include un esempio di esecuzione di un'app Firstline Worker in un dispositivo iOS in modalità dispositivo condiviso:To see shared device mode in action, the following code sample on GitHub includes an example of running a Firstline Worker app on an iOS device in shared device mode:

[Esempio di API Microsoft Graph Swift di MSAL](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
