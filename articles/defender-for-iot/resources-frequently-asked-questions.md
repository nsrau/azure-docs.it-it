---
title: Domande frequenti su Defender
description: Trovare le risposte alle domande più frequenti su Azure Defender per le funzionalità e il servizio Internet.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.openlocfilehash: 5e7eabd44ea8c56fbb102f9e48812745a31de62a
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089197"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Domande frequenti su Azure Defender

Questo articolo fornisce un elenco di domande frequenti e risposte su Defender for Internet.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Qual è la proposta di valore unica di Azure per la sicurezza per IoT?

Defender for Internet consente alle aziende di estendere la propria vista di sicurezza informatica esistente alla soluzione intera. Azure offre una visione end-to-end della soluzione aziendale che consente di intraprendere azioni e decisioni di business in base all’approccio alla sicurezza scelto e ai dati raccolti. La combinazione delle funzionalità per la sicurezza di Azure IoT, Azure IoT Edge e del Centro sicurezza di Azure consente di creare le soluzioni di scelta con le caratteristiche di sicurezza necessarie.

## <a name="our-organization-uses-proprietary-non-standard-industrial-protocols-are-they-supported"></a>L'organizzazione usa protocolli industriali non standard proprietari. Sono supportati? 

Azure Defender per l'it offre un supporto completo del protocollo. Oltre al supporto del protocollo incorporato, è possibile proteggere i dispositivi e i dispositivi che eseguono protocolli proprietari e personalizzati, oppure protocolli che deviano da qualsiasi standard. Utilizzando l'SDK dell'ambiente di sviluppo open Horizon (ODE), gli sviluppatori possono creare plug-in di dissettore che decodificano il traffico di rete in base a protocolli definiti. Il traffico viene analizzato dai servizi per offrire monitoraggio completo, avvisi e report. USA orizzonte per:
- Espandi visibilità e controllo senza la necessità di eseguire l'aggiornamento a nuove versioni.
- Proteggere le informazioni proprietarie sviluppando il sito come plug-in esterno. 
- Localizzare il testo per gli avvisi, gli eventi e i parametri del protocollo.

Questa soluzione univoca per lo sviluppo di protocolli come plug-in non richiede team di sviluppo dedicati o versioni di versione per supportare un nuovo protocollo. Sviluppatori, partner e clienti possono sviluppare protocolli in modo sicuro e condividere informazioni e conoscenze usando Horizon. 

## <a name="do-i-have-to-purchase-hardware-appliances-from-microsoft-partners"></a>È necessario acquistare appliance hardware dai partner Microsoft?
Azure Defender per il sensore Internet viene eseguito su specifiche hardware specifiche, come descritto nella [Guida alle specifiche hardware](https://aka.ms/AzureDefenderforIoTBareMetalAppliance), i clienti possono acquistare hardware certificato da partner Microsoft oppure usare la fattura di materiali (BOM) fornita e acquistarla autonomamente. 

L'hardware certificato è stato testato nei nostri laboratori per la stabilità dei driver, le gocce di pacchetti e il dimensionamento di rete.


## <a name="regulation-does-not-allow-us-to-connect-our-system-to-the-internet-can-we-still-utilize-defender-for-iot"></a>Il regolamento non ci consente di connettere il sistema a Internet. Possiamo ancora usare Defender per le cose?

Sì, è possibile. La soluzione locale di Azure Defender per la piattaforma Internet viene distribuita come appliance del sensore fisico o virtuale che inserisce passivamente il traffico di rete (via SPAN, RSPAN o TAP) per analizzare, individuare e monitorare continuamente le reti IT, OT e Internet. Per le aziende di grandi dimensioni, più sensori possono aggregare i dati a una console di gestione locale.

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>In quale punto della rete è necessario connettere le porte di monitoraggio?

Il sensore Azure Defender per l'it si connette a una porta di intervallo o a un TAP di rete e inizia immediatamente a raccogliere il traffico di rete ICS tramite il monitoraggio passivo (senza agenti). Non ha alcun effetto sulle reti OT perché non è disponibile nel percorso dati e non esegue l'analisi attiva dei dispositivi OT.

Esempio:
- Una singola appliance (virtuale di fisica) può trovarsi nel livello della rete perimetrale del negozio, in modo che tutto il traffico delle celle del negozio venga indirizzato a questo livello.
- In alternativa, è possibile individuare piccoli sensori in ogni cella del negozio con la gestione cloud o locale che risiederà nel livello della rete perimetrale del negozio. Un altro dispositivo (virtuale o fisico) può monitorare il traffico nel livello della rete perimetrale del negozio (per SCADA, storico o MES).

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Quali sono le differenze tra Defender e il concorso?

Sebbene altre soluzioni forniscano una serie di funzionalità che consentono ai clienti di creare soluzioni personalizzate, Defender for Internet offre una soluzione di sicurezza per gli elementi End-to-end univoca che offre una panoramica completa della sicurezza di tutte le risorse di Azure correlate. Azure assicura una distribuzione rapida e la piena integrazione con gli elementi gemelli del modulo hub IoT per una facile integrazione con gli strumenti di gestione dei dispositivi esistenti.


## <a name="do-i-have-to-be-an-azure-iot-customer"></a>È necessario essere clienti di Azure?

Sì. Per le distribuzioni connesse al cloud, Azure Defender per l'it si basa sulla connettività e sull'infrastruttura di Azure.
## <a name="can-i-create-my-own-alerts"></a>È possibile creare avvisi personalizzati?

Sì. È possibile impostare un avviso personalizzato per un set di comportamenti predefinito, ad esempio l'indirizzo IP e le porte aperte. Vedere la pagina sulla [creazione di avvisi personalizzati](quickstart-create-custom-alerts.md) per ulteriori informazioni sugli avvisi personalizzati e su come crearli.

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Dove è possibile visualizzare I log? È possibile personalizzare I log?

- Visualizzare i log e le raccomandazioni tramite l'area di lavoro Log Analytics connessa. Configurare la durata e le dimensioni per l’archiviazione nell'area di lavoro.

- I dati non elaborati dall'agente di sicurezza possono anche essere memorizzati nell'account Log Analytics. Prima di modificare la configurazione di questa opzione, prendere in considerazione le dimensioni, la durata, i requisiti di archiviazione e i costi associati.



## <a name="what-happens-when-the-internet-connection-stops-working"></a>Cosa accade quando la connessione Internet si interrompe?

I sensori e gli agenti continuano a funzionare e a archiviare i dati finché il dispositivo è in esecuzione. I dati vengono memorizzati nella cache dei messaggi di sicurezza in base alla configurazione delle dimensioni. Quando il dispositivo recupera la connettività, viene ripreso anche l’invio dei messaggi sulla sicurezza.





## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come iniziare a usare Defender per l'it, vedere gli articoli seguenti:

- Leggi la [Panoramica](overview.md) di Defender for Internet
- Verificare i [prerequisiti del servizio](service-prerequisites.md)
- Vedere ulteriori informazioni su come [iniziare](getting-started.md)
- Informazioni sugli [avvisi di sicurezza di Defender](concept-security-alerts.md)
