<properties 
	pageTitle="Controllo degli accessi in base al ruolo in Servizi mobili e Azure Active Directory (Windows Store) | Microsoft Azure" 
	description="Informazioni su come controllare gli accessi in base al ruolo di Azure Active Directory nell'applicazione per Windows Store." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/15/2015" 
	ms.author="wesmc"/>

# Controllo degli accessi in base al ruolo in Servizi mobili e Azure Active Directory

[AZURE.INCLUDE [mobile-services-selector-rbac](../../includes/mobile-services-selector-rbac.md)]

##Panoramica

Il controllo degli accessi in base al ruolo consiste nell'assegnazione di autorizzazioni ai ruoli degli utenti e definisce in modo chiaro i limiti relativi alle azioni che alcune classi di utenti possono o meno eseguire. Questa esercitazione illustra come aggiungere un controllo degli accessi in base al ruolo di base a Servizi mobili di Azure.

Questa esercitazione illustra il controllo degli accessi in base al ruolo, verificando l'appartenenza di ciascun utente a un gruppo Vendite definito in Azure Active Directory (AAD). Il controllo degli accessi viene eseguito con JavaScript nel back-end del servizio mobile usando l'[API Graph] per Azure Active Directory. Solo gli utenti che appartengono al ruolo Vendite possono eseguire query sui dati.


>[AZURE.NOTE]L'intento di questa esercitazione è approfondire la conoscenza dell'autenticazione per includere procedure di autorizzazione. Si presuppone che sia stata completata l'esercitazione [Aggiungere l'autenticazione all'app] usando il provider di autenticazione di Azure Active Directory. Questa esercitazione continua ad aggiornare l'applicazione TodoItem usata nell'esercitazione [Aggiungere l'autenticazione all'app].

##Prerequisiti

Per completare questa esercitazione, è necessario disporre di:

* Visual Studio 2013 in esecuzione su Windows 8.1.
* Completamento dell'esercitazione [Aggiungere l'autenticazione all'app] con il provider di autenticazione di Azure Active Directory.
* Completamento dell'esercitazione sull'[archiviazione degli script del server] per acquisire familiarità con un repository Git per archiviare gli script del server.
 


##Creare un gruppo Vendite con l'appartenenza

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../../includes/mobile-services-aad-rbac-create-sales-group.md)]


##Generare una chiave per l'applicazione integrata


Nel corso dell'esercitazione [Aggiungere l'autenticazione all'app] è stata creata una registrazione per l'applicazione integrata quando è stato completato il passaggio relativo alla [registrazione delle app per l'uso delle credenziali di accesso di un account Azure Active Directory]. In questa sezione viene generata una chiave da usare nella lettura delle informazioni sulla directory con l'ID client dell'applicazione integrata.

Se è stata eseguita in precedenza l'esercitazione relativa all'[accesso alle informazioni di Azure Active Directory Graph], questo passaggio è già stato completato ed è possibile ignorare questa sezione.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]





##Aggiungere uno script condiviso al servizio mobile per verificare l'appartenenza

In questa sezione si userà Git per distribuire un file di script condiviso denominato *rbac.js* nel servizio mobile. Questo file di script condiviso file contiene le funzioni che usano l'[API Graph] per verificare l'appartenenza dell'utente al gruppo.

Se non si conosce come distribuire script nel servizio mobile con Git, vedere l'esercitazione sull'[archiviazione degli script del server] prima di completare questa sezione.

1. Creare un nuovo file di script denominato *rbac.js* nella directory *./service/shared/* del repository locale per il servizio mobile.
2. Aggiungere lo script seguente all'inizio del file che definisce la funzione `getAADToken`. Dati i valori di *tenant\_domain*, *client id* dell'applicazione integrata e *key* dell'applicazione, questa funzione fornisce un token di accesso a Graph usato per la lettura delle informazioni delle directory.

    >[AZURE.NOTE]Invece di creare un nuovo token a ogni controllo di accesso, è consigliabile memorizzarlo nella cache. Aggiornare quindi la cache quando i tentativi di usare il token generano una risposta 401 Authentication\_ExpiredToken come indicato in [Codici di errore di Graph di AD di Microsoft Azure]. Nel codice seguente questo passaggio non viene mostrato per semplicità, ma consente di alleggerire il traffico di rete aggiuntivo in Active Directory.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;
        exports.SalesGroup = appSettings.AAD_GROUP_ID;
         
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


3. Aggiungere il codice seguente a *rbac.js* per definire la funzione `getGroupId`. Questa funzione usa il token di accesso per ottenere l'ID del gruppo in base al nome del gruppo usato in un filtro.
 
    >[AZURE.NOTE]Il codice cerca il gruppo Active Directory per nome. In molti casi è tuttavia consigliabile memorizzare l'ID gruppo come impostazione app del servizio mobile e usarlo. Il nome del gruppo, infatti, può cambiare mentre l'ID resta inalterato. Tuttavia, quando si modifica il nome del gruppo, si verifica anche almeno una modifica nell'ambito del ruolo che può richiedere un aggiornamento del codice del servizio mobile.

        function getGroupId(groupname, accessToken, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/groups" + 
                      "?$filter=displayName%20eq%20'" + groupname + "'" + 
	              "&api-version=2013-04-05" ,
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + accessToken,
                    "Content-Type": "application/json",
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value[0].objectId);
            });
        }


4. Aggiungere il codice seguente a *rbac.js* per definire la funzione `isMemberOf` che chiama l'endpoint [IsMemberOf] dell'API REST di Graph.

    Questa funzione è un wrapper semplice che circonda l'endpoint [IsMemberOf] dell'API REST di Graph. Usa il token di accesso di Graph per verificare se l'ID dell'oggetto directory dell'utente appartiene al gruppo in base all'ID del gruppo.

        function isMemberOf(access_token, userObjectId, groupObjectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/isMemberOf" + "?api-version=2013-04-05",
                method: 'POST',
                headers: {
                    "Authorization": "Bearer " + access_token,
                    "Content-Type": "application/json",
                },
                body: JSON.stringify({
                "groupId": groupObjectId,
                "memberId": userObjectId
                })
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value);
            });
        };

    

7. Aggiungere la funzione seguente `checkGroupMembership` esportata a *rbac.js*.

    Questa funzione esegue il wrapping dell'uso delle funzioni dell'altro script ed è esportata dallo script condiviso per essere chiamata da altri script per eseguire le verifiche di accesso vere e proprie. Dati l'oggetto utente del servizio mobile e l'ID del gruppo, lo script recupera l'ID dell'oggetto di Azure Active Directory per l'identità dell'utente e verifica l'appartenenza al gruppo.

        exports.checkGroupMembership = function (user, groupName, callback) {
            user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) callback(err);
		        else getGroupId(groupName, access_token, function (err, groupId) { 
                            if (err) callback(err);
                            else isMemberOf(access_token, objectId, groupId, function (err, isInGroup) {
       	                        if (err) errorHandler(err);
               	                else callback(null, isInGroup);
                            });
                        });
                    });
                },
                error: callback
            });
        }

8. Salvare le modifiche apportate a *rbac.js*.

##Aggiungere il controllo di accesso basato sui ruoli alle operazioni del database


Dopo aver completato l'esercitazione [Aggiungere l'autenticazione all'app di Servizi mobili], le operazioni della tabella per richiedere l'autenticazione dovrebbero già essere state impostate come illustrato di seguito.

![][3]

Con ogni operazione del database che richiede l'autenticazione è possibile aggiungere script che usano l'oggetto utente per i controlli di accesso.

I passaggi seguenti illustrano come distribuire il controllo di accesso basato sui ruoli tramite script in tutte le operazioni di tabella del servizio mobile. Per ogni operazione della tabella viene eseguito uno script che usa lo script condiviso *rbac.js*.

1. Aggiungere un nuovo file di script denominato *todoitem.insert.js* alla directory *./service/table/* nel repository Git locale del servizio mobile. Incollare lo script seguente nel file.

        function insert(item, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

2. Aggiungere un nuovo file di script denominato *todoitem.read.js* alla directory *./service/table/* nel repository Git locale del servizio mobile. Incollare lo script seguente nel file.

        function read(query, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

3. Aggiungere un nuovo file di script denominato *todoitem.update.js* alla directory *./service/table/* nel repository Git locale del servizio mobile. Incollare lo script seguente nel file.

        function update(item, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

4. Aggiungere un nuovo file di script denominato *todoitem.delete.js* alla directory *./service/table/* nel repository Git locale del servizio mobile. Incollare lo script seguente nel file.

        function del(id, user, request) {
        
            var RBAC = require('../shared/rbac.js');
        
            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

5. Nell'interfaccia della riga di comando del repository Git aggiungere la verifica per i file di script aggiunti eseguendo il comando seguente.

        git add .

6. Nell'interfaccia della riga di comando del repository Git eseguire il commit degli aggiornamenti eseguendo il comando seguente.

        git commit -m "Added role based access control to table operations"
  
7. Nell'interfaccia della riga di comando del repository Git distribuire gli aggiornamenti nel repository Git locale del servizio mobile eseguendo il comando seguente. Questo comando presuppone che gli aggiornamenti siano stati effettuati nel branch *master*.

        git push origin master

8. Nel [portale di gestione di Azure] dovrebbe essere possibile passare alle operazioni della tabella per il servizio mobile e vedere gli aggiornamenti come illustrato di seguito.

    ![][4]

##Testare l'accesso client

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../../includes/mobile-services-aad-rbac-test-app.md)]







<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/users.png
[1]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/group-membership.png
[2]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group.png
[3]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/table-perms.png
[4]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/insert-table-op-view.png
[5]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group-id.png
[6]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/client-id-and-key.png

<!-- URLs. -->
[Aggiungere l'autenticazione all'app]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md
[Aggiungere l'autenticazione all'app di Servizi mobili]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[portale di gestione di Azure]: https://manage.windowsazure.com/
[Directory Sync Scenarios]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[archiviazione degli script del server]: mobile-services-store-scripts-source-control.md
[registrazione delle app per l'uso delle credenziali di accesso di un account Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[API Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Codici di errore di Graph di AD di Microsoft Azure]: http://msdn.microsoft.com/library/azure/hh974480.aspx
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
[accesso alle informazioni di Azure Active Directory Graph]: mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info.md

<!---HONumber=August15_HO8-->