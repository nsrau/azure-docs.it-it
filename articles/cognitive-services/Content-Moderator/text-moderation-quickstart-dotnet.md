---
title: 'Avvio rapido: Analizzare il contenuto di testo in C# - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Come analizzare il contenuto del testo di vario materiale discutibile usando Content Moderator SDK per .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 6383a495ed3e3876e96b1249cb7b2286b240a481
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884597"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-c"></a>Guida introduttiva: Analizzare il contenuto del testo per individuare materiale inappropriato in C# 

Questo articolo contiene informazioni ed esempi di codice utili per iniziare a usare [Content Moderator SDK per .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). Si imparerà a filtrare e classificare il contenuto di testo in base a determinati termini allo scopo di sottoporre a moderazione materiale potenzialmente sgradevole.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti
- Una chiave di sottoscrizione di Content Moderator. Seguire le istruzioni in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per effettuare la sottoscrizione a Content Moderator e ottenere la chiave.
- Qualsiasi edizione di [Visual Studio 2015 o 2017](https://www.visualstudio.com/downloads/).

> [!NOTE]
> In questa guida viene usata una sottoscrizione di Content Moderator del livello gratuito. Per informazioni su quanto offerto da ogni livello di sottoscrizione, vedere la pagina [Prezzi e limiti](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/).

## <a name="create-the-visual-studio-project"></a>Creare il progetto di Visual Studio

1. In Visual Studio creare un nuovo progetto **App console (.NET Framework)** e assegnargli il nome **TextModeration**. 
1. Se la soluzione contiene anche altri progetti, selezionare questo come solo progetto di avvio.
1. Ottenere i pacchetti NuGet necessari. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**, quindi trovare e installare i pacchetti seguenti:
    - Microsoft.Azure.CognitiveServices.ContentModerator
    - Microsoft.Rest.ClientRuntime
    - Newtonsoft.Json

## <a name="add-text-moderation-code"></a>Aggiungere il codice di moderazione testo

Copiare e incollare nel progetto il codice contenuto in questa guida per implementare uno scenario di base di moderazione del contenuto.

### <a name="include-namespaces"></a>Includere gli spazi dei nomi

Aggiungere le istruzioni `using` seguenti all'inizio del file *Program.cs*.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>Creare il client di Content Moderator

Aggiungere il codice seguente al file *Program.cs* per creare un provider client di Content Moderator per la sottoscrizione. Aggiungere il codice accanto alla classe **Program**, nello stesso spazio dei nomi. Sarà necessario aggiornare i campi **AzureRegion** e **CMSubscriptionKey** con i valori rispettivamente dell'identificatore di area e della chiave di sottoscrizione.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=54-77)]

### <a name="set-up-input-and-output-targets"></a>Configurare le destinazioni di input e output

Aggiungere i campi statici seguenti alla classe **Program** in _Program.cs_. Questi campi specificano i file per il contenuto del testo di input e il contenuto JSON di output.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=15-19)]

Occorrerà creare il file di input *TextFile.txt* e aggiornarne il percorso di conseguenza (i percorsi relativi sono relativi alla directory di esecuzione). Aprire _TextFile.txt_ e aggiungere il testo da sottoporre a moderazione. Questa guida introduttiva usa il testo di esempio seguente:

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="load-the-input-text"></a>Caricare il testo di input

Aggiungere il codice seguente al metodo **Main**. Il metodo **ScreenText** è l'operazione essenziale. I suoi parametri specificano le operazioni di moderazione del contenuto che verranno eseguite. In questo esempio il metodo è configurato per:
- Rilevare possibile contenuto volgare nel testo.
- Normalizzare il testo e correggere automaticamente gli errori di digitazione.
- Rilevare informazioni personali (PII), ad esempio numeri di telefono degli Stati Uniti e del Regno Unito, indirizzi di posta elettronica e indirizzi postali degli Stati Uniti.
- Usare modelli basati su Machine Learning per classificare il testo in tre categorie.

Per informazioni più approfondite su queste operazioni, selezionare il collegamento nella sezione [Passaggi successivi](#next-steps).

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=23-48)]

## <a name="run-the-program"></a>Eseguire il programma

Il programma scriverà i dati di stringa JSON nel file _TextModerationOutput.txt_. Il testo di esempio usato in questa guida introduttiva produce l'output seguente:

```json
Autocorrect typos, check for matching terms, PII, and classify.
{
"OriginalText": "\"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).\"",
"NormalizedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"AutoCorrectedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"Misrepresentation": null,
    
"Classification": {
        "Category1": {
        "Score": 1.5113095059859916E-06
        },
        "Category2": {
        "Score": 0.12747249007225037
        },
        "Category3": {
        "Score": 0.98799997568130493
        },
        "ReviewRecommended": true
  },
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "PII": {
        "Email": [
            {
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 33
            }
            ],
        "IPA": [
            {
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 73
            }
            ],
        "Phone": [
            {
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 57
            },
            {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 211
            },
            {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 207
            },
            {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 227
            },
            {
        "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 244
            }
            ],
         "Address": [{
             "Text": "1 Microsoft Way, Redmond, WA 98052",
             "Index": 89
                }]
    },
  "Language": "eng",
  "Terms": [
    {
        "Index": 22,
        "OriginalIndex": 22,
        "ListId": 0,
        "Term": "crap"
    }
  ],
  "TrackingId": "9392c53c-d11a-441d-a874-eb2b93d978d3"
}
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata sviluppata una semplice applicazione .NET che usa il servizio Content Moderator per restituire informazioni rilevanti su un determinato esempio di testo. Nel prossimo articolo si imparerà il significato dei diversi flag e delle diverse classificazioni, in modo da poter decidere quali dati sono necessari e in che modo devono essere gestiti dall'app.

> [!div class="nextstepaction"]
> [Guida alla moderazione del testo](text-moderation-api.md)
