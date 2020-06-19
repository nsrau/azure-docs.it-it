---
title: Che cos'è NAT di rete virtuale di Azure?
titlesuffix: Azure Virtual Network
description: Panoramica sulle funzionalità, le risorse, l'architettura e l'implementazione di NAT di rete virtuale. Informazioni sul funzionamento di NAT di rete virtuale e su come usare le risorse gateway NAT nel cloud.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to learn more about Virtual Network NAT, its NAT gateway resources, and what I can use them for.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2020
ms.author: allensu
ms.openlocfilehash: 94ef9b9754527bbb36ef1028f80e957da8c631dd
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84688399"
---
# <a name="what-is-virtual-network-nat"></a>Che cos'è NAT di rete virtuale?

NAT (Network Address Translation) di rete virtuale semplifica la connettività Internet solo in uscita per le reti virtuali. Quando viene configurato in una subnet, per tutta la connettività in uscita vengono usati gli indirizzi IP pubblici statici specificati.  La connettività in uscita è possibile senza bilanciamento del carico o indirizzi IP pubblici collegati direttamente alle macchine virtuali. Il NAT è completamente gestito e altamente resiliente.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" width="256" title="NAT di rete virtuale">
</p>



*Figura: NAT di rete virtuale*

## <a name="static-ip-addresses-for-outbound-only"></a>Indirizzi IP statici solo per la connettività in uscita

È possibile definire la connettività in uscita per ogni subnet con NAT.  Più subnet nella stessa rete virtuale possono avere NAT diversi. Per configurare una subnet, specificare la risorsa gateway NAT da usare. NAT verrà usato per tutti i flussi UDP e TCP in uscita di qualsiasi istanza di macchina virtuale. 

NAT è compatibile con le risorse di indirizzi IP pubblici o di prefissi IP pubblici dello SKU o con una combinazioni di entrambe.  È possibile usare un prefisso di indirizzo IP pubblico direttamente oppure distribuire gli indirizzi IP pubblici del prefisso tra più risorse gateway NAT. NAT pulirà tutto il traffico verso l'intervallo di indirizzi IP del prefisso.  L'inserimento di distribuzioni nell'elenco elementi consentiti è ora più agevole.

Tutto il traffico in uscita per la subnet viene elaborato automaticamente da NAT senza richiedere alcuna configurazione da parte del cliente.  Le route definite dall'utente non sono necessarie. NAT ha la precedenza su altri scenari di connessioni in uscita e sostituisce la destinazione Internet predefinita di una subnet.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>SNAT su richiesta con più indirizzi IP per la scalabilità

NAT usa PNAT (Port Network Address Translation) o PAT (Port Address Translation) ed è consigliato per la maggior parte dei carichi di lavoro. I carichi di lavoro dinamici o divergenti possono essere gestiti facilmente grazie all'allocazione di flussi in uscita su richiesta. In questo modo non è necessario dedicare tempo a pianificazione e pre-allocazione e di può evitare un provisioning eccessivo delle risorse in uscita. Le risorse della porta SNAT sono condivise e disponibili in tutte le subnet che usano una specifica risorsa gateway NAT e vengono fornite quando necessario.

Un indirizzo IP pubblico collegato a NAT fornisce fino a 64.000 flussi simultanei per UDP e TCP. È possibile iniziare con un singolo indirizzo IP e arrivare fino a 16 indirizzi IP pubblici.

NAT consente di creare flussi dalla rete virtuale a Internet. Il traffico restituito da Internet è consentito solo in risposta a un flusso attivo.

A differenza di SNAT in uscita del bilanciamento del carico, NAT non prevede restrizioni relative all'indirizzo IP privato di un'istanza di macchina virtuale utilizzabile per le connessioni in uscita.  Le configurazioni IP secondarie possono creare una connessione Internet in uscita con NAT.

## <a name="coexistence-of-inbound-and-outbound"></a>Coesistenza di flussi in ingresso e in uscita

NAT è compatibile con le risorse di SKU standard seguenti:

- Bilanciamento del carico
- Indirizzo IP pubblico
- Prefisso di indirizzo IP pubblico

Se usate insieme a NAT, queste risorse offrono connettività Internet in ingresso alle subnet. NAT fornisce tutta la connettività Internet in uscita da una o più subnet.

Le funzionalità NAT e di SKU standard compatibili riconoscono la direzione di avvio del flusso. Gli scenari in ingresso e in uscita possono coesistere. Questi scenari riceveranno valori NAT corretti perché queste funzionalità riconoscono la direzione del flusso. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" width="512" title="Direzione del flusso del servizio NAT di rete virtuale">
</p>

*Figura: direzione del flusso del servizio NAT di rete virtuale*

## <a name="fully-managed-highly-resilient"></a>Completamente gestito, altamente resiliente

NAT viene completamente ampliato rispetto dall'inizio. Non è necessaria alcuna operazione di incremento o scale-out.  Azure gestisce automaticamente il funzionamento di NAT.  NAT include sempre più domini di errore e può gestire più errori senza interruzioni del servizio.

## <a name="tcp-reset-for-unrecognized-flows"></a>Reimpostazione TCP per flussi non riconosciuti

Il lato privato del NAT invia pacchetti di reimpostazione TCP relativi ai tentativi di comunicazione tramite una connessione TCP che non esiste. Un esempio è dato dalle connessioni che hanno raggiunto il timeout di inattività. Il pacchetto successivo ricevuto restituirà un valore di reimpostazione TCP all'indirizzo IP privato per segnalare e forzare la chiusura della connessione.

Il lato pubblico di NAT non genera pacchetti di reimpostazione TCP o altro traffico.  Viene restituito solo il traffico prodotto dalla rete virtuale del cliente.

## <a name="configurable-tcp-idle-timeout"></a>Timeout di inattività TCP configurabile

Viene usato un timeout di inattività TCP predefinito di 4 minuti, che può essere aumentato fino a 120 minuti. Qualsiasi attività in un flusso può anche reimpostare il timer di inattività, inclusi i keep-alive TCP.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Isolamento a livello di area o zona con le zone di disponibilità

NAT è locale per impostazione predefinita. Quando si creano scenari per [zone di disponibilità](../availability-zones/az-overview.md), è possibile isolare NAT in una zona specifica (distribuzione a livello di zona).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" width="512" title="NAT di rete virtuale con zone di disponibilità">
</p>

*Figura: NAT di rete virtuale con zone di disponibilità*

## <a name="multi-dimensional-metrics-for-observability"></a>Metriche multidimensionali per l'osservabilità

È possibile monitorare il funzionamento di NAT tramite metriche multidimensionali esposte in Monitoraggio di Azure. Queste metriche consentono di osservare l'utilizzo e risolvere i problemi.  Le risorse gateway NAT espongono le metriche seguenti:
- Byte
- Pacchetti
- Pacchetti eliminati
- Totale connessioni SNAT
- Transizioni di stato della connessione SNAT per intervallo.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>Contratto di servizio

Quando disponibile a livello generale, la disponibilità del percorso dei dati NAT è pari ad almeno il 99,9%.


## <a name="pricing"></a>Prezzi

Per la fatturazione del gateway NAT, si usano due contatori distinti:

| Contatore | Tariffa |
| --- | --- |
| Ore risorsa | $ 0,045/ora |
| Dati elaborati | $ 0,045/GB |

La metrica Ore risorsa rappresenta il periodo di tempo in cui una risorsa gateway NAT risulta esistente.
La metrica Dati elaborati rappresenta tutti il traffico generato da una risorsa gateway NAT.

## <a name="availability"></a>Disponibilità

NAT di rete virtuale e la risorsa gateway NAT sono disponibili in tutte le [aree](https://azure.microsoft.com/global-infrastructure/regions/) del cloud pubblico di Azure.

## <a name="support"></a>Supporto

NAT è supportato tramite i normali canali di assistenza.

## <a name="feedback"></a>Commenti e suggerimenti

Ci interessa sapere come possiamo migliorare il servizio. Proporre nuove funzionalità o esprimere un voto in [UserVoice per NAT](https://aka.ms/natuservoice).


## <a name="limitations"></a>Limitazioni

* NAT è compatibile con risorse di indirizzi IP pubblici, prefissi di indirizzi IP pubblici e servizi di bilanciamento del carico di SKU standard. Le risorse di base, ad esempio Load Balancer di base, nonché qualsiasi prodotto derivato, non sono compatibili con NAT.  Le risorse di base devono essere inserite in una subnet non configurata con NAT.
* La famiglia di indirizzi IPv4 è supportata.  NAT non interagisce con la famiglia di indirizzi IPv6.  Non è possibile distribuire NAT in una subnet con prefisso IPv6.
* La registrazione del flusso del gruppo di sicurezza di rete non è supportato con l'uso di NAT.
* NAT non può estendersi in più reti virtuali.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla [risorsa gateway NAT](./nat-gateway-resource.md).
* [Segnalare le nuove funzionalità richieste per NAT di rete virtuale in UserVoice](https://aka.ms/natuservoice).

