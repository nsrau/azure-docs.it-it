---
title: Competenza Divisione del testo della ricerca cognitiva - Ricerca di Azure
description: Suddividere il testo in blocchi o pagine di testo in base alla lunghezza in una pipeline di arricchimento di Ricerca di Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 376a5a2214c5f1d77439923d6a11aaad5e011e73
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313854"
---
#   <a name="text-split-cognitive-skill"></a>Competenza cognitiva Divisione del testo

La competenza **Divisione del testo** suddivide il testo in blocchi. È possibile specificare se si desidera suddividere il testo nelle frasi o nelle pagine di una determinata lunghezza. Questa competenza è particolarmente utile se ci sono requisiti di lunghezza massima del testo in altre competenze a valle. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Nome parametro     | DESCRIZIONE |
|--------------------|-------------|
| textSplitMode      | "Pagine" o "frasi" | 
| maximumPageLength | Se textSplitMode è impostato su "pagine", si riferisce alla lunghezza massima della pagina misurata da `String.Length`. Il valore minimo è 100.  Se textSplitMode è impostato su "pages", l'algoritmo tenterà di suddividere il testo in blocchi con una dimensione massima di "maximumPageLength". In questo caso, l'algoritmo cercherà di interrompere una frase al suo termine, per questo motivo la dimensione del blocco potrebbe essere leggermente inferiore a "maximumPageLength". | 
| defaultLanguageCode   | (facoltativo) Uno dei codici lingua seguenti: `da, de, en, es, fi, fr, it, ko, pt`. La lingua predefinita è Inglese (en). Alcuni aspetti da considerare:<ul><li>Se si passa un formato languagecode-countrycode, viene usata solo la parte languagecode del formato.</li><li>Se la lingua non è presente nell'elenco precedente, la competenza di suddivisione divide il testo in base ai limiti di carattere.</li><li>Fornire un codice lingua è utile per evitare di tagliare una parola a metà per le lingue che non usano spazi come il cinese, il giapponese e il coreano.</li></ul>  |


## <a name="skill-inputs"></a>Input competenze

| Nome parametro       | DESCRIZIONE      |
|----------------------|------------------|
| text  | Testo da suddividere in sottostringhe. |
| languageCode  | (Facoltativo) Codice lingua per il documento.  |

## <a name="skill-outputs"></a>Output competenze 

| Nome parametro     | DESCRIZIONE |
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
                "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia…",
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
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
