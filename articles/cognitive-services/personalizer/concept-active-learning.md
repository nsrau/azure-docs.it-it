---
title: Eventi attivi e inattivi-personalizzatore
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: aa6f53901f21dcb0726454d641a4a2a66007f9e0
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429046"
---
# <a name="active-and-inactive-events"></a>Eventi attivi e inattivi

Quando l'applicazione chiama l'API Rank, viene visualizzata l'azione che l'applicazione deve visualizzare nel campo rewardActionId.  Da quel momento, il personale di personalizzazione si aspetta una chiamata Reward con lo stesso ID evento. Il Punteggio di ricompensa verrà usato per il training del modello che verrà usato per le chiamate di rango futura. Se non viene ricevuta alcuna chiamata Reward per gli eventId, viene applicata una ricompensa predefinita. I premi predefiniti vengono stabiliti nel portale di Azure.

In alcuni casi, è possibile che l'applicazione debba chiamare Rank Bore, anche se il risultato verrà usato o disinterpretato dall'utente. Questo problema può verificarsi in situazioni in cui, ad esempio, il rendering della pagina dei contenuti promossi viene sovrascritto con una campagna di marketing. Se il risultato della chiamata di rango non è mai stato usato e l'utente non ha mai dovuto vederlo, non sarebbe corretto eseguirne il training con qualsiasi ricompensa, zero o altro.
Questa situazione si verifica in genere nei casi seguenti:

* Viene eseguito il pre-rendering di parte dell'interfaccia utente che potrebbe o meno essere visibile all'utente. 
* L'applicazione potrebbe eseguire una personalizzazione predittiva in cui le chiamate a Rank vengono effettuate con meno contesto in tempo reale e l'output potrebbe o meno essere usato dall'applicazione. 

In questi casi, il modo corretto per usare la personalizzazione è chiamare Rank che richiede l'evento come _inattivo_. Il Personalizzatore non prevede una ricompensa per questo evento e non applica una ricompensa predefinita. Letr nella logica di business, se l'applicazione usa le informazioni della chiamata di rango, è sufficiente _attivare_ l'evento. Dal momento in cui l'evento è attivo, il personale si aspetta un premio per l'evento o applica una ricompensa predefinita se non viene effettuata alcuna chiamata esplicita all'API Reward.

## <a name="get-inactive-events"></a>Ottenere eventi inattivi

Per disabilitare il training per un evento, chiamare Rank con `learningEnabled = False`.

L'apprendimento di un evento inattivo viene attivato in modo implicito se si invia una ricompensa per gli EventID oppure si chiama l'API `activate` per l'ID evento.

## <a name="learning-settings"></a>Impostazioni di apprendimento

Le impostazioni di apprendimento determinano gli *iperparametri* specifici del training del modello. Due modelli degli stessi dati, sottoposti a training su diverse impostazioni di apprendimento, finiranno in modi diversi.

### <a name="import-and-export-learning-policies"></a>Importare ed esportare i criteri di apprendimento

È possibile importare ed esportare i file dei criteri di apprendimento nel portale di Azure. In questo modo è possibile salvare i criteri esistenti, testarli, sostituirli e archiviarli nel controllo del codice sorgente come artefatti per riferimento e controllo futuri.

### <a name="learning-policy-settings"></a>Impostazioni dei criteri di apprendimento

Le impostazioni di **Criteri di apprendimento** non dovrebbero essere cambiate. Cambiarle solo dopo averne verificato l'impatto su Personalizza esperienze. In caso contrario, si verificheranno effetti collaterali, tra cui l'invalidamento dei modelli di Personalizza esperienze.

### <a name="comparing-effectiveness-of-learning-policies"></a>Confronto dell'efficacia dei criteri di apprendimento

È possibile confrontare le prestazioni dei diversi criteri di apprendimento rispetto a dati passati nei log di Personalizza esperienze eseguendo [valutazioni offline](concepts-offline-evaluation.md).

[Caricare i propri criteri di apprendimento](how-to-offline-evaluation.md) per confrontarli con quelli correnti.

### <a name="discovery-of-optimized-learning-policies"></a>Individuazione dei criteri di apprendimento ottimizzati

Personalizza esperienze può creare criteri di apprendimento ottimizzati quando si esegue una [valutazione offline](how-to-offline-evaluation.md). I criteri di apprendimento ottimizzati, che è stato dimostrato offrono ricompense migliori nelle valutazioni offline, generano risultati più efficaci quando vengono usati online in Personalizza esperienze.

Dopo aver creato i criteri di apprendimento ottimizzati, è possibile applicarli direttamente in Personalizza esperienze, in modo da sostituire immediatamente i criteri correnti, oppure è possibile salvarli per una ulteriore valutazione e decidere in futuro se rimuoverli, salvarli o applicarli in un secondo momento.
