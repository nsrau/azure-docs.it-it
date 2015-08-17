## <a name="register-mobile-service-aad"></a>Registrare il servizio mobile in Azure Active Directory


In questa sezione verrà registrato il servizio mobile in Azure Active Directory e verranno configurate le autorizzazioni per consentire la rappresentazione Single Sign-On.

1. Registrare l'applicazione per l'uso di Azure Active Directory attenendosi alla procedura descritta nell'argomento [Come eseguire la registrazione ad Azure Active Directory].

2. Nel [portale di gestione di Azure] tornare all'estensione Active Directory e fare clic sulla directory attiva.

3. Fare clic sulla scheda **Applicazioni** e quindi sull'applicazione creata.

4. Fare clic su **Gestisci manifesto** e quindi su **Scarica manifesto** e salvare il manifesto dell'applicazione in una directory locale.

   ![](./media/mobile-services-dotnet-adal-register-service/mobile-services-aad-app-manage-manifest.png)

5. Aprire il file manifesto dell'applicazione in Visual Studio. Nella parte superiore del file individuare la riga relativa alle autorizzazioni dell'app, che dovrebbe presentare un aspetto simile al seguente:

        "oauth2Permissions": [],

    Sostituire la riga con le autorizzazioni dell'app seguenti e quindi salvare il file.

        "oauth2Permissions": [
            {
                "adminConsentDescription": "Allow the application access to the mobile service",
                "adminConsentDisplayName": "Have full access to the mobile service",
                "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
                "isEnabled": true,
                "origin": "Application",
                "type": "User",
                "userConsentDescription": "Allow the application full access to the mobile service on your behalf",
                "userConsentDisplayName": "Have full access to the mobile service",
                "value": "user_impersonation"
            }
        ],

6. Nel portale di gestione di Azure fare di nuovo clic su **Gestisci manifesto** per l'applicazione e quindi su **Carica manifesto**. Passare al percorso del manifesto dell'applicazione appena aggiornato e caricare il manifesto.

<!-- URLs. -->
[Come eseguire la registrazione ad Azure Active Directory]: ../articles/mobile-services/mobile-services-how-to-register-active-directory-authentication.md
[portale di gestione di Azure]: https://manage.windowsazure.com/

<!---HONumber=August15_HO6-->