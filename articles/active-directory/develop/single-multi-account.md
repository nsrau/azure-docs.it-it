---
title: App client pubbliche con account singolo e multipli | Azure
description: Panoramica delle app client pubbliche con account singolo e multipli.
services: active-directory
documentationcenter: ''
author: shoatman
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae798c6108ec78b92b1ee6ac167b01c2f72c26d9
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679711"
---
# <a name="single-and-multiple-account-public-client-apps"></a>App client pubbliche con account singolo e multipli

Questo articolo consente di comprendere i tipi usati nelle app client pubbliche con account singolo e multipli, concentrandosi sulle app client pubbliche con account singolo. 

La libreria di autenticazione Azure Active Directory (ADAL) modella il server.  Microsoft Authentication Library (MSAL) modella invece l'applicazione client.  La maggior parte delle app Android sono considerate client pubblici. Un client pubblico è un'app che non è in grado di gestire in modo sicuro un segreto.  

MSAL specializza la superficie dell'API di `PublicClientApplication` per semplificare e chiarire l'esperienza di sviluppo per le app che consentono l'uso di un solo account per volta. `PublicClientApplication` è sottoclassata da `SingleAccountPublicClientApplication` e `MultipleAccountPublicClientApplication`.  Nel diagramma seguente viene illustrata la relazione tra queste classi.

![Diagramma classi UML SingleAccountPublicClientApplication](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Applicazione client pubblica con account singolo

La classe `SingleAccountPublicClientApplication` consente di creare un'app basata su MSAL che consente l'accesso a un solo account alla volta. `SingleAccountPublicClientApplication` differisce da `PublicClientApplication` nei modi seguenti:

- MSAL tiene traccia dell'account attualmente connesso.
  - Se l'app usa un broker (impostazione predefinita durante la registrazione dell'app portale di Azure) e viene installato in un dispositivo in cui è presente un broker, MSAL verificherà che l'account sia ancora disponibile sul dispositivo.
- `signIn` consente di accedere in modo esplicito a un account e separatamente dagli ambiti richiesti.
- `acquireTokenSilent` non richiede un parametro dell'account.  Se si specifica un account e l'account fornito non corrisponde all'account corrente rilevato da MSAL, viene generata un'eccezione `MsalClientException`.
- `acquireToken` non consente all'utente di cambiare account. Se l'utente tenta di passare a un account diverso, viene generata un'eccezione.
- `getCurrentAccount` restituisce un oggetto risultato che fornisce gli elementi seguenti:
  - Valore booleano che indica se l'account è stato modificato. Un account può essere modificato come risultato della rimozione dal dispositivo, ad esempio.
  - Account precedente. Questa operazione è utile se è necessario eseguire la pulizia dei dati locali quando l'account viene rimosso dal dispositivo o quando viene eseguito l'accesso a un nuovo account.
  - Delle.
- `signOut` rimuove tutti i token associati al client dal dispositivo.  

Quando nel dispositivo è installato un broker di autenticazione Android, ad esempio Microsoft Authenticator o Portale aziendale Intune, e l'app è configurata per l'uso del broker, `signOut` non rimuove l'account dal dispositivo.

## <a name="single-account-scenario"></a>Scenario con singolo account

Lo pseudo codice seguente illustra l'uso di `SingleAccountPublicClientApplication`.

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>Applicazione client pubblica per più account

La classe `MultipleAccountPublicClientApplication` viene usata per creare app basate su MSAL che consentono l'accesso di più account contemporaneamente. Consente di ottenere, aggiungere e rimuovere gli account come indicato di seguito:

### <a name="add-an-account"></a>Aggiungi un account

Usare uno o più account nell'applicazione chiamando `acquireToken` una o più volte.  

### <a name="get-accounts"></a>Ottenere gli account

- Chiamare `getAccount` per ottenere un account specifico.
- Chiamare `getAccounts`to ottenere un elenco di account attualmente noti all'app.

L'app non sarà in grado di enumerare tutti gli account della piattaforma Microsoft Identity sul dispositivo noto all'app Broker. Può solo enumerare gli account usati dall'app.  Gli account rimossi dal dispositivo non verranno restituiti da queste funzioni.

### <a name="remove-an-account"></a>Rimuovere un account

Rimuovere un account chiamando `removeAccount` con un identificatore di account.

Se l'app è configurata per l'uso di un broker e un broker è installato nel dispositivo, l'account non verrà rimosso dal broker quando si chiama `removeAccount`.  Solo i token associati al client vengono rimossi.

## <a name="multiple-account-scenario"></a>Scenario per più account

Lo pseudo codice seguente mostra come creare un'app per più account, elencare gli account nel dispositivo e acquisire i token.

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```
