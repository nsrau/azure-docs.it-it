---
title: 'Esercitazione: Integrare più risorse dello strumento di lettura immersiva'
titleSuffix: Azure Cognitive Services
description: In questa esercitazione si creerà un'applicazione Node.js che avvia lo strumento di lettura immersiva usando più risorse.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: skamal
ms.openlocfilehash: f68112095bc8a8fd9bcc1bd67ff77827d6d00fd7
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195622"
---
# <a name="tutorial-integrate-multiple-immersive-reader-resources"></a>Esercitazione: Integrare più risorse dello strumento di lettura immersiva

Nella [panoramica](./overview.md) si è appreso che cos'è lo strumento di lettura immersiva e come questo strumento implementa tecniche comprovate per migliorare la comprensione della lettura per studenti di lingue, lettori emergenti e studenti con differenze nell'apprendimento. Nell'[argomento di avvio rapido per Node.js](./quickstart-nodejs.md) si è appreso come usare uno strumento di lettura immersiva con una singola risorsa. Questa esercitazione illustra come integrare più risorse dello strumento di lettura immersiva nella stessa applicazione. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare più risorse dello strumento di lettura immersiva in un gruppo di risorse esistente
> * Avviare lo strumento di lettura immersiva usando più risorse

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Seguire l'[argomento di avvio rapido](./quickstart-nodejs.md) per creare un'app Web che avvia lo strumento di lettura immersiva con Node.js. In questo argomento di avvio rapido si configura una singola risorsa dello strumento di lettura immersiva. Questa esercitazione sviluppa ulteriormente questo scenario.

## <a name="create-the-immersive-reader-resources"></a>Creare le risorse dello strumento di lettura immersiva

Seguire [queste istruzioni](./how-to-create-immersive-reader.md) per creare ogni risorsa dello strumento di lettura immersiva. Lo script **Create-ImmersiveReaderResource** include i parametri `ResourceName`, `ResourceSubdomain`e `ResourceLocation`. Devono essere univoci per ogni risorsa da creare. I parametri rimanenti devono essere uguali a quelli usati per la configurazione della prima risorsa dello strumento di lettura immersiva. In questo modo, ogni risorsa può essere collegata allo stesso gruppo di risorse di Azure e alla stessa applicazione di Azure AD.

L'esempio seguente illustra come creare due risorse, una nell'area WestUS e l'altra in EastUS. Si notino i valori univoci specificati per `ResourceName`, `ResourceSubdomain` e `ResourceLocation`.

```azurepowershell-interactive
Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_wus `
  -ResourceSubdomain resource-subdomain-wus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation westus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>

Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_eus `
  -ResourceSubdomain resource-subdomain-eus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation eastus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
```

## <a name="add-resources-to-environment-configuration"></a>Aggiungere risorse alla configurazione dell'ambiente

Nell'argomento di avvio rapido è stato creato un file di configurazione dell'ambiente che contiene i parametri `TenantId`, `ClientId`, `ClientSecret` e `Subdomain`. Poiché tutte le risorse usano la stessa applicazione di Azure AD, è possibile usare gli stessi valori per `TenantId`, `ClientId` e `ClientSecret`. L'unica modifica che è necessario effettuare consiste nell'elencare ogni sottodominio per ogni risorsa.

Il nuovo file con estensione __env__ avrà ora un aspetto simile al seguente:

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

Fare attenzione a non eseguire il commit di questo file nel controllo del codice sorgente, poiché contiene segreti che non devono essere resi pubblici.

Quindi, sarà necessario modificare il file _routes\index.js_ creato per supportare più risorse. Sostituirne il contenuto con il codice seguente.

Come prima, questo codice crea un endpoint API che acquisisce un token di autenticazione di Azure AD usando la password dell'entità servizio. Questa volta, consente all'utente di specificare la posizione della risorsa e di passarla come parametro di query. Restituisce quindi un oggetto che contiene il token e il sottodominio corrispondente.

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
            headers: {
                'content-type': 'application/x-www-form-urlencoded'
            },
            url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: process.env.CLIENT_ID,
                client_secret: process.env.CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;

            var subdomain = "";
            var region = req.query && req.query.region;
            switch (region) {
                case "eus":
                    subdomain = process.env.SUBDOMAIN_EUS
                    break;
                case "wus":
                default:
                    subdomain = process.env.SUBDOMAIN_WUS
            }

            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

L'endpoint API **getimmersivereaderlaunchparams** deve essere protetto da qualche forma di autenticazione, ad esempio [OAuth](https://oauth.net/2/), per impedire agli utenti non autorizzati di ottenere token da usare per il servizio Strumento di lettura immersiva e la fatturazione, ma la relativa procedura esula dall'ambito di questa esercitazione.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Avviare lo strumento di lettura immersiva con contenuto di esempio

1. Aprire _views\index.pug_ e sostituirne il contenuto con il codice seguente. Questo codice popola la pagina con contenuto di esempio e aggiunge due pulsanti che avviano lo strumento di lettura immersiva, uno per la risorsa EastUS e l'altro per la risorsa WestUS.

    ```pug
    doctype html
    html
        head
            title Immersive Reader Quickstart Node.js

            link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

            // A polyfill for Promise is needed for IE11 support.
            script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

            script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
            script(src='https://code.jquery.com/jquery-3.3.1.min.js')

            style(type="text/css").
                .immersive-reader-button {
                background-color: white;
                margin-top: 5px;
                border: 1px solid black;
                float: right;
                }
        body
            div(class="container")
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("wus")') WestUS Immersive Reader
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("eus")') EastUS Immersive Reader

                h1(id="ir-title") About Immersive Reader
                div(id="ir-content" lang="en-us")
                p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

                    ul
                        li Shows content in a minimal reading view
                        li Displays pictures of commonly used words
                        li Highlights nouns, verbs, adjectives, and adverbs
                        li Reads your content out loud to you
                        li Translates your content into another language
                        li Breaks down words into syllables

                h3 The Immersive Reader is available in many languages.

                p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
                p(lang="zh-cn") 沉浸式阅读器支持许多语言
                p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
                p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

    script(type="text/javascript").
    function getTokenAndSubdomainAsync(region) {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "/GetTokenAndSubdomain",
                    type: "GET",
                    data: {
                        region: region
                    },
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

        function handleLaunchImmersiveReader(region) {
            getTokenAndSubdomainAsync(region)
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
                    // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
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
    ```

3. L'app Web è ora pronta. Avviare l'app con il comando seguente:

    ```bash
    npm start
    ```

4. Aprire il browser e passare a `http://localhost:3000`. Nella pagina verrà visualizzato il contenuto sopra riportato. Fare clic sul pulsante **EastUS Immersive Reader** o sul pulsante**WestUS Immersive Reader** per avviare lo strumento di lettura immersiva usando le rispettive risorse.

## <a name="next-steps"></a>Passaggi successivi

* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](./reference.md)
* Visualizzare gli esempi di codice su [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)