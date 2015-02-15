

Per poter autenticare gli utenti, è necessario registrare la propria app presso un provider di identità. È quindi necessario registrare il segreto client generato dal provider con Servizi mobili.

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sul servizio mobile.

   	![](./media/mobile-services-register-authentication/mobile-services-selection.png)

2. Fare clic sulla scheda **Dashboard** e prendere nota del valore **URL servizio mobile**.

   	![](./media/mobile-services-register-authentication/mobile-service-uri.png)

    Potrebbe essere necessario fornire questo valore al provider di identità durante la registrazione dell'app.

3. Scegliere un provider di identità supportato nell'elenco riportato di seguito e seguire la procedura per registrare l'app con tale provider:

 - <a href="/it-it/documentation/articles/mobile-services-how-to-register-microsoft-authentication/" target="_blank">Account Microsoft</a>
 - <a href="/it-it/documentation/articles/mobile-services-how-to-register-facebook-authentication/" target="_blank">Accesso Facebook</a>
 - <a href="/it-it/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Accesso Twitter</a>
 - <a href="/it-it/documentation/articles/mobile-services-how-to-register-google-authentication/" target="_blank">Accesso Google</a>
 - <a href="/it-it/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>

   Non dimenticare di prendere nota dei valori dell'identità e del segreto client generati dal provider.

   > [AZURE.IMPORTANT] La chiave privata generata dal provider è una credenziale di sicurezza importante. Non condividere questo valore con altri né distribuirlo con l'app.

4. Tornare al portale di gestione, fare clic sulla scheda **Identità**, immettere l'identificatore dell'app e il segreto condiviso forniti dal provider di identità e fare clic su **Salva**.

   	![](./media/mobile-services-register-authentication/mobile-identity-tab.png)

	Il servizio mobile e l'app sono ora configurati per funzionare con il provider di autenticazione scelto.

<!-- URLs. -->
[Portale di gestione di Azure]: https://manage.windowsazure.com/
<!--HONumber=42-->
