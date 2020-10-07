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
ms.openlocfilehash: 5c9066f369183de3b4cfe19cc5635e8f1b4a94a2
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779363"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- La versione più recente della [libreria client .NET Core](https://dotnet.microsoft.com/download/dotnet-core) per il sistema operativo in uso.
- Una risorsa attiva di Servizi di comunicazione e una stringa di connessione. [Creare una risorsa di Servizi di comunicazione](../create-communication-resource.md).

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `UserAccessTokensQuickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: **Program.cs**.

```console
dotnet new console -o UserAccessTokensQuickstart
```

Impostare la directory sulla cartella dell'app appena creata e usare il comando `dotnet build` per compilare l'applicazione.

```console
cd UserAccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installare il pacchetto

Rimanendo nella directory dell'applicazione, installare la libreria di amministrazione di Servizi di comunicazione di Azure per il pacchetto .NET usando il comando `dotnet add package`.

```console
dotnet add package Azure.Communication.Administration --version 1.0.0-beta.2
```

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Dalla directory del progetto:

1. Aprire il file **Program.cs** in un editor di testo
1. Aggiungere una direttiva `using` per includere lo spazio dei nomi `Azure.Communication.Administration`
1. Aggiornare la dichiarazione del metodo `Main` per supportare codice asincrono

Per iniziare, usare il codice seguente:

```csharp
using System;
using Azure.Communication.Administration;

namespace UserAccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - User Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Autenticare il client

Inizializzare `CommunicationIdentityClient` con la stringa di connessione. Il codice seguente recupera la stringa di connessione per la risorsa da una variabile di ambiente denominata `COMMUNICATION_SERVICES_CONNECTION_STRING`. Vedere come [gestire la stringa di connessione della risorsa](../create-communication-resource.md#store-your-connection-string).

Aggiungere al metodo `Main` il codice seguente:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string ConnectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(ConnectionString);
```

## <a name="create-a-user"></a>Creare un utente

Servizi di comunicazione di Azure gestisce una directory di identità leggera. Usare il metodo `createUser` per creare una nuova voce nella directory con `Id` univoco. È necessario mantenere un mapping tra gli utenti dell'applicazione e le identità generate da Servizi di comunicazione, ad esempio archiviandoli nel database del server applicazioni.

```csharp
var userResponse = await client.CreateUserAsync();
var user = userResponse.Value;
Console.WriteLine($"\nCreated a user with ID: {user.Id}");
```

## <a name="issue-user-access-tokens"></a>Emettere i token di accesso utente

Usare il metodo `issueToken` per emettere un token di accesso per un utente di Servizi di comunicazione. Se non viene fornito il parametro `user` facoltativo, verrà creato un nuovo utente e restituito con il token.

```csharp
// Issue an access token with the "voip" scope for a new user
var tokenResponse = await client.IssueTokenAsync(user, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued a token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

I token di accesso utente sono credenziali di breve durata che devono essere riemesse per impedire che si verifichino interruzioni del servizio per gli utenti. La proprietà di risposta `expiresOn` indica la durata del token.

## <a name="revoke-user-access-tokens"></a>Revocare i token di accesso utente

In alcuni casi potrebbe essere necessario revocare i token di accesso utente in modo esplicito, ad esempio quando un utente modifica la password usata per eseguire l'autenticazione al servizio. Questa funzionalità è disponibile tramite la libreria client di amministrazione di Servizi di comunicazione di Azure.

```csharp  
await client.RevokeTokensAsync(user);
Console.WriteLine($"\nSuccessfully revoked all tokens for user with ID: {user.Id}");
```

## <a name="delete-a-user"></a>Eliminare un utente

L'eliminazione di un'identità determina la revoca di tutti i token attivi e impedisce l'emissione di token successivi per le identità. Viene anche rimosso tutto il contenuto persistente associato all'utente.

```csharp
await client.DeleteUserAsync(user);
Console.WriteLine($"\nDeleted the user with ID: {user.Id}");
```

## <a name="run-the-code"></a>Eseguire il codice

Eseguire l'applicazione dalla directory dell'applicazione con il comando `dotnet run`.

```console
dotnet run
```
