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
ms.openlocfilehash: 3b408de6b60e7e7704ee228b52c399e5b80e3a9e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718422"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Guida introduttiva: Creare un'app Web che consenta di avviare lo strumento di lettura immersiva (C#)

Lo [strumento di lettura immersiva](https://www.onenote.com/learningtools) è uno strumento incluso che implementa tecniche comprovate per migliorare la comprensione della lettura.

In questo argomento di avvio rapido si creerà da zero un'app Web in cui si integrerà lo strumento di lettura immersiva mediante Immersive Reader SDK. Un esempio pratico completo di questo argomento di avvio rapido è disponibile [qui](https://github.com/microsoft/immersive-reader-sdk/tree/master/samples/quickstart-csharp).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Una chiave di sottoscrizione per lo strumento di lettura immersiva. Per ottenerne una, seguire [queste istruzioni](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

## <a name="create-a-web-app-project"></a>Creare un progetto di app Web

Creare un nuovo progetto in Visual Studio usando il modello dell'applicazione Web ASP.NET Core con Model-View-Controller integrato.

![Nuovo progetto](./media/vswebapp.png)

![Nuova applicazione Web ASP.NET Core](./media/vsmvc.png)

## <a name="acquire-an-access-token"></a>Acquisire un token di accesso

Per questo passaggio è necessario conoscere la chiave di sottoscrizione e l'endpoint. È possibile trovare la chiave di sottoscrizione nella pagina delle chiavi dello strumento di lettura immersiva nel portale di Azure. È possibile trovare l'endpoint nella pagina di panoramica.

Fare clic con il pulsante destro del mouse sul progetto in _Esplora soluzioni_ e scegliere **Gestisci segreti utente**. Verrà visualizzato un file denominato _secrets.json_. Sostituire il contenuto del file con il codice seguente, specificando la chiave di sottoscrizione e l'endpoint dove appropriato.

```json
{
  "SubscriptionKey": YOUR_SUBSCRIPTION_KEY,
  "Endpoint": YOUR_ENDPOINT
}
```

Aprire _Controllers\HomeController.cs_ e sostituire la classe `HomeController` con il codice seguente.

```csharp
public class HomeController : Controller
{
    private readonly string SubscriptionKey;
    private readonly string Endpoint;

    public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
    {
        SubscriptionKey = configuration["SubscriptionKey"];
        Endpoint = configuration["Endpoint"];

        if (string.IsNullOrEmpty(Endpoint) || string.IsNullOrEmpty(SubscriptionKey))
        {
            throw new ArgumentNullException("Endpoint or subscriptionKey is null!");
        }
    }

    public IActionResult Index()
    {
        return View();
    }

    [Route("token")]
    public async Task<string> Token()
    {
        return await GetTokenAsync();
    }

    /// <summary>
    /// Exchange your Azure subscription key for an access token
    /// </summary>
    private async Task<string> GetTokenAsync()
    {
        using (var client = new System.Net.Http.HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
            using (var response = await client.PostAsync(Endpoint, null))
            {
                return await response.Content.ReadAsStringAsync();
            }
        }
    }
}
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
<script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js'></script>
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

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        ImmersiveReader.launchAsync(token, content, { uiZIndex: 1000000 });
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
