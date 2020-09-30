---
title: Abilità cognitiva rilevamento informazioni personali (anteprima)
titleSuffix: Azure Cognitive Search
description: Estrarre e mascherare le informazioni personali dal testo in una pipeline di arricchimento in Azure ricerca cognitiva. Questa competenza è attualmente disponibile in anteprima pubblica.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: acacf617d3f1d9ab891d08b32fc2dfb14deb64a4
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540524"
---
# <a name="pii-detection-cognitive-skill"></a>Competenze cognitive per il rilevamento delle informazioni personali

> [!IMPORTANT] 
> Questa competenza è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Attualmente non è disponibile alcun portale o supporto per .NET SDK.

L'abilità di **rilevamento** delle informazioni personali estrae informazioni personali da un testo di input e offre la possibilità di mascherarle. Questa competenza usa i modelli di Machine Learning forniti da [Analisi del testo](../cognitive-services/text-analytics/overview.md) in Servizi cognitivi.

> [!NOTE]
> Se si espande l'ambito aumentando la frequenza di elaborazione, aggiungendo più documenti oppure aggiungendo altri algoritmi di intelligenza artificiale, sarà necessario [collegare una risorsa fatturabile di Servizi cognitivi](cognitive-search-attach-cognitive-services.md). Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di cracking dei documenti in Ricerca cognitiva di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione di immagini sono descritti nella [pagina dei prezzi di Ricerca cognitiva di Azure](https://azure.microsoft.com/pricing/details/search/).

## <a name="odatatype"></a>@odata.type

Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>Limiti dei dati

Le dimensioni massime di un record devono essere di 50.000 caratteri in base alla misurazione di [`String.Length`](/dotnet/api/system.string.length). Se è necessario suddividere i dati prima di inviarli alla competenza, provare a usare la [capacità di suddivisione del testo](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole e sono tutti facoltativi.

| Nome parametro     | Descrizione |
|--------------------|-------------|
| `defaultLanguageCode` |    Codice lingua del testo di input. Per il momento, `en` è supportato solo. |
| `minimumPrecision` | Valore compreso tra 0,0 e 1,0. Se il Punteggio di confidenza (nell' `piiEntities` output) è inferiore al `minimumPrecision` valore impostato, l'entità non viene restituita o nascosta. Il valore predefinito è 0,0. |
| `maskingMode` | Parametro che fornisce vari modi per mascherare le informazioni personali rilevate nel testo di input. Sono supportate le opzioni seguenti: <ul><li>`none` (impostazione predefinita): non viene eseguita alcuna maschera e l' `maskedText` output non viene restituito. </li><li> `redact`: Rimuove le entità rilevate dal testo di input e non sostituisce i valori eliminati. In questo caso, l'offset nell' `piiEntities` output sarà correlato al testo originale e non al testo mascherato. </li><li> `replace`: Sostituisce le entità rilevate con il carattere specificato nel `maskingCharacter` parametro. Il carattere verrà ripetuto fino alla lunghezza dell'entità rilevata, in modo che gli offset corrispondano correttamente sia al testo di input sia all'output `maskedText` .</li></ul> |
| `maskingCharacter` | Il carattere utilizzato per mascherare il testo se il `maskingMode` parametro è impostato su `replace` . Sono supportate le opzioni seguenti: `*` (impostazione predefinita), `#` , `X` . Questo parametro può essere solo `null` se `maskingMode` non è impostato su `replace` . |

## <a name="skill-inputs"></a>Input competenze

| Nome input      | Descrizione                   |
|---------------|-------------------------------|
| `languageCode`    | Facoltativa. Il valore predefinito è `en`.  |
| `text`          | Testo da analizzare.          |

## <a name="skill-outputs"></a>Output competenze

| Nome output      | Descrizione                   |
|---------------|-------------------------------|
| `piiEntities` | Una matrice di tipi complessi, che contiene i campi seguenti: <ul><li>testo (informazioni personali effettive estratte)</li> <li>tipo</li><li>Sottotipo</li><li>Score (valore più elevato significa che è più probabile che sia un'entità reale)</li><li>offset (nel testo di input)</li><li>length</li></ul> </br> [I tipi e i sottotipi possibili sono disponibili qui.](../cognitive-services/text-analytics/named-entity-types.md?tabs=personal) |
| `maskedText` | Se `maskingMode` è impostato su un valore diverso da `none` , questo output sarà il risultato della stringa della maschera eseguita sul testo di input, come descritto dall'oggetto selezionato `maskingMode` .  Se `maskingMode` è impostato su `none` , l'output non sarà presente. |

## <a name="sample-definition"></a>Definizione di esempio

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
      }
    ]
  }
```

## <a name="sample-input"></a>Input di esempio

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
           }
      }
    ]
}
```

## <a name="sample-output"></a>Output di esempio

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

Gli offset restituiti per le entità nell'output di questa competenza vengono restituiti direttamente dalla [API analisi del testo](../cognitive-services/text-analytics/overview.md), il che significa che se li si usa per eseguire l'indicizzazione nella stringa originale, è necessario usare la classe [StringInfo](/dotnet/api/system.globalization.stringinfo) in .NET per estrarre il contenuto corretto.  [Altre informazioni sono disponibili qui.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="errors-and-warnings"></a>Errori e avvisi

Se il codice della lingua per il documento non è supportato, viene restituito un avviso e non viene estratta alcuna entità.
Se il testo è vuoto, viene restituito un avviso.
Se il testo contiene più di 50.000 caratteri, verranno analizzati solo i primi 50.000 caratteri e verrà generato un avviso.

Se la competenza restituisce un avviso, l'output `maskedText` può essere vuoto, che può influito sulle competenze downstream che prevedono l'output. Per questo motivo, assicurarsi di esaminare tutti gli avvisi correlati all'output mancante durante la scrittura della definizione di competenze.

## <a name="see-also"></a>Vedere anche

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)