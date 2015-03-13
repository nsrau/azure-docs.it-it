<properties 
	pageTitle="Accesso alle informazioni di Azure Active Directory Graph (Windows Store) | Mobile Dev Center" 
	description="Informazioni su come accedere a dati di Azure Active Directory usando l'API Graph nell'applicazione per Windows Store." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="wesmc"/>

# Accesso alle informazioni di Azure Active Directory Graph

[AZURE.INCLUDE [mobile-services-selector-aad-graph](../includes/mobile-services-selector-aad-graph.md)]


Come l'altro provider di identità offerto con i Servizi mobili, anche il provider di Azure Active Directory (AAD) supporta un'[API Graph] avanzata, che può essere usata per l'accesso alla directory a livello di codice. In questa esercitazione si aggiornerà l'app ToDoList in modo da personalizzare l'esperienza dell'app dell'utente autenticato in base alle informazioni aggiuntive sull'utente recuperate dalla directory con l'[API Graph].

>[AZURE.NOTE] L'intento di questa esercitazione è di approfondire la conoscenza dell'autenticazione con Azure Active Directory. Si presuppone che sia stata già completata l'esercitazione [Introduzione all'autenticazione] usando il provider di autenticazione di Azure Active Directory. Questa esercitazione continua ad aggiornare l'applicazione ToDoItem usata nell'esercitazione [Introduzione all'autenticazione].



In questa esercitazione viene descritta la procedura seguente:


1. [Generare una chiave di accesso per la registrazione dell'app in AAD] 
2. [Creare un'API personalizzata GetUserInfo] 
3. [Aggiornare l'app da usare per la chiamata all'API personalizzata]
4. [Testare l'app]

##Prerequisiti 

Prima di iniziare questa esercitazione, è necessario aver già completato queste esercitazioni su Servizi mobili:

+ [Introduzione all'autenticazione]<br/>Aggiunge un requisito di accesso all'app di esempio TodoList.

+ [Esercitazione sull'API personalizzata]<br/>Illustra come chiamare un'API personalizzata. 



## <a name="generate-key"></a>Generare una chiave di accesso per la registrazione dell'app in AAD


Nel corso dell'esercitazione [Introduzione all'autenticazione] è stata creata una registrazione per l'applicazione integrata quando è stato completato il passaggio [Registrazione delle app per l'uso delle credenziali di accesso di un account Azure Active Directory]. In questa sezione viene generata una chiave da usare nella lettura delle informazioni sulla directory con l'ID client dell'applicazione integrata. 

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]



## <a name="create-api"></a>Creare un'API personalizzata GetUserInfo

In questa sezione viene creata l'API personalizzata GetUserInfo che userà l'[API REST Graph] per recuperare informazioni aggiuntive sull'utente da AAD.

Se non sono mai state usate le API personalizzate con i Servizi mobili, è consigliabile consultare l'[esercitazione sull'API personalizzata] prima di completare questa sezione.

1. Nel [portale di gestione di Azure] creare la nuova API personalizzata GetUserInfo per il servizio mobile e impostare le autorizzazioni per il metodo get su **Solo utenti autenticati**.

    ![][0]

2. Aprire l'editor di script per la nuova API GetUserInfo e aggiungere le seguenti variabili all'inizio dello script.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;



3. Aggiungere la definizione seguente per la funzione  `getAADToken`.

        function getAADToken(callback) {
            var req = require("request");
            var options = {
                url: "https://login.windows.net/" + tenant_domain + "/oauth2/token?api-version=1.0",
                method: 'POST',
                form: {
                    grant_type: "client_credentials",
                    resource: "https://graph.windows.net",
                    client_id: clientID,
                    client_secret: key
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).access_token);
            });
        }

    Dati i valori di *tenant_domain*,  *client id* dell'applicazione integrata e *key* dell'applicazione, questa funzione fornisce un token di accesso a Graph usato per la lettura delle informazioni delle directory.

4. Aggiungere la funzione  `getUser` seguente che usa l'API Graph per restituire le informazioni dell'utente.

        function getUser(access_token, objectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/users/" + objectId + "?api-version=1.0",
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + access_token
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body));
            });
        };

    Questa funzione è un wrapper sottile che circonda l'endpoint [GetUser] dell'API REST di Graph. Usa il token di accesso a Graph per ottenere le informazioni utente usando l'ID oggetto dell'utente.

5. Aggiornare il metodo  `get` esportato nel modo seguente per restituire informazioni utente usando le altre funzioni.

        exports.get = function (request, response) {
            if (request.user.level == 'anonymous') {
                response.send(statusCodes.UNAUTHORIZED, null);
                return;
            }
            var errorHandler = function (err) {
                console.log(err);
                response.send(statusCodes.INTERNAL_SERVER_ERROR, err);
            };
            request.user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) errorHandler(err);
                        else getUser(access_token, objectId, function (err, user_info) {
                            if (err) errorHandler(err);
                            else {
                                console.log('GetUserInfo: ' + JSON.stringify(user_info, null, 4));
                                response.send(statusCodes.OK, user_info);
                              }
                            });
                    });
                },
                error: errorHandler
            });
        };


## <a name="update-app"></a>Aggiornare l'app per l'uso di GetUserInfo


In questa sezione verrà aggiornato il metodo  `AuthenticateAsync` implementato nell'esercitazione [Introduzione all'autenticazione] per chiamare l'API personalizzata e restituire informazioni aggiuntive sull'utente da AAD. 

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]


 


## <a name="test-app"></a>Testare l'app

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]




##<a name="next-steps"></a>Passaggi successivi

Nell'esercitazione successiva, [Controllo di accesso basato sui ruoli con AAD in Servizi mobili], si userà il controllo di accesso basato sui ruoli con Azure Active Directory (AAD) per verificare l'appartenenza al gruppo prima di consentire l'accesso. 


<!-- Anchors. -->
[Generare una chiave di accesso per la registrazione dell'app in AAD]: #generate-key
[Creare un'API personalizzata GetUserInfo]: #create-api
[Aggiornare l'app da usare per la chiamata all'API personalizzata]: #update-app
[Testare l'app]: #test-app
[Passaggi successivi]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png


<!-- URLs. -->
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Come eseguire la registrazione ad Azure Active Directory]: /it-it/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Esercitazione sull'API personalizzata]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
[Archiviazione degli script del server]: /it-it/documentation/articles/mobile-services-store-scripts-source-control/
[Registrazione delle app per l'uso delle credenziali di accesso di un account Azure Active Directory]: /it-it/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[API Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[API REST di Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[GetUser]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Controllo di accesso basato sui ruoli con AAD in Servizi mobili]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/


<!--HONumber=42-->
