---
title: Scrivere il codice di autenticazione dell'app
titleSuffix: Azure Digital Twins
description: Vedere come scrivere il codice di autenticazione in un'applicazione client
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bf7b829d70af27850affe619d47ed4a4f5ec1bea
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93279917"
---
# <a name="write-client-app-authentication-code"></a>Scrivere il codice di autenticazione dell'app client

Dopo aver [configurato un'istanza di dispositivi gemelli digitali di Azure e l'autenticazione](how-to-set-up-instance-portal.md), è possibile creare un'applicazione client che si userà per interagire con l'istanza. Dopo aver configurato un progetto client iniziale, sarà necessario **scrivere il codice nell'app client per autenticarlo** nell'istanza di Azure Digital Twins.

I dispositivi gemelli digitali di Azure eseguono l'autenticazione usando [Azure ad token di sicurezza basati su OAUTH 2,0](../active-directory/develop/security-tokens.md#json-web-tokens-jwts-and-claims). Per autenticare l'SDK, è necessario ottenere una bearer token con le autorizzazioni appropriate per i dispositivi gemelli digitali di Azure e passarla insieme alle chiamate API. 

Questo articolo descrive come ottenere le credenziali usando la `Azure.Identity` libreria client. Questo articolo illustra esempi di codice in C#, ad esempio quello che si scriverebbe per [.NET (c#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true), è possibile usare una versione di `Azure.Identity` indipendentemente dall'SDK in uso. per altre informazioni sugli SDK disponibili per i dispositivi gemelli digitali di Azure, vedere [*procedura: usare le API e gli SDK di dispositivi digitali gemelli di Azure*](how-to-use-apis-sdks.md).

## <a name="prerequisites"></a>Prerequisiti

Per prima cosa, completare la procedura di configurazione in [*procedura: configurare un'istanza e l'autenticazione*](how-to-set-up-instance-portal.md). In questo modo sarà possibile disporre di un'istanza di dispositivi gemelli digitali di Azure e che l'utente disponga delle autorizzazioni di accesso. Dopo questa configurazione, è possibile scrivere il codice dell'app client.

Per continuare, sarà necessario un progetto di app client in cui si scrive il codice. Se non è già stato configurato un progetto di app client, creare un progetto di base nella lingua preferita da usare con questa esercitazione.

## <a name="common-authentication-methods-with-azureidentity"></a>Metodi di autenticazione comuni con Azure. Identity

`Azure.Identity` è una libreria client che fornisce diversi metodi per ottenere le credenziali che è possibile usare per ottenere un bearer token ed eseguire l'autenticazione con l'SDK. Sebbene in questo articolo vengano forniti esempi in C#, è possibile visualizzare `Azure.Identity` diverse lingue, tra cui...

* [.NET (C#)](/dotnet/api/azure.identity?preserve-view=true&view=azure-dotnet)
* [Java](/java/api/overview/azure/identity-readme?preserve-view=true&view=azure-java-stable)
* [JavaScript](/javascript/api/overview/azure/identity-readme?preserve-view=true&view=azure-node-latest)
* [Python](/python/api/overview/azure/identity-readme?preserve-view=true&view=azure-python)

Tre metodi comuni di recupero delle credenziali in `Azure.Identity` sono:

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) fornisce un `TokenCredential` flusso di autenticazione predefinito per le applicazioni che verranno distribuite in Azure ed è **la scelta consigliata per lo sviluppo locale**. È anche possibile abilitare per provare gli altri due metodi consigliati in questo articolo. esegue il wrapping `ManagedIdentityCredential` e può accedere `InteractiveBrowserCredential` con una variabile di configurazione.
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?preserve-view=true&view=azure-dotnet) funziona benissimo nei casi in cui sono necessarie [identità gestite (MSI)](../active-directory/managed-identities-azure-resources/overview.md)ed è un buon candidato per l'uso di funzioni di Azure e la distribuzione nei servizi di Azure.
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet) è destinato alle applicazioni interattive e può essere usato per creare un client SDK autenticato

Nell'esempio seguente viene illustrato come utilizzare ognuno di questi con .NET (C#) SDK.

## <a name="authentication-examples-net-c-sdk"></a>Esempi di autenticazione: .NET (C#) SDK

Questa sezione illustra un esempio in C# per l'uso di .NET SDK fornito per scrivere codice di autenticazione.

In primo luogo, includere il pacchetto SDK `Azure.DigitalTwins.Core` e il `Azure.Identity` pacchetto nel progetto. A seconda degli strumenti scelti, è possibile includere i pacchetti tramite Gestione pacchetti di Visual Studio o lo strumento da `dotnet` riga di comando. 

Sarà anche necessario aggiungere le istruzioni using seguenti al codice del progetto:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

Aggiungere quindi il codice per ottenere le credenziali utilizzando uno dei metodi in `Azure.Identity` .

### <a name="defaultazurecredential-method"></a>Metodo DefaultAzureCredential

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) fornisce un `TokenCredential` flusso di autenticazione predefinito per le applicazioni che verranno distribuite in Azure ed è **la scelta consigliata per lo sviluppo locale**.

Per usare le credenziali di Azure predefinite, è necessario l'URL dell'istanza di Azure Digital Twins ([istruzioni per la ricerca](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Ecco un esempio di codice per aggiungere un `DefaultAzureCredential` al progetto:

```csharp
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new DefaultAzureCredential();
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

#### <a name="set-up-local-azure-credentials"></a>Configurare le credenziali di Azure locali

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>Metodo ManagedIdentityCredential

Il metodo [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?preserve-view=true&view=azure-dotnet) funziona in modo ottimale nei casi in cui sono necessarie [identità gestite (MSI)](../active-directory/managed-identities-azure-resources/overview.md), ad esempio quando si lavora con funzioni di Azure.

Ciò significa che è possibile utilizzare `ManagedIdentityCredential` nello stesso progetto di `DefaultAzureCredential` o `InteractiveBrowserCredential` per autenticare una parte diversa del progetto.

Per usare le credenziali di Azure predefinite, è necessario l'URL dell'istanza di Azure Digital Twins ([istruzioni per la ricerca](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

In una funzione di Azure è possibile usare le credenziali di identità gestite come la seguente:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

### <a name="interactivebrowsercredential-method"></a>Metodo InteractiveBrowserCredential

Il metodo [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet) è destinato alle applicazioni interattive e consente di visualizzare un browser Web per l'autenticazione. È possibile usarlo anziché `DefaultAzureCredential` nei casi in cui è necessaria l'autenticazione interattiva.

Per usare le credenziali del browser interattivo, è necessaria una **registrazione dell'app** con le autorizzazioni per le API dei dispositivi gemelli digitali di Azure. Per i passaggi su come configurare questa registrazione per l'app, vedere [*procedura: creare una registrazione dell'*](how-to-create-app-registration.md)app. Una volta configurata la registrazione dell'app, sarà necessario...
* *ID dell'applicazione (client)* di registrazione dell'app ( [istruzioni da trovare](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* *ID directory (tenant)* della registrazione dell'app ( [istruzioni da trovare](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* URL dell'istanza di Azure Digital Twins ([istruzioni da trovare](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))

Di seguito è riportato un esempio di codice per la creazione di un client SDK autenticato usando `InteractiveBrowserCredential` .

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

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

#### <a name="other-notes-about-authenticating-azure-functions"></a>Altre note sull'autenticazione di funzioni di Azure

Vedere [*procedura: configurare una funzione di Azure per l'elaborazione dei dati*](how-to-create-azure-function.md) per un esempio più completo che illustra alcune delle importanti scelte di configurazione nel contesto delle funzioni.

Inoltre, per usare l'autenticazione in una funzione, ricordarsi di:
* [Abilitare l'identità gestita](../app-service/overview-managed-identity.md?tabs=dotnet)
* Usare le [variabili di ambiente](/sandbox/functions-recipes/environment-variables?tabs=csharp) nel modo appropriato
* Assegnare le autorizzazioni all'app funzioni che consente di accedere alle API dei dispositivi gemelli digitali. Per altre informazioni sui processi di funzioni di Azure, vedere [*procedura: configurare una funzione di Azure per l'elaborazione dei dati*](how-to-create-azure-function.md).

## <a name="other-credential-methods"></a>Altri metodi Credential

Se gli scenari di autenticazione evidenziati sopra non coprono le esigenze dell'app, è possibile esplorare altri tipi di autenticazione offerti nella [**piattaforma di identità Microsoft**](../active-directory/develop/v2-overview.md#getting-started). La documentazione relativa a questa piattaforma riguarda scenari di autenticazione aggiuntivi, organizzati in base al tipo di applicazione.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sul funzionamento della sicurezza nei dispositivi gemelli digitali di Azure:
* [*Concetti: sicurezza per le soluzioni di dispositivi gemelli digitali di Azure*](concepts-security.md)

In alternativa, ora che l'autenticazione è configurata, procedere alla creazione di modelli nell'istanza:
* [*Procedura: Gestire modelli personalizzati*](how-to-manage-model.md)