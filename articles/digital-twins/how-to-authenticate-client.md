---
title: Scrivere il codice di autenticazione dell'app
titleSuffix: Azure Digital Twins
description: Vedere come scrivere il codice di autenticazione in un'applicazione client
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-javascript
ms.openlocfilehash: 1eebacefa2e91970263c301de4ff160ea7c9f2eb
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420346"
---
# <a name="write-client-app-authentication-code"></a>Scrivere il codice di autenticazione dell'app client

Dopo aver [configurato un'istanza di dispositivi gemelli digitali di Azure e l'autenticazione](how-to-set-up-instance-scripted.md), è possibile creare un'applicazione client che si userà per interagire con l'istanza. Dopo aver configurato un progetto client iniziale, in questo articolo viene illustrato **come scrivere il codice nell'app client per autenticarlo** nell'istanza di Azure Digital gemelli.

Sono disponibili due approcci per eseguire il codice di esempio in questo articolo. È possibile usare quello più adatto alle proprie esigenze, a seconda del linguaggio scelto:
* La prima sezione del codice di esempio USA Azure Digital Twins .NET (C#) SDK. L'SDK è incluso in Azure SDK per .NET ed è disponibile qui: [*libreria client gemelli digitali di Azure Internet per .NET*](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).
* La seconda sezione del codice di esempio è destinata agli utenti che non usano .NET SDK e usano invece gli SDK generati automaticamente da Rest in altri linguaggi. Per altre informazioni su questa strategia, vedere [*procedura: creare SDK personalizzati per i dispositivi gemelli digitali di Azure con autorest*](how-to-create-custom-sdks.md).

Per altre informazioni sulle API e sugli SDK per i dispositivi gemelli di Azure, vedere [*procedura: usare gli SDK e le API per i dispositivi digitali gemelli di Azure*](how-to-use-apis-sdks.md).

## <a name="prerequisites"></a>Prerequisiti

Per prima cosa, completare la procedura di configurazione in [*procedura: configurare un'istanza e l'autenticazione*](how-to-set-up-instance-scripted.md). In questo modo si avrà a disposizione un'istanza di Azure Digital gemelli, l'utente dispone delle autorizzazioni di accesso e sono state configurate le autorizzazioni per le applicazioni client. Dopo questa configurazione, è possibile scrivere il codice dell'app client.

Per continuare, sarà necessario un progetto di app client in cui si scrive il codice. Se non è già stato configurato un progetto di app client, creare un progetto di base nella lingua preferita da usare con questa esercitazione.

## <a name="authentication-and-client-creation-net-c-sdk"></a>Autenticazione e creazione di client: .NET (C#) SDK

Per prima cosa, includere i pacchetti seguenti nel progetto per usare .NET SDK e gli strumenti di autenticazione per questa procedura:
* `Azure.DigitalTwins.Core`(versione `1.0.0-preview.2` )
* `Azure.Identity`

A seconda degli strumenti scelti, è possibile includere i pacchetti tramite Gestione pacchetti di Visual Studio o lo strumento da `dotnet` riga di comando. 

Per eseguire l'autenticazione con .NET SDK, usare uno dei metodi per ottenere le credenziali definite nella libreria [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) .

Di seguito sono riportati due comunemente utilizzati: 
* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet). Questo metodo è destinato alle applicazioni interattive e consente di visualizzare un Web browser per l'autenticazione.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet). Questo metodo funziona in modo ottimale nei casi in cui sono necessarie [identità gestite (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), ad esempio quando si lavora con funzioni di Azure. 

Sono necessarie anche le istruzioni using seguenti:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

Per usare le credenziali del browser interattivo per creare un client SDK autenticato, aggiungere il codice seguente:

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> Sebbene sia possibile inserire l'ID client, l'ID tenant e l'URL dell'istanza direttamente nel codice, come illustrato in precedenza, è consigliabile fare in modo che il codice ottenga questi valori da un file di configurazione o da una variabile di ambiente.

In una funzione di Azure è quindi possibile usare le credenziali di identità gestite come questa:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

Vedere [*procedura: configurare una funzione di Azure per l'elaborazione dei dati*](how-to-create-azure-function.md) per un esempio più completo che illustra alcune delle importanti scelte di configurazione nel contesto delle funzioni.

Inoltre, per usare l'autenticazione in una funzione, ricordarsi di:
* [Abilitare l'identità gestita](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* Usare le [variabili di ambiente](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) nel modo appropriato
* Assegnare le autorizzazioni all'app funzioni che consente di accedere alle API dei dispositivi gemelli digitali. Per altre informazioni sui processi di funzioni di Azure, vedere [*procedura: configurare una funzione di Azure per l'elaborazione dei dati*](how-to-create-azure-function.md).

## <a name="authentication-with-an-autorest-generated-sdk"></a>Autenticazione con un SDK generato automaticamente

Se non si usa .NET, è possibile scegliere di creare una libreria SDK in un linguaggio di propria scelta, come descritto in [*procedura: creare SDK personalizzati per i dispositivi gemelli digitali di Azure con autorest*](how-to-create-custom-sdks.md).

In questa sezione viene illustrato come eseguire l'autenticazione in questo caso.

### <a name="prerequisites"></a>Prerequisiti

Per prima cosa, è necessario completare i passaggi per creare un SDK personalizzato con autorest, seguendo la procedura [*illustrata in procedura: creare SDK personalizzati per i dispositivi gemelli digitali di Azure con autorest*](how-to-create-custom-sdks.md).

Questo esempio usa un SDK typescript generato con autorest. Di conseguenza, è necessario anche:
* [MSAL-JS](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [MS-Rest-JS](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Esempio di codice di autenticazione minimo

Per autenticare un'app con i servizi di Azure, è possibile usare il codice minimo seguente nell'app client.

Sono necessari l'ID *dell'applicazione (client)* e la *Directory (tenant)* precedenti, nonché l'URL dell'istanza di Azure Digital gemelli.

> [!TIP]
> L'URL dell'istanza di Azure Digital gemelli viene creato aggiungendo *https://* all'inizio del *nome host*dell'istanza di Azure Digital gemelli. Per visualizzare il *nome host*, insieme a tutte le proprietà dell'istanza, è possibile eseguire `az dt show --dt-name <your-Azure-Digital-Twins-instance>` . È possibile usare il `az account show --query tenantId` comando per visualizzare l' *ID della directory (tenant)*. 

```javascript
import * as Msal from "msal";
import { TokenCredentials } from "@azure/ms-rest-js";
// Autorest-generated SDK
import { AzureDigitalTwinsAPI } from './azureDigitalTwinsAPI';

// Client / app registration ID
var ClientId = "<your-client-ID>";
// Azure tenant / directory ID
var TenantId = "<your-tenant-ID>";
// URL of the Azure Digital Twins instance
var AdtInstanceUrl = "<your-instance-URL>"; 

var AdtAppId = "https://digitaltwins.azure.net";

let client = null;

export async function login() {

    const msalConfig = {
        auth: {
            clientId: ClientId,
            redirectUri: "http://localhost:3000",
            authority: "https://login.microsoftonline.com/"+TenantId
        }
    };

    const msalInstance = new Msal.UserAgentApplication(msalConfig);

    msalInstance.handleRedirectCallback((error, response) => {
        // handle redirect response or error
    });

    var loginRequest = {
        scopes: [AdtAppId + "/.default"] 
    };

    try {
        await msalInstance.loginPopup(loginRequest)
        var accessToken;
        // if the user is already logged in you can acquire a token
        if (msalInstance.getAccount()) {
            var tokenRequest = {
                scopes: [AdtAppId + "/.default"]
            };
            try {
                const response = await msalInstance.acquireTokenSilent(tokenRequest);
                accessToken = response.accessToken;
            } catch (err) {
                if (err.name === "InteractionRequiredAuthError") {
                    const response = await msalInstance.acquireTokenPopup(tokenRequest)
                    accessToken = response.accessToken;
                }
            }
        }
        if (accessToken!=null)
        {
            var tokenCredentials = new TokenCredentials(accessToken);
                
            // Add token and server URL to service instance
            const clientConfig = {
                baseUri: AdtInstanceUrl
            };
            client = new AzureDigitalTwinsAPI(tokenCredentials, clientConfig);
            appDataStore.client = client;
        }
    } catch (err) {
        ...
    }
}
```

Si noti anche che, in cui il codice precedente posiziona l'ID client, l'ID tenant e l'URL dell'istanza direttamente nel codice per semplicità, è consigliabile fare in modo che il codice ottenga questi valori da un file di configurazione o da una variabile di ambiente.

MSAL offre molte altre opzioni che è possibile usare per implementare elementi come la memorizzazione nella cache e altri flussi di autenticazione. Per ulteriori informazioni, vedere [*Panoramica di Microsoft Authentication Library (MSAL)*](../active-directory/develop/msal-overview.md).

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sul funzionamento della sicurezza nei dispositivi gemelli digitali di Azure:
* [*Concetti: sicurezza per le soluzioni di dispositivi gemelli digitali di Azure*](concepts-security.md)

In alternativa, ora che l'autenticazione è configurata, procedere alla creazione di modelli nell'istanza:
* [*Procedura: Gestire modelli personalizzati*](how-to-manage-model.md)