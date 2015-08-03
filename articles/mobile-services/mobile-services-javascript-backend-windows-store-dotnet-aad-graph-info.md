<properties 
	pageTitle="Accesso alle informazioni di Azure Active Directory Graph (Windows Store) | Mobile Dev Center" 
	description="Informazioni su come accedere a dati di Azure Active Directory usando l'API Graph nell'applicazione per Windows Store." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="wesmc"/>

# Accesso alle informazioni di Azure Active Directory Graph


[AZURE.INCLUDE [mobile-services-selector-aad-graph](../../includes/mobile-services-selector-aad-graph.md)]
##Panoramica

Come l'altro provider di identità offerto con i Servizi mobili, anche il provider di Azure Active Directory (AAD) supporta un'API Graph avanzata, che può essere usata per l'accesso alla directory a livello di codice. Questa esercitazione consente di aggiornare l'app ToDoList in modo da personalizzare l'esperienza dell'app dell'utente autenticato restituendo informazioni aggiuntive sull'utente recuperate dalla directory tramite l'[API REST Graph].

Per altre informazioni sull'API Azure AD Graph, vedere il [blog del Team di Azure Active Directory Graph].

>[AZURE.NOTE]L'intento di questa esercitazione è di approfondire la conoscenza dell'autenticazione con Azure Active Directory. Si presuppone che sia stata completata l'esercitazione [Aggiungere l'autenticazione all'app] usando il provider di autenticazione di Azure Active Directory. Questa esercitazione continua ad aggiornare l'applicazione TodoItem usata nell'esercitazione [Aggiungere l'autenticazione all'app].



##Prerequisiti 

Prima di iniziare questa esercitazione, è necessario aver già completato queste esercitazioni su Servizi mobili:

+ [Aggiungere l'autenticazione all'app]<br/>Aggiunge un requisito di accesso all'app di esempio TodoList.

+ [Esercitazione sull'API personalizzata]<br/>Dimostra come chiamare un'API personalizzata.



##Generare una chiave di accesso per la registrazione dell'app in AAD


Nel corso dell'esercitazione [Aggiungere l'autenticazione all'app] è stata creata una registrazione per l'applicazione integrata quando è stato completato il passaggio relativo alla [registrazione delle app per l'uso delle credenziali di accesso di un account Azure Active Directory]. In questa sezione viene generata una chiave da usare nella lettura delle informazioni sulla directory con l'ID client dell'applicazione integrata.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]



##Creare un'API personalizzata GetUserInfo

In questa sezione viene creata l'API personalizzata GetUserInfo che userà l'[API REST Graph] per recuperare informazioni aggiuntive sull'utente da AAD.

Se non sono mai state usate le API personalizzate con i Servizi mobili, è consigliabile consultare l'[esercitazione sull'API personalizzata] prima di completare questa sezione.

1. Nel [portale di gestione di Azure] creare la nuova API personalizzata GetUserInfo per il servizio mobile e impostare le autorizzazioni per il metodo get su **Solo utenti autenticati**.

    ![][0]

2. Aprire l'editor di script per la nuova API GetUserInfo e aggiungere le seguenti variabili all'inizio dello script.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;



3. Aggiungere la definizione seguente per la funzione `getAADToken`.

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

    Dati i valori di *tenant_domain*, *client id* dell'applicazione integrata e *key* dell'applicazione, questa funzione fornisce un token di accesso a Graph usato per la lettura delle informazioni delle directory.

4. Aggiungere la funzione `getUser` seguente che usa l'API Graph per restituire le informazioni dell'utente.

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

5. Aggiornare il metodo `get` esportato nel modo seguente per restituire informazioni utente usando le altre funzioni.

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


##Aggiornare l'app per l'uso di GetUserInfo


In questa sezione viene aggiornato il metodo `AuthenticateAsync` implementato nell'esercitazione [Introduzione all'autenticazione] per chiamare l'API personalizzata e restituire informazioni aggiuntive sull'utente da AAD.

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../../includes/mobile-services-aad-graph-info-update-app.md)]


 


##Testare l'app

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../../includes/mobile-services-aad-graph-info-test-app.md)]




##Passaggi successivi

Nell'esercitazione successiva, [Controllo di accesso basato sui ruoli con AAD in Servizi mobili], si userà il controllo di accesso basato sui ruoli con Azure Active Directory (AAD) per verificare l'appartenenza al gruppo prima di consentire l'accesso.



<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png


<!-- URLs. -->
[Aggiungere l'autenticazione all'app]: ../mobile-services-windows-store-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[portale di gestione di Azure]: https://manage.windowsazure.com/
[Esercitazione sull'API personalizzata]: mobile-services-windows-store-dotnet-call-custom-api.md
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[registrazione delle app per l'uso delle credenziali di accesso di un account Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Graph API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[API REST Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[GetUser]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Controllo di accesso basato sui ruoli con AAD in Servizi mobili]: mobile-services-javascript-backend-windows-store-dotnet-aad-rbac.md
[blog del Team di Azure Active Directory Graph]: http://go.microsoft.com/fwlink/?LinkId=510536

<!---HONumber=July15_HO4-->