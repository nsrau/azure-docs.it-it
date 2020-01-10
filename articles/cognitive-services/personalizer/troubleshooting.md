---
title: Risoluzione dei problemi-personalizzatore
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene le risposte alle domande frequenti sulla risoluzione dei problemi relativi a personalizzazione.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: diberry
ms.openlocfilehash: 5aeda9abcebda50cf97e1473b458d8f1f9d15970
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832168"
---
# <a name="personalizer-troubleshooting"></a>Risoluzione dei problemi di personalizzazione

Questo articolo contiene le risposte alle domande frequenti sulla risoluzione dei problemi relativi a personalizzazione.

## <a name="transaction-errors"></a>Errori di transazione

### <a name="i-get-an-http-429-too-many-requests-response-from-the-service-what-can-i-do"></a>Ricevo una risposta HTTP 429 (troppe richieste) dal servizio. In che modo è possibile risolvere questo problema?

Se è stato scelto un piano tariffario gratuito quando è stata creata l'istanza di personalizzazione, è previsto un limite di quota per il numero di richieste di rango consentite. Controllare la frequenza delle chiamate API per l'API Rank (nel riquadro metrica della portale di Azure per la risorsa di personalizzazione) e modificare il piano tariffario (nel riquadro piano tariffario) se il volume di chiamate deve aumentare oltre la soglia per il piano tariffario scelto.

### <a name="im-getting-a-5xx-error-on-rank-or-reward-apis-what-should-i-do"></a>Viene ricevuto un errore 5xx per le API Rank o Reward. Cosa devo fare?

Questi problemi devono essere trasparenti. Se continuano, contattare il supporto tecnico selezionando **nuova richiesta di supporto** nella sezione **supporto e risoluzione dei problemi** nella portale di Azure per la risorsa di personalizzazione.


## <a name="learning-loop"></a>Ciclo di apprendimento

<!--

### How do I import a learning policy?


-->

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Il ciclo di apprendimento non sembra essere appreso. Com'è possibile risolvere il problema?

Il ciclo di apprendimento necessita di alcune migliaia di chiamate prima di classificare in modo efficace la priorità delle chiamate di rango.

Se non si è certi della modalità di funzionamento del ciclo di apprendimento, eseguire una [valutazione offline](concepts-offline-evaluation.md)e applicare i criteri di apprendimento corretti.

### <a name="i-keep-getting-rank-results-with-all-the-same-probabilities-for-all-items-how-do-i-know-personalizer-is-learning"></a>Continuo a ottenere risultati di rango con tutte le stesse probabilità per tutti gli elementi. Ricerca per categorie Conosci il personale che sta imparando?

La personalizzazione restituisce le stesse probabilità in un risultato dell'API di rango quando è appena iniziata e ha un modello _vuoto_ oppure quando si reimposta il ciclo di personalizzazione e il modello è ancora nel periodo di **frequenza di aggiornamento del modello** .

Quando inizia il nuovo periodo di aggiornamento, viene usato il modello aggiornato e si noterà la modifica delle probabilità.

### <a name="the-learning-loop-was-learning-but-seems-to-not-learn-anymore-and-the-quality-of-the-rank-results-isnt-that-good-what-should-i-do"></a>Il ciclo di apprendimento era in formazione ma sembra non essere più appreso e la qualità dei risultati di rango non è così efficace. Cosa devo fare?

* Assicurarsi di aver completato e applicato una valutazione nel portale di Azure per la risorsa di personalizzazione (ciclo di apprendimento).
* Assicurarsi che tutti i premi vengano inviati, tramite l'API Reward ed elaborati.

### <a name="how-do-i-know-that-the-learning-loop-is-getting-updated-regularly-and-is-used-to-score-my-data"></a>Ricerca per categorie sapere che il ciclo di apprendimento viene aggiornato regolarmente e viene usato per assegnare un punteggio ai dati?

È possibile trovare l'ora dell'ultimo aggiornamento del modello nella pagina **Impostazioni modello e apprendimento** della portale di Azure. Se viene visualizzato un timestamp obsoleto, è probabile che non vengano inviate le chiamate di rango e di ricompensa. Se il servizio non dispone di dati in ingresso, non aggiorna l'apprendimento. Se il ciclo di apprendimento non viene aggiornato con frequenza sufficiente, è possibile modificare la frequenza di **aggiornamento del modello**del ciclo.


## <a name="offline-evaluations"></a>Valutazioni offline

### <a name="an-offline-evaluations-feature-importance-returns-a-long-list-with-hundreds-or-thousands-of-items-what-happened"></a>L'importanza della funzionalità della valutazione offline restituisce un lungo elenco con centinaia o migliaia di elementi. Che cosa è successo?

Questa operazione è in genere dovuta a timestamp, ID utente o altre funzionalità con granularità fine inviate in.

### <a name="i-created-an-offline-evaluation-and-it-succeeded-almost-instantly-why-is-that-i-dont-see-any-results"></a>È stata creata una valutazione offline che è riuscita quasi immediatamente. Perché? Non vengono visualizzati risultati?

La valutazione offline usa i dati del modello sottoposto a training dagli eventi in quel periodo di tempo. Se non sono stati inviati dati nel periodo di tempo compreso tra l'ora di inizio e di fine della valutazione, l'operazione verrà completata senza alcun risultato. Invia una nuova valutazione offline selezionando un intervallo di tempo con gli eventi che conosci sono stati inviati al personalizzatore.

## <a name="learning-policy"></a>Criteri di apprendimento

### <a name="how-do-i-import-a-learning-policy"></a>Ricerca per categorie importare un criterio di formazione?

Altre informazioni sui [concetti](concept-active-learning.md#understand-learning-policy-settings) relativi ai criteri di formazione e su [come applicare](how-to-learning-policy.md) un nuovo criterio di formazione. Se non si vuole selezionare un criterio di formazione, è possibile usare la [valutazione offline](how-to-offline-evaluation.md) per suggerire un criterio di apprendimento, in base agli eventi correnti.


## <a name="security"></a>Sicurezza

### <a name="the-api-key-for-my-loop-has-been-compromised-what-can-i-do"></a>La chiave API per il ciclo è stata compromessa. In che modo è possibile risolvere questo problema?

È possibile rigenerare una chiave dopo aver scambiato i client in modo da usare l'altra chiave. La presenza di due chiavi consente di propagare la chiave in modo lazy senza dover incorrere in tempi di inattività. È consigliabile eseguire questa operazione in un ciclo normale come misura di sicurezza.


## <a name="next-steps"></a>Passaggi successivi

[Configurare la frequenza di aggiornamento del modello](how-to-settings.md#model-update-frequency)