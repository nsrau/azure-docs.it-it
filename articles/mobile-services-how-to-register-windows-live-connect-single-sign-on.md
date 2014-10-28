<properties pageTitle="Register for single sign-on - Azure Mobile Services" metaKeywords="" description="Learn how to register for single sign-on authentication in your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store apps to use Windows Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Registrazione delle app di Windows Store per l'utilizzo dell'accesso Single Sign-On di Windows Live Connect

In questo argomento viene illustrato come registrare un'app con Windows Store in modo che possa utilizzare Live Connect per l'accesso Single Sign-On come provider di identità per Servizi mobili di Azure. In questo passaggio è inoltre richiesto l'utilizzo delle notifiche push.

<div class="dev-callout"><b>Nota</b>
<p>Non &egrave; necessario registrare l'app con Windows Store per utilizzare l'account Microsoft per l'autenticazione prima di pubblicare l'app. Quando l'app di Windows Store non richiede l'accesso Single Sign-On o le notifiche push, &egrave; sufficiente registrare l'app con Live Connect per utilizzare un accesso con account Microsoft.  Per ulteriori informazioni, vedere <a href="/it-it/develop/mobile/how-to-guides/register-for-microsoft-authentication">Registrazione delle app di Windows Store per l'utilizzo delle credenziali di accesso di un account Microsoft</a>.</p>
</div>

1.  Se l'app non è ancora stata registrata, passare alla pagina relativa all'[invio di app][] nel Centro per sviluppatori di app di Windows Store, accedere con il proprio account Microsoft e quindi fare clic su **App name**.

    ![][]

2.  Immettere un nome per l'app in **Nome app**, fare clic su **Riserva nome applicazione** e quindi su **Salva**.

    ![][1]

    Verrà creata una nuova registrazione a Windows Store per l'app.

3.  In Visual Studio 2012 Express per Windows 8 aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili][].

4.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, scegliere **Store** e quindi fare clic su **Associa applicazione a Store**.

    ![][2]

    Verrà visualizzata la procedura guidata **Associa l'applicazione con Windows Store**.

5.  Nella procedura guidata fare clic su **Accedi** e quindi accedere con il proprio account Microsoft.

6.  Selezionare l'app registrata nel passaggio 2, fare clic su **Avanti** e quindi su **Associa**.

    ![][3]

    Le necessarie informazioni di registrazione a Windows Store verranno aggiunte al manifesto dell'applicazione.

7.  Passare alla pagina [Applicazioni personali][] nel Centro sviluppatori Live Connect e fare clic sull'app nell'elenco **Applicazioni personali**.

    ![][4]

8.  Fare clic su **Modifica impostazioni**, quindi su **Impostazioni API** e prendere nota del valore di **Segreto client**.

    ![][5]

    <div class="dev-callout"><b>Nota sulla sicurezza</b>
<p>Il segreto client &egrave; un'importante credenziale di sicurezza. Non condividere questo valore con altri n&eacute; distribuirlo con l'app.</p>
</div>

9.  In **Dominio di reindirizzamento** immettere l'URL del servizio mobile indicato nel passaggio 8 e quindi fare clic su **Salva**.

È ora possibile integrare l'autenticazione mediante Live Connect nell'app. Servizi mobili fornisce i seguenti due metodi per l'autenticazione degli utenti mediante:

-   Single Sign-On per le app di Windows Store. Questo metodo prevede che gli utenti autorizzino una volta l'autenticazione nell'applicazione tramite Live Connect e in seguito le credenziali vengano gestite da Windows, in base alle preferenze utente. Per ulteriori informazioni, vedere [Single Sign-On per app di Windows Store tramite Live Connect][].

-   Autenticazione di base. Questo metodo, che supporta diversi provider di autenticazione, richiede che gli utenti eseguano l'accesso ogni volta che l'app viene avviata. Per ulteriori informazioni, vedere [Introduzione all'autenticazione in Servizi mobili][].

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Registrazione delle app di Windows Store per l'utilizzo delle credenziali di accesso di un account Microsoft]: /it-it/develop/mobile/how-to-guides/register-for-microsoft-authentication
  [invio di app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  []: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-win8-app-name.png
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started
  [2]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-store-association.png
  [3]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-select-app-name.png
  [Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  [4]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-apps-list.png
  [5]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-app-api-settings.png
  [Single Sign-On per app di Windows Store tramite Live Connect]: /it-it/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
  [Introduzione all'autenticazione in Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-with-users-dotnet
