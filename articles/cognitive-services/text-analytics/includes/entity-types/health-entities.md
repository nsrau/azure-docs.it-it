---
title: Entità denominate per Healthcare
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 6880391fb54791fe5f597de2305d24f8c0e47ec6
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122448"
---
## <a name="health-entity-categories"></a>Categorie di entità di integrità:

Le categorie di entità seguenti vengono restituite da [analisi del testo per l'integrità](../../how-tos/text-analytics-for-health.md).  Si noti che in questa anteprima del contenitore è supportato solo testo in lingua inglese e in ogni immagine del contenitore viene fornita solo una versione del modello.

### <a name="named-entity-recognition"></a>Riconoscimento di entità denominate

|Categoria  |Descrizione   |
|----------|--------------|
| Età | Età. Ad esempio *30 anni fa*. |
| AdministrativeEvent | Evento amministrativo. |
| BodyStructure | Parti del corpo umano che includono organi e altre strutture. Ad esempio, *ARM*o *Heart*. | 
| CareEnvironment | Ambiente in cui viene gestita la cura o il trattamento. Ad esempio, *stanza di emergenza* | 
| ConditionQualifier | Livelli di condizione. Ad esempio, *lieve*, *esteso*o *diffuso*. | 
| Diagnosi | Condizioni mediche. Ad esempio, *ipertensione*. | 
| Direction | Le direzioni. Ad esempio *Left* o *anteriore*. | 
| Dosaggio | Dimensioni o quantità di un farmaco. Ad esempio *25mg*.  | 
| Examinname | Metodo o procedura di esame. Ad esempio *X-Ray*. | 
| RelationalOperator | Operatore che definisce una relazione tra due entità. Ad esempio *minore di*o `>=` .  | 
| MeasurementUnit | Unità di misura (ad esempio una percentuale). | 
| MeasurementValue | Valore numerico di un'unità di misura. | 
| FamilyRelation | Relazione familial. Ad esempio, *Sister*.  | 
| Frequenza | Frequenze.   | 
| Sesso | I sessi. | 
| Gene | Entità gene, ad esempio *TP53*.   | 
| HealthcareProfession | Metodo di amministrazione dei farmaci. Ad esempio, *amministrazione orale*. | 
| MedicationClass | Classi di farmaci. Ad esempio gli *antibiotici*.  | 
| MedicationForm | Forma di farmaco. Ad esempio *capsula*. | 
| Medicinename  | Farmaci generici e con nome. Ad esempio *Ibuprofen*. | 
| MedicationRoute | Metodo di amministrazione dei farmaci. Ad esempio, *amministrazione orale*. | 
| SymptomOrSign  | Sintomi di malattia. Ad esempio, *mal di gola*. | 
| Tempo | Volte. Ad esempio *8 anni* o *2: AM di oggi* |
| Treatmentname  | Nomi di trattamento. Ad esempio, la *terapia*. | 
| Variant | Variante genetica dell'entità gene. | 

### <a name="relation-extraction"></a>Estrazione di relazioni

L'estrazione di relazioni identifica connessioni significative tra i concetti indicati nel testo. Ad esempio, una relazione "ora della condizione" viene rilevata associando il nome di una condizione a un'ora. Analisi del testo per l'integrità può identificare le relazioni seguenti:

|Categoria  |Descrizione   |
|----------|--------------|
| DirectionOfBodyStructure | Direzione di una struttura del corpo. |
| DirectionOfCondition | Direzione di una condizione. |
| DirectionOfExamination | Direzione di un esame. |
| DirectionOfTreatment | Direzione di un trattamento. |
| TimeOfCondition | Tempo associato all'inizio di una condizione. |
| QualifierOfCondition | Qualificatore associato per una condizione. |
| DosageOfMedication | Dosaggio dei farmaci. |
| FormOfMedication | Forma di farmaco. |
| RouteOfMedication | Route o modalità di utilizzo di un medicinale. Ad esempio *orale*. |
| FrequencyOfMedication | Frequenza con cui viene utilizzato un farmaco. | 
| ValueOfCondition | Valore numerico associato a una condizione. |
| UnitOfCondition | Unità (ad esempio ora) associata a una condizione. |
| TimeOfMedication | Ora in cui un farmaco è stato utilizzato. |
| TimeOfTreatment | Ora di amministrazione di un trattamento. | 
| FrequencyOfTreatment | Frequenza con cui viene gestito un trattamento. |
| ValueOfExamination | Valore numerico associato a un esame. | 
| UnitOfExamination | Unità, ad esempio una percentuale, associata a un esame. |
| RelationOfExamination | Relazione tra un'entità e un esame. | 
| TimeOfExamination | Tempo associato a un esame. |
| Abbreviazione | Abbreviazione.  | 
