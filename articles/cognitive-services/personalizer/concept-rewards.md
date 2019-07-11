---
title: Punteggio di ricompensa - Personalizza esperienze
titleSuffix: Azure Cognitive Services
description: Il punteggio di ricompensa indica quanto ha funzionato la scelta di personalizzazione, RewardActionID, per l'utente. Il valore del punteggio di ricompensa è determinato dalla logica di business sulla base delle osservazioni del comportamento degli utenti. Personalizza esperienze esegue il training dei modelli di Machine Learning valutando le ricompense.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 06/07/2019
ms.author: edjez
ms.openlocfilehash: c64d43301fd173203bd1625b8d37120b71c22805
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077404"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>I punteggi di ricompensa indicano il grado di successo della personalizzazione

Il punteggio di ricompensa indica l'efficacia della scelta di personalizzazione, [RewardActionID](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response), per l'utente. Il valore del punteggio di ricompensa è determinato dalla logica di business sulla base delle osservazioni del comportamento degli utenti.

Personalizza esperienze esegue il training dei modelli di Machine Learning valutando le ricompense. 

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Usare l'API Ricompensa per inviare il punteggio di ricompensa a Personalizza esperienze

Le ricompense vengono inviate a Personalizza esperienze dall'[API Ricompensa](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward). Una ricompensa è un numero compreso tra -1 e 1. Personalizza esperienze esegue il training del modello per ottenere la somma più alta possibile di ricompense nel tempo.

Le ricompense vengono inviate dopo che si è verificato il comportamento degli utenti, che può avvenire alcuni giorni più tardi. Il tempo massimo di attesa prima che Personalizza esperienze consideri un evento come evento senza ricompensa, o con una ricompensa predefinita, viene configurato con [Reward Wait Time](#reward-wait-time) (Tempo di attesa ricompense) nel portale di Azure.

Se non si riceve il punteggio di ricompensa per un evento nel tempo indicato per **Reward Wait Time** (Tempo di attesa ricompense), verrà applicato il valore di **Default Reward** (Ricompensa predefinita). Il valore di **[Default Reward](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** (Ricompensa predefinita) è impostato su zero.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Comportamenti e dati da considerare per le ricompense

Prendere in considerazione questi segnali e comportamenti per il contesto del punteggio di ricompensa:

* Input utente diretto per i suggerimenti quando si tratta di opzioni ("Intendi X?").
* Durata della sessione.
* Tempo tra le sessioni.
* Analisi del sentiment delle interazioni dell'utente.
* Domande dirette e brevi sondaggi in cui il bot chiede all'utente un feedback sull'utilità e sull'accuratezza.
* Risposta agli avvisi o ritardo nel rispondere agli avvisi.

## <a name="composing-reward-scores"></a>Composizione dei punteggi di ricompensa

Un punteggio di ricompensa deve essere calcolato nella logica di business. Il punteggio può essere rappresentato come:

* Un singolo numero inviato una volta 
* Un punteggio inviato immediatamente (ad esempio 0,8) e un punteggio aggiuntivo inviato in un secondo momento (in genere 0,2).

## <a name="default-rewards"></a>Ricompense predefinite

Se non viene ricevuta alcuna ricompensa nel tempo indicato per [Reward Wait Time](#reward-wait-time) (Tempo di attesa ricompense), il tempo trascorso dopo la chiamata a Classifica, Personalizza esperienze applica implicitamente il valore di **Default Reward** (Ricompensa predefinita) all'evento di Classifica.

## <a name="building-up-rewards-with-multiple-factors"></a>Creazione di ricompense con più fattori  

Per una personalizzazione valida, è possibile creare il punteggio di ricompensa (qualsiasi numero compreso tra -1 e 1) in base a più fattori. 

È ad esempio possibile applicare queste regole per la personalizzazione di un elenco di contenuti video:

|Comportamento utente|Valore punteggio parziale|
|--|--|
|L'utente ha fatto clic sul primo elemento.|Ricompensa +0,5|
|L'utente ha aperto il contenuto effettivo di tale elemento.|Ricompensa +0,3|
|L'utente ha guardato 5 minuti di contenuto o il 30% del contenuto, a seconda del valore corrispondente al tempo più lungo.|Ricompensa +0,2|
|||

È quindi possibile inviare la ricompensa totale all'API.

## <a name="calling-the-reward-api-multiple-times"></a>Chiamata all'API Ricompensa ripetuta più volte

È anche possibile chiamare l'API Ricompensa con lo stesso ID evento inviando punteggi di ricompensa diversi. Quando ottiene le ricompense, Personalizza esperienze determina la ricompensa finale per l'evento aggregandole in base alle impostazioni.

Impostazioni di aggregazione:

*  **Primo**: accetta il primo punteggio di ricompensa ricevuto per l'evento e rimuove il resto.
* **Somma**: accetta tutti i punteggi di ricompensa raccolti per il corrispondente eventId e li somma.

Tutte le ricompense di un evento che vengono ricevute dopo il tempo specificato per **Reward Wait Time** (Tempo di attesa ricompense) vengono ignorate e non hanno effetto sul training dei modelli.

Sommando i punteggi di ricompensa, è possibile che una ricompensa finale risulti maggiore di 1 o minore di -1. Questo risultato non comporta l'esito negativo del servizio.

<!--
@edjez - is the number ignored if it is outside the acceptable range?
-->

## <a name="best-practices-for-calculating-reward-score"></a>Procedure consigliate per il calcolo del punteggio di ricompensa

* **Prendere in considerazione indicatori effettivi dell'esito positivo della personalizzazione**: è facile pensare in termini di clic, ma una buona ricompensa si basa su ciò che gli utenti devono *ottenere* e non su ciò che devono *fare*.  Il calcolo delle ricompense in base ai clic, ad esempio, può determinare la selezione di contenuto soggetto a clickbait.

* **Usare un punteggio di ricompensa per indicare il successo della personalizzazione**: la personalizzazione di un suggerimento per un film dovrebbe indurre l'utente a guardare il film e assegnare una valutazione elevata. Poiché la valutazione del film dipende probabilmente da molti fattori (la qualità della recitazione, lo stato d'animo dell'utente), non è un segnale di ricompensa appropriato per il funzionamento della *personalizzazione*. Il fatto che l'utente guardi i primi minuti del film, tuttavia, può essere un segnale migliore dell'efficacia della personalizzazione e l'invio di una ricompensa pari a 1 dopo cinque minuti sarà un segnale migliore.

* **Le ricompense si applicano solo a RewardActionID**: Personalizza esperienze applica le ricompense per comprendere l'efficacia dell'azione specificata in RewardActionID. Se si sceglie di visualizzare altre azioni e l'utente fa clic su di esse, la ricompensa deve essere pari a zero.

* **Prendere in considerazione le conseguenze impreviste**: creare funzioni di ricompensa che portano a risultati responsabili con [etica e uso responsabile](ethics-responsible-use.md).

* **Usare ricompense incrementali**: l'aggiunta di ricompense parziali per comportamenti utente minori consente a Personalizza esperienze di ottenere ricompense migliori. La ricompensa incrementale consente all'algoritmo di capire che si sta avvicinando all'obiettivo di indurre nell'utente il comportamento desiderato finale.
    * Se si mostra un elenco di film e l'utente passa il puntatore del mouse sul primo per un periodo di tempo e visualizza altre informazioni, è possibile determinare che si è verificato un engagement dell'utente. Al comportamento può essere assegnato un punteggio di ricompensa pari a 0,1. 
    * Se l'utente ha aperto la pagina per poi uscire, il punteggio di ricompensa può essere di 0,2. 

## <a name="reward-wait-time"></a>Reward wait time (Tempo di attesa ricompense)

Personalizza esperienze mette in correlazione le informazioni di una chiamata a Classifica con le ricompense inviate nelle chiamate a Ricompensa per il training del modello. Queste chiamate possono verificarsi in momenti diversi. Personalizza esperienze attende per un periodo di tempo limitato, a partire dal momento in cui si verifica la chiamata a Classifica, anche se tale chiamata viene eseguita come evento inattivo e attivata in un secondo momento.

Se il valore di **Reward Wait Time** (Tempo di attesa ricompense) scade e non sono state fornite informazioni di ricompensa, verrà applicata una ricompensa predefinita a tale evento per il training. La durata di attesa massima è sei giorni.

## <a name="best-practices-for-setting-reward-wait-time"></a>Procedure consigliate per l'impostazione del tempo di attesa delle ricompense

Seguire questi consigli per ottenere risultati ottimali.

* Impostare il tempo di attesa ricompense più breve possibile, lasciando un tempo sufficiente per ottenere il feedback degli utenti. 

<!--@Edjez - storage quota? -->

* Non scegliere una durata inferiore al tempo necessario per ottenere il feedback. Se ad esempio alcune ricompense vengono fornite dopo che un utente ha guardato un video per un minuto, la durata dell'esperimento deve corrispondere almeno al doppio.

## <a name="next-steps"></a>Passaggi successivi

* [Apprendimento per rinforzo](concepts-reinforcement-learning.md) 
* [Provare l'API Classifica](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Provare l'API Ricompensa](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
