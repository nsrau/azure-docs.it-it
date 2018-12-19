---
title: 'Esercitazione: moderazione del catalogo di e-commerce - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Moderare automaticamente i cataloghi di e-commerce con Machine Learning e intelligenza artificiale.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: sajagtap
ms.openlocfilehash: 0bd61c3f1a4f660076be4e87bb5443302e5dc013
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363995"
---
# <a name="tutorial-ecommerce-catalog-moderation-with-machine-learning"></a>Esercitazione: moderazione di cataloghi di e-commerce con il Machine Learning

In questa esercitazione si apprenderà come implementare la moderazione intelligente dei cataloghi di e-commerce basata su Machine Learning combinando tecnologie di intelligenza artificiale automatiche con la moderazione eseguita da umani per fornire un sistema di catalogo intelligente.

![Immagini di prodotto classificate](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="business-scenario"></a>Scenario aziendale

Usare tecnologie automatiche per classificare e moderare immagini di prodotti in queste categorie:

1. Per adulti (scene di nudo)
2. Spinti (con riferimenti sessuali)
3. Celebrità
4. Bandiere USA
5. Giocattoli
6. Penne

## <a name="tutorial-steps"></a>Passaggi dell'esercitazione

Questa esercitazione illustra i passaggi seguenti:

1. Iscriversi e creare un team di Content Moderator.
2. Configurare i tag di moderazione (etichette) per il potenziale contenuto di tipo celebrità e bandiere.
3. Usare l'API per le immagini di Content Moderator per rilevare il contenuto potenzialmente spinto e per adulti.
4. Usare l'API Visione artificiale per rilevare la presenza di potenziali celebrità.
5. Usare il Servizio visione artificiale personalizzato per rilevare la possibile presenza di bandiere.
6. Presentare i risultati dell'analisi meno netti per la revisione umana e la decisione finale.

## <a name="create-a-team"></a>Creare un team

Fare riferimento alla pagina [Introduzione](quick-start.md) per iscriversi a Content Moderator e creare un team. Prendere nota dell'**ID team** nella pagina **Credenziali**.


## <a name="define-custom-tags"></a>Definire tag personalizzati

Fare riferimento all'articolo [Informazioni sui tag](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) per aggiungere tag personalizzati. Oltre ai tag predefiniti **per adulti** e **spinti**, i nuovi tag consentono allo strumento di revisione di visualizzare i nomi descrittivi per i tag.

In questo caso verranno definiti i tag personalizzati **celebrità**, **bandiera**, **USA**, **giocattolo**, **penna**:

![Configurare i tag personalizzati](images/tutorial-ecommerce-tags2.PNG)

## <a name="list-your-api-keys-and-endpoints"></a>Elencare le chiavi API e gli endpoint

1. L'esercitazione usa tre API e le chiavi e gli endpoint corrispondenti.
2. Gli endpoint delle API variano in base alle aree della sottoscrizione e all'ID del team di revisione di Content Moderator.

> [!NOTE]
> L'esercitazione è progettata per usare chiavi di sottoscrizione nelle aree visibili negli endpoint seguenti. Assicurarsi che le chiavi API corrispondano agli URI delle aree o potrebbero non funzionare con gli endpoint seguenti:

        // Your API keys
        public const string ContentModeratorKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string ComputerVisionKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string CustomVisionKey = "XXXXXXXXXXXXXXXXXXXX";

        // Your end points URLs will look different based on your region and Content Moderator Team ID.
        public const string ImageUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/moderate/v1.0/ProcessImage/Evaluate";
        public const string ReviewUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0/teams/YOURTEAMID/reviews";
        public const string ComputerVisionUri = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0";
        public const string CustomVisionUri = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/Prediction/XXXXXXXXXXXXXXXXXXXX/url";

## <a name="scan-for-adult-and-racy-content"></a>Analizzare le immagini per rilevare contenuti spinti e per adulti

1. La funzione accetta come parametri un URL di immagine e una matrice di coppie chiave-valore.
2. Chiama l'API per le immagini di Content Moderator per ottenere i punteggi dei contenuti spinti o per adulti.
3. Se il punteggio è superiore a 0,4 (l'intervallo è da 0 a 1), il valore della matrice **ReviewTags** viene impostato su **True**.
4. La matrice **ReviewTags** viene usata per evidenziare il tag corrispondente nello strumento di revisione.

        public static bool EvaluateAdultRacy(string ImageUrl, ref KeyValuePair[] ReviewTags)
        {
            float AdultScore = 0;
            float RacyScore = 0;

            var File = ImageUrl;
            string Body = $"{{\"DataRepresentation\":\"URL\",\"Value\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(ImageUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // {“answers”:[{“answer”:“Hello”,“questions”:[“Hi”],“score”:100.0}]}
                // Parse the response body. Blocking!
                GetAdultRacyScores(response.Content.ReadAsStringAsync().Result, out AdultScore, out RacyScore);
            }

            ReviewTags[0] = new KeyValuePair();
            ReviewTags[0].Key = "a";
            ReviewTags[0].Value = "false";
            if (AdultScore > 0.4)
            {
                ReviewTags[0].Value = "true";
            }

            ReviewTags[1] = new KeyValuePair();
            ReviewTags[1].Key = "r";
            ReviewTags[1].Value = "false";
            if (RacyScore > 0.3)
            {
                ReviewTags[1].Value = "true";
            }
            return response.IsSuccessStatusCode;
        }

## <a name="scan-for-celebrities"></a>Analizzare le immagini per rilevare le celebrità

1. Registrarsi per una [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) dell'[API Visione artificiale](https://azure.microsoft.com/services/cognitive-services/computer-vision/).
2. Fare clic sul pulsante **Ottieni chiave API**.
3. Accettare le condizioni.
4. Per accedere, scegliere dall'elenco di account Internet disponibili.
5. Prendere nota delle chiavi API visualizzate nella pagina del servizio.
    
    ![Chiavi dell'API Visione artificiale](images/tutorial-computer-vision-keys.PNG)
    
6. Fare riferimento al codice sorgente del progetto per la funzione che esegue l'analisi dell'immagine con l'API Visione artificiale.

        public static bool EvaluateComputerVisionTags(string ImageUrl, string ComputerVisionUri, string ComputerVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage Response = CallAPI(ComputerVisionUri, ComputerVisionKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (Response.IsSuccessStatusCode)
            {
                ReviewTags[2] = new KeyValuePair();
                ReviewTags[2].Key = "cb";
                ReviewTags[2].Value = "false";

                ComputerVisionPrediction CVObject = JsonConvert.DeserializeObject<ComputerVisionPrediction>(Response.Content.ReadAsStringAsync().Result);

                if ((CVObject.categories[0].detail != null) && (CVObject.categories[0].detail.celebrities.Count() > 0))
                {
                    ReviewTags[2].Value = "true";
                }
            }

            return Response.IsSuccessStatusCode;
        }

## <a name="classify-into-flags-toys-and-pens"></a>Classificare in bandiere, giocattoli e penne

1. [Accedere](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) all'[anteprima dell'API Visione personalizzata](https://www.customvision.ai/).
2. Fare riferimento all'[Introduzione](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) per compilare il classificatore personalizzato per rilevare la potenziale presenza di bandiere, giocattoli e penne.
   ![Immagini di training di Visione personalizzata](images/tutorial-ecommerce-custom-vision.PNG)
3. [Ottenere l'URL dell'endpoint di stima](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api) per il classificatore personalizzato.
4. Fare riferimento al codice sorgente del progetto per vedere la funzione che chiama l'endpoint di stima del classificatore personalizzato per analizzare l'immagine.

        public static bool EvaluateCustomVisionTags(string ImageUrl, string CustomVisionUri, string CustomVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(CustomVisionUri, CustomVisionKey, CallType.POST,
                                                   "Prediction-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // Parse the response body. Blocking!
                SaveCustomVisionTags(response.Content.ReadAsStringAsync().Result, ref ReviewTags);
            }
            return response.IsSuccessStatusCode;
        }
 
## <a name="reviews-for-human-in-the-loop"></a>Revisioni human-in-the-loop

1. Nelle sezioni precedenti si sono analizzate le immagini in ingresso per rilevare la presenza di contenuti per adulti e spinti (Content Moderator), celebrità (Visione artificiale) e bandiere (Visione personalizzata).
2. In base alle soglie di corrispondenza di ogni analisi, rendere disponibili i casi meno netti per la revisione umana nello strumento di revisione.

        public static bool CreateReview(string ImageUrl, KeyValuePair[] Metadata) {

            ReviewCreationRequest Review = new ReviewCreationRequest();
            Review.Item[0] = new ReviewItem();
            Review.Item[0].Content = ImageUrl;
            Review.Item[0].Metadata = new KeyValuePair[MAXTAGSCOUNT];
            Metadata.CopyTo(Review.Item[0].Metadata, 0);

            //SortReviewItems(ref Review);

            string Body = JsonConvert.SerializeObject(Review.Item);

            HttpResponseMessage response = CallAPI(ReviewUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            return response.IsSuccessStatusCode;
        }

## <a name="submit-batch-of-images"></a>Inviare batch di immagini

1. Questa esercitazione presuppone la presenza di una directory "C:Test" con un file di testo che include un elenco di URL di immagini.
2. Il codice seguente controlla l'esistenza del file e legge tutti gli URL in memoria.

            // Verificare la presenza di una directory di test con un file di testo con l'elenco di URL di immagini
            var topdir = @"C:\test\";
            var Urlsfile = topdir + "Urls.txt";

            if (!Directory.Exists(topdir))
                return;

            if (!File.Exists(Urlsfile))
            {
                return;
            }

            // Read all image URLs in the file
            var Urls = File.ReadLines(Urlsfile);

## <a name="initiate-all-scans"></a>Avviare tutte le analisi

1. Questa funzione di primo livello esegue un ciclo su tutti gli URL delle immagini nel file di testo menzionati in precedenza.
2. Li analizza con ogni API e se il punteggio di attendibilità della corrispondenza rientra nei criteri stabiliti, crea una revisione per moderatori umani.

            // per ogni URL di immagine nel file...
            foreach (var Url in Urls) {
                // Inizializza una nuova matrice di tag di revisione
                ReviewTags = new KeyValuePair[MAXTAGSCOUNT];

                // Evaluate for potential adult and racy content with Content Moderator API
                EvaluateAdultRacy(Url, ref ReviewTags);

                // Evaluate for potential presence of celebrity (ies) in images with Computer Vision API
                EvaluateComputerVisionTags(Url, ComputerVisionUri, ComputerVisionKey, ref ReviewTags);

                // Evaluate for potential presence of custom categories other than Marijuana
                EvaluateCustomVisionTags(Url, CustomVisionUri, CustomVisionKey, ref ReviewTags);

                // Create review in the Content Moderator review tool
                CreateReview(Url, ReviewTags);
            }

## <a name="license"></a>Licenza

Tutti gli SDK e gli esempi di Servizi cognitivi Microsoft sono concessi su licenza con la licenza MIT. Per altre informazioni, vedere la [licenza](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Codice di comportamento per gli sviluppatori

Gli sviluppatori che usano Servizi cognitivi, compresi questo esempio e questa libreria client, sono tenuti a rispettare il "Codice di comportamento per lo sviluppatore per Servizi cognitivi Microsoft", disponibile alla pagina http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Passaggi successivi

Compilare ed estendere l'esercitazione usando i [file di origine del progetto](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) su GitHub.
