---
title: 'Avvio rapido: Liberia client dello strumento di lettura immersiva per C#'
titleSuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido si creerà da zero un'app Web a cui si aggiungerà la funzionalità dell'API dello strumento di lettura immersiva.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 09/14/2020
ms.author: nitinme
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1c7587a4434a84c6934179393baa5ae684f9b1fd
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92116698"
---
Lo [strumento di lettura immersiva](https://www.onenote.com/learningtools) è stato progettato per l'inclusività e implementa tecniche comprovate per migliorare la comprensione della lettura per nuovi lettori, studenti di lingue e persone con differenze di apprendimento, come la dislessia. È possibile usare lo strumento di lettura immersiva nelle applicazioni per isolare il testo stimolare l'attenzione, visualizzare immagini per le parole di uso comune, evidenziare parti del discorso, leggere il testo selezionato ad alta voce, tradurre parole e frasi in tempo reale e altro ancora.

In questo argomento di avvio rapido si crea un'app Web da zero e si integra lo strumento di lettura immersiva tramite l'apposita libreria client. Un esempio pratico completo di questo argomento di avvio rapido è disponibile [in GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* Una risorsa dello strumento di lettura immersiva configurata per l'autenticazione Azure Active Directory. Seguire [queste istruzioni](../../how-to-create-immersive-reader.md) per configurare l'ambiente. Per la configurazione delle proprietà del progetto di esempio, saranno necessari alcuni dei valori creati qui. Salvare l'output della sessione in un file di testo per riferimento futuro.

## <a name="create-a-web-app-project"></a>Creare un progetto di app Web

Creare un nuovo progetto in Visual Studio usando il modello di applicazione Web ASP.NET Core con Model-View-Controller incorporato e ASP.NET Core 2.1. Assegnare al progetto il nome "QuickstartSampleWebApp".

![Nuovo progetto - C#](../../media/quickstart-csharp/1-createproject.png)

![Configurazione del nuovo progetto - C#](../../media/quickstart-csharp/2-configureproject.png)

![Nuova applicazione Web ASP.NET Core - C#](../../media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>Configurare l'autenticazione

### <a name="configure-authentication-values"></a>Configurare i valori di autenticazione

Fare clic con il pulsante destro del mouse sul progetto in _Esplora soluzioni_ e scegliere **Gestisci segreti utente** . Verrà visualizzato un file denominato _secrets.json_ . Il file non viene archiviato nel controllo del codice sorgente. Fare clic [qui](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=windows&preserve-view=true) per altre informazioni. Sostituire il contenuto del file _secrets.json_ con il seguente, specificando i valori forniti durante la creazione della risorsa dello strumento di lettura immersiva.

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="install-active-directory-nuget-package"></a>Installare il pacchetto NuGet di Active Directory

Il codice seguente usa oggetti dal pacchetto NuGet **Microsoft.IdentityModel.Clients.ActiveDirectory** , quindi sarà necessario aggiungere un riferimento al pacchetto nel progetto.

Aprire la console di gestione pacchetti NuGet in **Strumenti -> Gestione pacchetti NuGet -> Console di Gestione pacchetti** e digitare il comando seguente:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.2.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>Aggiornare il controller per l'acquisizione del token 

Aprire _Controllers\HomeController.cs_ e aggiungere il codice seguente dopo le istruzioni _using_ all'inizio del file.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

A questo punto, il controller verrà configurato per ottenere i valori Azure AD da _secrets.json_ . Nella parte superiore della classe _HomeController_ , dopo ```public class HomeController : Controller {```, aggiungere il codice seguente.

```csharp
private readonly string TenantId;     // Azure subscription TenantId
private readonly string ClientId;     // Azure AD ApplicationId
private readonly string ClientSecret; // Azure AD Application Service Principal password
private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
{
    TenantId = configuration["TenantId"];
    ClientId = configuration["ClientId"];
    ClientSecret = configuration["ClientSecret"];
    Subdomain = configuration["Subdomain"];

    if (string.IsNullOrWhiteSpace(TenantId))
    {
        throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientId))
    {
        throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientSecret))
    {
        throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(Subdomain))
    {
        throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
    }
}

/// <summary>
/// Get an Azure AD authentication token
/// </summary>
private async Task<string> GetTokenAsync()
{
    string authority = $"https://login.windows.net/{TenantId}";
    const string resource = "https://cognitiveservices.azure.com/";

    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

    AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

    return authResult.AccessToken;
}

[HttpGet]
public async Task<JsonResult> GetTokenAndSubdomain()
{
    try
    {
        string tokenResult = await GetTokenAsync();

        return new JsonResult(new { token = tokenResult, subdomain = Subdomain });
    }
    catch (Exception e)
    {
        string message = "Unable to acquire Azure AD token. Check the debugger for more information.";
        Debug.WriteLine(message, e);
        return new JsonResult(new { error = message });
    }
}
```

## <a name="add-sample-content"></a>Aggiungere contenuto di esempio
Aprire prima di tutto _Views\Shared\Layout.cshtml_ . Aggiungere il codice seguente prima della riga ```</head>```:

```html
@RenderSection("Styles", required: false)
```

Nell'app Web verranno ora aggiunti alcuni contenuti di esempio. Aprire _Views\Home\Index.cshtml_ e sostituire tutto il codice generato automaticamente con questo esempio:

```html
@{
    ViewData["Title"] = "Immersive Reader C# Quickstart";
}

@section Styles {
    <style type="text/css">
        .immersive-reader-button {
            background-color: white;
            margin-top: 5px;
            border: 1px solid black;
            float: right;
        }
    </style>
}

<div class="container">
    <button class="immersive-reader-button" data-button-style="iconAndText" data-locale="en"></button>

    <h1 id="ir-title">About Immersive Reader</h1>
    <div id="ir-content" lang="en-us">
        <p>
            Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences.
            The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader
            <ul>
                <li>
                    Shows content in a minimal reading view
                </li>
                <li>
                    Displays pictures of commonly used words
                </li>
                <li>
                    Highlights nouns, verbs, adjectives, and adverbs
                </li>
                <li>
                    Reads your content out loud to you
                </li>
                <li>
                    Translates your content into another language
                </li>
                <li>
                    Breaks down words into syllables
                </li>
            </ul>
        </p>
        <h3>
            The Immersive Reader is available in many languages.
        </h3>
        <p lang="es-es">
            El Lector inmersivo está disponible en varios idiomas.
        </p>
        <p lang="zh-cn">
            沉浸式阅读器支持许多语言
        </p>
        <p lang="de-de">
            Der plastische Reader ist in vielen Sprachen verfügbar.
        </p>
        <p lang="ar-eg" dir="rtl" style="text-align:right">
            يتوفر \"القارئ الشامل\" في العديد من اللغات.
        </p>
    </div>
</div>
```

Si noti che tutto il testo ha un attributo **lang** , che ne descrive le lingue. Questo attributo consente allo strumento di lettura immersiva di fornire funzionalità di lingua e grammatica pertinenti.

## <a name="add-javascript-to-handle-launching-immersive-reader"></a>Aggiungere codice JavaScript per gestire l'avvio dello strumento di lettura immersiva

La libreria dello strumento di lettura immersiva fornisce funzionalità come l'avvio dello strumento e la visualizzazione dei relativi pulsanti. Fare clic [qui](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference) per altre informazioni.

Aggiungere il codice seguente alla fine di _Views\Home\Index.cshtml_ :

```html
@section Scripts
{
    <script src="https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js"></script>
    <script>
        function getTokenAndSubdomainAsync() {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "@Url.Action("GetTokenAndSubdomain", "Home")",
                    type: "GET",
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }
    
        $(".immersive-reader-button").click(function () {
            handleLaunchImmersiveReader();
        });
    
        function handleLaunchImmersiveReader() {
            getTokenAndSubdomainAsync()
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
    
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
    
                    // Learn more about options https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
    
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }
    
        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    </script>
}
```

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

Nella barra dei menu selezionare **Debug > Avvia debug** oppure premere **F5** per avviare l'applicazione.

Nel browser verrà visualizzato il contenuto seguente:

![App di esempio - C#](../../media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Avviare lo strumento di lettura immersiva

Quando si fa clic sul pulsante "Strumento di lettura immersiva", verrà avviato lo strumento di lettura immersiva con il contenuto della pagina.

![Strumento di lettura immersiva - C#](../../media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>Passaggi successivi

* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](../../reference.md)
