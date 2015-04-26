## <a name="register-app-aad"></a>Registrare l'app client in Azure Active Directory

1. Passare ad **Active Directory** nel [portale di gestione di Azure], quindi fare clic sulla directory.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-select-aad.png)

2. Fare clic sulla scheda **Applicazioni** nella parte superiore, quindi fare clic su **AGGIUNGI** per aggiungere un'app. 

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-aad-applications-tab.png)

3. Fare clic su **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.

4. Nella procedura guidata di aggiunta applicazione immettere un **Nome** per l'applicazione e fare clic sul tipo **Applicazione client nativa**. Fare quindi clic per continuare.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-selection.png)

5. Nella casella **URI di reindirizzamento** immettere l'endpoint /login/done per il servizio mobile. Questo valore dovrebbe essere simile a https://todolist.azure-mobile.net/login/done.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-redirect-uri.png)

6. Fare clic sulla scheda **Configura** per l'applicazione nativa e copiare l'**ID client** che sarà necessario più avanti.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-client-id.png)

7. Scorrere in basso nella pagina fino alla sezione **Autorizzazioni per altre applicazioni** e fare clic sul pulsante **Aggiungi applicazione**. Scegliere **Altro** dal menu Mostra e cercare todo. Fare clic su **TodoList** per aggiungere il servizio mobile registrato in precedenza, quindi fare clic sul segno di spunta per confermare. Concedere l'accesso all'applicazione del servizio mobile. Fare quindi clic su **Salva**

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-add-permissions.png)

Il servizio mobile è ora configurato in Azure Active Directory per ricevere accessi tramite Single Sign-On dall'app.


[Portale di gestione di Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
