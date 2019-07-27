---
title: Moderazione testo - Content Moderator
titleSuffix: Azure Cognitive Services
description: Usare la moderazione del testo per rilevare possibili contenuti, informazioni personali ed elenchi personalizzati di termini indesiderati.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: e1d5224d8dc86c82624613b0d2a984ceef3ae5bf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564386"
---
# <a name="learn-text-moderation-concepts"></a>Informazioni sui concetti di moderazione del testo

Usare la moderazione del testo automatica e le funzioni che prevedono la [revisione umana](Review-Tool-User-Guide/human-in-the-loop.md) di Content Moderator per moderare i contenuti di testo.

Bloccare, approvare o revisionare il contenuto in base ai propri criteri e alle proprie soglie. Usarlo per estendere la moderazione umana degli ambienti in cui partner, dipendenti e consumer generano contenuto di testo. Tra questi vi sono chat room, forum di discussione, chatbot, cataloghi e-commerce e documenti. 

La risposta del servizio include le informazioni seguenti:

- Contenuto volgare: corrispondenza dei termini all'elenco integrato di termini volgari in varie lingue
- Classificazione: classificazione automatica in tre categorie
- Dati personali
- Testo corretto automaticamente
- Testo originale
- Linguaggio

## <a name="profanity"></a>Contenuto volgare

Se l'API rileva eventuali termini volgari in una qualsiasi delle [lingue supportate](Text-Moderation-API-Languages.md), tali termini vengono inclusi nella risposta. La risposta contiene inoltre la loro posizione (`Index`) nel testo originale. Il `ListId` nell'esempio di JSON seguente fa riferimento a termini trovati negli [elenchi di termini personalizzati](try-terms-list-api.md), se disponibili.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Per il parametro **language** assegnare `eng` o lasciarlo vuoto per vedere la risposta alla **classificazione** automatica (funzionalità di anteprima). **Questa funzionalità supporta solo la lingua inglese**.
>
> Per rilevare i **termini volgari** usare il [codice ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) delle lingue supportate elencate in questo articolo o lasciare il campo vuoto.

## <a name="classification"></a>classificazione

La **funzione di classificazione del testo** automatica di Content Moderator supporta **solo la lingua inglese** e consente di rilevare i contenuti potenzialmente indesiderati. Il contenuto contrassegnato può essere valutato inappropriato in base al contesto. Indica la probabilità di ogni categoria e potrebbe consigliare una revisione umana. La funzione usa un modello impostato per identificare un eventuale linguaggio offensivo, dispregiativo o discriminatorio. Sono inclusi termini gergali, parole abbreviate, parole offensive e parole intenzionalmente errate per la revisione. 

L'estratto seguente nell'estratto JSON mostra un esempio dell'output:

    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
            },
        "Category2": {
            "Score": 0.12747249007225037
            },
        "Category3": {
            "Score": 0.98799997568130493
        }
    }

### <a name="explanation"></a>Spiegazione

- `Category1` si riferisce alla potenziale presenza di un linguaggio che può essere considerato sessualmente esplicito o per adulti in determinate situazioni.
- `Category2` si riferisce alla potenziale presenza di un linguaggio che può essere considerato sessualmente esplicito o per adulti in determinate situazioni.
- `Category3` si riferisce alla potenziale presenza di un linguaggio che può essere considerato offensivo in determinate situazioni.
- `Score` è compreso tra 0 e 1. Maggiore è il punteggio, più alto è il modello che prevede che la categoria possa essere applicabile. Questa funzionalità si basa su un modello statistico e non su risultati codificati manualmente. Si consiglia di eseguire i test con i propri contenuti per determinare come ogni categoria si allinei alle proprie esigenze.
- `ReviewRecommended` è true o false a seconda delle soglie interne del punteggio. I clienti devono valutare se usare questo valore o optare per le soglie personalizzate in base ai propri criteri relativi ai contenuti.

## <a name="personal-data"></a>Dati personali

La funzionalità Informazioni personali rileva la possibile presenza di queste informazioni:

- Indirizzo di posta elettronica
- Indirizzo postale degli Stati Uniti
- Indirizzo IP
- Numero di telefono degli Stati Uniti
- Numero di telefono del Regno Unito
- Codice fiscale

L'esempio seguente riporta una risposta di esempio:

    "PII": {
        "Email": [{
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 32
            }],
        "IPA": [{
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 72
            }],
        "Phone": [{
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 56
            }, {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 212
            }, {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 208
            }, {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 228
            }, {
            "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 245
            }],
        "Address": [{
            "Text": "1 Microsoft Way, Redmond, WA 98052",
            "Index": 89
            }],
        "SSN": [{
            "Text": "999999999",
            "Index": 56
            }, {
            "Text": "999-99-9999",
            "Index": 267
            }]
        }

## <a name="auto-correction"></a>Correzione automatica

Si supponga che il testo di input sia ('lzay' e 'f0x' sono intenzionali):

    The qu!ck brown f0x jumps over the lzay dog.

Se si richiede la correzione automatica, la risposta conterrà la versione corretta del testo:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Creazione e gestione di elenchi di termini personalizzati

Sebbene l'elenco globale dei termini predefinito funzioni alla perfezione nella maggior parte dei casi, è consigliabile confrontarlo con termini specifici per i propri requisiti aziendali. Ad esempio, si consiglia di filtrare qualsiasi marchio della concorrenza dai messaggi degli utenti.

> [!NOTE]
> È previsto un limite massimo di **cinque elenchi di termini** e ogni elenco **non può includere più di 10.000 termini**.
>

L'esempio illustra l'ID elenco corrispondente:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Content Moderator fornisce un'[API per elenchi di termini](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) con operazioni per gestire elenchi di termini personalizzati. Iniziare con la [Console dell'API per elenchi di termini](try-terms-list-api.md) e usare gli esempi di codice API REST. Estrarre anche l'[Avvio rapido per gli elenchi di termini in .NET](term-lists-quickstart-dotnet.md) se si ha familiarità con Visual Studio e C#.

## <a name="next-steps"></a>Passaggi successivi

Eseguire il test drive della [Console dell'API Moderazione testo](try-text-api.md) e usare gli esempi di codice API REST. Usare anche [Avvio rapido per la moderazione del testo .NET](text-moderation-quickstart-dotnet.md) se si ha familiarità con Visual Studio e C#.
