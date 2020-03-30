---
title: Abilità cognitive di rilevamento PII (anteprima)PII Detection cognitive skill (preview)
titleSuffix: Azure Cognitive Search
description: Estrarre e mascherare le informazioni personali dal testo in una pipeline di arricchimento in Ricerca cognitiva di Azure.Extract and mask personally identifiable information from text in an enrichment pipeline in Azure Cognitive Search. Questa abilità è attualmente in anteprima pubblica.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: f21200bc6f5b25f3330f5bb87c0843caa5a84e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298887"
---
#    <a name="pii-detection-cognitive-skill"></a>Abilità cognitiva di rilevamento PII

> [!IMPORTANT] 
> Questa abilità è attualmente in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Attualmente non è disponibile alcun supporto per il portale o .NET SDK.

La competenza **Di rilevamento PII** estrae le informazioni personali da un testo di input e offre la possibilità di mascherarle da tale testo in vari modi. Questa competenza usa i modelli di Machine Learning forniti da [Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) in Servizi cognitivi.

> [!NOTE]
> Man mano che si espande l'ambito aumentando la frequenza di elaborazione, aggiungendo più documenti o aggiungendo più algoritmi di ia', sarà necessario [collegare una risorsa servizi cognitivi fatturabile.](cognitive-search-attach-cognitive-services.md) Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di cracking dei documenti in Ricerca cognitiva di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione di immagini sono descritti nella [pagina dei prezzi di Ricerca cognitiva di Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>Limiti dei dati
La dimensione massima di un record deve essere di [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)50.000 caratteri misurata da . Se è necessario suddividere i dati prima di inviarli alla competenza, è consigliabile utilizzare la [competenza Suddivisione testo](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole e sono tutti facoltativi.

| Nome parametro     | Descrizione |
|--------------------|-------------|
| defaultLanguageCode |    Codice lingua del testo di input. Per ora, `en` solo è supportato. |
| minimumPrecision | Un valore compreso tra 0,0 e 1,0. Se il punteggio `piiEntities` di confidenza `minimumPrecision` (nell'output) è inferiore al valore impostato, l'entità non viene restituita o mascherata. Il valore predefinito è 0,0. |
| maskingMode (Modalità mascheramento) | Parametro che fornisce vari modi per mascherare le informazioni personali rilevate nel testo di input. Sono supportate le opzioni seguenti: <ul><li>`none`(impostazione predefinita): ciò significa che `maskedText` non verrà eseguita alcuna mascheratura e l'output non verrà restituito. </li><li> `redact`: questa opzione rimuoverà le entità rilevate dal testo di input e non le sostituirà con nulla. Si noti che in questo `piiEntities` caso, l'offset nell'output sarà in relazione al testo originale e non al testo mascherato. </li><li> `replace`: questa opzione sostituirà le entità rilevate con il carattere specificato nel `maskingCharacter` parametro.  Il carattere verrà ripetuto alla lunghezza dell'entità rilevata in modo che gli offset `maskedText`corrispondano correttamente sia al testo di input che all'output.</li></ul> |
| mascheraturaCarattere | Carattere che verrà utilizzato per mascherare `maskingMode` il testo `replace`se il parametro è impostato su . Sono supportate le `*` seguenti `#`opzioni: (impostazione predefinita), , `X`. Questo parametro `null` può `maskingMode` essere solo `replace`se non è impostato su . |


## <a name="skill-inputs"></a>Input competenze

| Nome input      | Descrizione                   |
|---------------|-------------------------------|
| languageCode    | Facoltativa. Il valore predefinito è `en`.  |
| text          | Testo da analizzare.          |

## <a name="skill-outputs"></a>Output competenze

| Nome output      | Descrizione                   |
|---------------|-------------------------------|
| piiEentità | Una matrice di tipi complessi, che contiene i campi seguenti: <ul><li>testo (le informazioni personali effettive come estratto)</li> <li>type</li><li>Sottotipo</li><li>punteggio (valore più alto significa che è più probabile che sia un'entità reale)</li><li>offset (nel testo di input)</li><li>length</li></ul> </br> [I tipi e i sottotipi possibili sono disponibili qui.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| maskedText (testo mascherato) | Se `maskingMode` è impostato su `none`un valore diverso da , questo output sarà il risultato della `maskingMode`stringa della maschera eseguita sul testo di input come descritto dall'oggetto selezionato.  Se `maskingMode` è `none`impostato su , questo output non sarà presente. |

##    <a name="sample-definition"></a>Definizione di esempio

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
##    <a name="sample-input"></a>Input di esempio

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

##    <a name="sample-output"></a>Output di esempio

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

Si noti che gli offset restituiti per le entità nell'output di questa competenza vengono restituiti direttamente [dall'API Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), il che significa che se vengono utilizzati per indicizzare nella stringa originale, è necessario utilizzare la classe [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) in .NET per estrarre il contenuto corretto.  [Maggiori dettagli possono essere trovati qui.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-and-warning-cases"></a>Casi di errore e avviso
Se il codice lingua per il documento non è supportato, viene restituito un avviso e non viene estratta alcuna entità.
Se il testo è vuoto, verrà generato un avviso.
Se il testo contiene più di 50.000 caratteri, verranno analizzati solo i primi 50.000 caratteri e verrà generato un avviso.

Se la competenza restituisce `maskedText` un avviso, l'output potrebbe essere vuoto.  Ciò significa che se si prevede che l'output esista per l'input nelle competenze successive, non funzionerà come previsto. Tenere presente questo aspetto quando si scrive la definizione del set di competenze.

## <a name="see-also"></a>Vedere anche

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
