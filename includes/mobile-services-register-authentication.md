

Per poter autenticare gli utenti, è necessario registrare la propria app presso un provider di identità. È quindi necessario registrare il segreto client generato dal provider con Servizi mobili.

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su **Mobile Services** e quindi sul servizio mobile.

    ![](./media/mobile-services-register-authentication/mobile-services-selection.png)

2.  Fare clic sulla scheda **Dashboard** e prendere nota del valore in **Mobile Service URL**.

    ![](./media/mobile-services-register-authentication/mobile-service-uri.png)

    Potrebbe essere necessario fornire questo valore al provider di identità durante la registrazione dell'app.

3.  Scegliere un provider di identità supportato nell'elenco riportato di seguito e seguire la procedura per registrare l'app con tale provider:

 -   [Account Microsoft](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
 -   [Account di accesso di Facebook](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
 -   [Account di accesso di Twitter](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
 -   [Account di accesso di Google](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
 -   [Azure Active Directory](/en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    Non dimenticare di prendere nota dei valori dell'identità e del segreto client generati dal provider.

    **Nota sulla sicurezza**

    Il segreto generato dal provider è una credenziale di sicurezza importante. Non condividere questo valore con altri né distribuirlo con l'app.

1.  Tornare al portale di gestione, fare clic sulla scheda **Identity**, immettere l'identificatore dell'app e il segreto condiviso forniti dal provider di identità e fare clic su **Save**.

    ![](./media/mobile-services-register-authentication/mobile-identity-tab.png)

    Il servizio mobile e l'app sono ora configurati per funzionare con il provider di autenticazione scelto.


