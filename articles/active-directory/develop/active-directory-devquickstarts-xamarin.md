---
title: Introduzione a Xamarin per Azure AD | Microsoft Docs
description: Compilare un'applicazione Xamarin che si integra con Azure AD per l'accesso e chiama le API protette da Azure AD usando OAuth.
services: active-directory
documentationcenter: xamarin
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 11/30/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 94a7d35115420d455fe94e1173abf76622172f6f
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="azure-ad-xamarin-getting-started"></a>Introduzione a Xamarin per Azure AD
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Xamarin consente di scrivere app per dispositivi mobili in C# che possono essere eseguite in iOS, Android e Windows, in dispositivi mobili e PC. Per la compilazione di un'app con Xamarin, Azure Active Directory (Azure AD) semplifica l'autenticazione degli utenti con i relativi account Azure AD. L'app può utilizzare in modo sicuro qualsiasi API Web protetta da Azure AD, come le API di Office 365 o l'API di Azure.

Per le app Xamarin che devono accedere a risorse protette, in Azure AD è disponibile Active Directory Authentication Library (ADAL). L'unica funzione di ADAL è semplificare l'acquisizione dei token di accesso da parte delle app. Per far capire quanto sia semplice, questo articolo illustra come compilare un'app DirectorySearcher che:

* Viene eseguita in iOS, Android, Windows Desktop, Windows Phone e Windows Store.
* Usa una sola libreria di classi portabile per autenticare gli utenti e ottenere i token per l'API Graph di Azure AD.
* Cerca in una directory gli utenti con un determinato UPN.

## <a name="before-you-get-started"></a>Prima di iniziare
* Scaricare il [progetto struttura](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) oppure l'[esempio completato](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Ognuno è una soluzione di Visual Studio 2013.
* È necessario anche un tenant di Azure AD in cui creare gli utenti e registrare l'app. Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

Quando si è pronti, attenersi alle procedure descritte nelle quattro sezioni seguenti.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>Passaggio 1: Configurare l'ambiente di sviluppo Xamarin
Dal momento che questa esercitazione include progetti per iOS, Android e Windows, sono necessari sia Visual Studio che Xamarin. Per creare l'ambiente necessario, seguire la procedura riportata in [Configurazione e installazione di Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) in MSDN. Le istruzioni includono informazioni relative a Xamarin che è possibile leggere in attesa del completamento delle installazioni.

Al termine della configurazione, aprire la soluzione in Visual Studio. Sono presenti sei progetti: cinque progetti specifici delle piattaforme e una libreria di classi portabile, DirectorySearcher.cs, che verrà condivisa tra tutte le piattaforme.

## <a name="step-2-register-the-directorysearcher-app"></a>Passaggio 2: Registrare l'app DirectorySearcher
Per consentire all'app di ottenere i token, sarà prima necessario registrarla nel tenant di Azure AD e concederle l'autorizzazione per accedere all'API Graph di Azure AD. Ecco come:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra superiore fare clic sull'account. Nell'elenco **Directory** selezionare quindi il tenant di Active Directory in cui si vuole registrare l'app.
3. Fare clic su **Altri servizi** nel riquadro a sinistra e scegliere **Azure Active Directory**.
4. Fare clic su **Registrazioni per l'app** e scegliere **Aggiungi**.
5. Seguire le istruzioni e creare una nuova **Applicazione client nativa**.
  * Il **Nome** descrive l'app agli utenti.
  * L'**URI di reindirizzamento** è una combinazione dello schema e della stringa che Azure AD usa per restituire le risposte dei token. Immettere un valore, ad esempio http://DirectorySearcher.
6. Dopo aver completato la registrazione, Azure AD assegna all'app un ID applicazione univoco. Copiare il valore dalla scheda **Applicazione**, perché sarà necessario in un secondo momento.
7. Nella pagina **Impostazioni** selezionare **Autorizzazioni necessarie** e selezionare **Aggiungi**.
8. Selezionare l'API **Microsoft Graph**. In **Autorizzazioni delegate** aggiungere l'autorizzazione **Lettura dati directory**.  
Ciò consente all'app di eseguire query nell'API Graph per gli utenti.

## <a name="step-3-install-and-configure-adal"></a>Passaggio 3: Installare e configurare ADAL
Ora che è disponibile un'app in Azure AD, è possibile installare ADAL e scrivere il codice relativo all'identità. Per abilitare ADAL alla comunicazione con Azure AD, fornire alcune informazioni sulla registrazione dell'app.

1. Aggiungere ADAL al progetto DirectorySearcher usando la console di Gestione pacchetti.

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
    `

    Si noti che a ogni progetto vengono aggiunti due riferimenti alla libreria, ovvero la parte relativa alla libreria di classi portabile di ADAL e una parte specifica della piattaforma.
2. Nel progetto DirectorySearcherLib aprire DirectorySearcher.cs.
3. Sostituire i valori dei membri della classe con i valori immessi nel portale di Azure. Il codice fa riferimento a questi valori ogni volta che usa ADAL.

  * Il *tenant* è il dominio del tenant di Azure AD, ad esempio contoso.onmicrosoft.com.
  * Il *clientId* è l'ID client dell'app che è stato copiato dal portale.
  * Il *returnUri* è l'URI di reindirizzamento immesso nel portale, ad esempio http://DirectorySearcher.

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Passaggio 4: Usare ADAL per ottenere token da Azure AD
Quasi tutta la logica di autenticazione dell'app si basa su `DirectorySearcher.SearchByAlias(...)`. Nei progetti specifici delle piattaforme è sufficiente passare un parametro contestuale alla libreria di classi portabile di `DirectorySearcher`.

1. Aprire DirectorySearcher.cs e quindi aggiungere un nuovo parametro per il metodo `SearchByAlias(...)`. `IPlatformParameters` è il parametro contestuale che incapsula gli oggetti specifici delle piattaforme necessari ad ADAL per eseguire l'autenticazione.

    ```csharp
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. Inizializzare `AuthenticationContext`, ovvero la classe primaria di ADAL.  
Questa azione permette di passare ad ADAL le coordinate necessarie per comunicare con Azure AD.
3. Chiamare `AcquireTokenAsync(...)`, che accetta l'oggetto `IPlatformParameters` e richiama il flusso di autenticazione necessario per restituire un token all'app.

    ```csharp
    ...
        AuthenticationResult authResult = null;
        try
        {
            AuthenticationContext authContext = new AuthenticationContext(authority);
            authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
        }
        catch (Exception ee)
        {
            results.Add(new User { error = ee.Message });
            return results;
        }
    ...
    ```

    `AcquireTokenAsync(...)` tenta prima di restituire un token per la risorsa necessaria, in questo caso, l'API Graph, senza chiedere all'utente di immettere le credenziali. A questo scopo vengono usati i token memorizzati nella cache o vengono aggiornati i token precedenti. Se necessario, viene visualizzata la pagina di accesso di Azure AD prima di acquisire il token richiesto.
4. Associare il token di accesso alla richiesta dell'API Graph nell'intestazione **Authorization**:

    ```csharp
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

Non sono necessarie altre operazioni per la libreria di classi portabile di `DirectorySearcher` e il codice relativo all'identità dell'app. Non resta che chiamare il metodo `SearchByAlias(...)` nelle visualizzazioni di ciascuna piattaforma e, se necessario, aggiungere il codice per la corretta gestione del ciclo di vita dell'interfaccia utente.

### <a name="android"></a>Android
1. In MainActivity.cs aggiungere una chiamata a `SearchByAlias(...)` nel gestore di clic del pulsante:

    ```csharp
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. Eseguire l'override del metodo del ciclo di vita `OnActivityResult` per inoltrare i reindirizzamenti dell'autenticazione al metodo appropriato. ADAL fornisce un apposito metodo helper in Android:

    ```csharp
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Desktop di Windows
In MainWindow.xaml.cs effettuare una chiamata a `SearchByAlias(...)` passando `WindowInteropHelper` nell'oggetto `PlatformParameters` del desktop:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
In DirSearchClient_iOSViewController.cs l'oggetto iOS `PlatformParameters` accetta un riferimento al controller di visualizzazione:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Windows Universal
In Windows universale, aprire MainPage.xaml.cs e quindi implementare il metodo `Search`. Quest'ultimo si serve di un metodo helper in un progetto condiviso per aggiornare l'interfaccia utente in base alle esigenze.

```csharp
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

## <a name="whats-next"></a>Passaggi successivi
È stata compilata un'app Xamarin funzionante che può autenticare gli utenti e chiamare in modo sicuro le API Web usando OAuth 2.0 in cinque diverse piattaforme.

Se non è stato ancora popolato il tenant con degli utenti, ora è possibile farlo.

1. Eseguire l'app DirectorySearcher e accedere usando le credenziali di uno di questi utenti.
2. Cercare altri utenti in base al relativo UPN.

ADAL consente di incorporare facilmente nell'app funzionalità comuni relative alle identità. Esegue automaticamente le attività più complesse, ovvero gestione della cache, supporto del protocollo OAuth, visualizzazione all'utente di un'interfaccia utente di accesso e aggiornamento dei token scaduti. È necessario conoscere una sola chiamata API, `authContext.AcquireToken*(…)`.

Per riferimento, scaricare l'[esempio completato](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip), che non contiene i valori di configurazione immessi durante la procedura sopra indicata.

Ora è possibile passare ad altri scenari relativi alle identità. Provare ad esempio [Proteggere un'API Web .NET con Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
