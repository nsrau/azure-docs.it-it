---
title: Espressioni in app LUIS
titleSuffix: Azure Cognitive Services
description: Le espressioni sono gli input dell'utente che l'app ha bisogno di interpretare. Raccogliere le frasi che si ritiene verranno immesse dagli utenti. Includere espressioni con lo stesso significato ma con una costruzione diversa in termini di lunghezza e posizione delle parole.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 2b21102a5416c1fd7e2abb35be677c48ffc63263
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638076"
---
# <a name="utterances-in-luis"></a>Espressioni in LUIS

Le **espressioni** sono gli input dell'utente che l'app ha bisogno di interpretare. Per insegnare a LUIS a estrarre le finalità e le entità, è importante acquisire una varietà di input diversi per ogni finalità. L'apprendimento attivo, o processo di training continuativo di nuove espressioni, è fondamentale per l'intelligenza con apprendimento automatico fornito da LUIS.

Raccogliere le frasi che si ritiene verranno immesse dagli utenti. Includere espressioni con lo stesso significato ma con una costruzione diversa in termini di lunghezza e posizione delle parole. 

## <a name="how-to-choose-varied-utterances"></a>Come scegliere espressioni varie
Di seguito sono descritti alcuni principi da tenere a mente quando si inizia [aggiungendo espressioni di esempio](luis-how-to-add-example-utterances.md) al modello LUIS.

### <a name="utterances-arent-always-well-formed"></a>Le espressioni non sono sempre ben formate
Un'espressione potrebbe essere una frase come "Prenota un biglietto per Parigi" oppure un frammento di frase, come "Prenotazione" o "Volo Parigi".  Spesso gli utenti fanno errori di ortografia. Quando si pianifica l'app, valutare se eseguire il controllo ortografico o meno sull'input dell'utente prima di passarlo a LUIS. L'[API Controllo ortografico Bing][BingSpellCheck] è integrata con LUIS. È possibile associare l'app LUIS a una chiave esterna per l'API Controllo ortografico Bing quando si pubblica. Se non si esegue il controllo ortografico sulle espressioni dell'utente, è necessario formare LUIS sulle espressioni che includono errori di digitazione e di ortografia.

### <a name="use-the-representative-language-of-the-user"></a>Usare il linguaggio rappresentativo dell'utente
Quando si scelgono le espressioni, tenere presente che quello che si ritiene un termine o una frase comune potrebbe non esserlo per l'utente tipico dell'applicazione client. L'utente potrebbe non avere esperienza nel dominio. Fare quindi attenzione quando si usano termini o frasi che l'utente pronuncerebbe solo se fosse esperto.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Scegliere terminologia e frasi alternative
Si noterà che, anche se si fanno sforzi per creare una modelli di frase alternativi, alcuni termini verranno comunque ripetuti.

Considerare queste espressioni di esempio:

|Espressioni di esempio|
|--|
|come ottenere un computer?|
|Dove ottenere un computer?|
|Voglio ottenere un computer, come posso fare?|
|Quando posso ottenere un computer?| 

In questo caso non vengono usate alternative per il termine fondamentale "computer". Si potrebbe dire computer desktop, portatile, workstation o anche solo macchina. LUIS deduce in modo intelligente i sinonimi dal contesto, ma quando vengono create espressioni per il training, è comunque preferibile variare.

## <a name="example-utterances-in-each-intent"></a>Espressioni di esempio in ogni finalità
Ogni finalità deve avere espressioni di esempio, almeno 10 e 15. Se la finalità non prevede espressioni di esempio, non sarà possibile formare LUIS. Se una finalità contiene una o qualche espressione di esempio, LUIS non è in grado di prevederla con precisione. 

## <a name="add-small-groups-of-10-15-utterances-for-each-authoring-iteration"></a>Aggiungere piccoli gruppi di 10-15 espressioni per ogni iterazione di creazione
In ogni iterazione del modello, non aggiungere un'ampia quantità di espressioni. Aggiungere espressioni nella misura delle decine. Ripetere [training](luis-how-to-train.md), [pubblicazione](luis-how-to-publish-app.md) e [test](luis-interactive-test.md).  

LUIS compila modelli efficaci con espressioni selezionate con attenzione. L'aggiunta di un numero eccessivo di espressioni non è utile perché introduce confusione.  

È consigliabile iniziare con poche espressioni, poi [esaminare le espressioni dell'endpoint](luis-how-to-review-endoint-utt.md) per prevederne correttamente la finalità e per estrarre l'entità.

## <a name="ignoring-words-and-punctuation"></a>Ignorare parole e punteggiatura
Se si desidera che vengano ignorati parole o segni di punteggiatura specifici nell'espressione di esempio, usare un [criterio](luis-concept-patterns.md#pattern-syntax) con la sintassi _ignore_. 

## <a name="training-utterances"></a>Eseguire il training sulle espressioni
Il training non è determinante, in quanto la previsione delle espressioni potrebbe variare leggermente a seconda delle versioni o delle app.

## <a name="testing-utterances"></a>Eseguire il test delle espressioni 

Gli sviluppatori devono iniziare il test dell'applicazione LUIS con traffico reale inviando espressioni all'endpoint. Queste espressioni vengono usate per migliorare le prestazioni di finalità ed entità con l'[esame delle espressioni](luis-how-to-review-endoint-utt.md). I test inviati attraverso il riquadro di test del sito Web LUIS non vengono mandati attraverso l'endpoint e pertanto non contribuiscono all'apprendimento attivo. 

## <a name="review-utterances"></a>Esaminare le espressioni
Dopo che il modello è stato formato e pubblicato e dopo aver ricevuto le query dell'[endpoint](luis-glossary.md#endpoint), [esaminare le espressioni](luis-how-to-review-endoint-utt.md) inviate da LUIS. LUIS seleziona le espressioni dell'endpoint con punteggi bassi in termini di finalità o entità. 

## <a name="best-practices"></a>Procedure consigliate
Esaminare le [best practice](luis-concept-best-practices.md) per scoprire di più.

## <a name="next-steps"></a>Passaggi successivi
Consultare [Add example utterances](luis-how-to-add-example-utterances.md) (Aggiungere espressioni di esempio) per informazioni su come insegnare a un'app LUIS a capire le espressioni degli utenti.

[BingSpellCheck]: https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/proof-text