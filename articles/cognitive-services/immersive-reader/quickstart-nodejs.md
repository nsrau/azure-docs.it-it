---
title: "Guida introduttiva: Creare un'app Web che avvia lo strumento di lettura immersiva con Node.js"
titleSuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido si creerà da zero un'app Web a cui si aggiungerà la funzionalità dell'API dello strumento di lettura immersiva.
author: pasta
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: 749e75fed409632c613713a49154e4cd8dc265b3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945968"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-nodejs"></a>Guida introduttiva: Creare un'app Web che avvia lo strumento di lettura immersiva (Node.js)

Lo [strumento di lettura immersiva](https://www.onenote.com/learningtools) è uno strumento incluso che implementa tecniche comprovate per migliorare la comprensione della lettura.

In questo argomento di avvio rapido si creerà da zero un'app Web in cui si integrerà lo strumento di lettura immersiva mediante Immersive Reader SDK. Un esempio pratico completo di questo argomento di avvio rapido è disponibile [qui](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Una risorsa dello strumento di lettura immersiva configurata per l'autenticazione Azure Active Directory. Seguire [queste istruzioni](./how-to-create-immersive-reader.md) per configurare l'ambiente. Per la configurazione delle proprietà dell'ambiente, saranno necessari alcuni dei valori creati qui. Salvare l'output della sessione in un file di testo per riferimento futuro.
* [Node.js](https://nodejs.org/) e [Yarn](https://yarnpkg.com)
* Un ambiente di sviluppo integrato, ad esempio [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Creare un'app Web Node.js con Express

Creare un'app Web Node.js con lo strumento `express-generator`.

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

Installare le dipendenze di Yarn e aggiungere le dipendenze `request` e `dotenv`, che verranno usate più avanti nell'argomento di avvio rapido.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="set-up-authentication"></a>Configurare l'autenticazione

### <a name="configure-authentication-values"></a>Configurare i valori di autenticazione

Creare un nuovo file denominato _.env_ nella radice del progetto. Incollare il codice seguente al suo interno, specificando i valori forniti durante la creazione della risorsa dello strumento di lettura immersiva.
Non includere virgolette o i caratteri "{" e "}".

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Fare attenzione a non eseguire il commit di questo file nel controllo del codice sorgente, poiché contiene segreti che non devono essere resi pubblici.

Aprire quindi _app.js_ e aggiungere il codice seguente all'inizio del file. Verranno caricate le proprietà definite nel file .env come variabili di ambiente in Node.

```javascript
require('dotenv').config();
```

### <a name="update-the-router-to-acquire-the-token"></a>Aggiornare il router per acquisire il token
Aprire il file _routes\index.js_ e sostituirne il codice generato automaticamente con il codice seguente.

Questo codice crea un endpoint API che acquisisce un token di autenticazione di Azure AD usando la password dell'entità servizio e recupera anche il sottodominio, restituendo quindi un oggetto che contiene il token e il sottodominio.

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
            var subdomain = process.env.SUBDOMAIN;
            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

L'endpoint API **GetTokenAndSubdomain** deve essere protetto tramite una forma di autenticazione, ad esempio [OAuth](https://oauth.net/2/), per impedire a utenti non autorizzati di ottenere token da usare per il servizio e la fatturazione dello strumento di lettura immersiva. Tuttavia, questa parte esula dall'ambito di questo argomento di avvio rapido.

## <a name="add-sample-content"></a>Aggiungere contenuto di esempio

Nell'app Web verranno ora aggiunti alcuni contenuti di esempio. Aprire_views\index.pug_ e sostituire il codice generato automaticamente con questo codice di esempio:

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
        button(class="immersive-reader-button" data-button-style="iconAndText" data-locale="en")

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
  function getTokenAndSubdomainAsync() {
        return new Promise(function (resolve, reject) {
            $.ajax({
                url: "/GetTokenAndSubdomain",
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


Si noti che tutto il testo ha un attributo **lang**, che ne descrive le lingue. Questo attributo consente allo strumento di lettura immersiva di fornire funzionalità di lingua e grammatica pertinenti.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

L'app Web è ora pronta. Avviare l'app con il comando seguente:

```bash
npm start
```

Aprire il browser e passare a _http://localhost:3000_ . Dovrebbe essere visualizzata la seguente schermata:

![App di esempio](./media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Avviare lo strumento di lettura immersiva

Quando si fa clic sul pulsante "Strumento di lettura immersiva", verrà avviato lo strumento di lettura immersiva con il contenuto della pagina.

![Strumento di lettura immersiva](./media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>Passaggi successivi

* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](./reference.md)