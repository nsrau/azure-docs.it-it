---
title: Entità denominate per Healthcare
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/15/2020
ms.author: aahi
ms.openlocfilehash: 906e8e9e6cde80a2151c6b8671f64854cb431250
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108500"
---
## <a name="health-entity-categories"></a>Categorie di entità di integrità:

Le categorie di entità seguenti vengono restituite da [analisi del testo per l'integrità](../../how-tos/text-analytics-for-health.md).  Si noti che in questa anteprima del contenitore è supportato solo testo in lingua inglese e in ogni immagine del contenitore viene fornita solo una versione del modello.

### <a name="named-entity-recognition"></a>Riconoscimento di entità denominate

|Category  |Descrizione   |
|----------|--------------|
| ETÀ | Età. |
| BODY_STRUCTURE | Parti del corpo umano che includono organi e altre strutture. | 
| CONDITION_QUALIFIER | Livelli di condizione quali *lieve*, *esteso*o *diffuso*. | 
| DIAGNOSI | Condizioni mediche. Ad esempio, *ipertensione* . | 
| DIREZIONE | Direzioni come *Left* o *anteriore*. | 
| DOSAGGIO | Dimensioni o quantità di un farmaco.  | 
| EXAMINATION_NAME | Metodo o procedura di esame. | 
| EXAMINATION_RELATION | associazione tra un'unità di misura e un esame.  | 
| EXAMINATION_UNIT | Unità di misura per un esame. | 
| EXAMINATION_VALUE | Valore dell'unità di misura dell'esame. | 
| FAMILY_RELATION | Relazione familiare, ad esempio *Sister*.  | 
| FREQUENZA | Frequenze.   | 
| GENERE | I sessi. | 
| GENE | Entità gene, ad esempio *TP53*.   | 
| MEDICATION_CLASS | Classi di farmaci. Ad esempio gli *antibiotici*.  | 
| MEDICATION_NAME  | Farmaci generici e con nome.| 
| ROUTE_OR_MODE  | Metodo di amministrazione dei farmaci. | 
| SYMPTOM_OR_SIGN  | Sintomi di malattia. | 
| TIME  | Volte. Ad esempio "8 anni" o "2:7.30 di oggi" |
| TREATMENT_NAME  | Nomi di trattamento. | 
| VARIANT  | Variante genetica dell'entità gene | 

### <a name="relation-extraction"></a>Estrazione di relazioni

L'estrazione di relazioni identifica connessioni significative tra i concetti indicati nel testo. Ad esempio, una relazione "ora della condizione" viene rilevata associando il nome di una condizione a un'ora. Analisi del testo per l'integrità può identificare le relazioni seguenti:

* DIRECTION_OF_BODY_STRUCTURE  
* TIME_OF_CONDITION
* QUALIFIER_OF_CONDITION  
* DOSAGE_OF_MEDICATION 
* FORM_OF_MEDICATION  
* ROUTE_OR_MODE_OF_MEDICATION   
* STRENGTH_OF_MEDICATION 
* ADMINISTRATION_RATE_OF_MEDICATION   
* FREQUENCY_OF_MEDICATION 
* TIME_OF_MEDICATION 
* TIME_OF_TREATMENT 
* FREQUENCY_OF_TREATMENT  
* VALUE_OF_EXAMINATION
* UNIT_OF_EXAMINATION 
* RELATION_OF_EXAMINATION 
* TIME_OF_EXAMINATION  
* ABBREVIAZIONE 
