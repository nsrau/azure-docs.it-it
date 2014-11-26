<properties linkid="develop-mobile-tutorials-get-started-with-users-js" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store JavaScript app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Introduzione all'autenticazione in Servizi mobili

> [AZURE.SELECTOR-LIST (Platform | Backend )]
- [(iOS | .NET)](/it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/)
- [(iOS | JavaScript)](/it-it/documentation/articles/mobile-services-ios-get-started-users/)
- [(Windows C# | .NET)](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/)
- [(Windows C# | Javascript)](/it-it/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-users/)
- [(Windows JavaScript | .NET)](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/)
- [(Windows JavaScript | Javascript)](/it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-users/)
- [(Windows Phone | .NET)](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/)
- [(Windows Phone | Javascript)](/it-it/documentation/articles/mobile-services-windows-phone-get-started-users/)
- [(Android | .NET)](/it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/)
- [(Android | Javascript)](/it-it/documentation/articles/mobile-services-android-get-started-users/)
- [(Xamarin iOS | .NET)](/it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/)
- [(Xamarin iOS | Javascript)](/it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/)
- [(HTML | Javascript)](/it-it/documentation/articles/mobile-services-html-get-started-users/)
- [(Xamarin Android | .NET)](/it-it/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/)
- [(Xamarin Android | Javascript)](/it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started-users/)
- [(Appcelerator | Javascript)](/it-it/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users/)

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]
3.  [Aggiunta dell'autenticazione all'app]
4.  [Archiviazione dei token di autenticazione sul client]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili].

> [WACOM.NOTE]Questa esercitazione illustra il flusso di autenticazione gestito da Servizi mobili tramite diversi provider di identità. Questo metodo è semplice da configurare e supporta più provider, Per usare invece Live Connect con l'autenticazione gestita da client e offrire un ambiente di accesso Single Sign-On nella propria app per Windows Phone, vedere l'argomento [Single Sign-On per app di Windows Store tramite Live Connect]. Usando l'autenticazione gestita da client, l'app accede a dati utente aggiuntivi gestiti dal provider di identità. È possibile ottenere gli stessi dati utente nel servizio mobile chiamando la funzione **user.getIdentities()** negli script del server. Per altre informazioni, vedere [questo post](http://go.microsoft.com/fwlink/p/?LinkId=506605).

## <a name="register"></a> Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

1.  (Facoltativo) Completare la procedura descritta in <a href="/it-it/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft</a>.

    <div class="dev-callout">

    <b>Nota</b>
    <p>Questo passaggio è facoltativo perché si applica solo al provider di accesso con account Microsoft. Quando si registrano le informazioni del pacchetto dell'app di Windows Store con Servizi mobili, il client è in grado di riutilizzare le credenziali di accesso dell'account Microsoft per un ambiente Single Sign-On. In caso contrario, gli utenti che accedono tramite un account Microsoft dovranno specificare le credenziali di accesso ogni volta che viene chiamato il metodo di accesso. Completare questo passaggio se si prevede di utilizzare il provider di identità per account Microsoft.

    </div>

Il servizio mobile e l'app sono ora configurati per funzionare con il provider di autenticazione scelto.

## <a name="permissions"></a> Limitazione delle autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

1.  In Visual Studio 2012 Express per Windows 8 aprire il progetto creato dopo aver completato l'esercitazione <a href="/it-it/develop/mobile/tutorials/get-started/">Introduzione a Servizi mobili</a>.

2.  Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).

    L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a> Aggiunta dell'autenticazione all'app

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)] 

## <a name="tokens"></a>Archiviazione dei token di autenticazione sul client

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token](../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][Autorizzazione sul lato servizio degli utenti di Servizi mobili], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili.



  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
  [Aggiunta dell'autenticazione all'app]: #add-authentication
  [Archiviazione dei token di autenticazione sul client]: #tokens
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-get-started/
  [Single Sign-On per app di Windows Store tramite Live Connect]: /it-it/documentation/articles/mobile-services-windows-store-javascript-single-sign-on
  [questo post]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [Autorizzazione sul lato servizio degli utenti di Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts
