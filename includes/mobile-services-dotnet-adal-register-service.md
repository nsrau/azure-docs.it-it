## <a name="register-mobile-service-aad"></a>Registrare il servizio mobile in Azure Active Directory

In questa sezione verrà registrato il servizio mobile in Azure Active Directory e verranno configurate le autorizzazioni per consentire la rappresentazione Single Sign-On.

1.  Registrare l'applicazione per l'uso di Azure Active Directory attenendosi alla procedura descritta nell'argomento [Come eseguire la registrazione ad Azure Active Directory][Come eseguire la registrazione ad Azure Active Directory].

2.  Nel [portale di gestione di Azure][portale di gestione di Azure] tornare all'estensione Active Directory e fare clic sulla directory attiva.

3.  Fare clic sulla scheda **Applicazioni** e quindi sull'applicazione creata.

4.  Fare clic su **Gestisci manifesto** e quindi su **Scarica manifesto** e salvare il manifesto dell'applicazione in una directory locale.

![](./media/mobile-services-dotnet-adal-register-service/mobile-services-aad-app-manage-manifest.png)

1.  Aprire il file manifesto dell'applicazione in Visual Studio. Nella parte superiore del file individuare la riga relativa alle autorizzazioni dell'app, che dovrebbe presentare un aspetto simile al seguente:

        "appPermissions": [],

    Sostituire la riga con le autorizzazioni dell'app seguenti e quindi salvare il file.

        "appPermissions": [
            {
                "claimValue": "user_impersonation",
                "description": "Allow the application access to the mobile service",
                "directAccessGrantTypes": [],
                "displayName": "Have full access to the mobile service",
                "impersonationAccessGrantTypes": [
                    {
                        "impersonated": "User",
                        "impersonator": "Application"
                    }
                ],
                "isDisabled": false,
                "origin": "Application",
                "permissionId": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
                "resourceScopeType": "Personal",
                "userConsentDescription": "Allow the application full access to the mobile service on your behalf",
                "userConsentDisplayName": "Have full access to the mobile service"
            }
        ],

2.  Nel portale di gestione di Azure fare di nuovo clic su **Gestisci manifesto** per l'applicazione e quindi su **Carica manifesto**. Passare al percorso del manifesto dell'applicazione appena aggiornato e caricare il manifesto.



  [Come eseguire la registrazione ad Azure Active Directory]: /it-it/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  
