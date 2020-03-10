---
title: Risolvere i problemi di connettività del servizio NAT di rete virtuale di Azure
titleSuffix: Azure Virtual Network NAT troubleshooting
description: Risolvere i problemi relativi al servizio NAT di rete virtuale.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: d56cd3b3d286d69a51d8cc14eb8020343cf7295a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302985"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Risolvere i problemi di connettività del servizio NAT di rete virtuale di Azure

Questo articolo consente agli amministratori di diagnosticare e risolvere i problemi di connettività quando si usa il servizio NAT di rete virtuale.

>[!NOTE] 
>NAT di rete virtuale è attualmente disponibile in anteprima pubblica e in un set limitato di [aree](nat-overview.md#region-availability). Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="problems"></a>I problemi

- [Esaurimento SNAT](#snat-exhaustion).
- [Errore del ping ICMP](#icmp-ping-is-failing).

Per risolvere questi problemi, attenersi alla procedura descritta nella sezione seguente.

## <a name="resolution"></a>Risoluzione

### <a name="snat-exhaustion"></a>Esaurimento SNAT

Una singola [risorsa gateway NAT](nat-gateway-resource.md) supporta un numero di flussi simultanei compreso tra 64.000 e 1 milione.  Ogni indirizzo IP fornisce 64.000 porte SNAT all'inventario disponibile. È possibile usare fino a 16 indirizzi IP per ogni risorsa gateway NAT.  Il meccanismo SNAT è descritto [qui](nat-gateway-resource.md#source-network-address-translation) in modo più dettagliato.

#### <a name="steps"></a>Passaggi:

1. Esaminare il modo in cui l'applicazione crea la connettività in uscita, ad esempio, revisione del codice o acquisizione di pacchetti. 
2. Determinare se questa attività è un comportamento previsto o se l'applicazione non funziona correttamente.  Usare le [metriche](nat-metrics.md) in Monitoraggio di Azure per convalidare i risultati.
3. Valutare se vengono seguiti i modelli appropriati.
4. Valutare se il problema relativo l'esaurimento delle porte SNAT deve essere corretto mediante l'assegnazione di ulteriori indirizzi IP alla risorsa gateway NAT.

#### <a name="design-pattern"></a>Schema progettuale:

Quando possibile, sfruttare sempre i vantaggi del riutilizzo delle connessioni e del pool di connessioni.  Questo modello eviterà problemi di esaurimento delle risorse e genererà un comportamento prevedibile. Le primitive per questi modelli sono reperibili in molti framework e librerie di sviluppo.
- Valutare l'uso di [modelli di polling asincroni](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) per le operazioni a esecuzione prolungata per liberare risorse di connessione per altre operazioni.
- I flussi di lunga durata, ad esempio le connessioni TCP riutilizzate, devono usare i keep-alive TCP o i keep-alive del livello dell'applicazione per evitare il timeout dei sistemi intermedi.
- È consigliabile usare i [criteri di ripetizione](https://docs.microsoft.com/azure/architecture/patterns/retry) dei tentativi per evitare tentativi o picchi aggressivi durante errori temporanei o ripristini da errori.
La creazione di una nuova connessione TCP per ogni operazione HTTP (nota anche come "connessioni atomiche") è un anti-pattern.  Le connessioni atomiche impediscono il ridimensionamento ottimale dell'applicazione e generano uno spreco di risorse.  Concatenare sempre più operazioni nella stessa connessione.  L'applicazione otterrà un vantaggio in termini di velocità delle transazioni e costi delle risorse.  Quando l'applicazione usa la crittografia Transport Layer Security (TLS), l'elaborazione delle nuove connessioni comporta un costo significativo.  Vedere [Schemi progettuali per il cloud di Azure](https://docs.microsoft.com/azure/architecture/patterns/) per altri modelli di procedure consigliate.

#### <a name="mitigations"></a>Soluzioni di prevenzione

È possibile ridimensionare la connettività in uscita come indicato di seguito:

| Scenario | Strategia di riduzione del rischio |
|---|---|
| Si riscontra una contesa per le porte SNAT e l'esaurimento delle porte SNAT durante i periodi di utilizzo elevato. | Determinare se è possibile aggiungere risorse indirizzo IP pubblico o risorse prefisso IP pubblico aggiuntive. Questa aggiunta garantirà fino a un totale di 16 indirizzi IP per il gateway NAT, fornirà un inventario più esteso per le porte SNAT disponibili (64.000 per indirizzo IP) e consentirà di dimensionare ulteriormente lo scenario.|
| Sono già stati assegnati 16 indirizzi IP e si riscontra ancora l'esaurimento delle porte SNAT. | Distribuire l'ambiente dell'applicazione tra più subnet e specificare una risorsa gateway NAT per ogni subnet. |

>[!NOTE]
>È importante comprendere la causa dell'esaurimento delle porte SNAT. Assicurarsi di usare i modelli corretti per gli scenari affidabili e scalabili.  L'aggiunta di più porte SNAT a uno scenario senza aver compreso la causa della domanda deve essere l'ultima eventualità. Se non si comprende il motivo per cui lo scenario fa pressione sull'inventario di porte SNAT, l'aggiunta di più porte SNAT mediante l'aggiunta di altri indirizzi IP ritarderà solamente lo stesso errore di esaurimento man mano che le prestazioni dell'applicazione aumentano.  È possibile che si stiano mascherando altre inefficienze e anti-pattern.

### <a name="icmp-ping-is-failing"></a>Errore del ping ICMP

Il servizio [NAT di rete virtuale](nat-overview.md) supporta i protocolli UDP e TCP IPv4. Il protocollo ICMP non è supportato e genererà quindi errori.  Usare invece i test di connessione TCP, ad esempio "ping TCP", e i test del livello dell'applicazione specifici per UDP per convalidare la connettività end-to-end.

La tabella seguente può essere usata come punto di partenza per gli strumenti da usare per avviare i test.

| Sistema operativo | Test di connessione TCP generico | Test del livello dell'applicazione TCP | UDP |
|---|---|---|---|
| Linux | nc (test di connessione generico) | curl (test del livello dell'applicazione TCP) | specifico dell'applicazione |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) di PowerShell | specifico dell'applicazione |

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [servizio NAT di rete virtuale](nat-overview.md)
- Informazioni sulla [risorsa gateway NAT](nat-gateway-resource.md)
- Informazioni su [metriche e avvisi per le risorse gateway NAT](nat-metrics.md).
