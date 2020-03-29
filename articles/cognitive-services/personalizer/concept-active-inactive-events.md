---
title: Eventi attivi e inattivi - Personalizer
description: In questo articolo viene illustrato l'utilizzo di eventi attivi e inattivi all'interno del servizio Personalizer.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: a8f27542208965e2b820b9fc45cfcc5353a7f193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624253"
---
# <a name="active-and-inactive-events"></a>Eventi attivi e inattivi

Un evento **attivo** è qualsiasi chiamata a Rank in cui sai che stai per mostrare il risultato al cliente e determinare il punteggio premio. Questo è il comportamento predefinito.

Un evento **inattivo** è una chiamata a Rank in cui non si è certi che l'utente visualizzerà mai l'azione consigliata, a causa della logica di business. In questo modo è possibile eliminare l'evento in modo che Personalizer non venga addestrato con il premio predefinito. Gli eventi inattivi non devono chiamare l'API Reward.

È importante che il ciclo di apprendimento conosca il tipo effettivo di evento. Un evento inattivo non avrà una chiamata Ricompensa. Un evento attivo deve avere una chiamata Ricompensa, ma se la chiamata API non viene mai effettuata, viene applicato il punteggio di ricompensa predefinito. Modifica lo stato di un evento da inattivo ad attivo non appena sai che influenzerà l'esperienza utente.

## <a name="typical-active-events-scenario"></a>Scenario tipico di eventi attivi

Quando l'applicazione chiama l'API Rank, si riceve l'azione, che l'applicazione deve visualizzare nel campo **rewardActionId.**  Da quel momento, Personalizer si aspetta una chiamata Reward con un punteggio di ricompensa che ha lo stesso eventId. Il punteggio premio viene utilizzato per addestrare il modello per le future chiamate di classificazione. Se non viene ricevuta alcuna chiamata premio per eventId, viene applicato un premio predefinito. [I premi predefiniti](how-to-settings.md#configure-rewards-for-the-feedback-loop) vengono impostati nella risorsa Personalizer nel portale di Azure.Default rewards are set on your Personalizer resource in the Azure portal.

## <a name="other-event-type-scenarios"></a>Altri scenari di tipo di evento

In alcuni scenari, l'applicazione potrebbe essere necessario chiamare Rank prima ancora di sapere se il risultato verrà utilizzato o visualizzato all'utente. Ciò può verificarsi in situazioni in cui, ad esempio, il rendering della pagina del contenuto promosso viene sovrascritto da una campagna di marketing. Se il risultato della chiamata di classificazione non è mai stato utilizzato e l'utente non l'ha mai visto, non inviare una chiamata premio corrispondente.

In genere, questi scenari si verificano quando:Typically, these scenarios happen when:

* Stai prerendendo l'interfaccia utente che l'utente potrebbe o non potrebbe arrivare a vedere.
* L'applicazione sta eseguendo la personalizzazione predittiva in cui le chiamate Rank vengono effettuate con poco contesto in tempo reale e l'applicazione potrebbe o non potrebbe utilizzare l'output.

In questi casi, utilizzare Personalizer per chiamare Rank, richiedendo che l'evento sia _inattivo._ Il personalista non si aspetta una ricompensa per questo evento e non applicheranno un premio predefinito.

Più avanti nella logica di business, se l'applicazione utilizza le informazioni della chiamata Rank, è sufficiente _attivare_ l'evento. Non appena l'evento è attivo, Personalizer si aspetta un premio dell'evento. Se non viene effettuata alcuna chiamata esplicita all'API Reward, Personalizer applica un premio predefinito.

## <a name="inactive-events"></a>Eventi inattivi

Per disabilitare il training per `learningEnabled = False`un evento, chiamare Rank utilizzando .

Per un evento inattivo, l'apprendimento viene attivato in modo implicito se si invia un premio per eventId o si chiama l'API `activate` per tale eventId.

## <a name="next-steps"></a>Passaggi successivi

* Scopri come determinare il [punteggio premio e quali dati prendere in considerazione.](concept-rewards.md)
