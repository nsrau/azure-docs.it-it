Per poter autenticare gli utenti, è necessario registrare la propria app presso un provider di identità. È quindi necessario registrare il segreto client generato dal provider con Servizi mobili.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sul servizio mobile.

    ![][0]

2.  Fare clic sulla scheda **Dashboard** e prendere nota del valore in **Mobile Service URL**.

    ![][1]

    Potrebbe essere necessario fornire questo valore al provider di identità durante la registrazione dell'app.

3.  Scegliere un provider di identità supportato nell'elenco riportato di seguito e seguire la procedura per registrare l'app con tale provider:

 - [Account Microsoft][Account Microsoft]
 - [Account di accesso di Facebook][Account di accesso di Facebook]
 - [Account di accesso di Twitter][Account di accesso di Twitter]
 - [Account di accesso di Google][Account di accesso di Google]
 - [Azure Active Directory][Azure Active Directory]

    Non dimenticare di prendere nota dei valori dell'identità e del segreto client generati dal provider.

    <div class="dev-callout"><b>Nota sulla sicurezza</b>
<p>Il segreto generato dal provider &egrave; una credenziale di sicurezza importante. Non condividere questo valore con altri n&eacute; distribuirlo con l'app.</p>
</div>

1.  Tornare al portale di gestione, fare clic sulla scheda **Identity**, immettere l'identificatore dell'app e il segreto condiviso forniti dal provider di identità e fare clic su **Save**.

    ![][2]

    Il servizio mobile e l'app sono ora configurati per funzionare con il provider di autenticazione scelto.



  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-register-authentication/mobile-services-selection.png
  [1]: ./media/mobile-services-register-authentication/mobile-service-uri.png
  [Account Microsoft]: /it-it/documentation/articles/mobile-services-how-to-register-microsoft-authentication/
  [Account di accesso di Facebook]: /it-it/documentation/articles/mobile-services-how-to-register-facebook-authentication/
  [Account di accesso di Twitter]: /it-it/documentation/articles/mobile-services-how-to-register-twitter-authentication/
  [Account di accesso di Google]: /it-it/documentation/articles/mobile-services-how-to-register-google-authentication/
  [Azure Active Directory]: /it-it/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [2]: ./media/mobile-services-register-authentication/mobile-identity-tab.png
