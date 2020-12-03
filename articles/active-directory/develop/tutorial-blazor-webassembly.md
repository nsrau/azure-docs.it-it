---
title: Esercitazione - Concedere l'accesso agli utenti e chiamare un'API protetta da un'app WebAssembly Blazor
titleSuffix: Microsoft identity platform
description: In questa esercitazione viene illustrato come consentire agli utenti di accedere e chiamare un'API protetta usando Microsoft Identity Platform in un'app WebAssembly Blazor (WASM).
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 09e922ffddcce732d9213eb91026561528c0728a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169138"
---
# <a name="tutorial-sign-in-users-and-call-a-protected-api-from-a-blazor-webassembly-app"></a>Esercitazione: Concedere l'accesso agli utenti e chiamare un'API protetta da un'app WebAssembly Blazor

In questa esercitazione verrà creata un'app Blazor WebAssembly che concede l'accesso agli utenti e recupera dati da Microsoft Graph usando Microsoft Identity Platform e registrando l'app in Azure Active Directory (Azure AD).

È anche disponibile un'esercitazione per [Blazor Server](tutorial-blazor-server.md). 

In questa esercitazione:

> [!div class="checklist"]
>
> * Creare una nuova app WebAssembly Blazor configurata per l'uso di Azure Active Directory (Azure AD) per l'[autenticazione e l'autorizzazione](authentication-vs-authorization.md) con Microsoft Identity Platform
> * Recuperare i dati da un'API Web protetta, in questo caso [Microsoft Graph](/graph/overview)

## <a name="prerequisites"></a>Prerequisiti

* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* Un tenant di Azure AD in cui poter registrare un'app. Se non si ha accesso a un tenant di Azure AD, è possibile ottenerne uno eseguendo la registrazione al programma [Microsoft 365 Developer Program](https://developer.microsoft.com/microsoft-365/dev-program) o creando un [account Azure gratuito](https://azure.microsoft.com/free).

## <a name="register-the-app-in-the-azure-portal"></a>Registrare l'app nel portale di Azure

Ogni app che usa Azure Active Directory (Azure AD) per l'autenticazione deve essere registrata con Azure AD. Seguire le istruzioni incluse in [Registrare un'applicazione](quickstart-register-app.md) con le specifiche seguenti:

- Per **Tipi di account supportati** selezionare **Account solo in questa directory dell'organizzazione**.
- Lasciare **URI di reindirizzamento** impostato su **Web** e immettere `https://localhost:5001/authentication/login-callback`. La porta predefinita per un'app in esecuzione in Kestrel è la 5001. Se l'app è disponibile su una porta diversa, specificare il numero di porta invece di `5001`.

Dopo la registrazione, in **Autenticazione** > **Concessione implicita** selezionare le caselle di controllo **Token di accesso** e **Token ID**, quindi selezionare il pulsante **Salva**.

## <a name="create-the-app-using-the-net-core-cli"></a>Creare l'app usando l'interfaccia della riga di comando di .NET Core

Per creare l'app sono necessari i modelli di Blazor più recenti. È possibile installarli dall'interfaccia della riga di comando di .NET Core con il comando seguente:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.1
```

Eseguire quindi il comando seguente per creare l'applicazione. Sostituire i segnaposto nel comando con le informazioni appropriate nella pagina di panoramica dell'app ed eseguire il comando in una shell dei comandi. Il percorso di output specificato con l'opzione `-o|--output` consente di creare una cartella di progetto se non esiste e viene incluso nel nome dell'app.

```dotnetcli
dotnet new blazorwasm2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Segnaposto   | Nome portale di Azure       | Esempio                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorWASMSample`                         |
| `{CLIENT ID}` | ID applicazione (client) | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | ID directory (tenant)   | `e86c78e2-0000-0000-0000-918e0565a45e` |

## <a name="test-the-app"></a>Testare l'app

È ora possibile compilare ed eseguire l'app. Quando si esegue questa app modello, è necessario specificare il framework da eseguire usando --framework. Questa esercitazione usa .NET Standard 2.1, ma il modello supporta anche altri framework.

```dotnetcli
dotnet run --framework netstandard2.1
```

Nel browser passare a `https://localhost:5001` e accedere usando un account utente Azure AD per vedere l'app in esecuzione mentre concede l'accesso agli utenti con Microsoft Identity Platform.

Per altre informazioni sui componenti di questo modello che consentono gli accessi con Azure AD tramite Microsoft Identity Platform, vedere [Proteggere un'app WebAssembly Blazor autonoma di ASP.NET Core con Azure Active Directory](/aspnet/core/blazor/security/webassembly/standalone-with-azure-active-directory#authentication-package).

## <a name="retrieving-data-from-microsoft-graph"></a>Recupero dei dati da Microsoft Graph

[Microsoft Graph](/graph/overview) include numerose API che consentono di accedere ai dati Microsoft 365 degli utenti nel tenant. Usando Microsoft Identity Platform come provider di identità per l'app, è possibile accedere più facilmente a queste informazioni perché Microsoft Graph supporta direttamente i token rilasciati da Microsoft Identity Platform. In questa sezione viene aggiunto il codice che consente di visualizzare i messaggi di posta elettronica dell'utente connesso nella pagina "Recupera dati" dell'applicazione.

Prima di iniziare, disconnettersi dall'app perché verranno apportate modifiche alle autorizzazioni obbligatorie e il token corrente non funzionerà. Se non è già stato fatto, eseguire di nuovo l'app e selezionare **Disconnetti** prima di aggiornare il codice seguente.

A questo punto occorre aggiornare la registrazione e il codice dell'app per eseguire il pull della posta elettronica di un utente e visualizzare i messaggi nell'app.

Prima di tutto, aggiungere l'autorizzazione API `Mail.Read` alla registrazione dell'app in modo da indicare ad Azure AD che l'app richiederà l'accesso alla posta elettronica dell'utente.

1. Nel portale di Azure selezionare l'app in **Registrazioni app**.
1. In **Gestisci** selezionare **Autorizzazioni API**.
1. Selezionare **Aggiungi un'autorizzazione** > **Microsoft Graph**.
1. Selezionare **Autorizzazioni delegate** e quindi cercare e selezionare l'autorizzazione **Mail.Read**.
1. Selezionare **Aggiungi autorizzazioni**.

Aggiungere quindi il codice seguente al file con estensione *csproj* del progetto in **ItemGroup** netstandard2.1. Questo permetterà di creare l'HttpClient personalizzato nel passaggio successivo.

```xml
<PackageReference Include="Microsoft.Extensions.Http" Version="3.1.7" />
```

Quindi, modificare il codice come specificato nei passaggi successivi. Queste modifiche aggiungeranno [token di accesso](access-tokens.md) alle richieste in uscita inviate all'API Microsoft Graph. Questo modello è illustrato in maggiore dettaglio in [Altri scenari di sicurezza dell'app WebAssembly Blazor di ASP.NET Core](/aspnet/core/blazor/security/webassembly/additional-scenarios).

Prima di tutto, creare un nuovo file denominato *GraphAuthorizationMessageHandler.cs* con il codice seguente. Questo gestore verrà usato per aggiungere un token di accesso per gli ambiti `User.Read` e `Mail.Read` alle richieste in uscita all'API Microsoft Graph.

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Mail.Read" });
    }
}
```

Sostituire quindi il contenuto del metodo `Main` in *Program.cs* con il codice seguente. Questo codice usa il nuovo `GraphAPIAuthorizationMessageHandler` e aggiunge `User.Read` e `Mail.Read` come ambiti predefiniti che l'app richiederà al primo accesso dell'utente.

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("app");

builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("User.Read");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("Mail.Read");
});

await builder.Build().RunAsync();
```

Infine, sostituire il contenuto della pagina *FetchData.razor* con il codice seguente. Questo codice recupera i dati della posta elettronica dell'utente dall'API Microsoft Graph e li visualizza come elenco. In `OnInitializedAsync` viene creato il nuovo `HttpClient` che usa il token di accesso appropriato, che viene quindi usato per inviare le richieste all'API Microsoft Graph.

```c#
@page "/fetchdata"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject IHttpClientFactory HttpClientFactory

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient("GraphAPI");
        try {
            var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

            if (dataRequest.IsSuccessStatusCode)
            {
                var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
                userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
            }

            var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

            if (mailRequest.IsSuccessStatusCode)
            {
                var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
                var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

                foreach (var m in messagesArray)
                {
                    var message = new MailMessage();
                    message.Subject = m.GetProperty("subject").GetString();
                    message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                    message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                    messages.Add(message);
                }
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            // Tokens are not valid - redirect the user to log in again
            ex.Redirect();
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}
```

Avviare di nuovo l'app. Verrà chiesto di concedere all'app l'accesso per leggere la posta elettronica. È il comportamento previsto quando un'app richiede l'ambito `Mail.Read`.

Dopo aver concesso il consenso, passare alla pagina "Recupera dati" per leggere un messaggio di posta elettronica.

:::image type="content" source="./media/tutorial-blazor-webassembly/final-app.png" alt-text="Screenshot dell'app finale. Include un'intestazione Hello Nicholas e mostra un elenco di messaggi di posta elettronica appartenenti a Nicholas.":::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedure consigliate e raccomandazioni per Microsoft Identity Platform](./identity-platform-integration-checklist.md)