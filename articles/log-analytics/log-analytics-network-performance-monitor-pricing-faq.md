---
title: Domande frequenti sui prezzi di Monitoraggio prestazioni rete di Azure | Microsoft Docs
description: Domande frequenti - Monitoraggio prestazioni rete di Azure
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: ajaycode
ms.openlocfilehash: 5b2335ee2584af07ed23ce87be92a869f3a07ba1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Modifiche ai prezzi per Monitoraggio prestazioni rete di Azure

In risposta al feedback degli utenti, di recente è stata introdotta una [nuova esperienza di acquisto](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) per i vari servizi di monitoraggio disponibili in Azure.

Questo documento illustra le modifiche ai prezzi relative a [Monitoraggio prestazioni rete](https://docs.microsoft.com/azure/networking/network-monitoring-overview) di Azure in un semplice formato basato su domanda e risposta.

Monitoraggio prestazioni rete è costituito da tre componenti:
* [Monitoraggio prestazioni](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Monitoraggio endpoint di servizio](https://docs.microsoft.com/azure/networking/network-monitoring-overview#service-endpoint-monitor) e
* [Monitoraggio di ExpressRoute](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

La sezione di seguito illustra le modifiche ai prezzi per i componenti precedenti.

## <a name="performance-monitor-pm"></a>Performance Monitor

**Come era fatturato l'utilizzo di Performance Monitor nel vecchio modello?**

La fatturazione per Monitoraggio prestazioni rete era basata sull'utilizzo/consumo di due componenti:
* Nodi: tutte le transazioni sintetiche hanno origine e terminano a livello dei nodi. I nodi sono anche denominati agenti o MMA (Microsoft Monitoring Agent).
* Dati: i risultati dei vari test di rete diverse vengono archiviati nel repository di Log Analytics.

Nel modello precedente, la fattura era calcolata in base al numero di nodi e al volume dei dati generati. 

**Come è fatturato l'utilizzo di Performance Monitor in base al nuovo modello?**

La funzionalità Performance Monitor in prestazioni in NPM viene ora fatturata in base a una combinazione di: 

* collegamenti a subnet monitorati e
* volume dei dati

**Che cos'è un collegamento a subnet?**

Performance Monitor monitora la connettività tra due o più posizioni nella rete.  La connessione tra un gruppo di nodi/agenti in una subnet e un gruppo di nodi in un'altra subnet viene definito collegamento a subnet.

**Si dispone di due subnet (subnet A e B) e di vari agenti su ogni subnet.  Performance Monitor monitora la connettività da tutti gli agenti nella subnet A verso tutti gli agenti nella subnet B. Gli addebiti saranno basati sul numero di connessioni tra le subnet?**

di serie Ai fini della fatturazione, tutte le connessioni dalla subnet A alla subnet B vengono raggruppate in un unico collegamento a subnet e la fatturazione avviene per una singola connessione.  Performance Monitor continuerà a monitorare la connettività tra i vari agenti su ciascuna subnet.

**Quali sono i costi per il monitoraggio di un collegamento a subnet?**

Fare riferimento alla sezione [Ping Mesh](https://azure.microsoft.com/pricing/details/network-watcher/) per i costi di monitoraggio di un singolo collegamento a subnet per l'intero mese.

**Quali sono i costi per i dati generati da Performance Monitor?**

Il costo per l'inserimento (caricamento dei dati in Log Analytics, elaborazione e indicizzazione) è indicato nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/log-analytics/) di Log Analytics.  (sezione Inserimento dati).

Il costo per la conservazione dei dati (opzione dei dati conservati presso il cliente, oltre il primo mese) è anch'esso disponibile nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/log-analytics/)  (sezione Conservazione dei dati).


## <a name="expressroute-monitor-erm"></a>Monitoraggio di ExpressRoute

**Quali sono i costi per l'utilizzo di Monitoraggio di ExpressRoute?**

I costi per Monitoraggio di ExpressRoute vengono addebitati in base al volume dei dati generati durante il monitoraggio.   Fare riferimento alla domanda "Quali sono i costi per i dati generati da Performance Monitor?" per altre informazioni.

**Si usa Monitoraggio di ExpressRoute per monitorare più circuiti ExpressRoute. Gli addebiti sono basati sul numero di circuiti monitorati?**

Gli addebiti non sono basati sul numero di circuiti, né sul tipo di peering (ad esempio, peering privato o peering Microsoft),  bensì sul volume dei dati, come illustrato in precedenza.

**Qual è il volume dei dati generati per il monitoraggio di un singolo circuito?**

Il volume dei dati generati ogni mese per il monitoraggio di una connessione di peering privata è il seguente:

|Percentile      |Dati/mese (MB)|
| :---:          |           ---:|
|50<sup>°</sup> |            192|
|60<sup>°</sup> |            256|
|70<sup>°</sup> |            360|
|80<sup>°</sup> |            498|
|90<sup>°</sup> |            870|
|95<sup>°</sup> |           1560|


In base alla tabella precedente, i clienti al 50° percentile pagano per 192 MB di dati. A € 1,94/GB per il primo mese, il costo sostenuto per il monitoraggio di un circuito è di € 0.36 (= 192 * 0.36 / 1024) per il primo mese.

**Quali sono i motivi alla base delle variazioni nel volume dei dati?**

Il volume dei dati di monitoraggio generati dipende da diversi fattori, tra cui:
* numero di agenti: l'accuratezza dell'isolamento degli errori aumenta con l'aumento del numero di agenti
* numero di hop nella rete
* numero di percorsi tra l'origine e destinazione

I clienti nei percentili più alti (nella tabella precedente) in genere monitorano i circuiti da vari punti privilegiati sulla rete locale.  Altri agenti vengono inseriti anche più all'interno della rete, a una distanza maggiore dal router perimetrale del provider di servizi. Gli agenti sono spesso inseriti in più siti utente, rami e rack all'interno dei data center.

## <a name="service-endpoint-monitor-sepm"></a>Monitoraggio endpoint di servizio

**Quali sono i costi per l'utilizzo di Monitoraggio endpoint di servizio?**

I costi per l'utilizzo di Monitoraggio endpoint di servizio vengono calcolati in base a:
* numero di connessioni
* volume dei dati

**Che cos'è una connessione?**

Una connessione è un test della raggiungibilità di un endpoint (URL o servizio di rete) da parte di un singolo agente per l'intero mese. Ad esempio, il monitoraggio di una connessione a bing.com da parte di tre agenti rappresenta tre connessioni.

**Quali sono i costi di Monitoraggio endpoint di servizio?**

- Per il costo del monitoraggio di un endpoint per l'intero mese, vedere la sezione [Monitoraggio connessione](https://azure.microsoft.com/pricing/details/network-watcher/).
- Gli addebiti per i dati sono disponibili nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/log-analytics/) di Log Analytics  (sezione Inserimento dati).

## <a name="references"></a>Riferimenti

- [Domande frequenti sui prezzi di Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) - La sezione delle domande frequenti contiene informazioni sul livello gratuito, il prezzo per nodo e così via.

