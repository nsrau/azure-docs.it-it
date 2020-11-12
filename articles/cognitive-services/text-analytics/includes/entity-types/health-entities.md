---
title: Entità denominate per Healthcare
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 10/02/2020
ms.author: aahi
ms.openlocfilehash: 308b2a9cb00f44f0e330d4fef5592d8855ee3394
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553192"
---
## <a name="text-analytics-for-health-categories-entities-and-attributes"></a>Analisi del testo per categorie di integrità, entità e attributi

[Analisi del testo per l'integrità](../../how-tos/text-analytics-for-health.md) rileva i concetti medici nelle categorie seguenti.  Si noti che in questa anteprima del contenitore è supportato solo testo in lingua inglese e in ogni immagine del contenitore viene fornita una sola versione del modello.


| Category  | Descrizione  |
|---------|---------|
| ANATOMIA | concetti che acquisiscono informazioni su sistemi di corpo, anatomia, siti, posizioni o aree geografiche. |
 | Demografia | concetti che acquisiscono informazioni sul sesso e l'età. |
 | ESAME | concetti che consentono di acquisire informazioni su procedure e test diagnostici. |
 | GENOMICA | concetti che acquisiscono informazioni su geni e varianti. |
 | SETTORE sanitario | concetti che acquisiscono informazioni sugli eventi amministrativi, gli ambienti di assistenza e le professioni sanitarie. |
 | CONDIZIONE MEDICA | concetti che acquisiscono informazioni sulle diagnosi, i sintomi o i segni. |
 | FARMACO | concetti che acquisiscono informazioni sui medicinali, inclusi i nomi dei farmaci, le classi, il dosaggio e il percorso di amministrazione. |
 | SOCIALE | concetti che acquisiscono informazioni sugli aspetti sociali medici pertinenti, ad esempio la relazione tra le famiglie. |
 | TRATTAMENTO | concetti che consentono di acquisire informazioni sulle procedure terapeutiche. |
  
Ogni categoria può includere due gruppi di concetti:

* **Entità** : termini che acquisiscono concetti medici come la diagnosi, il nome del farmaco o il nome del trattamento.  Ad esempio, la *bronchite* è una diagnosi e l' *aspirina* è un nome di farmaco.  Le menzioni in questo gruppo possono essere collegate a un ID concetto UMLS.
* **Attributi** : le frasi che forniscono ulteriori informazioni sull'entità rilevata, ad esempio, *grave* è un qualificatore di condizione per la *bronchita* o *81 mg* è un dosaggio per l' *aspirina*.  Le menzioni in questa categoria non verranno collegate a un ID concetto UMLS.

Inoltre, il servizio riconosce le relazioni tra i diversi concetti, incluse le relazioni tra attributi ed entità, ad esempio, la *direzione* alla *struttura del corpo* o il *dosaggio* al *nome dei farmaci* e le relazioni tra le entità, ad esempio il rilevamento delle abbreviazioni.

## <a name="anatomy"></a>Anatomia

### <a name="entities"></a>Entità

Sistemi del corpo **BODY_STRUCTURE** , posizioni anatomiche o aree e siti del corpo. Ad esempio, ARM, ginocchio, addome, Nose, fegato, Head, sistema respiratorio, linfociti.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Esempio di entità della struttura del corpo.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Esempio espanso dell'entità della struttura del corpo.":::

### <a name="attributes"></a>Attributes

Termini **direzionali direzionali,** ad esempio: Left, laterale, Upper, posterior, che caratterizza una struttura del corpo.

:::image type="content" source="../../media/ta-for-health/anatomy-attributes.png" alt-text="Esempio di attributo direzionale.":::

### <a name="supported-relations"></a>Relazioni supportate

* **DIRECTION_OF_BODY_STRUCTURE**

## <a name="demographics"></a>Demographics

### <a name="entities"></a>Entità

**Età** : tutti i termini e le frasi, inclusi quelli di un paziente, i membri della famiglia e altri. Ad esempio, 40-anno-vecchio, 51 yo, 3 mesi precedenti, adulti, Infant, anziani, giovani, minori, a metà età.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Esempio di un'entità Age.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Un altro esempio di entità Age.":::


**Sesso** : termini che divulgano il sesso dell'oggetto. Ad esempio, Maschio, femminile, donna, gentiluomo, signora.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Esempio di entità Gender.":::

### <a name="attributes"></a>Attributes

**RELATIONAL_OPERATOR** le frasi che esprimono la relazione tra un'entità demografica e informazioni aggiuntive.

:::image type="content" source="../../media/ta-for-health/relational-operator.png" alt-text="Esempio di operatore relazionale.":::

## <a name="examinations"></a>Esami

### <a name="entities"></a>Entità

**EXAMINATION_NAME** : procedure e test diagnostici. Ad esempio, MRI, ECG, test HIV, emoglobina, numero di piastrine, sistemi di scalabilità come la *scala di Bristol sgabello*.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Esempio di entità Exam.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Un altro esempio di entità nome esame.":::

### <a name="attributes"></a>Attributes

**Direzione** : termini direzionali che caratterizzano un esame.

:::image type="content" source="../../media/ta-for-health/exam-direction-attribute.png" alt-text="Esempio di un attributo Direction con un'entità nome esame.":::

**MEASUREMENT_UNIT** : l'unità dell'esame. Ad esempio, in *emoglobina > 9,5 g/dl* , il termine *g/dl* è l'unità per il test di *emoglobina* .

:::image type="content" source="../../media/ta-for-health/exam-unit-attribute.png" alt-text="Esempio di un attributo di unità di misura con un'entità nome esame.":::

**MEASUREMENT_VALUE** : il valore dell'esame. Ad esempio, in *emoglobina > 9,5 g/dl* , il termine *9,5* è il valore per il test di *emoglobina* .

:::image type="content" source="../../media/ta-for-health/exam-value-attribute.png" alt-text="Esempio di attributo valore di misurazione con un'entità nome esame.":::

**RELATIONAL_OPERATOR** : frasi che esprimono la relazione tra un esame e informazioni aggiuntive. Ad esempio, il valore di misurazione richiesto per un esame di destinazione.

:::image type="content" source="../../media/ta-for-health/exam-relational-operator-attribute.png" alt-text="Un esempio di operatore relazionale con un'entità nome esame.":::

**Tempo** : termini temporali relativi all'inizio e/o alla lunghezza (durata) di un esame. Ad esempio, quando si è verificato il test.

:::image type="content" source="../../media/ta-for-health/exam-time-attribute.png" alt-text="Esempio di un attributo time con un'entità nome esame.":::

### <a name="supported-relations"></a>Relazioni supportate

+ **DIRECTION_OF_EXAMINATION**
+   **RELATION_OF_EXAMINATION**
+   **TIME_OF_EXAMINATION**
+   **UNIT_OF_EXAMINATION**
+   **VALUE_OF_EXAMINATION**

## <a name="genomics"></a>Genomica

### <a name="entities"></a>Entità

**Gene** : tutte le menzioni dei geni. Ad esempio, MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Esempio di un'entità gene.":::

**Variant** : tutte le menzioni delle varianti di gene. Ad esempio, c. 524C>T, (MTRR): r.1462_1557del96
  
## <a name="healthcare"></a>Servizi sanitari

### <a name="entities"></a>Entità
  
**ADMINISTRATIVE_EVENT** : eventi correlati al sistema sanitario, ma di natura amministrativa/semi-amministrativa. Ad esempio, registrazione, ammissione, valutazione, immissione di studi, trasferimento, discarico, ricovero, degenza, ospedale. 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Esempio di entità evento Healthcare.":::

**CARE_ENVIRONMENT** : un ambiente o una località in cui i pazienti sono interessati. Ad esempio, stanza di emergenza, ufficio del medico, unità cardio, Hospice, ospedale.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="Questa schermata mostra un esempio di entità dell'ambiente sanitario.":::

**HEALTHCARE_PROFESSION** : un professionista sanitario autorizzato o non concesso in licenza. Ad esempio, dentista, patologo, neurologo, radiologo, farmacista, nutrizionista, fisioterapista, chiropratico.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="Questo screenshot mostra un altro esempio di entità dell'ambiente sanitario.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Un altro esempio di entità dell'ambiente sanitario.":::

## <a name="medical-condition"></a>Condizione medica

### <a name="entities"></a>Entità

**Diagnosi** : malattia, sindrome, avvelenamento. Ad esempio, cancro del seno, Alzheimer, HTN, CHF, lesione del midollo spinale.

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Esempio di entità di condizione medica.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="Un altro esempio di entità condizione medica.":::

**SYMPTOM_OR_SIGN** : evidenza soggettiva o oggettiva della malattia o di altre diagnosi. Ad esempio, dolore toracico, cefalea, vertigini, rash, SINGHIOZZo, addome è stato leggero, ottimo suono intestinale, ben nutrito.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="Esempio di un segno di condizione medica o di un'entità sintomo.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="Un altro esempio di un segno di condizione medica o di un'entità sintomo.":::

### <a name="attributes"></a>Attributes

Termini di qualità **CONDITION_QUALIFIER** utilizzati per descrivere una condizione medica. Tutte le sottocategorie seguenti sono considerate qualificatori:

1.  Espressioni correlate al tempo: si tratta di termini che descrivono in modo qualitativo la dimensione temporale, ad esempio improvviso, acuto, cronico, di lunga durata. 
2.  Espressioni di qualità: si tratta di termini che descrivono la "natura" della condizione medica, ad esempio Burning, Sharp.
3.  Espressioni di gravità: grave, lieve, un bit, non controllato.
4.  Espressioni Extensivity: locale, focale, diffusa.
5.  Espressioni di radiazione: radianti, radiazioni.
6.  Scala della condizione: in alcuni casi, una condizione è caratterizzata da una scala, ovvero un elenco di valori ordinato finito. Ad esempio, pazienti con cancro pancreatico di fase III.
7.  Corso della condizione: un termine che è correlato al corso o alla progressione di una condizione, ad esempio miglioramento, peggioramento, risoluzione, restituzione. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="Un esempio di attributo di qualificatore della condizione e di un'entità di diagnostica.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="Un altro esempio di attributo di qualificatore della condizione e di un'entità di diagnostica.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="Esempio di attributo di qualificatore della condizione con entità sintomo e farmaco.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="Questa schermata mostra un altro esempio di attributo di qualificatore della condizione con un'entità di diagnostica.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="Questa schermata mostra un esempio aggiuntivo di attributo di qualificatore della condizione con un'entità di diagnosi.":::

Termini direzionali della **direzione** che caratterizzano una condizione medica del corpo.

:::image type="content" source="../../media/ta-for-health/medical-condition-direction-attribute.png" alt-text="Esempio di attributo Direction con un'entità Condition medica.":::

**Frequenza: frequenza** con cui si è verificata una condizione medica, o che si verifica.

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute.png" alt-text="Esempio di attributo Frequency con un'entità Condition medica.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute-2.png" alt-text="Un altro esempio di attributo Direction con un sintomo o un'entità Sign.":::

**MEASUREMENT_UNIT** : unità che caratterizza una condizione medica. Ad esempio, nel *tumore da 1,5 x2x1 cm* , il termine *cm* è l'unità di misura per il *tumore*. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-unit-attribute.png" alt-text="Esempio di attributo di unità di misura con entità di condizione medica.":::

**MEASUREMENT_VALUE** : valore che caratterizza una condizione medica. Ad esempio, nel *tumore da 1,5 x2x1 cm* il termine *1.5 x2x1* rappresenta il valore di misurazione del *tumore*. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-value-attribute.png" alt-text="Screenshot mostra un esempio di attributo Direction con un'entità sintomo o firma.":::

**RELATIONAL_OPERATOR** le frasi che esprimono la relazione tra informazioni aggiuntive sulla condizione medica. Ad esempio, valore time o Measurement. 

:::image type="content" source="../../media/ta-for-health/medical-condition-relational-operator.png" alt-text="Screenshot mostra un altro esempio di attributo Direction con un'entità sintomo o firma.":::

Termini **temporali** relativi all'inizio e/o alla lunghezza (durata) di una condizione medica. Ad esempio, quando viene avviato un sintomo (inizio) o quando si è verificata una malattia.

:::image type="content" source="../../media/ta-for-health/medical-condition-time-attribute.png" alt-text="Screenshot mostra un esempio aggiuntivo di un attributo Direction con un'entità sintomo o Sign.":::

### <a name="supported-relations"></a>Relazioni supportate

+ **DIRECTION_OF_CONDITION**
+   **QUALIFIER_OF_CONDITION**
+   **TIME_OF_CONDITION**
+   **UNIT_OF_CONDITION**
+   **VALUE_OF_CONDITION**

## <a name="medication"></a>Farmaco

### <a name="entities"></a>Entità

**MEDICATION_CLASS** : un set di farmaci che hanno un meccanismo di azione simile, una modalità di azione correlata, una struttura chimica simile e/o vengono usati per trattare la stessa malattia. Ad esempio, inibitore ACE, oppioidi, antibiotici, antidolorifici.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Esempio di entità di classe medica.":::

**MEDICATION_NAME** : i farmaci citati, inclusi i nomi delle marche con copyright e i nomi non di marchio. Ad esempio, Advil, Ibuprofen.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Esempio di entità nome di un farmaco.":::

### <a name="attributes"></a>Attributes

**Dosaggio** : quantità di medicinali ordinata. Ad esempio, infondere la soluzione cloruro di sodio *1000 ml*.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Esempio di attributo di dosaggio dei farmaci.":::

**Frequenza: frequenza** con cui deve essere eseguito un farmaco.

:::image type="content" source="../../media/ta-for-health/medication-frequency.png" alt-text="Esempio di attributo di frequenza dei farmaci.":::

:::image type="content" source="../../media/ta-for-health/medication-frequency-2.png" alt-text="Un altro esempio di attributo di frequenza dei farmaci.":::

**MEDICATION_FORM** : il formato del farmaco. Ad esempio soluzione, pillola, capsula, tablet, patch, gel, incolla, schiuma, spray, gocce, panna, sciroppo.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Esempio di attributo del form di un farmaco.":::

**MEDICATION_ROUTE** : metodo di amministrazione dei farmaci. Ad esempio, orale, vaginale, IV, epidurale, topico, inalato.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Esempio di attributo route di un farmaco.":::

**RELATIONAL_OPERATOR** le frasi che esprimono la relazione tra i farmaci e le informazioni aggiuntive. Ad esempio, il valore di misurazione obbligatorio.

:::image type="content" source="../../media/ta-for-health/medication-relational-operator.png" alt-text="Screenshot mostra un esempio di attributo di operatore relazionale con un'entità medica.":::

:::image type="content" source="../../media/ta-for-health/medication-time.png" alt-text="Screenshot mostra un altro esempio di un attributo di operatore relazionale con un'entità medica.":::

### <a name="supported-relations"></a>Relazioni supportate

+ **DOSAGE_OF_MEDICATION**
+   **FORM_OF_MEDICATION**
+   **FREQUENCY_OF_MEDICATION**
+   **ROUTE_OF_MEDICATION**
+   **TIME_OF_MEDICATION**
  
## <a name="treatment"></a>Modalità di gestione

### <a name="entities"></a>Entità

**TREATMENT_NAME** : procedure terapeutiche. Ad esempio, chirurgia di sostituzione del ginocchio, trapianto del midollo osseo, TAVI, dieta.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Esempio di entità nome trattamento.":::

### <a name="attributes"></a>Attributes

Termini **direzionali direzionali che** caratterizzano un trattamento.

:::image type="content" source="../../media/ta-for-health/treatment-direction.png" alt-text="Screenshot mostra un esempio di attributo direzione trattamento.":::

**Frequenza: frequenza** con cui si verifica un trattamento o che deve verificarsi.

:::image type="content" source="../../media/ta-for-health/treatment-frequency.png" alt-text="Screenshot mostra un altro esempio di attributo direzione trattamento.":::
 
**RELATIONAL_OPERATOR** le frasi che esprimono la relazione tra trattamento e informazioni aggiuntive.  Ad esempio, la quantità di tempo passata dalla procedura precedente.

:::image type="content" source="../../media/ta-for-health/treatment-relational-operator.png" alt-text="Esempio di attributo dell'operatore relazionale di trattamento.":::

Termini **temporali** relativi all'inizio e/o alla lunghezza (durata) di un trattamento. Ad esempio, la data in cui è stato fornito il trattamento.

:::image type="content" source="../../media/ta-for-health/treatment-time.png" alt-text="Screenshot mostra un esempio di attributo tempo di trattamento.":::


### <a name="supported-relations"></a>Relazioni supportate

+ **DIRECTION_OF_TREATMENT**
+   **TIME_OF_TREATMENT**
+   **FREQUENCY_OF_TREATMENT**

## <a name="social"></a>Social network

### <a name="entities"></a>Entità

**FAMILY_RELATION** : menzioni dei parenti della famiglia dell'oggetto. Ad esempio, father, Daughter, di pari livello, parents.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="Screenshot mostra un altro esempio di attributo tempo di trattamento.":::
