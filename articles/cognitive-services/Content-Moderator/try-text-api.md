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
ms.openlocfilehash: ad365c2d4c171105d8dec89d818ef481361d1ff8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81272594"
---
# <a name="moderate-text-from-the-api-console"></a>Moderare il testo dalla console dell'API

Usare l' [API di moderazione del testo](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) in Azure Content Moderator per analizzare il contenuto di testo in modo volgare e confrontarlo con elenchi personalizzati e condivisi.

## <a name="get-your-api-key"></a>Ottenere la chiave dell'API

Prima di poter eseguire il test drive dell'API nella console online è necessario disporre della chiave di sottoscrizione. Questa si trova nella casella **Ocp-Apim-Subscription-Key** della scheda **Settings** (Impostazioni). Per altre informazioni, vedere la [panoramica](overview.md).

## <a name="navigate-to-the-api-reference"></a>Passare al riferimento sulle API

Passare al [riferimento sulle API Moderazione testo](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  Viene visualizzata la pagina **Text - Screen**.

## <a name="open-the-api-console"></a>Aprire la console dell'API

Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione. 

  ![Selezione dell'area nella pagina Text - Screen](images/test-drive-region.png)

  Viene visualizzata la console dell'API **Text - Screen**.

## <a name="select-the-inputs"></a>Selezionare gli input

### <a name="parameters"></a>Parametri

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
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>Analizzare la risposta

La risposta seguente mostra le varie informazioni provenienti dall'API. Contiene potenziali volgarità, dati personali, classificazione (anteprima) e la versione con correzione automatica.

> [!NOTE]
> La funzionalità "Classification" (Classificazione) automatica è disponibile in anteprima e supporta solo la lingua inglese.

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
   "pii":{
      "email":[
         {
            "detected":"abcdef@abcd.com",
            "sub_type":"Regular",
            "text":"abcdef@abcd.com",
            "index":32
         }
      ],
      "ssn":[

      ],
      "ipa":[
         {
            "sub_type":"IPV4",
            "text":"255.255.255.255",
            "index":72
         }
      ],
      "phone":[
         {
            "country_code":"US",
            "text":"6657789887",
            "index":56
         }
      ],
      "address":[
         {
            "text":"1 Microsoft Way, Redmond, WA 98052",
            "index":89
         }
      ]
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

Per una spiegazione dettagliata di tutte le sezioni della risposta JSON, vedere la Guida concettuale relativa alla [moderazione del testo](text-moderation-api.md) .

## <a name="next-steps"></a>Passaggi successivi

Usare l'API REST nel codice o seguire la [Guida introduttiva di .NET SDK](dotnet-sdk-quickstart.md) per l'integrazione con l'applicazione.
