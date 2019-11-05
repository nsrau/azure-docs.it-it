---
title: Competenza cognitiva Divisione del testo
titleSuffix: Azure Cognitive Search
description: Suddividere il testo in blocchi o pagine di testo in base alla lunghezza in una pipeline di arricchimento di intelligenza artificiale in Azure ricerca cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3f80169808b1e6420f04b786d2bb06bde9c96231
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73479669"
---
# <a name="text-split-cognitive-skill"></a>Competenza cognitiva Divisione del testo

La competenza **Divisione del testo** suddivide il testo in blocchi. È possibile specificare se si desidera suddividere il testo nelle frasi o nelle pagine di una determinata lunghezza. Questa competenza è particolarmente utile se ci sono requisiti di lunghezza massima del testo in altre competenze a valle. 

> [!NOTE]
> Questa competenza non è associata a un'API Servizi cognitivi e non vengono addebitati costi in caso di utilizzo. È tuttavia necessario [collegare una risorsa di Servizi cognitivi](cognitive-search-attach-cognitive-services.md) per eseguire l'override dell'opzione di risorsa **Gratuito**, che consente solo un numero ridotto di arricchimenti al giorno.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Nome parametro     | Descrizione |
|--------------------|-------------|
| textSplitMode      | "Pagine" o "frasi" | 
| maximumPageLength | Se textSplitMode è impostato su "pagine", si riferisce alla lunghezza massima della pagina misurata da `String.Length`. Il valore minimo è 100.  Se textSplitMode è impostato su "pages", l'algoritmo tenterà di suddividere il testo in blocchi con una dimensione massima di "maximumPageLength". In questo caso, l'algoritmo cercherà di interrompere una frase al suo termine, per questo motivo la dimensione del blocco potrebbe essere leggermente inferiore a "maximumPageLength". | 
| defaultLanguageCode   | (facoltativo) Uno dei codici lingua seguenti: `da, de, en, es, fi, fr, it, ko, pt`. La lingua predefinita è Inglese (en). Alcuni aspetti da considerare:<ul><li>Se si passa un formato languagecode-countrycode, viene usata solo la parte languagecode del formato.</li><li>Se la lingua non è presente nell'elenco precedente, la competenza di suddivisione divide il testo in base ai limiti di carattere.</li><li>Fornire un codice di lingua è utile per evitare di tagliare una parola a metà per i linguaggi diversi da uno spazio, come il cinese, il giapponese e il coreano.</li><li>Se non si conosce la lingua (ad esempio, è necessario suddividere il testo per l'input in [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), il valore predefinito di inglese (en) dovrebbe essere sufficiente. </li></ul>  |


## <a name="skill-inputs"></a>Input competenze

| Nome parametro       | Descrizione      |
|----------------------|------------------|
| text  | Testo da suddividere in sottostringhe. |
| languageCode  | (Facoltativo) Codice lingua per il documento. Se non si conosce la lingua (ad esempio, è necessario suddividere il testo per l'input in [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), è possibile rimuovere questo input.  |

## <a name="skill-outputs"></a>Output competenze 

| Nome parametro     | Descrizione |
|--------------------|-------------|
| textItems | Una matrice di sottostringhe che sono state estratte. |


##  <a name="sample-definition"></a>Definizione di esempio

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
        }
    ]
}
```

##  <a name="sample-input"></a>Input di esempio

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "This is a the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Output di esempio

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>Casi di errore
Se una lingua non è supportata, viene generato un avviso e il testo viene suddiviso in base ai limiti di carattere.

## <a name="see-also"></a>Vedere anche

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un insieme di competenze](cognitive-search-defining-skillset.md)
