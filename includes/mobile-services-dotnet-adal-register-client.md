## <a name="register-app-aad"></a>Registrare l'app client in Azure Active Directory

1.  Passare ad **Active Directory** nel [portale di gestione di Azure][portale di gestione di Azure], quindi fare clic sulla directory.

![](./media/mobile-services-dotnet-adal-register-client/mobile-services-select-aad.png)

1.  Fare clic sulla scheda **Applicazioni** nella parte superiore, quindi fare clic su **AGGIUNGI** per aggiungere un'app.

![][1]

1.  Fare clic su **Add an application my organization is developing**.

2.  Nella procedura guidata di aggiunta applicazione immettere un **Nome** per l'applicazione e fare clic sul tipo **Applicazione client nativa**. Fare quindi clic per continuare.

![][2]

1.  Nella casella **URI di reindirizzamento** immettere l'endpoint /login/done per il servizio mobile. Questo valore dovrebbe essere simile a <https://todolist.azure-mobile.net/login/done>.

![][3]

1.  Fare clic sulla scheda **Configura** per l'applicazione nativa e copiare l'**ID client**, che sarà necessario più avanti.

![][4]

1.  Scorrere in basso nella pagina fino alla sezione **Autorizzazioni per altre applicazioni** e concedere accesso completo all'applicazione del servizio mobile registrata in precedenza. Fare quindi clic su **Salva**.

![][5]

Il servizio mobile è ora configurato in Azure Active Directory per ricevere accessi tramite Single Sign-On dall'app.

  [portale di gestione di Azure]: https://manage.windowsazure.com/
   
  [1]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-aad-applications-tab.png
  [2]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-selection.png
  [3]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-redirect-uri.png
  [4]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-client-id.png
  [5]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-add-permissions.png
