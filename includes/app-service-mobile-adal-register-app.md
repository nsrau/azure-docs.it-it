1. Registrare il backend dell'app mobile con il tenant Azure Active Directory come indicato nell'argomento [Come configurare l'app mobile con Azure Active Directory] topic.

2. Passare a **Active Directory** nel [Portale di gestione di Azure]

   ![](./media/app-service-mobile-adal-register-app/app-service-navigate-aad.png)

3. Selezionare la directory, quindi selezionare la scheda **Applicazioni** in alto. Fare clic su **AGGIUNGI** in fondo per creare una nuova registrazione di app. 

4. Fare clic su **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.

5. Nella procedura guidata di aggiunta applicazione immettere un **Nome** per l'applicazione e fare clic sul tipo **Applicazione client nativa**. Fare quindi clic per continuare.

6. Nella casella **URI di reindirizzamento**, inserire l'endpoint /login/done per il gateway del Servizio app. Questo valore deve essere simile a https://contoso.azurewebsites.net/login/done.

7. Una volta aggiunta l'applicazione nativa, fare clic sulla scheda **Configura**. Copiare l'**ID client**,  che sarà necessario più avanti.

8. Scorrere la pagina fino alle **autorizzazioni per altre applicazioni** e fare clic su **Aggiungi applicazione**.

9. Cercare l'applicazione Web registrata in precedenza e fare clic sull'icona più. Quindi, fare clic sul segni di spunta per chiudere la finestra di dialogo.

10. Nella nuova voce aggiunta, aprire l'elenco a discesa **Autorizzazioni delegate** e selezionare **Accesso (nomeApp)**. Quindi fare clic su **Salva**

   ![](./media/app-service-mobile-adal-register-app/aad-native-client-add-permissions.png)

L'applicazione adesso è configurata in AAD in modo che gli utenti possano accedervi sutilizzando AAD single sign-on.

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Come configurare l'app mobile con Azure Active Directory]: ../articles/app-service-how-to-configure-active-directory-authentication-preview.md

<!--HONumber=49-->