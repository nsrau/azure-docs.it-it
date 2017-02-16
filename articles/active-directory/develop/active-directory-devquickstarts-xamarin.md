---
title: Introduzione a Xamarin per Azure AD | Microsoft Docs
description: Come compilare un&quot;applicazione Xamarin che si integra con Azure AD per l&quot;accesso e chiama le API protette di Azure AD usando OAuth.
services: active-directory
documentationcenter: xamarin
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: 4852939cb577d0b41a606f0d94897edfb367c4d6


---
# <a name="integrate-azure-ad-into-a-xamarin-app"></a>Integrare Azure AD in un'app Xamarin
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Xamarin consente di scrivere app per dispositivi mobili in C# che possono essere eseguite in iOS, Android e Windows (dispositivi mobili e PC). Se si sta compilando un'app con Xamarin, Azure AD semplifica e facilita l'autenticazione degli utenti con gli account Active Directory. Consente inoltre all'applicazione di usare in modo sicuro qualsiasi API Web protetta da Azure AD, ad esempio le API di Office 365 o l'API di Azure.

Per le app Xamarin che devono accedere a risorse protette, Azure AD fornisce Active Directory Authentication Library (ADAL). La funzione di ADAL è di permettere all'app di ottenere facilmente i token di accesso. Per far capire quanto è semplice, verrà compilata un'app "Directory Searcher" che:

* Viene eseguita su iOS, Android, Windows Desktop, Windows Phone e Windows Store.
* Usa una sola libreria di classi portabile (PCL, Portable Class Library) per autenticare gli utenti e ottenere i token per l'API Graph di Azure AD
* Cerca in una directory gli utenti con un determinato UPN.

Per compilare l'applicazione funzionante completa, sarà necessario:

1. Configurare l'ambiente di sviluppo Xamarin.
2. Registrare l'applicazione con Azure AD.
3. Installare e configurare ADAL.
4. Usare ADAL per ottenere i token da Azure AD.

Per iniziare, [scaricare un progetto struttura](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) o [scaricare l'esempio completato](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Ognuno è una soluzione di Visual Studio 2013. Sarà necessario anche un tenant di Azure AD in cui poter creare gli utenti e registrare un'applicazione. Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## <a name="0-set-up-your-xamarin-development-environment"></a>0. Configurare l'ambiente di sviluppo Xamarin
Poiché questa esercitazione include progetti per iOS, Android e Windows, sono necessari Visual Studio e Xamarin. Per creare l'ambiente necessario, seguire le istruzioni indicate in [Configurazione e installazione per Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) in MSDN. Le istruzioni includono informazioni relative a Xamarin che è possibile leggere in attesa del completamento dell'esecuzione dei programmi di esecuzione. 

Dopo avere completato la configurazione necessaria, aprire la soluzione in Visual Studio per iniziare. Si troveranno sei progetti: cinque progetti specifici delle piattaforme e una libreria di classi portabile che verrà condivisa tra tutte le piattaforme, `DirectorySearcher.cs`.

## <a name="1-register-the-directory-searcher-application"></a>1. Registrare l'applicazione Directory Searcher
Per consentire all'applicazione di ottenere i token, sarà innanzitutto necessario registrarla nel tenant di Azure AD e concederle l'autorizzazione per accedere all'API Graph di Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra in alto fare clic sull'account e nell'elenco **Directory** scegliere il tenant di Active Directory in cui si vuole registrare l'applicazione.
3. Fare clic su **Altri servizi** nella barra di spostamento a sinistra e scegliere **Azure Active Directory**.
4. Fare clic su **App registrations (Registrazioni app)** e scegliere **Aggiungi**.
5. Seguire le istruzioni e creare una nuova **Applicazione client nativa**.
  * Il **Nome** dell'applicazione deve essere una descrizione per gli utenti finali.
  * L' **URI di reindirizzamento** è una combinazione dello schema e della stringa che Azure AD userà per restituire le risposte dei token. Immettere un valore, ad esempio `http://DirectorySearcher`.
6. Dopo avere completato la registrazione, AAD assegnerà all'app un ID app univoco. Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla scheda dell'applicazione.
7. Nella pagina **Impostazioni** scegliere **Autorizzazioni necessarie** e quindi scegliere **Aggiungi**. Selezionare **Microsoft Graph** come API e aggiungere l'autorizzazione **Lettura dati directory** in **Autorizzazioni delegate**.  In questo modo l'applicazione potrà cercare gli utenti nell'API Graph.

## <a name="2-install--configure-adal"></a>2. Installare e configurare ADAL
Ora che si dispone di un'applicazione in Azure AD, è possibile installare ADAL e scrivere il codice relativo all'identità. Affinché la libreria ADAL possa comunicare con Azure AD, è necessario fornire alcune informazioni relative alla registrazione dell'app.

* Iniziare aggiungendo ADAL a ogni progetto della soluzione usando la Console di Gestione pacchetti.

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

* Si noterà che a ogni progetto vengono aggiunti due riferimenti alla libreria: la parte PCL di ADAL e una parte specifica della piattaforma.
* Nel progetto DirectorySearcherLib aprire `DirectorySearcher.cs`. Modificare i valori dei membri della classe in modo che corrispondano ai valori inseriti nel portale di Azure. Il codice farà riferimento a questi valori ogni volta che userà ADAL.
  
  * `tenant` è il dominio del tenant di Azure AD, ad esempio, contoso.onmicrosoft.com.
  * `clientId` è l'ID client dell'applicazione copiato dal portale.
  * `returnUri` è l'URI di reindirizzamento immesso nel portale, ad esempio `http://DirectorySearcher`.

## <a name="3-use-adal-to-get-tokens-from-aad"></a>3. Usare ADAL per ottenere i token da AAD
*Quasi* tutta la logica di autenticazione dell'app si basa su `DirectorySearcher.SearchByAlias(...)`. Nei progetti specifici delle piattaforme è sufficiente passare un parametro contestuale alla libreria PCL di `DirectorySearcher` .

* Innanzitutto, aprire `DirectorySearcher.cs` e aggiungere un nuovo parametro per il metodo `SearchByAlias(...)`. `IPlatformParameters` è il parametro contestuale che incapsula gli oggetti specifici delle piattaforme necessari ad ADAL per eseguire l'autenticazione.

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

* Successivamente inizializzare `AuthenticationContext`, ovvero la classe primaria di ADAL, dove si passano ad ADAL le coordinate di cui ha bisogno per comunicare con Azure AD. Chiamare quindi `AcquireTokenAsync(...)`, che accetta l'oggetto `IPlatformParameters` e richiama il flusso di autenticazione necessario per restituire un token all'app.

```C#
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
* `AcquireTokenAsync(...)` prima tenterà di restituire un token per la risorsa richiesta (in questo caso, l'API Graph) senza chiedere all'utente di immettere le credenziali (tramite memorizzazione nella cache o aggiornamento dei vecchi token). Solo se necessario, mostrerà all'utente la pagina di accesso di Azure AD prima di acquisire il token richiesto.
* È quindi possibile associare il token di accesso alla richiesta relativa all'API Graph nell'intestazione dell'autorizzazione:

```C#
...
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

Non sono necessarie altre operazioni per la libreria PCL di `DirectorySearcher` e il codice relativo all'identità dell'app.  Non resta che chiamare il metodo `SearchByAlias(...)` nelle visualizzazioni di ciascuna piattaforma e, se necessario, aggiungere il codice per la corretta gestione del ciclo di vita dell'interfaccia utente.

#### <a name="android"></a>Android:
* In `MainActivity.cs` aggiungere una chiamata a `SearchByAlias(...)` nel gestore di clic del pulsante:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
* È inoltre necessario eseguire l'override del metodo del ciclo di vita `OnActivityResult` per inoltrare i reindirizzamenti dell'autenticazione al metodo appropriato.  ADAL fornisce un apposito metodo helper in Android:

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

#### <a name="windows-desktop"></a>Desktop di Windows:
* In `MainWindow.xaml.cs` effettuare una chiamata a `SearchByAlias(...)` passando `WindowInteropHelper` nell'oggetto `PlatformParameters` del desktop:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS:
* In `DirSearchClient_iOSViewController.cs`, l'oggetto `PlatformParameters` iOS accetta un riferimento al controller di visualizzazione:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="windows-universal"></a>Universale di Windows:
* Nella piattaforma universale di Windows aprire `MainPage.xaml.cs` e implementare il metodo `Search`, che usa un metodo helper in un progetto condiviso per aggiornare l'interfaccia utente in base alle esigenze.

```C#
...
    List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

Congratulazioni. È stata compilata un'app Xamarin funzionante in grado di autenticare gli utenti e di chiamare in modo sicuro le API Web usando OAuth 2.0 in cinque diverse piattaforme. Se non si è ancora popolato il tenant con alcuni utenti, ora è possibile farlo. Eseguire l'app DirectorySearcher e accedere con uno di tali utenti. Cercare altri utenti in base al relativo UPN.

ADAL consente di incorporare facilmente nell'app funzionalità comuni relative alle identità. Esegue automaticamente le attività più complesse: gestione della cache, supporto del protocollo OAuth, presentazione all'utente di un'interfaccia utente di accesso, aggiornamento dei token scaduti e altro. Tutto ciò che occorre conoscere è una sola chiamata all'API, `authContext.AcquireToken*(…)`.

Come riferimento, viene fornito l'esempio completato (senza i valori di configurazione) [qui](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Ora è possibile passare ad altri scenari relativi alle identità. È possibile:

[Proteggere un'API Web .NET con Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]




<!--HONumber=Jan17_HO3-->


