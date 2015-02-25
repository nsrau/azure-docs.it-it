<properties pageTitle="Registrazione per l'accesso Single Sign-On - Servizi mobili di Azure" description="Informazioni su come registrarsi per l'autenticazione Single Sign-On nell'applicazione Servizi mobili di Azure." services="mobile-services" documentationCenter="" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga"/>

# Registrazione delle app di Windows Store per l'uso dell'accesso Single Sign-On di Windows Live Connect

Questo argomento descrive come registrare un'app con Windows Store in modo che possa usare Live Connect per l'accesso Single Sign-On come provider di identità per Servizi mobili di Azure. In questo passaggio è inoltre richiesto l'uso delle notifiche push.

> [AZURE.IMPORTANT] Non è necessario registrare l'app con Windows Store per usare l'account Microsoft per l'autenticazione prima di pubblicare l'app. Quando l'app di Windows Store non richiede l'accesso Single Sign-On o le notifiche push, è sufficiente registrare l'app con Live Connect per usare un accesso con account Microsoft.  Per altre informazioni, vedere [Registrazione delle app di Windows Store per l'uso delle credenziali di accesso di un account Microsoft](/it-it/develop/mobile/how-to-guides/register-for-microsoft-authentication).

1. Se l'app non è ancora stata registrata, passare alla pagina [Invia un'app] nel Centro per sviluppatori di app di Windows Store, accedere con il proprio account Microsoft e quindi fare clic su **Nome app**.

   	![][0]

2. Immettere un nome per l'app in **Nome app**, fare clic su **Riserva nome dell'app** e quindi su **Salva**.

   	![][1]

   	Verrà creata una nuova registrazione a Windows Store per l'app.

3. In Visual Studio 2012 Express per Windows 8 aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili].

4. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, scegliere **Store** e quindi fare clic su **Associa applicazione a Store**. 

  	![][2]

   	Verrà visualizzata la procedura guidata **Associa l'applicazione con Windows Store**.

5. Nella procedura guidata fare clic su **Accedi**, quindi accedere con il proprio account Microsoft.

6. Selezionare l'app registrata nel passaggio 2, fare clic su **Avanti**, quindi su **Associa**.

   	![][3]

   	Le necessarie informazioni di registrazione a Windows Store verranno aggiunte al manifesto dell'applicazione.    

9. Passare alla pagina [Applicazioni personali] nel Centro sviluppatori Live Connect e fare clic sull'app nell'elenco **Applicazioni personali**.

   	![][6] 

10. Fare clic su **Modifica impostazioni**, quindi su **Impostazioni API** e prendere nota del valore di **Segreto client**. 

   	![][7]

    > [AZURE.NOTE] Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.

11. In **Dominio di reindirizzamento** immettere l'URL del servizio mobile indicato nel passaggio 8 e quindi fare clic su **Salva**.

È ora possibile integrare l'autenticazione mediante Live Connect nell'app. Servizi mobili fornisce i seguenti due metodi per l'autenticazione degli utenti mediante:

   - Single Sign-On per le app di Windows Store. Questo metodo prevede che gli utenti autorizzino una volta l'autenticazione nell'applicazione tramite Live Connect e in seguito le credenziali vengano gestite da Windows, in base alle preferenze utente. Per altre informazioni, vedere [Single Sign-On per app di Windows Store tramite Live Connect].

   - Autenticazione di base. Questo metodo, che supporta diversi provider di autenticazione, richiede che gli utenti eseguano l'accesso ogni volta che l'app viene avviata. Per altre informazioni, vedere [Introduzione all'autenticazione].

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-select-app-name.png


[6]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-app-api-settings.png



<!-- URLs. -->
[Single Sign-On per app di Windows Store tramite Live Connect]: /it-it/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-dotnet
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-dotnet/
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript e HTML]: /it-it/develop/mobile/tutorials/get-started-with-users-js/
[Portale di gestione di Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
