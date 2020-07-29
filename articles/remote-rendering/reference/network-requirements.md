---
title: Requisiti di rete
description: Requisiti e procedure consigliate per la rete per un'esperienza con le reti
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: fe684d15e2ce9b8d302db4eb6bd31dd1416abf21
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196538"
---
# <a name="network-requirements"></a>Requisiti di rete

Una connessione di rete stabile e a bassa latenza a una data center di Azure è di fondamentale importanza per garantire un'esperienza utente ottimale in Rendering remoto di Azure. Condizioni di rete inadeguate possono causare interruzioni della connessione, ologrammi non uniformi o instabilità, oltre a un percettibile ritardo durante l'aggiornamento del grafo della scena sul lato server.

## <a name="guidelines-for-network-connectivity"></a>Linee guida per la connettività di rete

I requisiti di rete esatti dipendono dal caso d'uso specifico, ad esempio il numero e la frequenza delle modifiche apportate al grafo della scena remota, oltre alla complessità della visualizzazione di cui è stato eseguito il rendering. Sono però disponibili alcune linee guida per garantire un'esperienza soddisfacente:

* La connettività Internet disponibile deve supportare regolarmente almeno **40 Mbps in downstream** e **5 Mbps in upstream** per una singola sessione utente di Rendering remoto di Azure, presupponendo che non vi sia traffico concorrente nella rete. Per esperienze ottimali è consigliabile usare velocità più elevate. Con l'aumentare del numero di utenti nella stessa rete, questi requisiti aumentano di conseguenza.
* Con una **banda Wi-Fi a 5 GHz** si ottengono in genere risultati migliori rispetto a una a 2,4 GHz, sebbene siano entrambe valide.
* Se sono presenti altre reti Wi-Fi nelle vicinanze, evitare di usare i canali Wi-Fi usati da queste altre reti. È possibile usare strumenti di analisi della rete come [WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) per verificare se i canali usati dalla rete Wi-Fi non presentino condizioni di traffico concorrente.
* **Evitare assolutamente l'uso di ripetitori Wi-Fi** o l'inoltre tramite LAN-over-powerline.
* **Evitare il traffico concorrente che usa una notevole quantità di larghezza di banda** nella stessa rete Wi-Fi, ad esempio per streaming video o giochi.
* Un **segnale Wi-Fi di intensità adeguata** è di fondamentale importanza. Se possibile, non allontanarsi dal punto di accesso Wi-Fi ed evitare ostacoli tra il dispositivo client e i punti di accesso.
* Assicurarsi di connettersi sempre al **data center di Azure** più vicino per la propria [area](regions.md). Maggiore è la vicinanza al data center, minore è la latenza di rete, che ha un effetto enorme sulla stabilità degli ologrammi.

> [!NOTE]
> La larghezza di banda downstream viene utilizzata principalmente dal flusso video, che a sua volta si divide tra informazioni relative al colore e alla profondità, ad esempio 60 Hz, stereo.

## <a name="network-performance-tests"></a>Test delle prestazioni di rete

Per sapere se la qualità della connettività di rete è sufficiente per eseguire Rendering remoto di Azure, è possibile usare gli strumenti online disponibili. È consigliabile eseguire questi strumenti online da un computer portatile di potenza adeguata connesso alla stessa rete Wi-Fi del dispositivo in cui si prevede di eseguire l'applicazione client Rendering remoto di Azure. I risultati ottenuti eseguendo i test su un telefono cellulare o su un dispositivo HoloLens2 sono in genere meno utili, perché hanno mostrato variazioni significative sui dispositivi endpoint a bassa potenza. Sistemare il computer portatile all'incirca nella stessa posizione in cui si prevede di usare il dispositivo che esegue l'applicazione client Rendering remoto di Azure.

Ecco alcuni semplici passaggi per un test rapido della connettività di rete:

1. **Eseguire uno strumento di test di rete come www.speedtest.net per ottenere i dati complessivi sulla latenza e sulla larghezza di banda upstream/downstream della connessione di rete.**
Selezionare il server più vicino ed eseguire il test. Anche se il server non è il data center di Azure a cui si connetterà Rendering remoto di Azure, i dati risultanti sono comunque utili per comprendere le prestazioni della connessione Internet e della rete Wi-Fi.
   * **Requisito minimo** per Rendering remoto di Azure: all'incirca 40 Mbps in downstream e 5 Mbps in upstream.
   * **Scelta consigliata** per Rendering remoto di Azure: all'incirca 100 Mbps in downstream e 10 Mbps in upstream.
È consigliabile eseguire il test più volte e prendere in considerazione i risultati peggiori.
1. **Usare uno strumento come www.azurespeed.com per misurare la latenza nei data center di Azure**. Selezionare il data center di Azure supportato da Rendering remoto di Azure più vicino (vedere le [aree supportate](regions.md)) ed eseguire un **test di latenza**. Se i valori visualizzati sono variabili, attendere qualche istante per consentire la stabilizzazione dei risultati.
   * **Requisito minimo** per Rendering remoto di Azure: la latenza deve essere costantemente inferiore a 100 ms.
   * **Scelta consigliata** per Rendering remoto di Azure: la latenza deve essere costantemente inferiore a 70 ms.

Anche se una bassa latenza non garantisce il corretto funzionamento di Rendering remoto di Azure nella rete, in genere è stato riscontrato un funzionamento adeguato in situazioni in cui questi test erano stati superati.
Se si rilevano artefatti, come ologrammi instabili, salti o discontinuità durante l'esecuzione di Rendering remoto di Azure, vedere la [guida alla risoluzione dei problemi](../resources/troubleshoot.md).

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Eseguire il rendering di un modello con Unity](../quickstarts/render-model.md)
