---
title: Autenticare un'applicazione client
titleSuffix: Azure Digital Twins
description: Vedere come autenticare un'applicazione client nel servizio dispositivi gemelli digitali di Azure.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e52307c92d9371af6479f64841c6f269ed10e4b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85390823"
---
# <a name="authenticate-a-client-application-with-azure-digital-twins"></a>Autenticare un'applicazione client con i dispositivi gemelli digitali di Azure

Dopo aver [creato un'istanza di dispositivi gemelli digitali di Azure](how-to-set-up-instance.md), è possibile creare un'applicazione client che si userà per interagire con l'istanza. Dopo aver configurato un progetto client iniziale, in questo articolo viene illustrato come autenticare correttamente l'applicazione client con l'istanza di Azure Digital gemelli.

Questa operazione viene eseguita in due passaggi:
1. Creare la registrazione di un'app
2. Scrivere il codice di autenticazione in un'applicazione client

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-an-app-registration"></a>Creare la registrazione di un'app

Per eseguire l'autenticazione con i dispositivi gemelli digitali di Azure da un'applicazione client, è necessario configurare una **registrazione dell'app** in [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md).

Questa registrazione dell'app è la posizione in cui vengono configurate le autorizzazioni di accesso alle [API di Azure Digital gemelli](how-to-use-apis-sdks.md) L'app client esegue l'autenticazione con la registrazione dell'app e, di conseguenza, vengono concesse le autorizzazioni di accesso configurate alle API.

Per creare una registrazione dell'app, è necessario fornire gli ID risorsa per le API di Azure Digital gemelli e le autorizzazioni di base per l'API. Nella directory di lavoro aprire un nuovo file e immettere il frammento di codice JSON seguente per configurare i dettagli: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Salvare il file come *manifest.js*.

> [!NOTE] 
> Ci sono alcune posizioni in cui è possibile usare una stringa leggibile "intuitiva" `https://digitaltwins.azure.net` per l'ID dell'app per le risorse di Azure Digital gemelli anziché il GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Molti esempi in questo set di documentazione, ad esempio, utilizzano l'autenticazione con la libreria MSAL e la stringa descrittiva può essere utilizzata. Tuttavia, durante questo passaggio della creazione della registrazione dell'app, il formato GUID dell'ID è necessario come illustrato in precedenza. 

Nella finestra di Cloud Shell fare clic sull'icona "Carica/Scarica file" e scegliere "carica".

:::image type="content" source="media/how-to-authenticate-client/upload-extension.png" alt-text="Cloud Shell finestra che mostra la selezione dell'opzione di caricamento":::
Passare almanifest.jsappena creato e fare clic *su* "Apri".

Eseguire quindi il comando seguente per creare una registrazione dell'app (sostituendo i segnaposto in base alle esigenze):

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

L'output di questo comando ha un aspetto simile al seguente.

:::image type="content" source="media/how-to-authenticate-client/new-app-registration.png" alt-text="Nuova registrazione per l'app AAD":::

Dopo la creazione della registrazione dell'app, seguire [questo collegamento](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) per passare alla pagina Panoramica della registrazione dell'app AAD nel portale di Azure.

Da questa panoramica selezionare la registrazione dell'app appena creata dall'elenco. In questo modo i dettagli vengono aperti in una pagina simile alla seguente:

:::image type="content" source="media/how-to-authenticate-client/get-authentication-ids.png" alt-text="Portale di Azure: ID autenticazione":::

Prendere nota dell'ID dell' *applicazione (client)* e della *Directory (tenant)* visualizzati nella **pagina.** Questi valori vengono usati in un secondo momento per autenticare un'app client nelle API dei dispositivi gemelli digitali di Azure.

> [!NOTE]
> A seconda dello scenario, potrebbe essere necessario apportare ulteriori modifiche alla registrazione dell'app. Ecco alcuni requisiti comuni che potrebbero essere necessari per soddisfare:
> * Attivare l'accesso client pubblico
> * Impostare URL di risposta specifici per l'accesso Web e desktop
> * Consenti i flussi di autenticazione OAuth2 impliciti
> * Se la sottoscrizione di Azure viene creata usando un account Microsoft come Live, Xbox o Hotmail, è necessario impostare *signInAudience* nella registrazione dell'app per supportare gli account personali.
> Il modo più semplice per configurare queste impostazioni consiste nell'usare il [portale di Azure](https://portal.azure.com/). Per altre informazioni su questo processo, vedere [registrare un'applicazione con la piattaforma di identità Microsoft](https://docs.microsoft.com/graph/auth-register-app-v2).

## <a name="write-client-app-authentication-code-net-c-sdk"></a>Scrivere il codice di autenticazione dell'app client: .NET (C#) SDK

In questa sezione viene descritto il codice che è necessario includere nell'applicazione client per completare il processo di autenticazione tramite .NET (C#) SDK.
Azure Digital Twins C# SDK è parte di Azure SDK per .NET. Si trova qui: [libreria client dei dispositivi digitali gemelli di Azure per .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).

### <a name="prerequisites"></a>Prerequisiti

Se non è già stato configurato un progetto di app client iniziale, creare un progetto .NET di base da usare con questa esercitazione.

Per usare .NET SDK, è necessario includere nel progetto i pacchetti seguenti:
* `Azure.DigitalTwins.Core`(versione `1.0.0-preview.2` )
* `Azure.Identity`

A seconda degli strumenti scelti, è possibile eseguire questa operazione con gestione pacchetti di Visual Studio o lo `dotnet` strumento da riga di comando. 

### <a name="authentication-and-client-creation-net"></a>Autenticazione e creazione di client: .NET

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

Vedere [procedura: configurare una funzione di Azure per l'elaborazione dei dati](how-to-create-azure-function.md) per un esempio più completo che illustra alcune delle importanti scelte di configurazione nel contesto delle funzioni.

Inoltre, per usare l'autenticazione in una funzione, ricordarsi di:
* [Abilitare un'identità gestita](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [Variabili di ambiente](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)
* Assegnare le autorizzazioni all'app funzioni che consente di accedere alle API dei dispositivi gemelli digitali. Per altre informazioni [, vedere Procedura: configurare una funzione di Azure per l'elaborazione dei dati](how-to-create-azure-function.md) .

## <a name="authentication-in-an-autorest-generated-sdk"></a>Autenticazione in un SDK generato automaticamente

Se non si usa .NET, è possibile scegliere di creare una libreria SDK in un linguaggio di propria scelta, come descritto in [procedura: creare SDK personalizzati per i dispositivi gemelli digitali di Azure con autorest](how-to-create-custom-sdks.md).

In questa sezione viene illustrato come eseguire l'autenticazione in questo caso.

### <a name="prerequisites"></a>Prerequisiti

Questo esempio usa un SDK typescript generato con autorest. Di conseguenza, è necessario anche:
* [MSAL-JS](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [MS-Rest-JS](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Esempio di codice di autenticazione minimo

Per autenticare un'app .NET con i servizi di Azure, è possibile usare il codice minimo seguente nell'app client.

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

MSAL offre molte altre opzioni che è possibile usare per implementare elementi come la memorizzazione nella cache e altri flussi di autenticazione. Per ulteriori informazioni, vedere [Panoramica di Microsoft Authentication Library (MSAL)](../active-directory/develop/msal-overview.md).

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sul funzionamento della sicurezza nei dispositivi gemelli digitali di Azure:
* [Concetti: sicurezza per le soluzioni di dispositivi gemelli digitali di Azure](concepts-security.md)

In alternativa, ora che l'autenticazione è configurata, procedere alla creazione di modelli nell'istanza:
* [Procedura: gestire modelli personalizzati](how-to-manage-model.md)