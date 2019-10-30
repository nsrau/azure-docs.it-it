---
title: Moderare il testo usando l'API Moderazione testo - Content Moderator
titleSuffix: Azure Cognitive Services
description: Provare a moderare il testo usando l'API Moderazione testo nella console online.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 467bf7fe26df2f826d6d44d42a9e30b98795232f
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043940"
---
# <a name="moderate-text-from-the-api-console"></a>Moderare il testo dalla console dell'API

Usare l' [API di moderazione del testo](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) in Azure Content Moderator per analizzare il contenuto di testo in modo volgare e confrontarlo con elenchi personalizzati e condivisi.

## <a name="get-your-api-key"></a>Ottenere la chiave dell'API

Prima di poter eseguire il test drive dell'API nella console online, è necessario avere la chiave di sottoscrizione. Questa si trova nella casella **Ocp-Apim-Subscription-Key** della scheda **Settings** (Impostazioni). Per altre informazioni, vedere la [panoramica](overview.md).

## <a name="navigate-to-the-api-reference"></a>Passare al riferimento sulle API

Passare al [riferimento sulle API Moderazione testo](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  Viene visualizzata la pagina **Text - Screen**.

## <a name="open-the-api-console"></a>Aprire la console dell'API

Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione. 

  ![Selezione dell'area nella pagina Text - Screen](images/test-drive-region.png)

  Viene visualizzata la console dell'API **Text - Screen**.

## <a name="select-the-inputs"></a>Selezionare gli input

### <a name="parameters"></a>parameters

Selezionare i parametri di query che si intende usare nella schermata di testo. Per questo esempio usare il valore predefinito per **language**. È possibile anche lasciarlo vuoto, perché l'operazione rileverà automaticamente la lingua nel corso della sua esecuzione.

> [!NOTE]
> Per il parametro **language** assegnare `eng` o lasciarlo vuoto per vedere la risposta alla **classificazione** automatica (funzionalità di anteprima). **Questa funzionalità supporta solo la lingua inglese**.
>
> Per rilevare i **termini volgari** usare il [codice ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) delle lingue supportate elencate in questo articolo o lasciare il campo vuoto.

Per **autocorrect**, **PII** e **classify (preview)** selezionare **true**. Lasciare vuoto il campo **ListId**.

  ![Parametri di query della console Text - Screen](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Tipo di contenuto

Per **Content-Type** selezionare il tipo di contenuto che si vuole visualizzare. Per questo esempio usare il tipo di contenuto **text/plain** predefinito. Nella casella **Ocp-Apim-Subscription-Key** immettere la chiave di sottoscrizione.

### <a name="sample-text-to-scan"></a>Testo di esempio da analizzare

Nella casella del **corpo della richiesta** immettere del testo. L'esempio seguente mostra un errore di digitazione intenzionale nel testo.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>Analizzare la risposta

La risposta seguente mostra le varie informazioni provenienti dall'API. Contiene potenziali volgarità, dati personali, classificazione (anteprima) e la versione con correzione automatica.

> [!NOTE]
> La funzionalità "Classification" (Classificazione) automatica è disponibile in anteprima e supporta solo la lingua inglese.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
"Misrepresentation":null,
"PII":{  
  "Email":[  
    {  
      "Detected":"abcdef@abcd.com",
      "SubType":"Regular",
      "Text":"abcdef@abcd.com",
      "Index":32
    }
  ],
  "IPA":[  
    {  
      "SubType":"IPV4",
      "Text":"255.255.255.255",
      "Index":72
    }
  ],
  "Phone":[  
    {  
      "CountryCode":"US",
      "Text":"6657789887",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"870 608 4000",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 870 608 4000",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0344 800 2400",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0800 820 3300",
      "Index":244
    }
  ],
  "Address":[  
    {  
      "Text":"1 Microsoft Way, Redmond, WA 98052",
      "Index":89
    }
  ],
  "SSN":[  
    {  
      "Text":"999999999",
      "Index":56
    },
    {  
      "Text":"999-99-9999",
      "Index":266
    }
  ]
},
"Classification":{  
  "ReviewRecommended":true,
  "Category1":{  
    "Score":1.5113095059859916E-06
  },
  "Category2":{  
    "Score":0.12747249007225037
  },
  "Category3":{  
    "Score":0.98799997568130493
  }
},
"Language":"eng",
"Terms":[  
  {  
    "Index":21,
    "OriginalIndex":21,
    "ListId":0,
    "Term":"crap"
  }
],
"Status":{  
  "Code":3000,
  "Description":"OK",
  "Exception":null
},
"TrackingId":"2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

Per una spiegazione dettagliata di tutte le sezioni della risposta JSON, vedere la Guida concettuale relativa alla [moderazione del testo](text-moderation-api.md) .

## <a name="next-steps"></a>Passaggi successivi

Usare l'API REST nel codice o seguire la [Guida introduttiva di .NET SDK](dotnet-sdk-quickstart.md) per l'integrazione con l'applicazione.
