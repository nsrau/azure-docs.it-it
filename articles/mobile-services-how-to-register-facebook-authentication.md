<properties  linkid="develop-mobile-how-to-guides-register-for-facebook-authentication" urlDisplayName="Register for Facebook Authentication" pageTitle="Register for Facebook authentication - Mobile Services" metaKeywords="Azure Facebook, Azure Facebook, Azure authenticate Mobile Services" description="Learn how to use Facebook authentication in your Azure Mobile Services app." metaCanonical="" services="" documentationCenter="" title="Register your apps for Facebook authentication with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

# Registrazione delle app per l'autenticazione di Facebook con Servizi mobili

In questo argomento viene illustrato come registrare le app in modo da poter utilizzare Facebook per l'autenticazione con Servizi mobili di Azure.

 
<div  class="dev-callout"><b>Nota</b>
<p>Per completare la procedura descritta in questo argomento, è necessario disporre di un account di Facebook con un indirizzo di posta elettronica verificato e un numero di cellulare. Per creare un nuovo account di Facebook, visitare il sito <a  href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">facebook.com</a>.</p>
</div>

1.  Passare al sito Web <a href="http://go.microsoft.com/fwlink/p/?LinkId=268286" target="_blank">Facebook Developers</a> e accedere con le proprie credenziali dell'account di Facebook.

2.  (Facoltativo) Se non è ancora stata effettuata la registrazione, fare clic su **Apps** e quindi su **Register as a Developer**,  accettare le condizioni e seguire la procedura di registrazione.
    
	![][0]

3.  Fare clic su **Apps**, quindi su **Create a New App**.
    
	![][1]

4.  Scegliere un nome univoco per l'app, selezionare **Apps for Pages**, fare clic su **Create App** e completare la domanda di verifica.
    
	![][2]
    
    L'app verrà registrata in Facebook

5.  Fare clic su **Settings**, digitare il nome del dominio del servizio  mobile in **App Domains**, quindi fare clic su **Add Platform** e  selezionare **Website**.
    
	![][3]

6.  Digitare l'URL del servizio mobile in **Site URL** e quindi fare clic su **Save Changes**.
    
	![][4]

7.  Fare clic su **Show**, digitare la propria password, se richiesto, quindi annotare i valori di **App ID** e **App Secret**.
    
	![][5]
    
    <div  class="dev-callout" markdown="1"><b>Nota sulla sicurezza</b>
	<p>Il segreto dell app è una credenziale di sicurezza importante. Non condividere questo valore con altri né distribuirlo con l app.</p>
	</div>

È ora possibile utilizzare un account di accesso di Facebook per l'autenticazione nell'app fornendo i valori dell'ID e del segreto dell'app a Servizi mobili.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
[1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
[2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Management Portal]: https://manage.windowsazure.com/
