---
title: Configurare un controllo del segnale per la registrazione video basata su eventi-Azure
description: Questo articolo fornisce indicazioni su come configurare un controllo del segnale in un grafico multimediale.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: afcec7c03f1353f08b58311278f5a533e0c911bc
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410794"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>Configurare un controllo del segnale per la registrazione video basata su eventi

All'interno di un grafico multimediale, un [nodo processore](media-graph-concept.md#signal-gate-processor) del controllo del segnale consente di trasmettere i file multimediali da un nodo a un altro quando il Gate viene attivato da un evento. Quando viene attivato, il controllo si apre e consente il flusso multimediale per una durata specificata. In assenza di eventi per attivare il controllo, il controllo si chiude e il file multimediale smette di scorrere. È possibile usare il processore del Gate di segnalazione per la registrazione video basata su eventi.

In questo articolo si apprenderà come configurare un processore di Gate Signal.

## <a name="suggested-prereading"></a>Prelettura consigliata
-   [Grafo multimediale](media-graph-concept.md)
-   [Registrazione di video basata su eventi](event-based-video-recording-concept.md)


## <a name="problem"></a>Problema
Un utente potrebbe voler avviare la registrazione in un determinato momento prima o dopo l'attivazione del Gate da un evento. L'utente conosce la latenza accettabile nel sistema. Quindi, vogliono specificare la latenza del processore del Gate del segnale. Desiderano inoltre specificare la durata minima e massima della registrazione, indipendentemente dal numero di nuovi eventi ricevuti.
 
### <a name="use-case-scenario"></a>Scenario di un caso d'uso
Si supponga di voler registrare il video ogni volta che viene aperta la porta anteriore della compilazione. Si vuole che la registrazione: 

- Includere i secondi *X* prima che lo sportello si apra. 
- Ultimi almeno *Y* secondi se la porta non viene riaperta. 
- Ultimi *Z* secondi se lo sportello viene aperto ripetutamente. 
 
Si sa che il sensore della porta ha una latenza di *K* secondi. Per ridurre la probabilità che gli eventi vengano ignorati come arrivi in ritardo, è necessario consentire almeno *K* secondi per l'arrivo degli eventi.


## <a name="solution"></a>Soluzione

Per risolvere il problema, modificare i parametri del processore del Gate del segnale.

Per configurare un processore di controllo del segnale, usare questi quattro parametri:
- Finestra di valutazione dell'attivazione
- Offset segnale di attivazione
- Finestra di attivazione minima
- Finestra di attivazione massima

Quando viene attivato, il processore del Gate di segnalazione rimane aperto per il tempo minimo di attivazione. L'evento di attivazione inizia al timestamp per il primo evento, più l'offset del segnale di attivazione. 

Se il processore del Gate di segnalazione viene attivato nuovamente mentre è aperto, il timer viene reimpostato e il controllo rimane aperto per almeno il tempo minimo di attivazione. Il processore del Gate di segnalazione non rimane mai aperto più a lungo del tempo massimo di attivazione. 

Un evento (evento 1) che si verifica prima di un altro evento (evento 2), basato sui timestamp del supporto, potrebbe essere ignorato se il sistema è in ritardo e l'evento 1 arriva al processore del Gate di segnalazione dopo l'evento 2. Se l'evento 1 non arriva tra l'arrivo dell'evento 2 e la finestra di valutazione dell'attivazione, l'evento 1 viene ignorato. Non viene passato attraverso il processore del Gate di segnalazione. 

Gli ID correlazione vengono impostati per ogni evento. Questi ID vengono impostati dall'evento iniziale. Sono sequenziali per ogni evento seguente.

> [!IMPORTANT]
> Il tempo di supporto è basato sul timestamp del supporto di quando si verifica un evento nel supporto. La sequenza di eventi che arrivano al Gate del segnale potrebbe non riflettere la sequenza di eventi che arrivano nel tempo di supporto.


### <a name="parameters-based-on-the-physical-time-that-events-arrive-at-the-signal-gate"></a>Parametri, basati sul tempo fisico che gli eventi arrivano al Gate del segnale

* **minimumActivationTime (durata più breve possibile di una registrazione)** : il numero minimo di secondi durante i quali il processore del Gate del segnale rimane aperto dopo che è stato attivato per ricevere nuovi eventi, a meno che non venga interrotto dal maximumActivationTime.
* **maximumActivationTime (durata più lunga possibile di una registrazione)** : il numero massimo di secondi dall'evento iniziale che il processore di Gate del segnale rimane aperto dopo essere stato attivato per ricevere nuovi eventi, indipendentemente dagli eventi ricevuti.
* **activationSignalOffset** : numero di secondi tra l'attivazione del processore del Gate del segnale e l'avvio della registrazione video. In genere, questo valore è negativo perché avvia la registrazione prima dell'evento di attivazione.
* **activationEvaluationWindow** : a partire dall'evento di attivazione iniziale, il numero di secondi durante i quali un evento che si è verificato prima dell'evento iniziale, nel tempo dei supporti, deve arrivare all'elaboratore di controllo del segnale prima che venga ignorato e considerato un arrivo in ritardo.

> [!NOTE]
> Un *arrivo in ritardo* è un evento che arriva dopo che la finestra di valutazione dell'attivazione è stata superata ma che arriva prima dell'evento iniziale nel tempo dei supporti.

### <a name="limits-of-parameters"></a>Limiti dei parametri

* **activationEvaluationWindow** : da 0 a 10 secondi
* **activationSignalOffset** : da 1 minuto a 1 minuto
* **minimumActivationTime** : da 1 secondo a 1 ora
* **maximumActivationTime** : da 1 secondo a 1 ora


Nel caso di utilizzo, è necessario impostare i parametri come indicato di seguito:

* **activationEvaluationWindow** : *K* secondi
* **activationSignalOffset** : *-X* secondi
* **minimumActivationWindow** : *Y* secondi
* **maximumActivationWindow** : *Z* secondi


Di seguito è riportato un esempio di come la sezione del nodo del processore del controllo del **segnale** cercherebbe in una topologia del grafico multimediale per i valori di parametro seguenti:
* **activationEvaluationWindow** : 1 secondo
* **activationSignalOffset** :-5 secondi
* **minimumActivationTime** : 20 secondi
* **maximumActivationTime** : 40 secondi

> [!IMPORTANT]
> Per ogni valore di parametro è previsto il [formato di durata ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations
) . Ad esempio, PT1S = 1 secondo.


```
"processors":              
[
          {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```


Si consideri ora il comportamento di questa configurazione del processore del controllo del segnale in diversi scenari di registrazione.

### <a name="recording-scenarios"></a>Scenari di registrazione

**Un evento da un'origine ( *attivazione normale* )**

Un processore di controllo del segnale che riceve un evento genera una registrazione che inizia 5 secondi (segnale di attivazione = 5 secondi) prima che l'evento arrivi al Gate. Il resto della registrazione è 20 secondi (tempo minimo di attivazione = 20 secondi) perché nessun altro evento arriva prima della fine del tempo di attivazione minimo per riattivare il controllo.

Diagramma di esempio:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="Diagramma che mostra l'attivazione normale di un evento da un'origine.":::

* Durata della registrazione =-offset + minimumActivationTime = [E1 + offset, E1 + minimumActivationTime]


**Due eventi da un'origine ( *attivazione riattivata* )**

Un processore di Gate per i segnali che riceve due eventi genera una registrazione che inizia 5 secondi (offset del segnale di attivazione = 5 secondi) prima che l'evento arrivi al Gate. Inoltre, l'evento 2 raggiunge 5 secondi dopo l'evento 1. Poiché l'evento 2 arriva prima della fine del periodo di attivazione minimo dell'evento 1 (20 secondi), il Gate viene riattivato. Il resto della registrazione è 20 secondi (tempo minimo di attivazione = 20 secondi) perché nessun altro evento arriva prima della fine del tempo di attivazione minimo dall'evento 2 per riattivare il controllo.

Diagramma di esempio:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Diagramma che mostra l'attivazione riattivata di due eventi da un'origine.":::

* Durata della registrazione =-offset + (arrivo dell'evento 2-arrivo dell'evento 1) + minimumActivationTime


***N* eventi da un'origine ( *attivazione massima* )**

Un processore di controllo del segnale che riceve *N* eventi genera una registrazione che inizia 5 secondi (offset del segnale di attivazione = 5 secondi) prima che il primo evento arrivi al Gate. Poiché ogni evento arriva prima della fine del tempo di attivazione minimo di 20 secondi dall'evento precedente, il controllo viene riattivato continuamente. Rimane aperto fino al tempo massimo di attivazione di 40 secondi dopo il primo evento. Il Gate si chiude e non accetta più nuovi eventi.

Diagramma di esempio:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="Diagramma che mostra la massima attivazione di N eventi da un'origine.":::
 
* Durata della registrazione =-offset + maximumActivationTime

> [!IMPORTANT]
> I diagrammi precedenti presuppongono che ogni evento arrivi nello stesso istante in tempo fisico e tempo di supporto. Ovvero si presuppone che non ci siano arrivi in ritardo.

## <a name="next-steps"></a>Passaggi successivi

Provare l'esercitazione relativa alla [registrazione video basata su eventi](event-based-video-recording-tutorial.md). Per iniziare, modificare il [topology.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json). Modificare i parametri per il nodo signalgateProcessor e quindi seguire il resto dell'esercitazione. Esaminare le registrazioni video per analizzare l'effetto dei parametri.



