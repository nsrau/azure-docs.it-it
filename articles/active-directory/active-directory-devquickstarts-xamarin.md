<properties
	pageTitle="Introduzione a Xamarin per Azure AD | Microsoft Azure"
	description="Come compilare un'applicazione Xamarin che si integra con Azure AD per l'accesso e chiama le API protette di Azure AD usando OAuth."
	services="active-directory"
	documentationCenter="xamarin"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="dastrock"/>


# Integrare Azure AD in un'app Xamarin

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Xamarin consente di scrivere applicazioni in C# che possono essere eseguite su più piattaforme diverse, inclusi dispositivi mobili e PC. Se si sta compilando un'app con Xamarin, Azure AD semplifica e facilita l'autenticazione degli utenti con gli account Active Directory. Consente inoltre all'applicazione di usare in modo sicuro qualsiasi API Web protetta da Azure AD, ad esempio le API di Office 365 o l'API di Azure.

Per le app Xamarin che devono accedere a risorse protette, Azure AD fornisce Active Directory Authentication Library (ADAL). La funzione di ADAL è di permettere all'app di ottenere facilmente i token di accesso. Per far capire quanto è semplice, verrà compilata un'app "Directory Searcher" che:

-	Viene eseguita su iOS, Android, Windows Desktop, Windows Phone e Windows Store.
- Usa una sola libreria di classi portabile (PCL, Portable Class Library) per autenticare gli utenti e ottenere i token per l'API Graph di Azure AD
-	Cerca in una directory gli utenti con un determinato UPN.

Per compilare l'applicazione funzionante completa, sarà necessario:

2. Configurare l'ambiente di sviluppo Xamarin.
2. Registrare l'applicazione con Azure AD.
3. Installare e configurare ADAL.
5. Usare ADAL per ottenere i token da Azure AD.

Per iniziare, [scaricare un progetto struttura](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) o [scaricare l'esempio completato](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Ognuno è una soluzione di Visual Studio 2013. Sarà necessario anche un tenant di Azure AD in cui poter creare gli utenti e registrare un'applicazione. Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## *0. Configurare l'ambiente di sviluppo Xamarin*
Esistono molti modi diversi per configurare Xamarin, a seconda delle specifiche piattaforme di destinazione. Poiché questa esercitazione include progetti per iOS, Android e Windows, si sceglierà di usare Visual Studio 2013 e l'[host di compilazione Xamarin.iOS](http://developer.xamarin.com/guides/ios/getting_started/installation/windows/), che richiederanno: un computer Windows per eseguire Visual Studio e le app di Windows; un computer OSX (per eseguire l'app iOS); una sottoscrizione di Xamarin Business (è sufficiente una [versione di valutazione gratuita](http://developer.xamarin.com/guides/cross-platform/getting_started/beginning_a_xamarin_trial/)); [Xamarin per Windows](https://xamarin.com/download), che include Xamarin.iOS, Xamarin.Android e Visual Studio Integration (consigliato per questo esempio); [Xamarin per OS X](https://xamarin.com/download), che include Xamarin.iOS (e l'host di compilazione Xamarin.iOS), Xamarin.Android, Xamarin.Mac e Xamarin Studio.

Si consiglia di iniziare con la [pagina di download di Xamarin](https://xamarin.com/download), installando Xamarin sia nel Mac che nel PC. Se non si dispone di entrambi i computer, è comunque possibile eseguire l'esempio, ma alcuni progetti dovranno essere omessi. Seguire le [guide di installazione dettagliate](http://developer.xamarin.com/guides/cross-platform/getting_started/installation/) per iOS e Android e, per saperne di più sulle possibili opzioni di sviluppo, vedere la guida sulla [compilazione di applicazioni per più piattaforme](http://developer.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/part_1_-_understanding_the_xamarin_mobile_platform/). Non è necessario configurare un dispositivo per lo sviluppo in questa fase né è necessaria una sottoscrizione di Apple Developer Program (a meno che, ovviamente, non si voglia eseguire l'app iOS su un dispositivo).

Dopo avere completato la configurazione necessaria, aprire la soluzione in Visual Studio per iniziare. Si troveranno sei progetti: cinque progetti specifici delle piattaforme e una libreria di classi portabile che verrà condivisa tra tutte le piattaforme, `DirectorySearcher.cs`.


## *1. Registrare l'applicazione Directory Searcher*
Per consentire all'applicazione di ottenere i token, sarà innanzitutto necessario registrarla nel tenant di Azure AD e concederle l'autorizzazione per accedere all'API Graph di Azure AD:

-	Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
-	Nel pannello di navigazione a sinistra fare clic su **Active Directory**.
-	Selezionare un tenant in cui registrare l'applicazione.
-	Fare clic sulla scheda **Applicazioni**, quindi fare clic su **Aggiungi** nel pannello in basso.
-	Seguire le istruzioni e creare una nuova **Applicazione client nativa**.
    -	Il **Nome** dell'applicazione deve essere una descrizione per gli utenti finali.
    -	L'**URI di reindirizzamento** è una combinazione dello schema e della stringa che Azure AD userà per restituire le risposte dei token. Immettere un valore, ad esempio `http://DirectorySearcher`.
-	Dopo avere completato la registrazione, AAD assegnerà all'app un identificatore client univoco. Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla scheda **Configura**.
- Sempre nella scheda **Configura** individuare la sezione "Autorizzazioni per altre applicazioni". Per l'applicazione "Azure Active Directory", aggiungere l'autorizzazione **Accedere alla directory dell'organizzazione** in **Autorizzazioni delegate**. In questo modo l'applicazione potrà cercare gli utenti nell'API Graph.

## *2. Installare e configurare ADAL*
Ora che si dispone di un'applicazione in Azure AD, è possibile installare ADAL e scrivere il codice relativo all'identità. Per consentire ad ADAL di comunicare con Azure AD, è necessario fornirgli alcune informazioni sulla registrazione dell'app. Iniziare aggiungendo ADAL a ogni progetto della soluzione con la Console di Gestione pacchetti.

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal.Windows -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal.WindowsPhone -IncludePrerelease
`

- Si noterà che a ogni progetto vengono aggiunti due riferimenti alla libreria: la parte PCL di ADAL e una parte specifica della piattaforma.

-	Nel progetto DirectorySearcherLib aprire `DirectorySearcher.cs`. Modificare i valori dei membri della classe in modo che corrispondano ai valori inseriti nel portale di Azure. Il codice farà riferimento a questi valori ogni volta che userà ADAL.
    -	`tenant` è il dominio del tenant di Azure AD, ad esempio, contoso.onmicrosoft.com.
    -	`clientId` è l'ID client dell'applicazione copiato dal portale.
    - `returnUri` è l'URI di reindirizzamento immesso nel portale, ad esempio `http://DirectorySearcher`.

## *3. Usare ADAL per ottenere i token da AAD*
*Quasi* tutta la logica di autenticazione dell'app si basa su `DirectorySearcher.SearchByAlias(...)`. Nei progetti specifici delle piattaforme è sufficiente passare un parametro contestuale alla libreria PCL di `DirectorySearcher`.

- Aprire prima `DirectorySearcher.cs` e aggiungere un nuovo parametro al metodo `SearchByAlias(...)`. `IPlatformParameters` è il parametro contestuale che incapsula gli oggetti specifici delle piattaforme necessari ad ADAL per eseguire l'autenticazione.

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-	Successivamente inizializzare `AuthenticationContext`, ovvero la classe primaria di ADAL, dove si passano ad ADAL le coordinate di cui ha bisogno per comunicare con Azure AD. Chiamare quindi `AcquireTokenAsync(...)`, che accetta l'oggetto `IPlatformParameters` e richiama il flusso di autenticazione necessario per restituire un token all'app.

```C#
...
AuthenticationResult authResult = null;

try
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
}
...
```
- `AcquireTokenAsync(...)` prima tenterà di restituire un token per la risorsa richiesta (in questo caso, l'API Graph) senza chiedere all'utente di immettere le credenziali (tramite memorizzazione nella cache o aggiornamento dei vecchi token). Solo se necessario, mostrerà all'utente la pagina di accesso di Azure AD prima di acquisire il token richiesto.


- È quindi possibile associare il token di accesso alla richiesta relativa all'API Graph nell'intestazione dell'autorizzazione:

```C#
...
request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

Non sono necessarie altre operazioni per la libreria PCL di `DirectorySearcher` e il codice relativo all'identità dell'app. Non resta che chiamare il metodo `SearchByAlias(...)` nelle visualizzazioni di ciascuna piattaforma e, se necessario, aggiungere il codice per la corretta gestione del ciclo di vita dell'interfaccia utente.

####Android:
- In `MainActivity.cs` aggiungere una chiamata a `SearchByAlias(...)` nel gestore di clic del pulsante:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- È inoltre necessario eseguire l'override del metodo del ciclo di vita `OnActivityResult` per inoltrare i reindirizzamenti dell'autenticazione al metodo appropriato. ADAL fornisce un apposito metodo helper in Android:

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####Desktop di Windows:
- In `MainWindow.xaml.cs` effettuare una chiamata a `SearchByAlias(...)` passando `WindowInteropHelper` nell'oggetto `PlatformParameters` del desktop:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####iOS:
- In `DirSearchClient_iOSViewController.cs`, l'oggetto `PlatformParameters` iOS accetta un riferimento al controller di visualizzazione:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####Windows Store
- In Windows Store aprire `MainPage.xaml.cs` e implementare il metodo `Search`, che usa un metodo helper in un progetto condiviso per aggiornare l'interfaccia utente in base alle esigenze.

```C#
await UnivDirectoryHelper.Search(
  sender, e,
  SearchResults,
  SearchTermText,
  StatusResult,
  new PlatformParameters(PromptBehavior.Auto, false));
```

####Windows Phone
- In Windows Phone aprire `MainPage.xaml.cs` e implementare il metodo `Search`, che usa lo stesso metodo helper in un progetto condiviso per aggiornare l'interfaccia utente.

```C#
await UnivDirectoryHelper.Search(
  sender, e,
  SearchResults,
  SearchTermText,
  StatusResult,
  new PlatformParameters());
```

Congratulazioni. È stata compilata un'app Xamarin funzionante in grado di autenticare gli utenti e di chiamare in modo sicuro le API Web usando OAuth 2.0 in cinque diverse piattaforme. Se non si è ancora popolato il tenant con alcuni utenti, ora è possibile farlo. Eseguire l'app DirectorySearcher e accedere con uno di tali utenti. Cercare altri utenti in base al relativo UPN.

ADAL consente di incorporare facilmente nell'applicazione funzionalità comuni relative alle identità. Esegue automaticamente le attività più complesse: gestione della cache, supporto del protocollo OAuth, presentazione all'utente di un'interfaccia utente di accesso, aggiornamento dei token scaduti e altro. Tutto ciò che occorre conoscere è una sola chiamata all'API, `authContext.AcquireToken*(…)`.

Come riferimento, viene fornito l'esempio completato (senza i valori di configurazione) [qui](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Ora è possibile passare ad altri scenari relativi alle identità. È possibile:

[Proteggere un'API Web .NET con Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

Per altre risorse, vedere: [AzureADSamples su GitHub >>](https://github.com/AzureAdSamples), [CloudIdentity.com >>](https://cloudidentity.com), la documentazione su Azure AD in [Azure.com >>](http://azure.microsoft.com/documentation/services/active-directory/)
 

<!---HONumber=July15_HO3-->