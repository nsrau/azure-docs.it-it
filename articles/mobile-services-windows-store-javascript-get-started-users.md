<properties urlDisplayName="Get Started with Authentication" pageTitle="Introduzione all'autenticazione (JavaScript) | Mobile Dev Center" metaKeywords="" description="Informazioni su come usare Servizi mobili per autenticare gli utenti dell'app di JavaScript Windows Store tramite numerosi provider di identità, tra cui Microsoft, Facebook, Twitter e Google." services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Aggiunta dell'autenticazione nell'app di Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-users-legacy](../includes/mobile-services-selector-get-started-users-legacy.md)]

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dalla propria app.  Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.  

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1. [Registrare l'app per l'autenticazione e configurare Servizi mobili]
2. [Limitare le autorizzazioni per la tabella agli utenti autenticati]
3. [Aggiungere l'autenticazione all'app]
4. [Archiviazione dei token di autenticazione sul client]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili]. 

>[WACOM.NOTE]In questa esercitazione viene illustrato il flusso di autenticazione gestito da Servizi mobili utilizzando vari provider di identità. Questo metodo è semplice da configurare e supporta più provider, Per usare in alternativa Live Connect con l'autenticazione gestita dal client e per offrire un ambiente Single Sign-On nella propria app per Windows Phone, vedere l'argomento [Autenticazione dell'app di Windows Store con l'accesso Single Sign-On di Live Connect]. L'uso dell'autenticazione gestita dal client consente all'app di accedere a dati utente aggiuntivi mantenuti dal provider di identità. È possibile ottenere gli stessi dati utente nel proprio servizio mobile chiamando la funzione **user.getIdentities()** negli script del server. Per altre informazioni, vedere [questo post](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a> Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>(Facoltativo) Completare i passaggi in <a href="/it-it/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft</a>.</p>

    <div class="dev-callout"><b>Nota</b>
	<p>Questo passaggio è facoltativo perché si applica solo al provider di accesso con account Microsoft. Quando si registrano le informazioni del pacchetto dell'app di Windows Store con Servizi mobili, il client è in grado di riutilizzare le credenziali di accesso dell'account Microsoft per un ambiente Single Sign-On. In caso contrario, gli utenti che accedono tramite un account Microsoft dovranno specificare le credenziali di accesso ogni volta che viene chiamato il metodo di accesso. Completare questo passaggio se si prevede di utilizzare il provider di identità per account Microsoft.</p>
    </div>
</li>
</ol>
Il servizio mobile e l'app sono ora configurati per funzionare con il provider di autenticazione scelto.

##<a name="permissions"></a> Limitazione delle autorizzazioni agli utenti autenticati

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>In Visual Studio 2012 Express per Windows 8, aprire il progetto creato dopo avere completato l'esercitazione <a href="/it-it/develop/mobile/tutorials/get-started/">Introduzione a Servizi mobili</a>.</p></li> 
<li><p>Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).</p>
   
   	<p>L'eccezione non gestita viene generata perché l'app prova ad accedere a Servizi mobili come utente non autenticato, mentre la tabella <em>TodoItem</em> richiede ora l'autenticazione.</p></li>
</ol>

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a> Aggiungere l'autenticazione all'app

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)] 

##<a name="tokens"></a>Archiviazione dei token di autorizzazione sul client

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token](../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione lato servizio degli utenti di Servizi mobili][Authorize users with scripts], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà usato per filtrare i dati restituiti da Servizi mobili. 


<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare Servizi mobili]: #register
[Limitare le autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Archiviazione dei token di autenticazione sul client]: #tokens
[Passaggi successivi]:#next-steps


<!-- URLs. -->
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single Sign-On per app di Windows Store tramite Live Connect]: /it-it/documentation/articles/mobile-services-windows-store-javascript-single-sign-on
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-push/
[Autorizzare gli utenti con gli script]: /it-it/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft]: /it-it/develop/mobile/how-to-guides/register-windows-store-app-package

<!--HONumber=35.2-->
