
Prima di tutto è necessario registrare l'app sul sito di un provider di identità e quindi impostare le credenziali nel servizio mobile.

1. Nel [portale di gestione di Azure] passare al servizio mobile, quindi fare clic su **Dashboard** e prendere nota del valore in **URL servizio mobile**.

2. Registrare l'app con uno dei provider di identità supportati elencati di seguito.

	* [Google](mobile-services-how-to-register-google-authentication.md)
	* [Facebook](mobile-services-how-to-register-facebook-authentication.md)
	* [Twitter](mobile-services-how-to-register-twitter-authentication.md)
	* [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
	* [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md).  
	
    >[AZURE.IMPORTANT]Assicurarsi di impostare correttamente l'URI di reindirizzamento per il servizio mobile nel sito per sviluppatori del provider di identità. Come descritto nelle istruzioni collegate per ogni provider riportato sopra, l'URL di reindirizzamento è diverso per un servizio mobile back-end .NET (`/signin-<provider>`) rispetto a un servizio mobile back-end JavaScript (`/login/<provider>`). Un URI di reindirizzamento configurato in modo non corretto impedisce al client di accedere all'app. <br/>Non distribuire o condividere il segreto client.

3. Nel servizio mobile nel [portale di gestione di Azure] fare clic sulla scheda **Identità** e immettere i valori relativi a ID e segreto dell'app ottenuti dal provider di identità.

Dopo aver configurato l'applicazione e il servizio mobile per supportare un provider di identità per l'autenticazione, è possibile ripetere questi passaggi per aggiungere il supporto per altri provider di identità.

[portale di gestione di Azure]: https://manage.windowsazure.com/

<!---HONumber=July15_HO2-->