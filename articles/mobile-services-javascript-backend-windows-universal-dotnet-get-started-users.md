<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/18/2014" ms.author="Glenn Gailey"></tags>

# Introduzione all'autenticazione in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dalla propria app Windows universale. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili][Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati][Limitazione delle autorizzazioni per la tabella agli utenti autenticati]
3.  [Aggiunta dell'autenticazione all'app][Aggiunta dell'autenticazione all'app]
4.  [Archiviazione dei token di autenticazione sul client][Archiviazione dei token di autenticazione sul client]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

> [WACOM.NOTE]In questa esercitazione viene illustrata la procedura di autenticazione degli utenti nelle app di Windows Store e Windows Phone Store 8.1. Per le app di Windows Phone 8.0 o Windows Phone Silverlight 8.1, consultare la sezione [Introduzione all'autenticazione in Servizi mobili][Introduzione all'autenticazione in Servizi mobili].

> In questa esercitazione viene illustrato il flusso di autenticazione gestito da Servizi mobili utilizzando vari provider di identità. Questo metodo è semplice da configurare e supporta più provider, Per utilizzare invece Live Connect con l'autenticazione gestita dal client e per offrire un ambiente Single Sign-On nella propria app per Windows Phone, vedere l'argomento [Single Sign-On per le app per Windows Phone tramite Live Connect][Single Sign-On per le app per Windows Phone tramite Live Connect]. L'uso dell'autenticazione gestita dal client consente all'app di accedere a dati utente aggiuntivi mantenuti dal provider di identità. È possibile ottenere gli stessi dati utente nel proprio servizio mobile chiamando la funzione **user.getIdentities()** negli script del server. Per ulteriori informazioni, vedere [questo post][questo post].

## <a name="register"></a> Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

<ol start="5">
<li><p>(Facoltativo) Completare la procedura descritta in <a href="/it-it/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft</a>.</p>

    <div class="dev-callout"><b>Nota</b>
    <p>Questo passaggio &egrave; facoltativo perch&eacute; si applica solo al provider di accesso con account Microsoft. Quando si registrano le informazioni del pacchetto dell'app di Windows Store con Servizi mobili, il client &egrave; in grado di riutilizzare le credenziali di accesso dell'account Microsoft per un ambiente Single Sign-On. In caso contrario, gli utenti che accedono tramite un account Microsoft dovranno specificare le credenziali di accesso ogni volta che viene chiamato il metodo di accesso. Completare questo passaggio se si prevede di utilizzare il provider di identit&agrave; per account Microsoft.</p>
    </div></li>
</ol>

## <a name="permissions"></a> Limitazione delle autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

<ol start="3">
<li><p>In Visual Studio fare clic con il pulsante destro del mouse sul progetto Windows Store per l'app TodoList, quindi selezionare <b>Imposta come progetto di avvio</b>.</p>

<li><p>Nel progetto condiviso, aprire il file di progetto App.xaml.cs, individuare la definizione di <a href="http://msdn.microsoft.com/it-it/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> e assicurarsi che sia configurata in modo tale da effettuare la connessione al servizio mobile in esecuzione su Azure.</p>

    <div class="dev-callout"><strong>Nota</strong><p>In caso di utilizzo di uno strumento di Visual Studio per connettere la propria app a un servizio mobile, lo strumento genera due set di definizioni <strong>MobileServiceClient</strong>, uno per ciascuna piattaforma client. Si consiglia di semplificare il codice generato unendo le definizioni <strong>MobileServiceClient</strong> <code data-inline="1">#if...#endif</code> con wrapping in un'unica definizione senza wrapping utilizzata da entrambe le versioni dell'app.</p></div></li>

<li><p>Premere F5 per eseguire l'app di Windows Store. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).</p>

    <p>L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella <em>TodoItem</em> richiede ora l'autenticazione.</p></li>
</ol>

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a> Aggiunta dell'autenticazione all'app

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)]

## <a name="tokens"></a>Archiviazione del token di autorizzazione sul client

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][Autorizzazione sul lato servizio degli utenti di Servizi mobili], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. Per ulteriori informazioni su come utilizzare Servizi mobili con .NET, vedere [Riferimento per i concetti e le procedure di .NET per Servizi mobili][Riferimento per i concetti e le procedure di .NET per Servizi mobili]

<!-- Anchors. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
  [Aggiunta dell'autenticazione all'app]: #add-authentication
  [Archiviazione dei token di autenticazione sul client]: #tokens
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started/
  [Introduzione all'autenticazione in Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-users
  [Single Sign-On per le app per Windows Phone tramite Live Connect]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
  [questo post]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft]: /it-it/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [MobileServiceClient]: http://msdn.microsoft.com/it-it/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [mobile-services-windows-universal-dotnet-authenticate-app]: ../includes/mobile-services-windows-universal-dotnet-authenticate-app.md
  [mobile-services-windows-store-dotnet-authenticate-app-with-token]: ../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md
  [Autorizzazione sul lato servizio degli utenti di Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
  [Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
