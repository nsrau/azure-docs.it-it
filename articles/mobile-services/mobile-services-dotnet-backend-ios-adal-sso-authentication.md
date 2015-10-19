<properties
	pageTitle="Autenticare l'app tramite il Single Sign-On di Active Directory Authentication Library (iOS) | Microsoft Azure"
	description="Informazioni su come autenticare gli utenti per l'accesso Single Sign-On con ADAL nell'applicazione per iOS."
	documentationCenter="ios"
	authors="mattchenderson"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/18/2015" 
	ms.author="mahender"/>

# Autenticare l'app tramite il Single Sign-On di Active Directory Authentication Library

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

##Panoramica

Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite Active Directory Authentication Library.

Per poter autenticare gli utenti è necessario registrare l'app in Azure Active Directory. Questa operazione si esegue in due passaggi. Prima di tutto, è necessario registrare il servizio mobile ed esporre le registrazioni sul servizio. In secondo luogo, è necessario registrare l'app di iOS e concederle accesso alle autorizzazioni.


>[AZURE.NOTE]Questa esercitazione è stata ideata per illustrare come usare Servizi mobili per eseguire l'autenticazione tramite Single Sign-On di Azure Active Directory per le app per iOS. Se si tratta della prima esperienza con Servizi mobili, iniziare dall'esercitazione [Introduzione a Servizi mobili].


##Prerequisiti


Per completare questa esercitazione, è necessario disporre di:

* XCode 4.5 e iOS 6.0 (o versioni successive)
* Esercitazione [Introduzione a Servizi mobili] completata
* Microsoft Azure Mobile Services SDK
* [Active Directory Authentication Library per iOS]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../../includes/mobile-services-dotnet-adal-register-client.md)]

##Configurare il servizio mobile in modo che richieda l'autenticazione

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

##Aggiungere il codice di autenticazione all'app client

1. Scaricare la libreria [Active Directory Authentication Library per iOS] e includerla nel progetto. Assicurarsi anche di aggiungere gli storyboard dell'origine ADAL (Active Directory Authentication Library).

2. In QSTodoListViewController includere ADAL con la seguente istruzione:

        #import "ADALiOS/ADAuthenticationContext.h"

2. Aggiungere quindi il metodo seguente:

        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            NSString *authority = @"<INSERT-AUTHORITY-HERE>";
            NSString *resourceURI = @"<INSERT-RESOURCE-URI-HERE>";
            NSString *clientID = @"<INSERT-CLIENT-ID-HERE>";
            NSString *redirectURI = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError *error;
            ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
            NSURL *redirectUri = [[NSURL alloc]initWithString:redirectURI];

            [authContext acquireTokenWithResource:resourceURI clientId:clientID redirectUri:redirectUri completionBlock:^(ADAuthenticationResult *result) {
                if (result.tokenCacheStoreItem == nil)
                {
                    return;
                }
                else
                {
                    NSDictionary *payload = @{
                        @"access_token" : result.tokenCacheStoreItem.accessToken
                    };
                    [client loginWithProvider:@"windowsazureactivedirectory" token:payload completion:^(MSUser *user, NSError *error) {
                        [self refresh];
                    }];
                }
            }];
        }


6. Nel codice per il metodo `loginAndGetData` precedente sostituire **INSERT-AUTHORITY-HERE** con il nome del tenant in cui è stato effettuato il provisioning dell'applicazione, nel formato https://login.windows.net/tenant-name.onmicrosoft.com. È possibile copiare questo valore dalla scheda Dominio di Azure Active Directory nel [portale di gestione di Azure].

7. Nel codice per il metodo `loginAndGetData` precedente sostituire **INSERT-RESOURCE-URI-HERE** con l'**URI ID app** del servizio mobile. Se sono state seguite le istruzioni riportate nell'argomento sulla [modalità di registrazione in Azure Active Directory], l'URI ID app dovrebbe essere simile a https://todolist.azure-mobile.net/login/aad.

8. Nel codice per il metodo `loginAndGetData` precedente sostituire **INSERT-CLIENT-ID-HERE** con l'ID client copiato dall'applicazione client nativa.

9. Nel codice per il metodo `loginAndGetData` precedente, sostituire **INSERT-REDIRECT-URI-HERE** con l'endpoint /login/done del servizio mobile. Questo valore dovrebbe essere simile a https://todolist.azure-mobile.net/login/done.


3. In QSTodoListViewController modificare `ViewDidLoad` sostituendo `[self refresh]` con quanto segue:

        [self loginAndGetData];

##Testare il client usando l'autenticazione

1. Nel menu Prodotto fare clic su Esegui per avviare l'app.
2. Verrà visualizzata la richiesta di accedere ad Azure Active Directory.  
3. L'app esegue l'autenticazione e restituisce gli elementi todo.

   ![](./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png)



<!-- URLs. -->
[Introduzione a Servizi mobili]: mobile-services-dotnet-backend-ios-get-started.md
[modalità di registrazione in Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[portale di gestione di Azure]: https://manage.windowsazure.com/
[Active Directory Authentication Library per iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios

<!---HONumber=Oct15_HO2-->