---
title: Tipi di entità - LUIS
titleSuffix: Azure Cognitive Services
description: "Le entità estraggono i dati dall'espressione. I tipi di entità forniscono un'estrazione prevedibile dei dati. Esistono due tipi di entità: apprese automaticamente e non apprese dalla macchina. È importante sapere con quale tipo di entità si sta lavorando nelle espressioni."
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 6ee156efb5512c92d86ba05513b6a2b91df4eae8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221029"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entità e relativo scopo in LUIS

Lo scopo principale delle entità è fornire all'applicazione client un'estrazione prevedibile dei dati. Uno scopo _facoltativo_e secondario è aumentare la previsione della finalità o di altre entità con descrittori.

Esistono due tipi di entità:

* apprese automaticamente - dal contesto
* non-machine-learned - per corrispondenze esatte di testo, corrispondenze di modelli o rilevamento da parte di entità predefinite

Le entità apprese automaticamente offrono la più ampia gamma di opzioni di estrazione dei dati. Le entità non apprese dal computer funzionano in base alla corrispondenza del testo e possono essere utilizzate in modo indipendente o come [vincolo](#design-entities-for-decomposition) in un'entità appresa dal computer.

## <a name="entities-represent-data"></a>Le entità rappresentano dati

Le entità sono dati che si desidera estrarre dall'espressione, ad esempio nomi, date, nomi di prodotti o qualsiasi gruppo significativo di parole. Un'espressione può includere molte entità oppure nessuna. Un'applicazione client _potrebbe richiedere_ i dati per eseguire l'attività.

Le entità devono essere etichettate in modo coerente in tutte le espressioni di training per ogni finalità in un modello.

 È possibile definire entità personalizzate o utilizzare entità predefinite per risparmiare tempo per concetti comuni quali [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinale,](luis-reference-prebuilt-ordinal.md) [email](luis-reference-prebuilt-email.md)e numero di [telefono](luis-reference-prebuilt-phonenumber.md).

|Espressione|Entità|Dati|
|--|--|--|
|Buy 3 tickets to New York|Number predefinito<br>Location.Destination|3<br>New York|
|Buy a ticket from New York to London on March 5|Location.Origin<br>Location.Destination<br>DatetimeV2 predefinito|New York<br>Londra<br>March 5, 2018|

### <a name="entities-are-optional"></a>Le entità sono facoltative

A differenza delle finalità che sono obbligatorie, le entità sono facoltative. Non è necessario creare entità per ogni concetto nell'app, ma solo per quelli necessari all'applicazione client per eseguire un'azione.

Se le espressioni non dispongono di dati necessari per l'applicazione client, non è necessario aggiungere entità. Man mano che l'applicazione si sviluppa e viene identificata una nuova necessità di dati, è possibile aggiungere le entità appropriate al modello LUIS in un secondo momento.

## <a name="entity-compared-to-intent"></a>Entità e finalità

L'entità rappresenta un concetto di dati all'interno dell'espressione che si desidera estrarre.

Un'espressione può includere facoltativamente entità. In confronto, la stima della finalità per un'espressione è _necessaria_ e rappresenta l'intera espressione. LUIS richiede espressioni di esempio contenute in una finalità.

Considerare le 4 espressioni seguenti:Consider the following 4 utterances:

|Espressione|Finalità stimata|Entità estratte|Spiegazione|
|--|--|--|--|
|Guida|help|-|Niente da estrarre.|
|Invia qualcosa|sendSomething|-|Niente da estrarre. Il modello non è `something` stato sottoposto a training per l'estrazione in questo contesto e non vi è alcun destinatario.|
|Invia un regalo a Bob|sendSomething|`Bob`, `present`|Il modello è stato sottoposto a training con l'entità predefinita [personName,](luis-reference-prebuilt-person.md) che ha estratto il nome `Bob`. Un'entità appresa dal computer `present`è stata utilizzata per estrarre .|
|Invia a Bob una scatola di cioccolatini|sendSomething|`Bob`, `box of chocolates`|I due dati importanti `Bob` e `box of chocolates`i , sono stati estratti da entità.|

## <a name="design-entities-for-decomposition"></a>Progettare entità per la decomposizione

È consigliabile progettare l'entità per rendere l'entità di primo livello un'entità appresa dal computer. Ciò consente di apportare modifiche alla progettazione dell'entità nel tempo e dell'utilizzo di **sottocomponenti** (entità figlio), facoltativamente con **vincoli** e **descrittori**, per scomporre l'entità di primo livello nelle parti necessarie per l'applicazione client.

La progettazione per la scomposizione consente a LUIS di restituire un livello di risoluzione dell'entità completo all'applicazione client. Ciò consente all'applicazione client di concentrarsi sulle regole di business e lasciare la risoluzione dei dati a LUIS.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Le entità apprese automaticamente sono raccolte di dati primarieMachine-learned entities are primary data collections

[**Le entità apprese automaticamente**](tutorial-machine-learned-entity.md) sono l'unità dati di primo livello. I sottocomponenti sono entità figlio di entità apprese automaticamente.

Un'entità appresa dal computer viene attivata in base al contesto appreso tramite le espressioni di training. **I vincoli** sono regole facoltative applicate a un'entità appresa dal computer che vincola ulteriormente l'attivazione in base alla definizione di corrispondenza esatto-testo di un'entità non appresa dal computer, ad esempio [List](reference-entity-list.md) o [Regex](reference-entity-regular-expression.md). Ad esempio, `size` un'entità appresa dal `sizeList` computer può avere un `size` vincolo di un'entità `sizeList` elenco che vincola l'entità a triggersolo quando vengono rilevati valori contenuti nell'entità.

[**I descrittori**](luis-concept-feature.md) sono caratteristiche applicate per aumentare la pertinenza delle parole o delle frasi per la stima. Sono chiamati descrittori perché vengono *utilizzati* per *descrivere* una finalità o un'entità. I descrittori descrivono tratti o attributi distintivi dei dati, ad esempio parole o frasi importanti che LUIS osserva e aptrae attraverso.

Quando crei una funzionalità di elenco di frasi nell'app LUIS, questa viene abilitata a livello globale per impostazione predefinita e viene applicata in modo uniforme a tutte le finalità ed entità. Tuttavia, se si applica l'elenco di frasi come descrittore (caratteristica) di un'entità (o *modello)* appresa dal computer, il relativo ambito si riduce per applicarsi solo a tale modello e non viene più utilizzato con tutti gli altri modelli. L'utilizzo di un elenco di frasi come descrittore di un modello consente di favorire la scomposizione facilitando l'accuratezza del modello a cui viene applicato.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Tipi di entità

Scegliere l'entità in base a come devono essere estratti i dati e a come devono essere rappresentati dopo l'estrazione.

|Tipo di entità|Scopo|
|--|--|
|[**Apprese automaticamente**](tutorial-machine-learned-entity.md)|Le entità apprese automaticamente vengono appese dal contesto nell'espressione. Raggruppamento padre di entità, indipendentemente dal tipo di entità. Ciò rende significativa la variazione del posizionamento nelle espressioni di esempio. |
|[**Elenco**](reference-entity-list.md)|Elenco di elementi e dei relativi sinonimi estratti con **il testo esatto corrispondente**.|
|[**Pattern.any**](reference-entity-pattern-any.md)|Entità di cui è difficile determinare la fine. |
|[**Predefinita**](luis-reference-prebuilt-entities.md)|Già addestrato per estrarre tipi specifici di dati come URL o e-mail. Alcune di queste entità predefinite sono definite nel progetto open source [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). Se una lingua o un'entità specifica non è attualmente supportata, è possibile collaborare al progetto.|
|[**Espressione regolare**](reference-entity-regular-expression.md)|Utilizza l'espressione regolare per la **corrispondenza esatta del testo**.|

## <a name="extracting-contextually-related-data"></a>Estrazione di dati contestuali

Un'espressione può contenere due o più occorrenze di un'entità in cui il significato dei dati è basato sul contesto all'interno dell'espressione. Un esempio è un'espressione per la prenotazione di un volo con due posizioni, origine e destinazione.

`Book a flight from Seattle to Cairo`

I due esempi `location` di un'entità devono essere estratti. L'applicazione client deve conoscere il tipo di posizione per ciascuno per completare l'acquisto del biglietto.

Esistono due tecniche per l'estrazione di dati contestualmente correlati:There are two techniques for extracting contextually-related data:

 * L'entità `location` è un'entità appresa dal computer e `origin` `destination` utilizza due entità di sottocomponente per acquisire e (preferito)
 * L'entità `location` utilizza `origin` due **ruoli** di e`destination`

Più entità possono esistere in un'espressione e possono essere estratte senza usare la scomposizione o i ruoli se il contesto in cui vengono usate non ha alcun significato. Ad esempio, se l'espressione include `I want to travel to Seattle, Cairo, and London.`un elenco di posizioni, , si tratta di un elenco in cui ogni elemento non ha un significato aggiuntivo.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>Utilizzo di entità sottocomponente di un'entità appresa dalla macchina per definire il contestoUsing subcomponent entities of a machine-learned entity to define context

È possibile utilizzare [**un'entità appresa dal computer**](tutorial-machine-learned-entity.md) per estrarre i dati che descrivono l'azione di prenotazione di un volo e quindi per scomporre l'entità di primo livello nelle parti separate necessarie per l'applicazione client.

In questo `Book a flight from Seattle to Cairo`esempio, , l'entità `travelAction` di primo `flight from Seattle to Cairo`livello potrebbe essere ed etichettata per estrarre . Vengono quindi create due entità `origin` di `destination`sottocomponente, denominate e `geographyV2` , entrambe con un vincolo applicato dell'entità predefinita. Nelle espressioni di formazione, `origin` `destination` il e sono etichettati in modo appropriato.

### <a name="using-entity-role-to-define-context"></a>Utilizzo del ruolo Entità per definire il contestoUsing Entity role to define context

Un ruolo è un alias denominato per un'entità in base al contesto all'interno dell'espressione. Un ruolo può essere usato con qualsiasi tipo di entità predefinita o personalizzata, e può essere usato sia nelle espressioni che nei modelli di esempio. In questo esempio, `location` l'entità `origin` `destination` deve essere composta da due ruoli di e ed entrambi devono essere contrassegnati nelle espressioni di esempio.

Se LUIS `location` trova il ma non è in grado di determinare il ruolo, l'entità percorso viene comunque restituita. L'applicazione client avrebbe bisogno di seguire con una domanda per determinare il tipo di posizione che l'utente intendeva.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Se sono necessarie più entità rispetto al numero massimo

Se hai bisogno di più del limite, contatta il supporto. A tale scopo, raccogliere informazioni dettagliate relative al sistema, visitare il sito Web [LUIS](luis-reference-regions.md#luis-website) e quindi selezionare l'opzione relativa al **supporto**. Se la sottoscrizione di Azure include servizi di assistenza, contattare [il team di supporto di Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status"></a>Stato di stima dell'entità

Il portale LUIS mostra quando l'entità, in un'espressione di esempio, ha una stima dell'entità diversa rispetto all'entità selezionata. Questo punteggio diverso si basa sul modello sottoposto a training corrente.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui concetti delle [espressioni](luis-concept-utterance.md) valide.

Vedere [Aggiungere entità](luis-how-to-add-entities.md) per ulteriori informazioni sull'aggiunta di entità all'app LUIS.

Vedere [Esercitazione: Estrarre dati strutturati dall'espressione dell'utente con entità apprese](tutorial-machine-learned-entity.md) automaticamente in Informazioni sulla lingua (LUIS) per informazioni su come estrarre dati strutturati da un'espressione usando l'entità appresa dal computer.
 
