---
title: Eventi attivi e inattivi-personalizzatore
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra l'uso di eventi attivi e inattivi, le impostazioni di apprendimento e i criteri di apprendimento all'interno del servizio di personalizzazione.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 1641a1020193395d7d2ddb9c4893bd7bc89cdcd0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681868"
---
# <a name="active-and-inactive-events"></a>Eventi attivi e inattivi

Quando l'applicazione chiama l'API Rank, viene visualizzata l'azione che l'applicazione deve visualizzare nel campo **rewardActionId** .  Da quel momento, la personalizzazione prevede una chiamata Reward con lo stesso ID evento. Il Punteggio di ricompensa verrà usato per il training del modello per le chiamate di rango futura. Se non viene ricevuta alcuna chiamata Reward per l'ID evento, viene applicata una ricompensa predefinita. I premi predefiniti vengono impostati nell'portale di Azure.

In alcuni scenari, l'applicazione potrebbe dover chiamare Rank prima di sapere se il risultato verrà usato o visualizzato all'utente. Questo problema può verificarsi in situazioni in cui, ad esempio, il rendering della pagina del contenuto innalzato di pagina viene sovrascritto da una campagna di marketing. Se il risultato della chiamata di rango non è mai stato usato e l'utente non l'ha mai visto, non inviare una chiamata di ricompensa corrispondente.

In genere, questi scenari si verificano quando:

* Si sta eseguendo il rendering dell'interfaccia utente che potrebbe essere visualizzata o meno per l'utente. 
* L'applicazione sta eseguendo una personalizzazione predittiva in cui le chiamate di rango vengono effettuate con un contesto poco in tempo reale e l'applicazione può o meno usare l'output. 

In questi casi, usare il Personalizzatore per chiamare Rank, richiedendo che l'evento sia _inattivo_. Il personale non prevede una ricompensa per questo evento e non applica una ricompensa predefinita. Successivamente, nella logica di business, se l'applicazione usa le informazioni della chiamata di rango, è sufficiente _attivare_ l'evento. Non appena l'evento è attivo, il personale di personalizzazione prevede una ricompensa per gli eventi. Se non viene effettuata alcuna chiamata esplicita all'API Reward, il Personalizzatore applica una ricompensa predefinita.

## <a name="inactive-events"></a>Eventi inattivi

Per disabilitare il training per un evento, chiamare Rank usando `learningEnabled = False`. Per un evento inattivo, l'apprendimento viene attivato in modo implicito se si invia una ricompensa per gli eventId o si chiama l'API `activate` per tale ID evento.

## <a name="learning-settings"></a>Impostazioni di apprendimento

Le impostazioni di apprendimento determinano gli *iperparametri* del training del modello. Due modelli degli stessi dati di cui viene eseguito il training in impostazioni di apprendimento diverse finiranno in modi diversi.

### <a name="import-and-export-learning-policies"></a>Importare ed esportare i criteri di apprendimento

È possibile importare ed esportare i file dei criteri di formazione dal portale di Azure. Usare questo metodo per salvare i criteri esistenti, testarli, sostituirli e archiviarli nel controllo del codice sorgente come elementi per riferimento e controllo futuri.

### <a name="understand-learning-policy-settings"></a>Informazioni sulle impostazioni dei criteri di apprendimento

Le impostazioni nei criteri di apprendimento non sono destinate a essere modificate. Modificare le impostazioni solo se si comprende il modo in cui influiscono sulla personalizzazione. Senza queste informazioni, è possibile che si verifichino problemi, inclusa l'invalidazione dei modelli di personalizzazione.

### <a name="compare-learning-policies"></a>Confrontare i criteri di apprendimento

È possibile confrontare il modo in cui i diversi criteri di apprendimento eseguono i dati precedenti nei log di personalizzazione eseguendo [valutazioni offline](concepts-offline-evaluation.md).

[Caricare i propri criteri di formazione](how-to-offline-evaluation.md) per confrontarli con i criteri di apprendimento correnti.

### <a name="optimize-learning-policies"></a>Ottimizzare i criteri di apprendimento

Il Personalizzatore può creare criteri di apprendimento ottimizzati in una [valutazione offline](how-to-offline-evaluation.md). Un criterio di apprendimento ottimizzato con migliori ricompense in una valutazione offline produrrà risultati migliori quando viene usato online in personalizzazione.

Dopo aver ottimizzato i criteri di apprendimento, è possibile applicarli direttamente a personalizzazione, in modo da sostituire immediatamente i criteri correnti. In alternativa, è possibile salvare i criteri ottimizzati per un'ulteriore valutazione e in seguito decidere se eliminarli, salvarli o applicarli.
