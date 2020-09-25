---
title: Eventi attivi e inattivi-personalizzatore
description: Questo articolo illustra l'uso di eventi attivi e inattivi all'interno del servizio di personalizzazione.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 7d1044d02ceba1f3d0996b1fe1c8a9a44b31049b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91253650"
---
# <a name="active-and-inactive-events"></a>Eventi attivi e inattivi

Un evento **attivo** è qualsiasi chiamata al rango in cui si è certi che il risultato verrà visualizzato al cliente e verrà determinato il Punteggio di ricompensa. Questo è il comportamento predefinito.

Un evento **inattivo** è una chiamata a Rank in cui non si è certi se l'utente visualizzerà l'azione consigliata, a causa della logica di business. In questo modo è possibile eliminare l'evento in modo che la personalizzazione non venga sottoposta a training con la ricompensa predefinita. Gli eventi inattivi non devono chiamare l'API Reward.

È importante che il ciclo di apprendimento conosca il tipo effettivo di evento. Un evento inattivo non avrà una chiamata Reward. Un evento attivo deve avere una chiamata di ricompensa, ma se la chiamata API non viene mai eseguita, viene applicato il Punteggio di ricompensa predefinito. Modificare lo stato di un evento da inattivo a attivo non appena si è certi che influirà sull'esperienza utente.

## <a name="typical-active-events-scenario"></a>Scenario tipico degli eventi attivi

Quando l'applicazione chiama l'API Rank, viene visualizzata l'azione che deve essere visualizzata nell'applicazione nel campo **rewardActionId** .  Da quel momento, la personalizzazione prevede una chiamata Reward con un punteggio di ricompensa con lo stesso ID evento. Il Punteggio di ricompensa viene usato per il training del modello per le chiamate di rango futura. Se non viene ricevuta alcuna chiamata Reward per l'ID evento, viene applicata una ricompensa predefinita. I [premi predefiniti](how-to-settings.md#configure-rewards-for-the-feedback-loop) vengono impostati sulla risorsa di personalizzazione nel portale di Azure.

## <a name="other-event-type-scenarios"></a>Altri scenari di tipo di evento

In alcuni scenari, l'applicazione potrebbe dover chiamare Rank prima di sapere se il risultato verrà usato o visualizzato all'utente. Questo problema può verificarsi in situazioni in cui, ad esempio, il rendering della pagina del contenuto innalzato di pagina viene sovrascritto da una campagna di marketing. Se il risultato della chiamata di rango non è mai stato usato e l'utente non l'ha mai visto, non inviare una chiamata di ricompensa corrispondente.

In genere, questi scenari si verificano quando:

* Si sta eseguendo il rendering dell'interfaccia utente che potrebbe essere visualizzata o meno per l'utente.
* L'applicazione sta eseguendo una personalizzazione predittiva in cui le chiamate di rango vengono effettuate con un contesto poco in tempo reale e l'applicazione può o meno usare l'output.

In questi casi, usare il Personalizzatore per chiamare Rank, richiedendo che l'evento sia _inattivo_. Il personale non prevede una ricompensa per questo evento e non applica una ricompensa predefinita.

Successivamente, nella logica di business, se l'applicazione usa le informazioni della chiamata di rango, è sufficiente _attivare_ l'evento. Non appena l'evento è attivo, il personale di personalizzazione prevede una ricompensa per gli eventi. Se non viene effettuata alcuna chiamata esplicita all'API Reward, il Personalizzatore applica una ricompensa predefinita.

## <a name="inactive-events"></a>Eventi inattivi

Per disabilitare il training per un evento, chiamare Rank usando `learningEnabled = False` .

Per un evento inattivo, l'apprendimento viene attivato in modo implicito se si invia una ricompensa per gli eventId o si chiama l' `activate` API per l'ID evento.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su [come determinare il Punteggio di ricompensa e i dati da considerare](concept-rewards.md).
