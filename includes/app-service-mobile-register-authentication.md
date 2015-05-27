

Per poter autenticare gli utenti, è necessario registrare la propria app presso un provider di identità. È quindi necessario registrare il segreto client generato dal provider con Servizi app.

1. Accedere al [portale di gestione di Azure], fare clic su **Sfoglia**, **Gruppo di risorse**, quindi selezionare il gruppo di risorse dell'app mobile.

2. Selezionare il gateway e prendere nota del valore **URL** in **Proprietà**. Potrebbe essere necessario fornire questo valore al provider di identità durante la registrazione dell'app.

   	![](./media/app-service-mobile-register-authentication/gateway-uri.png)

3. Scegliere un provider di identità supportato nell'elenco riportato di seguito e seguire la procedura per configurare l'app con tale provider:

 - <a href="/it-it/documentation/articles/app-service-mobile-how-to-configure-active-directory-authentication-preview/" target="_blank">Azure Active Directory</a>
 - <a href="/it-it/documentation/articles/app-service-mobile-how-to-configure-facebook-authentication-preview/" target="_blank">Account di accesso di Facebook</a>
 - <a href="/it-it/documentation/articles/app-service-mobile-how-to-configure-google-authentication-preview/" target="_blank">Account di accesso di Google</a>
 - <a href="/it-it/documentation/articles/app-service-mobile-how-to-configure-microsoft-authentication-preview/" target="_blank">Account Microsoft</a>
 - <a href="/it-it/documentation/articles/app-service-mobile-how-to-configure-twitter-authentication-preview/" target="_blank">Account di accesso di Twitter</a>

	L'applicazione è ora configurata per interagire con il provider di autenticazione scelto.

4. (Facoltativo) Ripetere il passaggio precedente per configurare tutti gli altri provider di identità che si vuole vengano supportati dall'app. 

<!-- URLs. -->
[portale di gestione di Azure]: https://manage.windowsazure.com/

<!--HONumber=54-->