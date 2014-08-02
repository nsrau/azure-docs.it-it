<properties  linkid="develop-mobile-tutorials-get-started-with-users-ios" urlDisplayName="Get Started with Authentication (iOS)" pageTitle="Get started with authentication (iOS) | Mobile Dev Center" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services iOS" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

# Introduzione all'autenticazione in Servizi mobili
<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users" title="Windows Store C#">Windows Store C#</a><a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/en-us/documentation/articles/mobile-services-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a><a href="/en-us/documentation/articles/mobile-services-ios-get-started-users" title="iOS" class="current">iOS</a><a href="/en-us/documentation/articles/mobile-services-android-get-started-users" title="Android">Android</a><a href="/en-us/documentation/articles/mobile-services-html-get-started-users" title="HTML">HTML</a><a href="/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/" title=".NET backend">.NET backend</a> | <a href="/en-us/documentation/articles/mobile-services-ios-get-started-users/"  title="JavaScript backend" class="current">JavaScript backend</a></div>

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dall'app per iOS. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]
3.  [Aggiunta dell'autenticazione all'app]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione
[Introduzione a Servizi mobili].

Per completare questa esercitazione, è necessario disporre di XCode 4.5 e iOS 5.0 o versioni successive.

## <a name="register"></a>Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a> Limitazione delle autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.   In Xcode aprire il progetto creato dopo avere completato l'esercitazione [Introduzione a Servizi mobili](/en-us/documentation/articles/mobile-services-ios-get-started).
    

2.   Premere il pulsante **Run** per compilare il progetto e avviare l'app nell'emulatore iPhone e verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.
    
    
     A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.
## <a name="add-authentication"></a>Aggiunta dell'autenticazione all'app

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

## <a name="next-steps"></a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][Autorizzazione di utenti con script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. 

 


<!-- Anchors. -->
[Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
[Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiunta dell'autenticazione all'app]: #add-authentication
[Passaggi successivi]:#next-steps

<!--Images. -->

[4]:./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]:./media/mobile-services-ios-get-started-users/mobile-service-uri.png
[13]:./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]:./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]:./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png
<!-- URLs. -->

[Invio di una pagina dell'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazionipersonali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single Sign-On per app di Windows Store tramite Live Connect]: /en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Introduzione a Servizi mobili]: /en-us/develop/mobile/tutorials/get-started-ios
[Introduzione ai dati]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
[Introduzione all'autenticazione]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
[Introduzione alle notifiche push]: /en-us/develop/mobile/tutorials/get-started-with-push-ios
[Autorizzazione di utenti con script]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Portale di gestione di Azure]: https://manage.windowsazure.com/
    
    
