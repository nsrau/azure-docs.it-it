---
title: App client pubbliche per account singolo e multiplo Azure
description: Panoramica delle app client pubbliche per account singolo e multiplo.
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
ms.openlocfilehash: f2ce993b8fbf2a1b04ea4ad9d992ba278dbc964e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701417"
---
# <a name="single-and-multiple-account-public-client-apps"></a>App client pubbliche con un account singolo e multiplo

Questo articolo ti aiuterà a comprendere i tipi usati nelle app client pubbliche per account singolo e multiplo, con particolare attenzione alle app client pubbliche per account singolo. 

Azure Active Directory Authentication Library (ADAL) modella il server.  La libreria di autenticazione Microsoft (MSAL) modella invece l'applicazione client.  La maggior parte delle app Android sono considerate client pubblici. Un client pubblico è un'app che non può mantenere in modo sicuro un segreto.  

MSAL è specializzata nella `PublicClientApplication` superficie dell'API per semplificare e chiarire l'esperienza di sviluppo per le app che consentono di usare un solo account alla volta. `PublicClientApplication`è sottoclassato da `SingleAccountPublicClientApplication` e `MultipleAccountPublicClientApplication`.  Nel diagramma seguente viene illustrata la relazione tra queste classi.

![Diagramma classi UML SingleAccountPublicClientApplication](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Applicazione client pubblica con account singolo

La `SingleAccountPublicClientApplication` classe consente di creare un'app basata su MSAL che consente l'accesso a un solo account alla volta. `SingleAccountPublicClientApplication` differisce da `PublicClientApplication` nei seguenti modi:

- MSAL tiene traccia dell'account attualmente connesso.
  - Se l'app usa un broker (impostazione predefinita durante la registrazione dell'app del portale di Azure) ed è installata in un dispositivo in cui è presente un broker, MSAL verificherà che l'account sia ancora disponibile nel dispositivo.
- `signIn`consente di accedere a un account in modo esplicito e separato dalla richiesta degli ambiti.
- `acquireTokenSilent`non richiede un parametro account.  Se si fornisce un account e l'account fornito non corrisponde all'account `MsalClientException` corrente registrato da MSAL, viene generato un errore.
- `acquireToken`non consente all'utente di cambiare account. Se l'utente tenta di passare a un account diverso, viene generata un'eccezione.
- `getCurrentAccount`restituisce un oggetto risultato che fornisce quanto segue:
  - Valore booleano che indica se l'account è stato modificato. Un account può essere modificato in seguito, ad esempio, la rimozione dal dispositivo.
  - Il conto precedente. Ciò è utile se è necessario eseguire qualsiasi pulizia dei dati locali quando l'account viene rimosso dal dispositivo o quando viene eseguito l'accesso a un nuovo account.
  - Oggetto currentAccount.
- `signOut`rimuove tutti i token associati al client dal dispositivo.  

Quando nel dispositivo è installato un broker di autenticazione Android, ad esempio Microsoft Authenticator o Intune Company Portal, e l'app è configurata per l'uso del broker, `signOut` l'account non viene rimosso dal dispositivo.

## <a name="single-account-scenario"></a>Scenario di account singolo

Lo pseudocodice seguente `SingleAccountPublicClientApplication`illustra l'utilizzo di .

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

## <a name="multiple-account-public-client-application"></a>Applicazione client pubblica con più account

La `MultipleAccountPublicClientApplication` classe viene utilizzata per creare app basate su MSAL che consentono l'accesso a più account contemporaneamente. Consente di ottenere, aggiungere e rimuovere account come segue:

### <a name="add-an-account"></a>Aggiungi un account

Utilizzare uno o più account `acquireToken` nell'applicazione chiamando una o più volte.  

### <a name="get-accounts"></a>Ottenere account

- Chiama `getAccount` per ottenere un account specifico.
- Chiama `getAccounts`per ottenere un elenco degli account attualmente noti all'app.

L'app non sarà in grado di enumerare tutti gli account della piattaforma di identità Microsoft nel dispositivo noto all'app broker. Può enumerare solo gli account che sono stati usati dall'app.  Gli account che sono stati rimossi dal dispositivo non verranno restituiti da queste funzioni.

### <a name="remove-an-account"></a>Rimuovere un account

Rimuovere un account `removeAccount` chiamando con un identificatore di account.

Se l'app è configurata per l'utilizzo di un broker e un broker è installato `removeAccount`nel dispositivo, l'account non verrà rimosso dal broker quando si chiama .  Vengono rimossi solo i token associati al client.

## <a name="multiple-account-scenario"></a>Scenario con più account

Lo pseudo codice seguente mostra come creare un'app con più account, elencare gli account nel dispositivo e acquisire token.

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
