---
title: Configurare un controllo del segnale per la registrazione video basata su eventi-Azure
description: Questo articolo fornisce indicazioni su come configurare un controllo del segnale in un grafico multimediale.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: 4204a43915f9c79cae7dfe82b37e741fee89fb4a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380172"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>Configurare un controllo del segnale per la registrazione video basata su eventi

All'interno di un grafico multimediale, un [nodo processore](media-graph-concept.md#signal-gate-processor) del controllo del segnale consente di trasmettere i file multimediali da un nodo a un altro, quando il Gate viene attivato da un evento. Quando viene attivato, il controllo si apre e consente il flusso multimediale per una durata specificata. In assenza di eventi per attivare il controllo, il controllo si chiude e il flusso dei supporti viene interrotto. Il processore del Gate di segnalazione è applicabile per la registrazione video basata su eventi.
In questo articolo vengono fornite informazioni dettagliate su come configurare un processore di controllo del segnale.

## <a name="suggested-pre-reading"></a>Letture consigliate
-   [Grafo multimediale](media-graph-concept.md)
-   [Registrazione di video basata su eventi](event-based-video-recording-concept.md)


## <a name="problem"></a>Problema
È possibile che l'utente desideri avviare la registrazione di una determinata ora prima o dopo l'attivazione del controllo da parte di un evento. L'utente conosce la latenza accettabile all'interno del sistema, pertanto l'utente desidera specificare la latenza del processore del Gate del segnale. L'utente desidera specificare il più breve e più lungo che la durata della registrazione può essere indipendentemente dal numero di nuovi eventi ricevuti.
 
### <a name="use-case-scenario"></a>Scenario di un caso d'uso
Si supponga di voler registrare il video ogni volta che viene aperta la porta anteriore della compilazione. Si desidera che la **X** secondi prima che lo sportello venga aperto incluso nella registrazione. Si vuole che la registrazione riguardi almeno **Y** secondi, se la porta non viene riaperta. Si desidera che la registrazione venga applicata al massimo **Z** secondi, se lo sportello viene aperto ripetutamente. Si sa che il sensore della porta ha una latenza di **K** secondi e si vuole ridurre la probabilità che gli eventi vengano ignorati ("arrivi in ritardo"), quindi si desidera consentire almeno **k** secondi per l'arrivo degli eventi.


## <a name="solution"></a>Soluzione

**_Modifica dei parametri del processore del Gate del segnale_* _

Un processore di Gate del segnale è configurato da 4 parametri:
- _ *finestra di valutazione dell'attivazione**
- **Offset segnale di attivazione**
- **finestra di attivazione minima**
- **intervallo massimo di attivazione**. 

Quando viene attivato, il processore del Gate di segnalazione rimane aperto per il tempo minimo di attivazione. L'evento di attivazione inizia in corrispondenza del timestamp per il primo evento, più l'offset del segnale di attivazione. Se il processore del Gate del segnale viene nuovamente attivato, mentre è aperto, il timer viene reimpostato e il controllo rimane aperto per almeno il tempo minimo di attivazione. Il processore del controllo del segnale non resterà mai aperto più a lungo del tempo massimo di attivazione. Un evento **(evento 1)** che si verifica prima di un altro evento **(evento 2)** , basato sui timestamp del supporto, è soggetto a essere ignorato, se il sistema è in ritardo e l' **evento 1** arriva al processore del Gate di segnalazione dopo l' **evento 2**. Se l' **evento 1** non arriva tra l'arrivo dell' **evento 2** e la **finestra di valutazione dell'attivazione** , l' **evento 1** verrà ignorato e non verrà passato attraverso il processore del Gate di segnalazione. Gli ID correlazione vengono impostati per ogni evento. Questi ID vengono impostati dall'evento iniziale e sono sequenziali per ogni evento riportato di seguito.

> [!IMPORTANT]
> Il tempo di supporto è basato sul timestamp del supporto di quando si è verificato un evento nel supporto. La sequenza di eventi che arrivano al Gate del segnale potrebbe non riflettere la sequenza di eventi in arrivo nel tempo dei supporti.


### <a name="parameters-based-on-when-events-arrive-in-physical-time-to-the-signal-gate"></a>Parameters: (in base al momento in cui gli eventi arrivano nel tempo fisico per il Gate del segnale)

* **minimumActivationTime (durata più breve possibile di una registrazione)** = il numero minimo di secondi durante i quali il processore del Gate del segnale resterà aperto dopo essere stato attivato per ricevere nuovi eventi, a meno che non venga interrotto da **maximumActivationTime**
* **maximumActivationTime (durata più lunga possibile di una registrazione)** = numero massimo di secondi dall'evento iniziale durante il quale il processore del controllo del segnale resterà aperto dopo essere stato attivato per ricevere nuovi eventi, indipendentemente dagli eventi ricevuti
* **activationSignalOffset** = il numero di secondi che intercorre tra l'attivazione del processore di controllo del segnale e l'inizio della registrazione video, in genere questo valore è negativo, per avviare la registrazione prima dell'evento di attivazione
* **activationEvaluationWindow** = numero di secondi a partire dall'evento di attivazione iniziale, in cui un evento che si è verificato prima dell'evento iniziale, nel tempo dei supporti, deve arrivare al processore del Gate del segnale prima di essere ignorato e considerato "arrivo in ritardo"

> [!NOTE]
> Un arrivo in ritardo è un evento che arriva una volta che la finestra di valutazione dell'attivazione è passata, ma questo evento è arrivato prima dell'evento iniziale nel tempo dei supporti.

### <a name="limits-of-parameters"></a>Limiti dei parametri

* **activationEvaluationWindow: da 0 a 10 secondi**

* **activationSignalOffset: da 1 minuto a 1 minuto**

* **minimumActivationTime: da 1 secondo a 1 ora**

* **maximumActivationTime: da 1 secondo a 1 ora**


In base al caso d'uso, i parametri vengono impostati nel modo seguente:

* **activationEvaluationWindow = K sec**
* **activationSignalOffset =-X sec**
* **minimumActivationWindow = Y sec**
* **maximumActivationWindow = Z sec**


Di seguito è riportato un esempio della sezione del nodo del processore del controllo del segnale in una topologia del grafico multimediale per i valori di parametro seguenti:
* **activationEvaluationWindow = 1 secondo**
* **activationSignalOffset =-5 secondi**
* **minimumActivationTime = 20 secondi**
* **maximumActivationTime = 40 secondi**

> [!IMPORTANT]
> Per ogni valore di parametro è previsto il [formato di durata ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations
) . 
**Esempio: PT1S = 1 secondo**


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


Si prenda in considerazione il comportamento di questa configurazione del processore del controllo del segnale in diversi scenari di registrazione.


**1 evento da 1 origine ( *attivazione normale* )**

Un processore di controllo del segnale che riceve un evento provocherebbe una registrazione che avvia l'"offset del segnale di attivazione" (5) secondi prima che l'evento arrivi al Gate. Il resto della registrazione è "tempo di attivazione minimo" (20) secondi, poiché nessun altro evento arriva prima del completamento del tempo di attivazione minimo per riattivare il controllo.

Diagramma di esempio:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="Attivazione normale":::

* Durata della registrazione =-offset + minimumActivationTime = [E1 + offset, E1 + minimumActivationTime]


**2 eventi da 1 origine ( *attivazione riattivata* )**

Un processore di controllo del segnale che riceve due eventi comporterebbe una registrazione che avvia l'"offset del segnale di attivazione" (cinque) secondi prima che il primo evento arrivi al Gate. Il secondo evento arriva cinque secondi dopo il primo evento, che precede il "tempo di attivazione minimo" (20) secondi dal completamento del primo evento, quindi il Gate viene riattivato per restare aperto. Il resto della registrazione è "tempo di attivazione minimo" (20) secondi, poiché nessun altro evento arriva prima del tempo di attivazione minimo dal secondo evento viene completato per riattivare il controllo.

Diagramma di esempio:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Attivazione riattivata":::

* Durata della registrazione =-offset + (arrivo del secondo evento-arrivo del primo evento) + minimumActivationTime


**N eventi da 1 origine ( *attivazione massima* )**

Un processore di Gate del segnale che riceve N eventi comporterebbe una registrazione che avvia "offset del segnale di attivazione" (5) secondi prima che il primo evento arrivi al Gate. Poiché ogni evento arriva prima del completamento del "tempo di attivazione minimo" (20) secondi dall'evento precedente, il controllo verrà riattivato continuamente e rimarrà aperto fino a "tempo di attivazione massimo" (40) secondi dopo il primo evento, in cui il Gate si chiude e non accetta più nuovi eventi.

Diagramma di esempio:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="Attivazione massima":::
 
* Durata della registrazione =-offset + maximumActivationTime

> [!IMPORTANT]
> I diagrammi presuppongono che ogni evento arrivi alla stessa istanza in tempo fisico e multimediale. (Nessun arrivo in ritardo)

## <a name="next-steps"></a>Passaggi successivi

### <a name="try-it-out"></a>Prova

[Esercitazione sulla registrazione video basata su eventi](event-based-video-recording-tutorial.md)

Usando l'esercitazione relativa alla registrazione video basata su eventi, modificare il [topology.jsin](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), modificare i parametri del nodo signalgateProcessor, quindi seguire il resto dell'esercitazione. Esaminare le registrazioni video per analizzare l'effetto dei parametri.



