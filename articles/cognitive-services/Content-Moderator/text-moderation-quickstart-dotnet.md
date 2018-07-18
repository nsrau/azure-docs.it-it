---
title: Content Moderator di Azure - Moderazione del testo usando .NET | Microsoft Docs
description: Come moderare il testo usando Content Moderator SDK di Azure per .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/04/2018
ms.author: sajagtap
ms.openlocfilehash: 238d086e87b0e52f0887af5c4db58e8f72796b49
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374484"
---
# <a name="moderate-text-using-net"></a>Moderare il testo usando .NET

Questo articolo contiene informazioni ed esempi di codice per iniziare a usare Content Moderator SDK per .NET allo scopo di:
- Rilevare potenziali messaggi con contenuto volgare nel testo con il filtro basato su termini
- Usare modelli basati su machine-learning per [classificare il testo](text-moderation-api.md#classification) in tre categorie.
- Rilevare informazioni personali (PII), ad esempio numeri di telefono degli Stati Uniti e del Regno Unito, indirizzi di posta elettronica e indirizzi postali degli Stati Uniti.
- Normalizzazione del testo e correzione automatica degli errori di digitazione

Questo articolo presuppone che si abbia già familiarità con Visual Studio e C#.

## <a name="sign-up-for-content-moderator-services"></a>Registrarsi nei servizi di Content Moderator

Prima di poter usare i servizi di Content Moderator tramite l'API REST o l'SDK, è necessario ottenere una chiave di sottoscrizione.
Consultare l'[Avvio rapido](quick-start.md) per informazioni su come ottenere la chiave.

## <a name="create-your-visual-studio-project"></a>Creare il progetto di Visual Studio

1. Aggiungere un nuovo progetto **App console (.NET Framework)** alla soluzione.

   Nel codice di esempio, denominare il progetto **TextModeration**.

1. Selezionare questo progetto come progetto di avvio singolo per la soluzione.

1. Aggiungere un riferimento all'assembly del progetto **ModeratorHelper** creato in [Content Moderator client helper quickstart](content-moderator-helper-quickstart-dotnet.md) (Avvio rapido helper del client di Content Moderator).

### <a name="install-required-packages"></a>Installare i pacchetti necessari

Installare i pacchetti NuGet seguenti:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Aggiornare le istruzioni using del programma

Modificare le istruzioni using del programma.

    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;


### <a name="initialize-application-specific-settings"></a>Inizializzare le impostazioni specifiche dell'applicazione

Aggiungere alla classe **Program** i campi statici seguenti in Program.cs.

    /// <summary>
    /// The name of the file that contains the text to evaluate.
    /// </summary>
    /// <remarks>You will need to create an input file and update this path
    /// accordingly. Relative paths are ralative the execution directory.</remarks>
    private static string TextFile = "TextFile.txt";

    /// <summary>
    /// The name of the file to contain the output from the evaluation.
    /// </summary>
    /// <remarks>Relative paths are ralative the execution directory.</remarks>
    private static string OutputFile = "TextModerationOutput.txt";

È stato usato il testo seguente per generare l'output di questo Avvio rapido:

> [!NOTE]
> Il numero di previdenza sociale non valido nel testo di esempio seguente è intenzionale. Lo scopo è fornire un esempio del formato di input e output.

    Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
    These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
    0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).

## <a name="add-code-to-load-and-evaluate-the-input-text"></a>Aggiungere il codice per caricare e valutare il testo di input

Aggiungere il codice seguente al metodo **Main**.

    // Load the input text.
    string text = File.ReadAllText(TextFile);
    text = text.Replace(System.Environment.NewLine, " ");

    // Save the moderation results to a file.
    using (StreamWriter outputWriter = new StreamWriter(OutputFile, false))
    {
        // Create a Content Moderator client and evaluate the text.
        using (var client = Clients.NewClient())
        {
            // Screen the input text: check for profanity, classify the text into three categories
                // do autocorrect text, and check for personally identifying 
                // information (PII)
                outputWriter.WriteLine("Autocorrect typos, check for matching terms, PII, and classify.");
                var screenResult =
                    client.TextModeration.ScreenText("eng", "text/plain", text, true, true, null, true);
                outputWriter.WriteLine(
                        JsonConvert.SerializeObject(screenResult, Formatting.Indented));
        }
        outputWriter.Flush();
        outputWriter.Close();
    }

> [!NOTE]
> La chiave del servizio Content Moderator ha un limite di frequenza di richieste al secondo (RPS); se questo limite viene superato, l'SDK genera un'eccezione con un codice di errore 429.
>
> Una chiave di livello gratuito prevede un unico limite di frequenza RPS.

## <a name="run-the-program-and-review-the-output"></a>Eseguire il programma ed esaminare l'output

L'esempio di output per il programma, così come scritto nel file di log è:

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
            "   CountryCode": "UK",
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

## <a name="next-steps"></a>Passaggi successivi

[Scaricare la soluzione Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) per questa e altre soluzioni per l'Avvio rapido di Content Moderator per .NET.
