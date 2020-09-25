---
title: includere file
description: includere file
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 77b1e9ab245f668ab81741451a5e032f37bc3625
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944592"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Versioni di Active LTS e Maintenance LTS [Node.js](https://nodejs.org/)(versioni consigliate 8.11.1 e 10.14.1).
- Una risorsa attiva di Servizi di comunicazione e una stringa di connessione. [Creare una risorsa di Servizi di comunicazione](../create-communication-resource.md).

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js

Aprire la finestra del terminale o di comando per creare una nuova directory per l'app e passare a tale directory.

```console
mkdir user-tokens-quickstart && cd user-tokens-quickstart
```

Eseguire `npm init -y` per creare un file **package.json** con le impostazioni predefinite.

```console
npm init -y
```

### <a name="install-the-package"></a>Installare il pacchetto

Usare il comando `npm install` per installare la libreria client di amministrazione di Servizi di comunicazione di Azure per JavaScript.

```console

npm install @azure/communication-administration --save

```

L'opzione `--save` elenca la libreria come dipendenza nel file **package.json**.

## <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Dalla directory del progetto:

1. Aprire un nuovo file di testo nell'editor del codice
1. Aggiungere una chiamata di `require` per caricare `CommunicationIdentityClient`
1. Creare la struttura per il programma, inclusa la gestione delle eccezioni di base

Per iniziare, usare il codice seguente:

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');

const main = async () => {
  console.log("Azure Communication Services - User Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. Salvare il nuovo file come **issue-token.js** nella directory *user-tokens-quickstart*.

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'istanza di `CommunicationIdentityClient` con la stringa di connessione. Il codice seguente recupera la stringa di connessione per la risorsa da una variabile di ambiente denominata `COMMUNICATION_SERVICES_CONNECTION_STRING`. Vedere come [gestire la stringa di connessione della risorsa](../create-communication-resource.md#store-your-connection-string).

Aggiungere al metodo `main` il codice seguente:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the user token client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-a-user"></a>Creare un utente

Servizi di comunicazione di Azure gestisce una directory di identità leggera. Usare il metodo `createUser` per creare una nuova voce nella directory con `Id` univoco. È necessario mantenere un mapping tra gli utenti dell'applicazione e le identità generate da Servizi di comunicazione, ad esempio archiviandoli nel database del server applicazioni.

```javascript
let userResponse = await identityClient.createUser();
console.log(`\nCreated a user with ID: ${userResponse.communicationUserId}`);
```

## <a name="issue-user-access-tokens"></a>Emettere i token di accesso utente

Usare il metodo `issueToken` per emettere un token di accesso per un utente di Servizi di comunicazione. Se non viene fornito il parametro `user` facoltativo, verrà creato un nuovo utente e restituito con il token.

```javascript
// Issue an access token with the "voip" scope for a new user
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued a token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

I token di accesso utente sono credenziali di breve durata che devono essere riemesse per impedire che si verifichino interruzioni del servizio per gli utenti. La proprietà di risposta `expiresOn` indica la durata del token.

## <a name="revoke-user-access-tokens"></a>Revocare i token di accesso utente

In alcuni casi potrebbe essere necessario revocare i token di accesso utente in modo esplicito, ad esempio quando un utente modifica la password usata per eseguire l'autenticazione al servizio. Questa operazione usa il metodo `revokeTokens` per invalidare tutti i token di accesso di un utente.

```javascript  
await identityClient.revokeTokens(userResponse);
console.log(`\nSuccessfully revoked all tokens for user with Id: ${userResponse.communicationUserId}`);
```

## <a name="delete-a-user"></a>Eliminare un utente

L'eliminazione di un utente determina la revoca di tutti i token attivi e impedisce l'emissione di token successivi per le identità. Viene anche rimosso tutto il contenuto persistente associato all'utente.

```javascript
await identityClient.deleteUser(userResponse);
console.log(`\nDeleted the user with Id: ${userResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>Eseguire il codice

Da un prompt della console passare alla directory contenente il file *issue-token.js* e quindi usare il comando `node` seguente per eseguire l'app.

```console
node ./issue-token.js
```
