---
title: Requisiti di rete
description: Requisiti di rete e procedure consigliate di rete per un'esperienza ottimale
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: 9bb8818d2bb91c14eafc272774e0b633c51e58e1
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617878"
---
# <a name="network-requirements"></a>Requisiti di rete

Una connessione di rete stabile e a bassa latenza a un data center di Azure è fondamentale per una buona esperienza utente nel rendering remoto di Azure.A stable, low-tency network connection to an Azure data center is critical for a good user experience in Azure Remote Rendering. Condizioni di rete inadeguate possono causare connessioni interrotte, ologrammi instabili, nervosi o "saltanti" e un notevole ritardo durante l'aggiornamento del grafico della scena sul lato server.

## <a name="guidelines-for-network-connectivity"></a>Linee guida per la connettività di rete

I requisiti di rete esatti dipendono dal caso d'uso specifico, ad esempio il numero e la frequenza delle modifiche al grafico della scena remota, nonché dalla complessità della visualizzazione sottoposta a rendering, ma esistono una serie di linee guida per garantire che l'esperienza sia il più buona possibile:

* La connettività Internet deve supportare almeno **50 Mbps a valle** e **10 Mbps a monte** in modo coerente per una singola sessione utente di Azure Remote Rendering, presupponendo che non vi sia traffico concorrente nella rete. Consigliamo tariffe più elevate per esperienze migliori. Con più utenti sulla stessa rete, questi requisiti aumentano di conseguenza.
* Utilizzando la **banda Wi-Fi** a 5 GHz di solito produrre risultati migliori rispetto alla banda Wi-Fi 2,4-GHz, anche se entrambi dovrebbero funzionare.
* Se ci sono altre reti Wi-Fi nelle vicinanze, evita di utilizzare canali Wi-Fi in uso da queste altre reti. È possibile utilizzare strumenti di scansione di rete come [WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) per verificare se i canali utilizzati dalla rete Wi-Fi sono privi di traffico concorrente.
* Evitare rigorosamente l'utilizzo di **ripetitori Wi-Fi** o l'inoltro LAN-over-powerline.
* **Evita di convocando traffico intenso** in larghezza di banda, come lo streaming di video o video, sulla stessa rete Wi-Fi.
* Avere una buona potenza del **segnale Wi-Fi** è essenziale. Se possibile, rimani vicino al tuo punto di accesso Wi-Fi ed evita gli ostacoli tra il tuo dispositivo client e i punti di accesso.
* Assicurarsi di connettersi sempre al **data center di Azure più vicino** per [l'area.](regions.md) Più il data center è vicino, minore è la latenza di rete, che ha un enorme effetto sulla stabilità dell'ologramma.

> [!NOTE]
> La larghezza di banda downstream è per lo più utilizzata dal flusso video, che a sua volta è diviso tra informazioni sul colore e sulla profondità (entrambi a 60 Hz, stereo).

## <a name="network-performance-tests"></a>Test delle prestazioni di rete

Se si vuole ottenere una comprensione iniziale se la qualità della connettività di rete è sufficiente per eseguire il rendering remoto di Azure, è possibile usare gli strumenti online esistenti. È consigliabile eseguire questi strumenti online da un computer portatile ragionevolmente potente connesso allo stesso Wi-Fi del dispositivo in cui si prevede di eseguire l'applicazione client di rendering remoto di Azure. I risultati ottenuti dall'esecuzione dei test in un telefono cellulare o HoloLens2 sono in genere meno utili, in quanto hanno dimostrato di mostrare variazioni significative nei dispositivi endpoint a basso consumo. La posizione in cui si posiziona il portatile deve trovarsi approssimativamente nella stessa posizione in cui si prevede di utilizzare il dispositivo che esegue l'applicazione client di rendering remoto di Azure.The location the laptop should be roughly at the same place at which you expect to use the device that runs your Azure Remote Rendering client application.

Ecco alcuni semplici passaggi per un rapido test della connettività di rete:

1. **Eseguire uno strumento di test di rete come www.speedtest.net per ottenere dati sulla latenza complessiva e sulla larghezza di banda upstream/downstream della connessione di rete.**
Scegliere un server più vicino a te ed eseguire il test. Anche se il server non sarà il data center di Azure a cui si connetterà il rendering remoto di Azure, i dati risultanti sono comunque utili per comprendere le prestazioni della connessione Internet e del Wi-Fi.
   * **Requisito minimo** per il rendering remoto di Azure: circa 40 Mbps a valle e 5 Mbps a monte.
   * **Consigliato** per il rendering remoto di Azure: ad esempio 100 Mbps a valle e a monte di 10 Mbps.
Si consiglia di eseguire il test più volte e prendere i risultati peggiori.
1. Usare uno strumento come www.azurespeed.com che misura la latenza dei data center di **Azure.Use**a tool like www.azurespeed.com that measures latency to Azure data centers . Selezionare il data center di Azure supportato dal rendering remoto di Azure più vicino all'utente (vedere [Aree supportate)](regions.md)ed eseguire un **test di latenza.** Se c'è una variazione nei numeri che vedi, dai ai risultati un po' di tempo per stabilizzarti.
   * **Requisito minimo** per il rendering remoto di Azure: la latenza deve essere inferiore a 100 ms.
   * **Consigliato** per il rendering remoto di Azure: la latenza deve essere sempre inferiore a 70 ms.

Anche se la bassa latenza non è una garanzia che il rendering remoto di Azure funzionerà bene sulla rete, in genere si è visto un andvolgimento corretta nelle situazioni in cui questi test sono stati superati correttamente.
Se si riscontrano elementi quali ologrammi instabili, nervosi o salti quando si esegue il rendering remoto di Azure, fare riferimento alla guida alla [risoluzione dei problemi](../resources/troubleshoot.md).

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Eseguire il rendering di un modello con Unity](../quickstarts/render-model.md)
