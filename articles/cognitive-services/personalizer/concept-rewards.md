---
title: Punteggio di ricompensa - Personalizza esperienze
titleSuffix: Azure Cognitive Services
description: Il punteggio di ricompensa indica quanto ha funzionato la scelta di personalizzazione, RewardActionID, per l'utente. Il valore del punteggio di ricompensa è determinato dalla logica di business sulla base delle osservazioni del comportamento degli utenti. Personalizza esperienze esegue il training dei modelli di Machine Learning valutando le ricompense.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: a47d6014e51dce81c9caf82f8624896c439f050d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490886"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>I punteggi di ricompensa indicano il grado di successo della personalizzazione

Il punteggio di ricompensa indica l'efficacia della scelta di personalizzazione, [RewardActionID](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response), per l'utente. Il valore del punteggio di ricompensa è determinato dalla logica di business sulla base delle osservazioni del comportamento degli utenti.

Personalizza esperienze esegue il training dei modelli di Machine Learning valutando le ricompense. 

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Usare l'API Ricompensa per inviare il punteggio di ricompensa a Personalizza esperienze

Le ricompense vengono inviate a Personalizza esperienze dall'[API Ricompensa](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward). In genere, una ricompensa è un numero compreso tra 0 e 1. Un premio negativo, con valore pari a-1, è possibile in determinati scenari e deve essere usato solo se si ha esperienza con l'apprendimento di rinforzo (RL). Personalizza esperienze esegue il training del modello per ottenere la somma più alta possibile di ricompense nel tempo.

Le ricompense vengono inviate dopo che si è verificato il comportamento degli utenti, che può avvenire alcuni giorni più tardi. Il tempo massimo di attesa prima che Personalizza esperienze consideri un evento come evento senza ricompensa, o con una ricompensa predefinita, viene configurato con [Tempo di attesa per la ricompensa](#reward-wait-time) nel portale di Azure.

Se non si riceve il punteggio di ricompensa per un evento nel tempo indicato per **Tempo di attesa per la ricompensa**, verrà applicato il valore di **Ricompensa predefinita**. Il valore di **[Ricompensa predefinita](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** è impostato su zero.


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

Se non viene ricevuta alcuna ricompensa nel tempo indicato per [Tempo di attesa per la ricompensa](#reward-wait-time), il tempo trascorso dopo la chiamata a Classifica, Personalizza esperienze applica implicitamente il valore di **Ricompensa predefinita** all'evento di Classifica.

## <a name="building-up-rewards-with-multiple-factors"></a>Creazione di ricompense con più fattori  

Per una personalizzazione efficace, è possibile creare il Punteggio di ricompensa in base a più fattori. 

È ad esempio possibile applicare queste regole per la personalizzazione di un elenco di contenuti video:

|Comportamento utente|Valore punteggio parziale|
|--|--|
|L'utente ha fatto clic sul primo elemento.|Ricompensa +0,5|
|L'utente ha aperto il contenuto effettivo di tale elemento.|Ricompensa +0,3|
|L'utente ha guardato 5 minuti di contenuto o il 30% del contenuto, a seconda del valore corrispondente al tempo più lungo.|Ricompensa +0,2|
|||

È quindi possibile inviare la ricompensa totale all'API.

## <a name="calling-the-reward-api-multiple-times"></a>Chiamata all'API Ricompensa ripetuta più volte

È anche possibile chiamare l'API Ricompensa con lo stesso ID evento inviando punteggi di ricompensa diversi. Quando il Personalizzatore ottiene tali ricompense, determina la ricompensa finale per l'evento aggregando tali ricompense come specificato nella configurazione di personalizzazione.

Valori di aggregazione:

*  **First**: accetta il primo punteggio di ricompensa ricevuto per l'evento e ignora il resto.
* **Sum**: accetta tutti i punteggi Reward raccolti per gli EventID e li aggiunge insieme.

Tutte le ricompense di un evento che vengono ricevute dopo il tempo specificato per **Tempo di attesa per la ricompensa** vengono ignorate e non hanno effetto sul training dei modelli.

Con l'aggiunta di punteggi Reward, il premio finale potrebbe non essere compreso nell'intervallo previsto per i punteggi. Questo risultato non comporta l'esito negativo del servizio.

## <a name="best-practices-for-calculating-reward-score"></a>Procedure consigliate per il calcolo del punteggio di ricompensa

* **Prendere in considerazione i veri indicatori di personalizzazione**: è facile pensare in termini di clic, ma una buona ricompensa si basa su ciò che si vuole che gli utenti *ottengano* invece di ciò che si vuole *fare*.  Il calcolo delle ricompense in base ai clic, ad esempio, può determinare la selezione di contenuto soggetto a clickbait.

* **Usare un punteggio di ricompensa per il corretto funzionamento della personalizzazione**: la personalizzazione di un suggerimento cinematografico può comportare l'osservazione del film da parte dell'utente e l'assegnazione di un rating elevato. Poiché la valutazione del film dipende probabilmente da molti fattori (la qualità della recitazione, lo stato d'animo dell'utente), non è un segnale di ricompensa appropriato per il funzionamento della *personalizzazione*. Il fatto che l'utente guardi i primi minuti del film, tuttavia, può essere un segnale migliore dell'efficacia della personalizzazione e l'invio di una ricompensa pari a 1 dopo cinque minuti sarà un segnale migliore.

* I **vantaggi si applicano solo a RewardActionID**: il Personalizzatore applica i premi per comprendere l'efficacia dell'azione specificata in RewardActionID. Se si sceglie di visualizzare altre azioni e l'utente fa clic su di esse, la ricompensa deve essere pari a zero.

* **Prendere in considerazione le conseguenze impreviste**: creare funzioni Reward che consentano di ottenere risultati responsabili con l' [etica e l'uso responsabile](ethics-responsible-use.md).

* **Usare i vantaggi incrementali**: l'aggiunta di premi parziali per i comportamenti degli utenti più piccoli aiuta la personalizzazione a ottenere migliori vantaggi. La ricompensa incrementale consente all'algoritmo di capire che si sta avvicinando all'obiettivo di indurre nell'utente il comportamento desiderato finale.
    * Se si mostra un elenco di film e l'utente passa il puntatore del mouse sul primo per un periodo di tempo e visualizza altre informazioni, è possibile determinare che si è verificato un engagement dell'utente. Al comportamento può essere assegnato un punteggio di ricompensa pari a 0,1. 
    * Se l'utente ha aperto la pagina per poi uscire, il punteggio di ricompensa può essere di 0,2. 

## <a name="reward-wait-time"></a>Reward wait time (Tempo di attesa ricompense)

Personalizza esperienze mette in correlazione le informazioni di una chiamata a Classifica con le ricompense inviate nelle chiamate a Ricompensa per il training del modello. Queste chiamate possono verificarsi in momenti diversi. Personalizza esperienze attende per un periodo di tempo limitato, a partire dal momento in cui si verifica la chiamata a Classifica, anche se tale chiamata viene eseguita come evento inattivo e attivata in un secondo momento.

Se il valore di **Tempo di attesa per la ricompensa** scade e non sono state fornite informazioni di ricompensa, verrà applicata una ricompensa predefinita a tale evento per il training. La durata di attesa massima è sei giorni.

## <a name="best-practices-for-reward-wait-time"></a>Procedure consigliate per il tempo di attesa della ricompensa

Seguire questi consigli per ottenere risultati ottimali.

* Impostare il tempo di attesa per la ricompensa più breve possibile, lasciando un tempo sufficiente per ottenere il feedback degli utenti. 

* Non scegliere una durata inferiore al tempo necessario per ottenere il feedback. Se ad esempio alcune ricompense vengono fornite dopo che un utente ha guardato un video per un minuto, la durata dell'esperimento deve corrispondere almeno al doppio.

## <a name="next-steps"></a>Passaggi successivi

* [Apprendimento per rinforzo](concepts-reinforcement-learning.md) 
* [Provare l'API Classifica](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Provare l'API Ricompensa](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
