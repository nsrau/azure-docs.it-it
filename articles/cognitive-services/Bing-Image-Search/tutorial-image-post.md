---
title: Caricamento di immagini Bing per ottenere informazioni dettagliate | Microsoft Docs
description: Applicazione console che usa l'API Ricerca immagini Bing per caricare un'immagine e ottenere informazioni dettagliate sull'immagine.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 12/07/2017
ms.author: v-gedod
ms.openlocfilehash: f0bf32a9951527a072fffe464f6b5f50d0f237a2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372841"
---
# <a name="tutorial-bing-image-upload-for-insights"></a>Esercitazione: Caricamento di immagini Bing per ottenere informazioni dettagliate

L'API Ricerca immagini Bing fornisce un'opzione `POST` per caricare un'immagine e cercare informazioni pertinenti all'immagine. Questa applicazione console C# carica un'immagine usando l'endpoint di Ricerca immagini per ottenere informazioni dettagliate sull'immagine.
I risultati, in breve, sono oggetti JSON, ad esempio:

![[Risultati JSON]](media/cognitive-services-bing-images-api/jsonResult.jpg)

In questa esercitazione viene illustrato come:

> [!div class="checklist"]
> * Usare l'endpoint `/details` di Ricerca immagini in una richiesta `POST`
> * Specificare le intestazioni per la richiesta
> * Usare i parametri dell'URL per specificare i risultati
> * Caricare i dati dell'immagine e inviare la richiesta `POST`
> * Stampare i risultati del file JSON nella console

## <a name="app-components"></a>Componenti dell'app

L'applicazione per l'esercitazione include tre parti:

> [!div class="checklist"]
> * Configurazione dell'endpoint per specificare l'endpoint di Ricerca immagini Bing e le intestazioni necessarie
> * Caricamento del file dell'immagine per la richiesta `POST` all'endpoint
> * Analisi dei risultati JSON che costituiscono i dettagli restituiti dalla richiesta `POST`

## <a name="scenario-overview"></a>Panoramica dello scenario
Esistono [tre endpoint di Ricerca immagini](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint). L'endpoint `/details` può usare una richiesta `POST` con i dati dell'immagine nel corpo della richiesta.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Il parametro dell'URL `modules` che segue `/details?` specifica la tipologia di dettagli contenuti nei risultati:
* `modules=All`
* `modules=RecognizedEntities` (persone o località visibili nell'immagine)

Specificare `modules=All` nella richiesta `POST` per ottenere il testo JSON che include l'elenco seguente:
* `bestRepresentativeQuery`: query Bing che restituisce immagini simili a quella caricata
* `detectedObjects`, ad esempio un rettangolo di selezione o aree sensibili nell'immagine
* Metadati `image`
* `imageInsightsToken`: token per una richiesta `GET` successiva che ottiene `RecognizedEntities` (persone o località visibili nell'immagine) 
* `imageTags`
* `pagesIncluding`: pagine Web che includono l'immagine
* `relatedSearches`
* `visuallySimilarImages`

Specificare `modules=RecognizedEntities` nella richiesta `POST` per ottenere solo `imageInsightsToken`, usato in una richiesta `GET` successiva. Identifica persone o località visibili nell'immagine.

## <a name="webclient-and-headers-for-the-post-request"></a>WebClient e intestazioni per la richiesta POST
Creare un oggetto `WebClient` e impostare le intestazioni. Tutte le richieste all'API di ricerca Bing richiedono un elemento `Ocp-Apim-Subscription-Key`. Una richiesta `POST` di caricamento di un'immagine deve specificare anche `ContentType: multipart/form-data`.

```
            WebClient client = new WebClient();
            client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            client.Headers["ContentType"] = "multipart/form-data"; 
```

## <a name="upload-the-image-and-get-results"></a>Caricare l'immagine e ottenere risultati

La classe `WebClient` include il metodo `UpLoadFile` che formatta i dati per la richiesta `POST`. Formatta `RequestStream` e chiama `HttpWebRequest`, evitando un eccesso di complessità.
Chiamare `WebClient.UpLoadFile` con l'endpoint `/details` e il file dell'immagine da caricare. La risposta è costituita da dati binari che vengono facilmente convertiti in JSON. 

Usare il testo JSON per inizializzare un'istanza della struttura `SearchResult`. Per il contesto, vedere il [codice sorgente dell'applicazione](tutorial-image-post-source.md).
```        
         const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";

        // The image to upload. Replace with your file and path.
        const string imageFile = "ansel-adams-tetons-snake-river.jpg";
            
        byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
        var json = System.Text.Encoding.Default.GetString(resp);

        // Create result object for return
        var searchResult = new SearchResult()
        {
            jsonResult = json,
            relevantHeaders = new Dictionary<String, String>()
        };
```

## <a name="print-the-results"></a>Stampare i risultati
Il resto del codice analizza il risultato JSON e lo stampa nella console.

```
        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
```
## <a name="get-request-using-the-imageinsightstoken"></a>Richiesta GET con ImageInsightsToken
Per usare l'elemento `ImageInsightsToken` restituito con i risultati di `POST`, creare una richiesta `GET`, ad esempio:
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```
Se nell'immagine sono presenti persone o località identificabili, questa richiesta restituirà informazioni su di esse.
Le [guide introduttive](https://docs.microsoft.com/azure/cognitive-services/bing-image-search) contengono diversi esempi di codice.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento per l'API Ricerca immagini Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

