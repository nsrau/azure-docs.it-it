---
title: Servizio attendibile con JavaScript
description: Si tratta della versione JavaScript della creazione di un servizio attendibile per Servizi di comunicazione.
author: dademath
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/28/2020
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: d4ef8baa123f805d380b14fa24abff65903cb41d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90944201"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. Per informazioni dettagliate, vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Visual Studio Code](https://code.visualstudio.com/) in una delle [piattaforme supportate](https://code.visualstudio.com/docs/supporting/requirements#_platforms).
- [Node.js](https://nodejs.org/), versioni Active LTS e Maintenance LTS (10.14.1 consigliata). Usare il comando `node --version` per controllare la versione in uso. 
- [Estensione Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per Visual Studio Code. 
- Una stringa di connessione e una risorsa attiva di Servizi di comunicazione. [Creare una risorsa di Servizi di comunicazione](../../quickstarts/create-communication-resource.md).

## <a name="overview"></a>Panoramica

:::image type="content" source="../media/trusted-service-architecture.png" alt-text="Diagramma dell'architettura del servizio attendibile":::

Per questa esercitazione verrà creata una funzione di Azure che fungerà da servizio di provisioning per il token attendibile. È possibile usare questa esercitazione per il bootstrap del servizio di provisioning dei token.

Questo servizio è responsabile dell'autenticazione degli utenti a Servizi di comunicazione di Azure. Gli utenti delle applicazioni di Servizi di comunicazione richiedono un oggetto `Access Token` per partecipare ai thread di chat e alle chiamate VoIP. La funzione di Azure agisce come un intermediario attendibile tra l'utente e Servizi di comunicazione. In questo modo, è possibile effettuare il provisioning dei token di accesso senza esporre la stringa di connessione della risorsa agli utenti.

Per altre informazioni, vedere la documentazione concettuale relativa all'[architettura client-server](../../concepts/client-and-server-architecture.md) e alle funzionalità di [autenticazione e autorizzazione](../../concepts/authentication.md).

## <a name="setting-up"></a>Configurazione

### <a name="azure-functions-set-up"></a>Configurazione di Funzioni di Azure

È innanzitutto necessario configurare la struttura di base per la funzione di Azure. Le istruzioni dettagliate sulla configurazione sono disponibili qui: [Creare una funzione con Visual Studio Code](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript)

Per la funzione di Azure sono necessarie le configurazioni seguenti:

- Language: JavaScript
- Template: Trigger HTTP
- Livello di autorizzazione: anonimo (se si preferisce un modello di autorizzazione diverso, questo passaggio può essere eseguito in un secondo momento)
- Nome funzione: definito dall'utente

Dopo aver seguito le [istruzioni di Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript) con la configurazione precedente, è necessario disporre di un progetto in Visual Studio Code per la funzione di Azure con un file `index.js` contenente la funzione stessa. Il codice all'interno di questo file deve essere il seguente:

```javascript

module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}

```

Si procederà con l'installazione delle librerie di Servizi di comunicazione di Azure.

### <a name="install-communication-services-libraries"></a>Installare le librerie di Servizi di comunicazione

Per generare `User Access Tokens`, verrà usata la libreria `Administration`.

Usare il comando `npm install` per installare la libreria client di amministrazione di Servizi di comunicazione di Azure per JavaScript.

```console

npm install @azure/communication-administration --save

```

L'opzione `--save` elenca la libreria come dipendenza nel file **package.json**.

Nella parte superiore del file `index.js` importare l'interfaccia per `CommunicationIdentityClient`

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');
```

## <a name="access-token-generation"></a>Generazione del token di accesso

Per consentire alla funzione di Azure di generare `User Access Tokens`, è innanzitutto necessario usare la stringa di connessione per la risorsa di Servizi di comunicazione.

Per altre informazioni sul recupero della stringa di connessione, vedere la [guida di avvio rapido al provisioning delle risorse](../../quickstarts/create-communication-resource.md).

``` javascript
const connectionString = 'INSERT YOUR RESOURCE CONNECTION STRING'
```

Successivamente, la funzione originale verrà modificata per generare `User Access Tokens`. 

`User Access Tokens` vengono generati creando un utente dal metodo `createUser`. Una volta creato l'utente, è possibile usare il metodo `issueToken` per generare un token per l'utente restituito dalla funzione di Azure.

Per questo esempio, verrà configurato l'ambito del token per `voip`. Per l'applicazione possono essere necessari altri ambiti. Altre informazioni sugli [ambiti](../../quickstarts/access-tokens.md).

```javascript
module.exports = async function (context, req) {
    let tokenClient = new CommunicationIdentityClient(connectionString);

    const user = await tokenClient.createUser();

    const userToken = await tokenClient.issueToken(user, ["voip"]);

    const response = {
        "User" : userToken.user,
        "Token": userToken.token,
        "ExpiresOn": userToken.expiresOn
    }

    context.res = {
        body: response
    };
}
```

Per `CommunicationUser` di Servizi di comunicazione, è possibile ignorare il passaggio relativo alla creazione e generare semplicemente un token di accesso. Per altre informazioni, vedere [Avvio rapido: Creare e gestire i token di accesso utente](../../quickstarts/access-tokens.md).

## <a name="test-the-azure-function"></a>Testare la funzione di Azure

Eseguire la funzione di Azure in locale premendo `F5`. Questa operazione consente di inizializzare la funzione di Azure in locale e la rende accessibile tramite: `http://localhost:7071/api/FUNCTION_NAME`. Vedere la documentazione aggiuntiva sull'[esecuzione in locale](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript#run-the-function-locally)

Aprire l'URL nel browser per visualizzare il corpo della risposta con l'ID utente di comunicazione, il token e la scadenza del token.

:::image type="content" source="../media/trusted-service-sample-response.png" alt-text="Diagramma dell'architettura del servizio attendibile":::

## <a name="deploy-the-function-to-azure"></a>Distribuire la funzione in Azure

Per distribuire la funzione di Azure, è possibile seguire le [istruzioni dettagliate](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript#sign-in-to-azure).

In generale, sarà necessario:
1. Accedere ad Azure da Visual Studio.
2. Pubblicare il progetto nell'account Azure. Scegliere una sottoscrizione esistente.
3. Creare una nuova risorsa della funzione di Azure usando la procedura guidata di Visual Studio o usare una risorsa esistente. Per una nuova risorsa, sarà necessario configurarla per l'area, il runtime e l'identificatore univoco desiderati.
4. Attendere il completamento della distribuzione.
5. Eseguire la funzione. 🎉

## <a name="run-azure-function"></a>Eseguire la funzione di Azure

Eseguire la funzione di Azure tramite l'URL `http://<function-appn-ame>.azurewebsites.net/api/<function-name>`

È possibile trovare l'URL facendo clic con il pulsante destro del mouse sulla funzione in Visual Studio Code e copiando l'URL della funzione.

Altre informazioni sull'[esecuzione della funzione di Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript#run-the-function-in-azure)
