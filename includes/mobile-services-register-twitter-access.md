

Le nuove API Twitter 1.1 richiedono l'autenticazione dell'app prima dell'accesso alle risorse. Ottenere innanzitutto le credenziali necessarie per richiedere l'accesso mediante OAuth 2.0. Archiviarle quindi in modo sicuro nelle impostazioni dell'app per il servizio mobile.

1. Completare la procedura illustrata nell'argomento <a href="/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Registrazione delle app per l'autenticazione di Twitter con Servizi mobili</a>, se non è ancora stata eseguita. 
  
  	In Twitter verranno generate le credenziali necessarie per consentire l'accesso alle API Twitter 1.1. È possibile ottenere le credenziali dal sito Web per sviluppatori di Twitter. 

2. Passare al sito Web per <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">sviluppatori di Twitter</a>, accedere con le credenziali dell'account Twitter e selezionare l'app Twitter.

3. Nella scheda **Keys and Access Tokens** dell'app, prendere nota dei seguenti valori:

	+ **Chiave utente**
	+ **Chiave privata utente**
	+ **Token di accesso**
	+ **Segreto del token di accesso**

4. Accedere al [portale di gestione di Microsoft Azure], fare clic su **Servizi mobili** e quindi sul servizio mobile.

5. Fare clic sulla scheda **Identità**, immettere i valori relativi alla **chiave utente ** e alla **chiave privata utente** ottenuti da Twitter e salvare****. 

	![](./media/mobile-services-register-twitter-access/mobile-identity-tab-twitter-only.png)

2. Fare clic sulla scheda **Configura**, scorrere fino a **Impostazioni app** e immettere una coppia **nome** e **valore** per ognuno dei seguenti elementi ottenuti dal sito Twitter, quindi fare clic su **Salva**.

	+ `TWITTER_ACCESS_TOKEN`
	+ `TWITTER_ACCESS_TOKEN_SECRET`

	![](./media/mobile-services-register-twitter-access/mobile-schedule-job-app-settings.png)

	Il token di accesso di Twitter verrà archiviato nelle impostazioni dell'app. Analogamente alle credenziali utente nella scheda **Identità**, anche le credenziali di accesso vengono archiviate crittografate nelle impostazioni dell'app ed è possibile accedervi negli script per server senza impostarle come hardcoded nel file di script. Per altre informazioni, vedere [Impostazioni app].

<!-- URLs. -->
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[Portale di gestione di Microsoft Azure]: https://manage.windowsazure.com/
[Registrare le app per l'autenticazione di Twitter con Servizi mobili]: /documentation/articles/mobile-services-how-to-register-twitter-authentication
[Sviluppatori di Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Impostazioni app]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
<!--HONumber=47-->
