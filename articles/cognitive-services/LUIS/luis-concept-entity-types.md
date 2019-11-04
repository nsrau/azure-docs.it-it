---
title: Tipi di entità-LUIS
titleSuffix: Azure Cognitive Services
description: "Le entità estraggono i dati dall'espressione. I tipi di entità offrono un'estrazione prevedibile dei dati. Esistono due tipi di entità: Machine-learned e non-Machine-Learned. È importante individuare il tipo di entità che si sta utilizzando nelle espressioni."
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9dc26e50e1c0f43e816e422f0fee91a246ea04a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487594"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entità e relativo scopo in LUIS

Lo scopo principale delle entità è fornire all'applicazione client l'estrazione dei dati prevedibile. Uno scopo _facoltativo_, secondario è quello di aumentare la stima della finalità con i descrittori. 

Esistono due tipi di entità: 

* dal contesto di apprendimento automatico
* non-Machine-learned-per corrispondenze esatte del testo

Iniziare sempre con un'entità appresa dal computer perché fornisce la gamma più ampia di opzioni di estrazione dei dati.

## <a name="entity-compared-to-intent"></a>Entità e finalità

L'entità rappresenta un concetto di dati all'interno dell'espressione che si desidera estrarre. 

Prendere in considerazione le 3 espressioni seguenti:

|Espressione|Dati estratti|Spiegazione|
|--|--|--|
|`Help`|-|Nessun elemento da estrarre.|
|`Send Bob a present`|Bob, presente|Bob è sicuramente importante per completare l'attività. È possibile che le informazioni presenti siano sufficienti o che il bot debba chiarire il problema con una domanda di completamento.|
|`Send Bob a box of chocolates.`|Le due parti importanti dei dati, Bob e la scatola dei cioccolati, sono importanti per completare la richiesta dell'utente.|

Un'espressione può includere molte entità oppure nessuna. Un'applicazione client _può_ richiedere l'entità per eseguire la relativa attività. 

Per confronto, la stima dello scopo di un enunciato è _obbligatoria_ e rappresenta l'intera espressione. LUIS richiede che le espressioni di esempio siano contenute in un Intent. Se l'intenzione principale dell'espressione non è importante per l'applicazione client, aggiungere tutte le espressioni alla finalità None. 

Se si trova, più avanti nel ciclo di vita dell'app si vuole suddividere le espressioni, è possibile farlo facilmente. Questa operazione può essere eseguita per organizzare le espressioni durante la creazione o per usare l'intenzione prevista nell'applicazione client. 

Non è necessario utilizzare lo scopo previsto nell'applicazione client, ma viene restituito come parte della risposta dell'endpoint di stima.

## <a name="entities-represent-data"></a>Le entità rappresentano dati

Le entità sono dati di cui si intende eseguire il pull dall'espressione. Può trattarsi di un nome, di una data, del nome di un prodotto o di un qualsiasi gruppo di parole. 

|Espressione|Persona giuridica|Dati|
|--|--|--|
|Buy 3 tickets to New York|Number predefinito<br>Location.Destination|3<br>New York|
|Buy a ticket from New York to London on March 5|Location.Origin<br>Location.Destination<br>DatetimeV2 predefinito|New York<br>Londra<br>5 marzo 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Le entità sono facoltative ma consigliate

A differenza delle finalità che sono obbligatorie, le entità sono facoltative. Non è necessario creare entità per ogni concetto nell'app, ma solo per quelli necessari all'applicazione client per eseguire un'azione. 

Se le espressioni non contengono dettagli che il bot deve continuare, non è necessario aggiungerle. Man mano che l'app evolve, sarà possibile aggiungerle in un secondo momento. 

In caso di dubbi su come usare le informazioni, aggiungere alcune entità predefinite comuni, ad esempio [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinal](luis-reference-prebuilt-ordinal.md), [email](luis-reference-prebuilt-email.md) e [phone number](luis-reference-prebuilt-phonenumber.md).

## <a name="design-entities-for-decomposition"></a>Progetta entità per la scomposizione

Iniziare la progettazione dell'entità con un'entità appresa dal computer. Questo consente di semplificare la crescita della progettazione e le modifiche dell'entità nel tempo. Aggiungere **sottocomponenti** (entità figlio) con **vincoli** e **descrittori** per completare la progettazione delle entità. 

La progettazione per la scomposizione consente a LUIS di restituire un livello di risoluzione approfondito dell'entità all'applicazione client. Ciò consente all'applicazione client di concentrarsi sulle regole business e lasciare la risoluzione dei dati a LUIS.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Le entità apprese dal computer sono raccolte dati primarie

Le entità apprese dal computer sono l'unità dati di primo livello. I sottocomponenti sono entità figlio di entità apprese dal computer. 

I **vincoli** sono entità corrispondenti al testo esatto che applicano regole per identificare ed estrarre i dati. I **descrittori** sono funzionalità applicate per aumentare la pertinenza delle parole o delle frasi per la stima.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Tipi di entità

Scegliere l'entità in base a come devono essere estratti i dati e a come devono essere rappresentati dopo l'estrazione.

|Tipo di entità|Finalità|
|--|--|
|[**Apprendimento automatico**](#composite-entity)|Raggruppamento padre di entità, indipendentemente dal tipo di entità. Le entità apprese dal computer imparano dal contesto nell'espressione. In questo modo la variazione del posizionamento in espressioni di esempio è significativa. |
|[**Elenco**](#list-entity)|Elenco di elementi e relativi sinonimi estratti con la **corrispondenza esatta del testo**.|
|[**Pattern.any**](#patternany-entity)|Entità di cui è difficile determinare la fine. |
|[**Predefinita**](#prebuilt-entity)|È già stato eseguito il training per estrarre un tipo specifico di dati, ad esempio URL o posta elettronica. Alcune di queste entità predefinite sono definite nel progetto open source [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). Se una lingua o un'entità specifica non è attualmente supportata, è possibile collaborare al progetto.|
|[**Espressione regolare**](#regular-expression-entity)|Usa l'espressione regolare per la **corrispondenza esatta del testo**.|

### <a name="entity-role-defines-context"></a>Il ruolo entità definisce il contesto

Il ruolo di un'entità è l'alias denominato basato sul contesto all'interno dell'espressione. Un esempio è un enunciato per la prenotazione di un volo con due località, Origin e Destination.

`Book a flight from Seattle to Cairo`

È necessario estrarre i due esempi di un'entità `location`. Per completare l'acquisto del ticket, l'applicazione client deve conoscerne il tipo di posizione. L'entità `location` necessita di due ruoli di `origin` e `destination` ed entrambi devono essere contrassegnati nelle espressioni di esempio. 

Se LUIS trova il `location` ma non riesce a determinare il ruolo, l'entità location viene ancora restituita. L'applicazione client dovrà completare una domanda per determinare il tipo di posizione che l'utente ha significato. 

Più entità possono esistere in un enunciato e possono essere estratte senza usare i ruoli. Se il contesto della frase indica il valore dell'entità, è necessario usare un ruolo.

Se l'espressione include un elenco di percorsi, `I want to travel to Seattle, Cairo, and London.`, si tratta di un elenco in cui ogni elemento non ha un significato aggiuntivo. 

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Se sono necessarie più entità rispetto al numero massimo 

Se è necessario più del limite, contattare il supporto tecnico. A tale scopo, raccogliere informazioni dettagliate relative al sistema, visitare il sito Web [LUIS](luis-reference-regions.md#luis-website) e quindi selezionare l'opzione relativa al **supporto**. Se la sottoscrizione di Azure include servizi di supporto tecnico, contattare [il supporto tecnico Azure](https://azure.microsoft.com/support/options/). 

## <a name="entity-prediction-status"></a>Stato stima entità

Il portale LUIS Mostra quando l'entità, in un enunciato di esempio, ha una stima di entità diversa rispetto all'entità selezionata. Questo punteggio diverso si basa sul modello attualmente sottoposto a training. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui concetti delle [espressioni](luis-concept-utterance.md) valide. 

Vedere [Aggiungere entità](luis-how-to-add-entities.md) per altre informazioni sull'aggiunta di entità all'app LUIS.
