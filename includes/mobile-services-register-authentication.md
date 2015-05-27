

Per autenticare gli utenti, registrare l'app con un provider di identità e quindi registrare le credenziali del client generate dal provider con Servizi mobili di Azure.

1. Accedere al portale di gestione di Azure, fare clic su **Servizi mobili** e quindi sul servizio mobile.

2. Fare clic sulla scheda **Dashboard** e prendere nota del valore in **URL servizio mobile**. Potrebbe essere necessario fornire questo valore al provider di identità durante la registrazione dell'app.

3. Scegliere un provider di identità supportato dall'elenco sottostante. Seguire i passaggi per registrare l'app con il provider. Non dimenticare di prendere nota dei valori dell'identità e del segreto client generati dal provider.

 - <a href="/documentation/articles/mobile-services-how-to-register-microsoft-authentication/" target="_blank">Account Microsoft</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-facebook-authentication/" target="_blank">Facebook</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Twitter</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-google-authentication/" target="_blank">Google</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>

    > [AZURE.IMPORTANT]Il segreto generato dal provider è una credenziale di sicurezza importante. Non condividere questo valore con altri né distribuirlo con l'app.

4. Tornare al portale di gestione, fare clic sulla scheda **Identity**, immettere l'identificatore dell'app e il segreto condiviso forniti dal provider di identità e fare clic su **Save**. Il servizio mobile e l'app sono ora configurati per funzionare con il provider di autenticazione scelto.

    > [AZURE.IMPORTANT]Verificare di aver impostato l'URI di reindirizzamento corretto nel sito per sviluppatori del provider di identità. Come descritto nelle istruzioni collegate per ogni provider riportato sopra, l'URI di reindirizzamento può essere diverso per un servizio back-end .NET e per un servizio back-end JavaScript. Se l'URI di reindirizzamento non è configurato in modo corretto, è possibile che la schermata di accesso non venga visualizzata correttamente e che si verifichino anomalie impreviste nel funzionamento dell'applicazione.

5. (Facoltativo) Ripetere i passaggi 3 e 4 per configurare tutti gli altri provider di identità che si vuole vengano supportati dall'app.

<!--HONumber=54-->