---
title: Modalità dispositivo condiviso per dispositivi iOS
titleSuffix: Microsoft identity platform | Azure
description: Informazioni su come abilitare la modalità dispositivo condiviso per consentire ai prima riga Worker di condividere un dispositivo iOS
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
ms.openlocfilehash: 0354010297942c3b9e18dc6f556cb0afa075ff5f
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89649103"
---
# <a name="shared-device-mode-for-ios-devices"></a>Modalità dispositivo condiviso per dispositivi iOS

> [!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I ruoli di lavoro di prima riga, ad esempio i Retail Associates, i membri del team di volo e i worker del servizio Field spesso usano un dispositivo mobile condiviso per eseguire il lavoro Questi dispositivi condivisi possono presentare rischi per la sicurezza se gli utenti condividono le password o i pin, intenzionalmente o meno, per accedere ai dati aziendali e ai clienti sul dispositivo condiviso.

La modalità dispositivo condiviso consente di configurare un dispositivo iOS 13 o superiore per una condivisione più semplice e sicura dei dipendenti. I dipendenti possono accedere rapidamente alle informazioni dei clienti. Al termine del turno o dell'attività, è possibile disconnettersi dal dispositivo ed è immediatamente pronto per l'uso da parte del dipendente successivo.

La modalità dispositivo condiviso fornisce anche la gestione supportata da Microsoft Identity del dispositivo.

Questa funzionalità Usa l' [app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) per gestire gli utenti sul dispositivo e per distribuire il [plug-in Microsoft Enterprise SSO per i dispositivi Apple](apple-sso-plugin.md).

## <a name="create-a-shared-device-mode-app"></a>Creare un'app in modalità dispositivo condiviso

Per creare un'app in modalità dispositivo condivisa, gli sviluppatori e gli amministratori di dispositivi cloud interagiscono tra loro:

1. **Gli sviluppatori di applicazioni** scrivono un'app con un solo account (le app con più account non sono supportate in modalità dispositivo condiviso) e scrivono codice per gestire elementi come la disconnessione dei dispositivi condivisi.

1. Gli **amministratori di dispositivi** preparano il dispositivo per la condivisione usando un provider di gestione di dispositivi mobili (MDM) come Microsoft Intune per gestire i dispositivi nell'organizzazione. Il MDM inserisce l'app Microsoft Authenticator nei dispositivi e attiva la modalità "Shared" per ogni dispositivo tramite un aggiornamento del profilo al dispositivo. Questa impostazione della modalità condivisa è la modifica del comportamento delle app supportate nel dispositivo. Questa configurazione dal provider MDM imposta la modalità del dispositivo condiviso per il dispositivo e Abilita il [plug-in Microsoft Enterprise SSO per i dispositivi Apple](apple-sso-plugin.md) , che è necessario per la modalità dispositivo condiviso.

1. [**Obbligatorio durante l'anteprima pubblica**] Un utente con ruolo di [amministratore del dispositivo cloud](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator) deve quindi avviare l' [app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) e aggiungere il dispositivo all'organizzazione.

    Per configurare l'appartenenza dei ruoli aziendali nel portale di Azure: **Azure Active Directory**  >  **ruoli e amministratori**  >  **amministratore del dispositivo cloud**

Le sezioni seguenti consentono di aggiornare l'applicazione per supportare la modalità dispositivo condiviso.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Usare Intune per abilitare la modalità dispositivo condiviso & estensione SSO

> [!NOTE]
> Il passaggio seguente è necessario solo durante l'anteprima pubblica.

Il dispositivo deve essere configurato per supportare la modalità dispositivo condiviso. È necessario che iOS 13 + sia installato e che sia registrato in MDM. Per la configurazione MDM è inoltre necessario abilitare il [plug-in Microsoft Enterprise SSO per i dispositivi Apple](apple-sso-plugin.md). Per ulteriori informazioni sulle estensioni SSO, vedere il [video Apple](https://developer.apple.com/videos/play/tech-talks/301/).

1. Nel portale di configurazione di Intune, indicare al dispositivo di abilitare il [plug-in Microsoft Enterprise SSO per i dispositivi Apple](apple-sso-plugin.md) con la seguente configurazione:

    - **Tipo**: Reindirizzamento
    - **ID estensione**: com. Microsoft. azureauthenticator. ssoextension
    - **ID team**: SGGM6D27TK
    - **URL**: https://login.microsoftonline.com
    - Dati aggiuntivi da configurare:
      - Chiave: sharedDeviceMode
      - Tipo: Boolean
      - Valore: true

    Per altre informazioni sulla configurazione con Intune, vedere la [documentazione di configurazione di Intune](/intune/configuration/ios-device-features-settings).

1. Configurare quindi il MDM per eseguire il push dell'app Microsoft Authenticator al dispositivo tramite un profilo MDM.

    Impostare le opzioni di configurazione seguenti per attivare la modalità dispositivo condiviso:

    - Configurazione 1:
      - Chiave: sharedDeviceMode
      - Tipo: Boolean
      - Valore: true

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>Modificare l'applicazione iOS per supportare la modalità dispositivo condiviso

Gli utenti dipendono dall'utente per assicurarsi che i dati non vengano divulgati a un altro utente. Le sezioni seguenti forniscono segnali utili per indicare all'applicazione che si è verificata una modifica e che devono essere gestite.

Si è responsabili del controllo dello stato dell'utente sul dispositivo ogni volta che viene usata l'app e quindi della cancellazione dei dati dell'utente precedente. Questo include se viene ricaricato da background in multitasking.

In caso di modifica dell'utente, è necessario assicurarsi che i dati dell'utente precedente siano deselezionati e che i dati memorizzati nella cache visualizzati nell'applicazione vengano rimossi. Si consiglia vivamente all'utente e all'azienda di eseguire un processo di revisione della sicurezza dopo aver aggiornato l'app per supportare la modalità dispositivo condiviso.

### <a name="detect-shared-device-mode"></a>Rileva modalità dispositivo condiviso

Il rilevamento della modalità dispositivo condiviso è importante per l'applicazione. Molte applicazioni richiedono una modifica nell'esperienza utente (UX) quando l'applicazione viene usata in un dispositivo condiviso. Ad esempio, è possibile che l'applicazione disponga di una funzionalità di "iscrizione", che non è appropriata per un prima riga Worker perché probabilmente ha già un account. Potrebbe anche essere necessario aggiungere ulteriore sicurezza alla gestione dei dati dell'applicazione se è in modalità dispositivo condiviso.

Usare l' `getDeviceInformationWithParameters:completionBlock:` API in `MSALPublicClientApplication` per determinare se un'app è in esecuzione in un dispositivo in modalità dispositivo condiviso.

I frammenti di codice seguenti mostrano esempi di uso dell' `getDeviceInformationWithParameters:completionBlock:` API.

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

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Ottenere l'utente che ha eseguito l'accesso e determinare se un utente è stato modificato nel dispositivo

Un'altra parte importante del supporto della modalità dispositivo condiviso è determinare lo stato dell'utente sul dispositivo e cancellare i dati dell'applicazione se un utente è stato modificato o se non è presente alcun utente sul dispositivo. L'utente è responsabile di garantire che i dati non vengano divulgati a un altro utente.

È possibile usare `getCurrentAccountWithParameters:completionBlock:` l'API per eseguire una query sull'account attualmente connesso sul dispositivo.

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

Quando un dispositivo è configurato come dispositivo condiviso, l'applicazione può chiamare l' `acquireTokenWithParameters:completionBlock:` API per accedere all'account. L'account sarà disponibile a livello globale per tutte le app idonee nel dispositivo dopo la firma del primo app nell'account.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Disconnettere un utente a livello globale

Il codice seguente rimuove l'account connesso e cancella i token memorizzati nella cache solo dall'app, ma anche dal dispositivo in modalità dispositivo condiviso. Non vengono tuttavia cancellati i *dati* dall'applicazione. È necessario cancellare i dati dall'applicazione, nonché cancellare tutti i dati memorizzati nella cache che l'applicazione potrebbe visualizzare all'utente.

#### <a name="clear-browser-state"></a>Cancella stato del browser

> [!NOTE]
> Il passaggio seguente è necessario solo durante l'anteprima pubblica.

In questa versione di anteprima pubblica, il [plug-in Microsoft Enterprise SSO per i dispositivi Apple](apple-sso-plugin.md) Cancella lo stato solo per le applicazioni. Non cancella lo stato nel browser Safari. Si consiglia di cancellare manualmente la sessione del browser per assicurarsi che non venga lasciata alcuna traccia dello stato utente. È possibile utilizzare la proprietà facoltativa `signoutFromBrowser` riportata di seguito per cancellare eventuali cookie. In questo modo il browser si avvierà brevemente sul dispositivo.

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

Per visualizzare la modalità dispositivo condiviso in azione, l'esempio di codice seguente in GitHub include un esempio di esecuzione di un'app prima riga worker in un dispositivo iOS in modalità dispositivo condiviso:

[Esempio di API Microsoft Graph Swift MSAL iOS](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
