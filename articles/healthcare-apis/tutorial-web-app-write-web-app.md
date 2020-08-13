---
title: Esercitazione su un'app Web - Scrivere un'applicazione Web
description: Questa esercitazione illustra un esempio di distribuzione di una semplice applicazione Web. Questa sezione dell'esercitazione descrive come scrivere l'applicazione Web.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: bb9c206a17a11f0cf710ac4ee3ac7ccae0fa9eb9
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87848027"
---
# <a name="write-azure-web-application-to-read-fhir-data"></a>Scrivere un'applicazione Web di Azure per leggere i dati FHIR
Ora che è possibile connettersi al server FHIR e pubblicare i dati, si è pronti per scrivere un'applicazione Web che leggerà i dati FHIR. In questo passaggio finale dell'esercitazione verrà illustrata la procedura dettagliata per la scrittura e l'accesso all'applicazione Web.

## <a name="create-web-application"></a>Creare l'applicazione Web
In Azure selezionare **Crea una risorsa** e quindi **App Web**. Assicurarsi di assegnare all'applicazione Web lo stesso nome specificato nell'URI di reindirizzamento per l'applicazione client oppure tornare indietro e aggiornare l'URI di reindirizzamento con il nuovo nome. 

![Creare l'applicazione Web](media/tutorial-web-app/create-web-app.png)

Quando l'applicazione Web è disponibile, selezionare **Vai alla risorsa**. Selezionare **Editor del servizio app (anteprima)** in Strumenti di sviluppo a destra e quindi selezionare **Vai**. Selezionando Vai si apre Editor del servizio app. Fare clic con il pulsante destro del mouse nello spazio grigio sotto *Esplora* e creare un nuovo file denominato **index.html**.

Di seguito è riportato il codice che è possibile inserire nel file **index.html**. Sarà necessario aggiornare gli elementi seguenti:
* **clientId**: aggiornare questo valore con l'ID applicazione client. Questo ID sarà identico a quello ottenuto con il recupero del token
* **authority**: aggiornare questo valore con l'ID tenant di Azure AD
* **FHIRendpoint**: aggiornare questo valore in modo che abbia il nome del servizio FHIR
* **scopes**: aggiornare questo valore in base all'URL completo del destinatario

``` HTML

<!DOCTYPE html>
<html>

<head>
    <title>FHIR Patient browser sample app</title>
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
</head>

<body>
    <div class="leftContainer">
        <p id="WelcomeMessage">Welcome to the FHIR Patient browsing sample Application</p>
        <button id="SignIn" onclick="signIn()">Sign In</button>
    </div>

    <div id="patientTable">
    </div>

    <script>
        var msalConfig = {
            auth: {
                clientId: '<CLIENT-ID>',
                authority: "https://login.microsoftonline.com/<AZURE-AD-TENANT-ID>"
            },
            cache: {
                cacheLocation: "localStorage",
                storeAuthStateInCookie: true
            }
        }

        var FHIRConfig = {
            FHIRendpoint: "https://<FHIR-SERVER-NAME>.azurehealthcareapis.com"
        }
        var requestObj = {
            scopes: ["https://<FHIR-SERVER-NAME>.azurehealthcareapis.com/user_impersonation"]
        }

        function authRedirectCallBack(error, response) {
            if (error) {
                console.log(error);
            } else {
                if (response.tokenType === "access_token") {
                    callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, response.accessToken, FHIRCallback);
                }
            }
        }

        var myMSALObj = new Msal.UserAgentApplication(msalConfig);
        myMSALObj.handleRedirectCallback(authRedirectCallBack);

        function signIn() {
            myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
                showWelcomeMessage();
                acquireTokenPopupAndCallFHIRServer();
            }).catch(function (error) {
                console.log(error);
            })
        }

        function showWelcomeMessage() {
            var divWelcome = document.getElementById('WelcomeMessage');
            divWelcome.innerHTML = "Welcome " + myMSALObj.getAccount().userName + " to FHIR Patient Browsing App";
            var loginbutton = document.getElementById('SignIn');
            loginbutton.innerHTML = 'Sign Out';
            loginbutton.setAttribute('onclick', 'signOut()')
        }

        function signOut() {
            myMSALObj.logout();
        }

        function acquireTokenPopupAndCallFHIRServer() {
            myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
                callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
            }).catch(function (error) {
                console.log(error);
                if (requiresInteraction(error.errorCode)) {
                    myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                        callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
                    }).catch(function (error) {
                        console.log(error);
                    })
                }
            });
        }

        function callFHIRServer(theUrl, method, message, accessToken, callBack) {
            var xmlHttp = new XMLHttpRequest();
            xmlHttp.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200)
                    callBack(JSON.parse(this.responseText));
            }
            xmlHttp.open(method, theUrl, true);
            xmlHttp.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
            xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
            xmlHttp.send(message);
        }

        function FHIRCallback(data) {
            patientListHtml = '<ol>';
            data.entry.forEach(function(e) {
                patientListHtml += '<li>' + e.resource.name[0].family + ', ' + e.resource.name[0].given + ' (' + e.resource.id + ')';
            });
            patientListHtml += '</ol>';
            document.getElementById("patientTable").innerHTML = patientListHtml;
        }
    </script>
</body>

</html>
```

Da qui è possibile tornare alla risorsa applicazione Web e aprire l'URL disponibile nella pagina Panoramica. Accedere per vedere il paziente James Tiberious Kirk creato in precedenza.

## <a name="next-steps"></a>Passaggi successivi
L'API di Azure per FHIR è stata distribuita correttamente, è stata registrata un'applicazione client pubblica, è stato testato l'accesso ed è stata creata una piccola applicazione Web. Come passaggio successivo, vedere le funzionalità supportate dall'API di Azure per FHIR.

>[!div class="nextstepaction"]
>[Funzionalità supportate](fhir-features-supported.md)





