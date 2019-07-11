---
title: Apprendimento attivo - Personalizza esperienze
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: edjez
ms.openlocfilehash: c44afc81a7ec9d05cdc04cc8bc46c77cd51ceaf8
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722520"
---
# <a name="active-learning-and-learning-policies"></a>Apprendimento attivo e criteri di apprendimento 

Quando l'applicazione chiama l'API Rank, si riceve una classifica del contenuto. La logica di business può usare questa classifica per determinare se il contenuto dovrà essere mostrato all'utente. La visualizzazione del contenuto classificato corrisponde a un evento di classifica _attivo_. La mancata visualizzazione del contenuto classificato nell'applicazione corrisponde a un evento di classifica _inattivo_. 

Le informazioni sugli eventi di classifica attivi vengono restituite a Personalizza esperienze. Queste informazioni vengono usate per continuare il training del modello tramite i criteri di apprendimento correnti.

## <a name="active-events"></a>Eventi attivi

Gli eventi attivi devono essere sempre mostrati agli utenti e, per chiudere il ciclo di apprendimento, è necessario che venga restituita la chiamata alla ricompensa. 

### <a name="inactive-events"></a>Eventi inattivi 

Gli eventi inattivi non dovrebbero cambiare il modello sottostante, perché all'utente non è stata offerta la possibilità di scegliere un contenuto classificato.

## <a name="dont-train-with-inactive-rank-events"></a>Non eseguire il training con eventi di classifica inattivi 

Per alcune applicazioni, può essere necessario chiamare l'API Rank senza sapere ancora se i risultati verranno mostrati all'utente. 

Ciò si verifica quando:

* Viene eseguito il pre-rendering di parte dell'interfaccia utente che potrebbe o meno essere visibile all'utente. 
* L'applicazione potrebbe eseguire una personalizzazione predittiva in cui le chiamate a Rank vengono effettuate con meno contesto in tempo reale e l'output potrebbe o meno essere usato dall'applicazione. 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>Disabilitare l'apprendimento attivo per gli eventi di classifica inattivi durante la chiamata a Rank

Per disabilitare l'apprendimento automatico, chiamare Rank con `learningEnabled = False`.

L'apprendimento per gli eventi inattivi viene attivato implicitamente se si invia una ricompensa per Rank.

## <a name="learning-policies"></a>Criteri di apprendimento

I criteri di apprendimento determinano gli *iperparametri* specifici del training del modello. Il comportamento di due modelli degli stessi dati sarà diverso se il training viene eseguito con criteri di apprendimento diversi.

### <a name="importing-and-exporting-learning-policies"></a>Importazione ed esportazione dei criteri di apprendimento

È possibile importare ed esportare i file dei criteri di apprendimento nel portale di Azure. In questo modo è possibile salvare i criteri esistenti, testarli, sostituirli e archiviarli nel controllo del codice sorgente come artefatti per riferimento e controllo futuri.

### <a name="learning-policy-settings"></a>Impostazioni dei criteri di apprendimento

Le impostazioni di **Criteri di apprendimento** non dovrebbero essere cambiate. Cambiarle solo dopo averne verificato l'impatto su Personalizza esperienze. In caso contrario, si verificheranno effetti collaterali, tra cui l'invalidamento dei modelli di Personalizza esperienze.

### <a name="comparing-effectiveness-of-learning-policies"></a>Confronto dell'efficacia dei criteri di apprendimento

È possibile confrontare le prestazioni dei diversi criteri di apprendimento rispetto a dati passati nei log di Personalizza esperienze eseguendo [valutazioni offline](concepts-offline-evaluation.md).

[Caricare i propri criteri di apprendimento](how-to-offline-evaluation.md) per confrontarli con quelli correnti.

### <a name="discovery-of-optimized-learning-policies"></a>Individuazione dei criteri di apprendimento ottimizzati

Personalizza esperienze può creare criteri di apprendimento ottimizzati quando si esegue una [valutazione offline](how-to-offline-evaluation.md). I criteri di apprendimento ottimizzati, che è stato dimostrato offrono ricompense migliori nelle valutazioni offline, generano risultati più efficaci quando vengono usati online in Personalizza esperienze.

Dopo aver creato i criteri di apprendimento ottimizzati, è possibile applicarli direttamente in Personalizza esperienze, in modo da sostituire immediatamente i criteri correnti, oppure è possibile salvarli per una ulteriore valutazione e decidere in futuro se rimuoverli, salvarli o applicarli in un secondo momento.