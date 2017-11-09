---
title: Aggiungere l'autenticazione in Apache Cordova con App per dispositivi mobili| Documentazione Microsoft
description: "Informazioni su come usare App per dispositivi mobili nel servizio app di Azure per autenticare gli utenti dell'app Apache Cordova tramite vari provider di identità, tra cui Google, Facebook, Twitter e Microsoft."
services: app-service\mobile
documentationcenter: javascript
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: glenga
ms.openlocfilehash: b7362b7f26859de541f792e714502851d74c98e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Aggiungere l'autenticazione all'app Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Summary
Questa esercitazione consente di aggiungere l'autenticazione al progetto introduttivo TodoList in Apache Cordova tramite un provider di identità supportato. Questa esercitazione è basata sull'esercitazione relativa alla [Introduzione alle app per dispositivi mobili] , che deve essere completata per prima.

## <a name="register"></a>Registrare l'app per l'autenticazione e configurare il servizio app
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Guardare un video che illustra una procedura simile](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

A questo punto, è possibile verificare che l'accesso anonimo al back-end è stato disabilitato. In Visual Studio:

* Aprire il progetto creato dopo avere completato l'esercitazione [Introduzione alle app per dispositivi mobili].
* Eseguire l'applicazione nell'**emulatore Android di Google**.
* Verificare che dopo l'avvio dell'applicazione venga visualizzato un errore di connessione imprevisto.

A questo punto, aggiornare l'app per autenticare gli utenti prima di richiedere risorse al back-end dell'app per dispositivi mobili.

## <a name="add-authentication"></a>Aggiungere l'autenticazione all'app
1. Aprire il progetto in **Visual Studio**, quindi aprire il file `www/index.html` per la modifica.
2. Individuare il meta tag `Content-Security-Policy` nella sezione di intestazione.  Aggiungere l'host di OAuth all'elenco di origini consentite.

   | Provider | Nome del provider SDK | Host OAuth |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | Twitter | https://api.twitter.com |

    Ecco un esempio di Content-Security-Policy implementato per Azure Active Directory:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Sostituire `https://login.microsoftonline.com` con l'host di OAuth indicato nella tabella precedente.  Per altre informazioni sul metatag content-security-policy, vedere la [documentazione su Content-Security-Policy].

    Alcuni provider di autenticazione non richiedono modifiche a Content-Security-Policy quando viene usato in dispositivi mobili appropriati.  Ad esempio, non sono richieste modifiche a Content-Security-Policy quando si usa l'autenticazione di Google in un dispositivo Android.

3. Aprire il file `www/js/index.js` per la modifica, individuare il metodo `onDeviceReady()` e nel codice di creazione del client aggiungere il codice seguente:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Questo codice sostituisce il codice esistente che crea il riferimento alla tabella e aggiorna l'interfaccia utente.

    Il metodo login() inizia l'autenticazione con il provider. Il metodo login() è una funzione asincrona che restituisce una promessa JavaScript.  Il resto dell'inizializzazione viene inserito nella risposta della promessa in modo che non venga eseguita finché non viene completato il metodo login().

4. Nel codice aggiunto, sostituire `SDK_Provider_Name` con il nome del provider di accesso. Ad esempio, per Azure Active Directory usare `client.login('aad')`.
5. Eseguire il progetto.  Al termine dell'inizializzazione del progetto, nell'applicazione viene visualizzata la pagina di accesso di OAuth per il provider di autenticazione scelto.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni, vedere [Autenticazione e autorizzazione] con il servizio app di Azure.
* Continuare l'esercitazione aggiungendo [Notifiche Push] all'app Apache Cordova.

Informazioni su come usare gli SDK.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs. -->
[Introduzione alle app per dispositivi mobili]: app-service-mobile-cordova-get-started.md
[documentazione su Content-Security-Policy]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Notifiche Push]: app-service-mobile-cordova-get-started-push.md
[Autenticazione e autorizzazione]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
