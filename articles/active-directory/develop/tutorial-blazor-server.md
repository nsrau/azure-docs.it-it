---
title: "Esercitazione: Creare un'app Blazor Server che usa Microsoft Identity Platform per l'autenticazione | Azure"
titleSuffix: Microsoft identity platform
description: In questa esercitazione si configurerà l'autenticazione usando Microsoft Identity Platform in un'app Blazor Server.
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: aaf716b4ac4c49f1d852e917ba818a10ecb541c4
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628034"
---
# <a name="tutorial-create-a-blazor-server-app-that-uses-the-microsoft-identity-platform-for-authentication"></a>Esercitazione: Creare un'app Blazor Server che usa Microsoft Identity Platform per l'autenticazione

Blazor Server fornisce il supporto per ospitare componenti Razor nel server in un'app ASP.NET Core. Questa esercitazione descrive come implementare l'autenticazione e recuperare i dati da Microsoft Graph in un'app Blazor Server usando Microsoft Identity Platform e registrando l'app in Azure Active Directory (Azure AD).

È anche disponibile un'esercitazione per [Blazor WASM](tutorial-blazor-webassembly.md).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una nuova app Blazor Server configurata per l'autenticazione con Azure Active Directory (Azure AD)
> * Gestire autenticazione e autorizzazione con Microsoft.Identity.Web
> * Recuperare i dati da un'API Web protetta, Microsoft Graph

## <a name="prerequisites"></a>Prerequisiti

- [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
- Un tenant di Azure AD in cui poter registrare un'app. Se non si ha accesso a un tenant di Azure AD, è possibile ottenerne uno eseguendo la registrazione al programma [Microsoft 365 Developer Program](https://developer.microsoft.com/microsoft-365/dev-program) oppure creando un [account Azure gratuito](https://azure.microsoft.com/free).

## <a name="register-the-app-in-the-azure-portal"></a>Registrare l'app nel portale di Azure

Ogni app che usa Azure Active Directory (Azure AD) per l'autenticazione deve essere registrata con Azure AD. Seguire le istruzioni incluse in [Registrare un'applicazione](quickstart-register-app.md) aggiungendo i passaggi seguenti:

- Per **Tipi di account supportati** selezionare **Account solo in questa directory dell'organizzazione**.
- Lasciare **URI di reindirizzamento** impostato su **Web** e immettere `https://localhost:5001/signin-oidc`. La porta predefinita per un'app in esecuzione in Kestrel è la 5001. Se l'app è disponibile su una porta diversa, specificare il numero di porta invece di `5001`.

In **Autenticazione** > **Concessione implicita**, selezionare le caselle di controllo per **Token di accesso** e **Token ID** e quindi selezionare il pulsante **Salva**.

Infine, dal momento che l'app chiama un'API protetta (in questo caso Microsoft Graph), necessita di un segreto client per verificarne l'identità quando richiede un token di accesso per chiamare tale API.

1. Nella stessa registrazione dell'app selezionare **Certificati e segreti** in **Gestisci**.
2. Creare un **nuovo segreto client** senza scadenza.
3. Prendere nota del **valore** del segreto perché verrà usato nel passaggio successivo. Non sarà più possibile accedervi una volta usciti dal pannello, ma sarà possibile ricrearlo se necessario.

## <a name="create-the-app-using-the-net-cli"></a>Creare l'app usando l'interfaccia della riga di comando di .NET

Eseguire il comando seguente per scaricare i modelli per Microsoft.Identity.Web, che verranno usati in questa esercitazione.

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::0.4.0-preview
```

Eseguire quindi il comando seguente per creare l'applicazione. Sostituire i segnaposto nel comando con le informazioni appropriate nella pagina di panoramica dell'app ed eseguire il comando in una shell dei comandi. Il percorso di output specificato con l'opzione `-o|--output` consente di creare una cartella di progetto se non esiste e viene incluso nel nome dell'app.

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Segnaposto   | Nome portale di Azure       | Esempio                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | ID applicazione (client) | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | ID directory (tenant)   | `e86c78e2-0000-0000-0000-918e0565a45e` |

Passare ora alla nuova app Blazor nell'editor e aggiungere il segreto client al file *appsettings.json*, sostituendo il testo "secret-from-app-registration".

```json
"ClientSecret": "xkAlNiG70000000_UI~d.OS4Dl.-Cy-1m3",
```

## <a name="test-the-app"></a>Testare l'app

È ora possibile compilare ed eseguire l'app. Quando si esegue questa app modello, è necessario specificare il framework da eseguire usando --framework. Per questa esercitazione si usa .NET Core 3.1 SDK.

```dotnetcli
dotnet run --framework netcoreapp3.1
```

Nel browser passare a `https://localhost:5001` e accedere usando un account utente Azure AD per vedere l'app in esecuzione.

## <a name="retrieving-data-from-microsoft-graph"></a>Recupero dei dati da Microsoft Graph

[Microsoft Graph](/graph/overview) include numerose API che consentono di accedere ai dati Microsoft 365 degli utenti. Usando Microsoft Identity Platform come provider di identità per l'app, è possibile accedere più facilmente a queste informazioni perché Microsoft Graph supporta direttamente i token rilasciati da Microsoft Identity Platform. In questa sezione viene aggiunto il codice per visualizzare i messaggi di posta elettronica dell'utente connesso nella pagina "Recupera dati" dell'applicazione.

Prima di iniziare, disconnettersi dall'app perché verranno apportate modifiche alle autorizzazioni obbligatorie e il token corrente non funzionerà. Se non è già stato fatto, eseguire di nuovo l'app e selezionare **Disconnetti** prima di aggiornare il codice seguente.

A questo punto, si aggiornerà la registrazione e il codice dell'app per eseguire il pull della posta elettronica di un utente e visualizzare i messaggi nell'app. A tale scopo, estendere prima di tutto le autorizzazioni di registrazione dell'app in Azure AD per consentire l'accesso ai dati di posta elettronica. Aggiungere quindi all'app Blazor il codice per recuperare e visualizzare i dati in una delle pagine.

1. Nel portale di Azure selezionare l'app in **Registrazioni app**.
1. In **Gestisci** selezionare **Autorizzazioni API**.
1. Selezionare **Aggiungi un'autorizzazione** > **Microsoft Graph**.
1. Selezionare **Autorizzazioni delegate** e quindi cercare e selezionare l'autorizzazione **Mail.Read**.
1. Selezionare **Aggiungi autorizzazioni**.

Nel file *appsettings.json* aggiornare il codice in modo che recuperi il token appropriato con le autorizzazioni corrette. Aggiungere "mail.read" dopo l'ambito "user.read" in "DownstreamAPI". In questo modo si specificano gli ambiti (o le autorizzazioni) per cui l'app richiederà l'accesso.

```json
"Scopes": "user.read mail.read"
```

Aggiornare quindi il codice nel file *FetchData.razor* in modo da recuperare i dati della posta elettronica invece dei dettagli del meteo predefiniti (casuali). Sostituire il codice in tale file con il seguente:

```csharp
@page "/fetchdata"

@inject IHttpClientFactory HttpClientFactory
@inject Microsoft.Identity.Web.ITokenAcquisition TokenAcquisitionService

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
        _httpClient = HttpClientFactory.CreateClient();


        // get a token
        var token = await TokenAcquisitionService.GetAccessTokenForUserAsync(new string[] { "User.Read", "Mail.Read" });

        // make API call
        _httpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}

```

Avviare l'app. Si noterà che vengono richieste le autorizzazioni appena aggiunte, a indicare che tutto funziona come previsto. A questo punto, oltre ai dati di base del profilo utente, l'app richiede l'accesso ai dati della posta elettronica.

Dopo aver concesso il consenso, passare alla pagina "Recupera dati" per leggere un messaggio di posta elettronica.

:::image type="content" source="./media/tutorial-blazor-server/final-app-2.png" alt-text="Screenshot dell'app finale. Include un'intestazione Hello Nicholas e mostra un elenco di messaggi di posta elettronica appartenenti a Nicholas.":::

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla creazione di app Web che concedono l'accesso agli utenti in una serie di scenari in più parti:

> [!div class="nextstepaction"]
> [Scenario: App Web per l'accesso degli utenti](scenario-web-app-sign-user-overview.md)
