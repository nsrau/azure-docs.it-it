---
title: Competenze cognitive deprecate
titleSuffix: Azure Cognitive Search
description: Questa pagina contiene un elenco di competenze cognitive considerate deprecate e non sarà supportata nel prossimo futuro in Azure ricerca cognitiva skillsets.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5f3587e4398be28cbaa2372be720258196bb48ff
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792027"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Competenze cognitive deprecate in Azure ricerca cognitiva

Questo documento descrive le competenze cognitive considerate deprecate. Usare la guida seguente per il contenuto:

* Nome della competenza: nome della competenza che sarà deprecata. Esegue il mapping all'attributo @odata.type.
* Ultima versione API disponibile: l'ultima versione dell'API di Azure ricerca cognitiva pubblico attraverso la quale è possibile creare o aggiornare skillsets che contengono la relativa abilità deprecata.
* Fine del supporto: ultimo giorno dopo il quale la competenza corrispondente è considerata non supportata. I set di competenze creati in precedenza dovrebbero continuare a funzionare, ma si consiglia agli utenti di eseguire la migrazione da una competenza deprecata.
* Raccomandazioni: percorso di migrazione per usare una competenza supportata. Gli utenti sono invitati a seguire le raccomandazioni per continuare a ricevere supporto.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Ultima versione disponibile dell'API

2017-11-11-Preview

### <a name="end-of-support"></a>Fine del supporto

15 febbraio 2019

### <a name="recommendations"></a>Raccomandazioni 

Usare invece [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). Fornisce la maggior parte delle funzionalità di NamedEntityRecognitionSkill, ma di qualità più elevata. Include anche informazioni più dettagliate nei campi dell'output complesso.

Per eseguire la migrazione alla [competenza di riconoscimento entità](cognitive-search-skill-entity-recognition.md), sarà necessario apportare una o più delle modifiche seguenti alla definizione della competenza. È possibile aggiornare la definizione della competenza usando l'[API di aggiornamento del set di competenze](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

> [!NOTE]
> il punteggio di attendibilità come concetto non è attualmente supportato. Il parametro `minimumPrecision` esiste in `EntityRecognitionSkill` per un utilizzo futuro e per la compatibilità con le versioni precedenti.

1. *(Obbligatorio)* Modificare `@odata.type` da `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` a `"#Microsoft.Skills.Text.EntityRecognitionSkill"`.

2. *(Facoltativo)* Se si usa l'output `entities`, usare invece l'output della raccolta complesso `namedEntities` di `EntityRecognitionSkill`. È possibile usare `targetName` nella definizione della competenza per eseguirne il mapping a un'annotazione denominata `entities`.

3. *(Facoltativo)* Se non si specifica in modo esplicito `categories`, `EntityRecognitionSkill` può restituire un tipo diverso di categorie oltre a quelle supportate da `NamedEntityRecognitionSkill`. Per non consentire questo comportamento, assicurarsi di impostare in modo esplicito il parametro `categories` su `["Person", "Location", "Organization"]`.

    _Definizioni di migrazione di esempio_

    * Migrazione di esempio

        _(Prima) Definizione della competenza NamedEntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _(Dopo) Definizione della competenza EntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * Migrazione leggermente complessa

        _(Prima) Definizione della competenza NamedEntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _(Dopo) Definizione della competenza EntityRecognition_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>Vedi anche

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un insieme di competenze](cognitive-search-defining-skillset.md)
+ [Competenza di riconoscimento entità](cognitive-search-skill-entity-recognition.md)
