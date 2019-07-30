---
title: "Guida introduttiva: Creare un'app Web che consenta di avviare lo strumento di lettura immersiva con C#"
titlesuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido si creerà da zero un'app Web a cui si aggiungerà la funzionalità dell'API dello strumento di lettura immersiva.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 6386c22044483a0ac4a324397cf2f9d22e83b579
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442852"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Guida introduttiva: Creare un'app Web che consenta di avviare lo strumento di lettura immersiva (C#)

Lo [strumento di lettura immersiva](https://www.onenote.com/learningtools) è uno strumento incluso che implementa tecniche comprovate per migliorare la comprensione della lettura.

In questo argomento di avvio rapido si creerà da zero un'app Web in cui si integrerà lo strumento di lettura immersiva mediante Immersive Reader SDK. Un esempio pratico completo di questo argomento di avvio rapido è disponibile [qui](https://github.com/microsoft/immersive-reader-sdk/tree/master/samples/quickstart-csharp).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Una risorsa Strumento di lettura immersiva configurata per l'autenticazione Azure Active Directory (Azure AD). Seguire [queste istruzioni](./azure-active-directory-authentication.md) per configurare l'ambiente. Per la configurazione delle proprietà del progetto di esempio, saranno necessari alcuni dei valori creati qui. Salvare l'output della sessione in un file di testo per riferimento futuro.

## <a name="create-a-web-app-project"></a>Creare un progetto di app Web

Creare un nuovo progetto in Visual Studio usando il modello dell'applicazione Web ASP.NET Core con Model-View-Controller integrato.

![Nuovo progetto](./media/vswebapp.png)

![Nuova applicazione Web ASP.NET Core](./media/vsmvc.png)

## <a name="acquire-an-azure-ad-authentication-token"></a>Acquisire un token di autenticazione di Azure AD

Per questa parte sono necessari alcuni valori del passaggio relativo ai prerequisiti di configurazione dell'autenticazione di Azure AD. Fare riferimento al file di testo salvato della sessione.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Fare clic con il pulsante destro del mouse sul progetto in _Esplora soluzioni_ e scegliere **Gestisci segreti utente**. Verrà visualizzato un file denominato _secrets.json_. Sostituire il contenuto del file con il codice seguente, specificando i valori delle proprietà personalizzate precedenti.

```json
{
  "TenantId": YOUR_TENANT_ID,
  "ClientId": YOUR_CLIENT_ID,
  "ClientSecret": YOUR_CLIENT_SECRET,
  "Subdomain": YOUR_SUBDOMAIN
}
```

Aprire _Controllers\HomeController.cs_ e sostituire il file con il codice seguente.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

namespace QuickstartSampleWebApp.Controllers
{
    public class HomeController : Controller
    {
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

        public IActionResult Index()
        {
            return View();
        }

        [Route("subdomain")]
        public string GetSubdomain()
        {
            return Subdomain;
        }

        [Route("token")]
        public async Task<string> GetToken()
        {
            return await GetTokenAsync();
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
    }
}
```

## <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Aggiungere il pacchetto NuGet Microsoft.IdentityModel.Clients.ActiveDirectory

Il codice precedente usa oggetti dal pacchetto NuGet **Microsoft.IdentityModel.Clients.ActiveDirectory**, quindi sarà necessario aggiungere un riferimento al pacchetto nel progetto.

Aprire la Console di Gestione pacchetti in **Strumenti -> Gestione pacchetti NuGet -> Console di Gestione pacchetti** e digitare quanto segue:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.1.0
```

## <a name="add-sample-content"></a>Aggiungere contenuto di esempio

Verranno ora aggiunti all'app Web alcuni contenuti di esempio. Aprire _Views\Home\Index.cshtml_ e sostituire il codice generato automaticamente con questo codice di esempio:

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
    <script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js'></script>
    <script type='text/javascript' src='https://code.jquery.com/jquery-3.3.1.min.js'></script>
    <script type='text/javascript'>
    function getImmersiveReaderTokenAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/token',
                type: 'GET',
                success: token => {
                    resolve(token);
                }
            });
        });
    }

    function getSubdomainAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/subdomain',
                type: 'GET',
                success: subdomain => {
                    resolve(subdomain);
                }
            });
        });
    }

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        var subdomain = await getSubdomainAsync();

        ImmersiveReader.launchAsync(token, subdomain, content, { uiZIndex: 1000000 });
    }
    </script>
}
```

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

Nella barra dei menu selezionare **Debug > Avvia debug** oppure premere **F5** per avviare l'applicazione.

Nel browser verrà visualizzato il contenuto seguente:

![App di esempio](./media/quickstart-result.png)

Quando si fa clic sul pulsante "Strumento di lettura immersiva", verrà avviato lo strumento di lettura immersiva con il contenuto della pagina.

![Strumento di lettura immersiva](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>Passaggi successivi

* Accedere all'[esercitazione](./tutorial.md) per scoprire quali altri operazioni è possibile eseguire con Immersive Reader SDK
* Esplorare [Immersive Reader SDK](https://github.com/Microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](./reference.md)
