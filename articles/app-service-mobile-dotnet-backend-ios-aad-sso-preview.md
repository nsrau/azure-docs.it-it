<properties 
        pageTitle="Autenticare gli utenti della propria app iOS con Single Sign-On di Azure Active Directory" 
        description="Informazioni su come fornire accesso agli utenti alla propria app iOS usando Active Directory Authentication Library." 
        documentationCenter="Mobile" 
        authors="mattchenderson" 
        services="app-service\mobile" 
        manager="dwrede"/>

<tags 
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="mahender"/>

# Aggiungere Single Sign-On di Azure Active Directory alla propria app iOS

[WACOM.INCLUDE [app-service-mobile-selector-aad-sso](../includes/app-service-mobile-selector-aad-sso.md)]

Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite Active Directory Authentication Library.

Per poter autenticare gli utenti è necessario registrare l'applicazione nel proprio tenant di Azure Active Directory (AAD). Questa operazione si esegue in due passaggi. Prima di tutto, è necessario registrare il servizio app ed esporre le autorizzazioni sul servizio. In secondo luogo, è necessario registrare l'app iOS e concedere a quest'ultima accesso alle autorizzazioni.

Per completare questa esercitazione, è necessario disporre di:

* XCode 4.5 e iOS 6.0 (o versioni successive)
* Completamento dell'esercitazione [Introduzione al servizio per app mobili].
* Mobile Services SDK di Microsoft Azure
* [Active Directory Authentication Library per iOS]

## <a name="register-application"></a>Registrare l'applicazione con Azure Active Directory

[WACOM.INCLUDE [app-service-mobile-adal-register-app](../includes/app-service-mobile-adal-register-app.md)]

## <a name="require-authentication"></a>Configurare l'applicazione in modo che richieda l'autenticazione

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

## <a name="add-adal"></a>Aggiungere un riferimento ad Active Directory Authentication Library

1. Scaricare [Active Directory Authentication Library per iOS].

2. Nello strumento di navigazione di Xcode selezionare il progetto e fare clic su **File** quindi sull'opzione per **l'aggiunta di file**. Passare alla posizione in cui è stata scaricata la libreria e selezionare **ADALiOS.xcodeproj**.

3. Selezionare nuovamente il progetto e aprire la scheda relativa alle **impostazioni di compilazione**. Passare alla sezione delle opzioni di **collegamento** e aggiungere `-ObjC` nell'area per gli **altri flag del linker**.

4. Selezionare la scheda relativa alle **fasi di compilazione**. Nell'area relativa alle **dipendenze della destinazione**, aggiungere `ADALiOS`.

5. Nell'area per il **collegamento del file binario con le librerie** aggiungere `libADALiOS.a`.

Sarà ora possibile fare riferimento ad Active Directory Authentication Library dal proprio progetto.

## <a name="add-authentication-code"></a>Aggiungere il codice di autenticazione all'app client

2. In QSTodoListViewController includere ADAL con la seguente istruzione:

        #import "ADALiOS/ADAuthenticationContext.h"

3. Aggiungere quindi il metodo seguente:

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

4. Nel codice per il metodo `loginAndGetData` precedente sostituire **INSERT-AUTHORITY-HERE** con il nome del tenant in cui è stato effettuato il provisioning dell'applicazione, nel formato https://login.windows.net/nome-tenant.onmicrosoft.com. È possibile copiare questo valore dalla scheda Dominio di Azure Active Directory nel [portale di gestione di Azure].

5. Nel codice per il metodo `loginAndGetData` precedente sostituire **INSERT-RESOURCE-URI-HERE** con l'**URI ID app** dell'app mobile. Se sono state seguite le istruzioni riportate nell'argomento sulla [modalità di configurazione di un'app mobile con Azure Active Directory], l'URI ID app dovrebbe essere simile a https://contosogateway.azurewebsites.net/login/aad.

6. Nel codice per il metodo `loginAndGetData` precedente sostituire **INSERT-CLIENT-ID-HERE** con l'ID client copiato dall'applicazione client nativa.

7. Nel codice per il metodo `loginAndGetData` precedente, sostituire **INSERT-REDIRECT-URI-HERE** con l'endpoint /login/done del gateway del servizio app. Questo valore dovrebbe essere simile a https://contosogateway.azurewebsites.net/login/done.

8. In QSTodoListViewController, modificare `ViewDidLoad` sostituendo `[self refresh]` con la riga seguente:

        [self loginAndGetData];

## <a name="test-client"></a>Testare il client usando l'autenticazione

1. Nel menu Prodotto fare clic su Esegui per avviare l'app.
2. Verrà visualizzata la richiesta di accedere ad Azure Active Directory.  
3. L'app esegue l'autenticazione e restituisce gli elementi todo.

<!-- URLs. -->
[modalità di configurazione di un'app mobile con Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication-preview.md
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Active Directory Authentication Library per iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
 [Introduzione al servizio per app mobili]: app-service-mobile-dotnet-backend-ios-get-started-preview.md

<!--HONumber=49-->