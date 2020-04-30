---
title: Requisiti di rete
description: Requisiti di rete e procedure consigliate per la rete per un'esperienza ottimale
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: 9bb8818d2bb91c14eafc272774e0b633c51e58e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617878"
---
# <a name="network-requirements"></a>Requisiti di rete

Una connessione di rete stabile e a bassa latenza a una data center di Azure è fondamentale per un'esperienza utente ottimale nel rendering remoto di Azure. Condizioni di rete insufficienti possono comportare l'eliminazione di connessioni, instabilità, jittery o ologrammi "saltanti" e un ritardo notevole durante l'aggiornamento del grafico della scena sul lato server.

## <a name="guidelines-for-network-connectivity"></a>Linee guida per la connettività di rete

I requisiti di rete esatti dipendono dal caso d'uso specifico, ad esempio il numero e la frequenza delle modifiche apportate al grafico della scena remota, oltre alla complessità della visualizzazione di cui è stato eseguito il rendering, ma sono disponibili diverse linee guida per assicurarsi che l'esperienza sia la più adatta possibile:

* La connettività Internet deve supportare almeno **50 Mbps a valle** e a **10 Mbps upstream** in modo coerente per una singola sessione utente del rendering remoto di Azure, presupponendo che non vi sia traffico concorrenziale sulla rete. Per esperienze migliori, è consigliabile usare tariffe più elevate. Con più utenti nella stessa rete, questi requisiti aumentano di conseguenza.
* Se si usa la **banda Wi-Fi a 5 GHz** , in genere si otterranno risultati migliori rispetto alla banda Wi-fi da 2,4 GHz, sebbene entrambe funzionino.
* Se sono presenti altre reti Wi-Fi vicine, evitare di usare i canali Wi-Fi usati da queste altre reti. È possibile usare strumenti di analisi della rete come [WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) per verificare se i canali usati dalla rete Wi-Fi sono privi di traffico in competizione.
* Evitare rigorosamente l' **uso di ripetitori Wi-Fi** o di inoltri LAN-over-Powerline.
* **Evitare il traffico in competizione** con la larghezza di banda, ad esempio streaming video o gioco, sulla stessa rete Wi-Fi.
* Un livello **di attendibilità del segnale Wi-Fi** è essenziale. Se possibile, restare vicino al punto di accesso Wi-Fi ed evitare gli ostacoli tra il dispositivo client e i punti di accesso.
* Assicurarsi di connettersi sempre al **Data Center più vicino di Azure** per la propria [area geografica](regions.md). Il data center più vicino, minore è la latenza di rete, che ha un effetto enorme sulla stabilità degli ologrammi.

> [!NOTE]
> La larghezza di banda downstream viene utilizzata principalmente dal flusso video, che a sua volta viene suddivisa tra le informazioni relative al colore e alla profondità (60 Hz, stereo).

## <a name="network-performance-tests"></a>Test delle prestazioni di rete

Per comprendere in modo iniziale se la qualità della connettività di rete è sufficiente per eseguire il rendering remoto di Azure, è possibile usare gli strumenti online disponibili. È consigliabile eseguire questi strumenti online da un computer portatile ragionevolmente potente connesso allo stesso Wi-Fi del dispositivo su cui si prevede di eseguire l'applicazione client per il rendering remoto di Azure. I risultati ottenuti dall'esecuzione dei test su un telefono cellulare o HoloLens2 sono in genere meno utili, perché hanno dimostrato di mostrare variazioni significative sui dispositivi endpoint a basso consumo. La posizione in cui posizionare il computer portatile dovrebbe essere approssimativamente nella stessa posizione in cui si prevede di usare il dispositivo che esegue l'applicazione client per il rendering remoto di Azure.

Ecco alcuni semplici passaggi per un rapido test della connettività di rete:

1. **Eseguire uno strumento di test di rete come www.speedtest.net per ottenere i dati sulla latenza complessiva e sulla larghezza di banda upstream/downstream della connessione di rete.**
Selezionare un server più vicino ed eseguire il test. Sebbene il server non sia il data center Azure a cui si connetterà il rendering remoto di Azure, i dati risultanti sono comunque utili per comprendere le prestazioni della connessione Internet e del Wi-Fi.
   * **Requisito minimo** per il rendering remoto di Azure: circa 40 Mbps a valle e 5 Mbps a Monte.
   * **Consigliato** per il rendering remoto di Azure: circa 100 Mbps a valle e a 10 Mbps a Monte.
È consigliabile eseguire il test più volte e ottenere i risultati peggiori.
1. **Usare uno strumento come www.azurespeed.com per misurare la latenza nei data center di Azure**. Selezionare il data center di Azure supportato dal rendering remoto di Azure più vicino (vedere le [aree supportate](regions.md)) ed eseguire un **test di latenza**. Se sono presenti variazioni nei numeri visualizzati, è necessario destabilizzare i risultati.
   * **Requisito minimo** per il rendering remoto di Azure: la latenza deve essere inferiore a 100 ms.
   * **Consigliato** per il rendering remoto di Azure: la latenza deve essere in modo coerente inferiore a 70 ms.

Sebbene la bassa latenza non sia una garanzia che il rendering remoto di Azure funzionerà correttamente sulla rete, in genere si è verificato un ottimo comportamento nelle situazioni in cui questi test sono stati superati.
Se si riscontrano elementi come gli ologrammi instabili, jittery o jumping durante l'esecuzione del rendering remoto di Azure, fare riferimento alla [Guida alla risoluzione dei problemi](../resources/troubleshoot.md).

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Eseguire il rendering di un modello con Unity](../quickstarts/render-model.md)
