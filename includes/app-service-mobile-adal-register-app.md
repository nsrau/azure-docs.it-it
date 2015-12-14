1. Registrare il backend dell'app per dispositivi mobili con il tenant Azure Active Directory come indicato nell'argomento [Come configurare l'app mobile con Azure Active Directory].

2. Passare a **Active Directory** nel [portale di Azure classico]

   ![](./media/app-service-mobile-adal-register-app/app-service-navigate-aad.png)

3. Selezionare la directory, quindi la scheda **Applicazioni** nella parte superiore. Fare clic su **AGGIUNGI** in basso per creare una nuova registrazione dell'app. 

4. Fare clic su **Add an application my organization is developing**.

5. Nella procedura guidata di aggiunta applicazione immettere un **Nome** per l'applicazione e fare clic sul tipo **Applicazione client nativa**. Fare quindi clic per continuare.

6. Nella casella **URI di reindirizzamento**, inserire l'endpoint /login/done per il gateway del Servizio app. Questo valore dovrebbe essere simile a https://contoso.azurewebsites.net/login/done.

7. Dopo aver aggiunto l'applicazione nativa, fare clic sulla scheda **Configura**. Copiare l'**ID client** che sarà necessario più avanti.

8. Scorrere verso il basso fino alla sezione **Autorizzazioni per altre applicazioni** e fare clic su **Aggiungi applicazione**.

9. Cercare l'applicazione Web registrata in precedenza e fare clic sull'icona più. Quindi, fare clic sul segni di spunta per chiudere la finestra di dialogo.

10. Sulla nuova voce appena aggiunta, aprire l'elenco a discesa **Delega autorizzazioni** e selezionare **Accesso (appName)**. Fare quindi clic su **Salva**.

   ![](./media/app-service-mobile-adal-register-app/aad-native-client-add-permissions.png)

L'applicazione adesso è configurata in AAD in modo che gli utenti possano accedervi usando AAD Sign-On.

[portale di Azure classico]: https://manage.windowsazure.com/
[Come configurare l'app mobile con Azure Active Directory]: ../articles/app-service-how-to-configure-active-directory-authentication.md

<!---HONumber=AcomDC_1203_2015-->