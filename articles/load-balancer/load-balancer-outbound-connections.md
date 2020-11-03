---
title: Azure Load Balancer proxy in uscita
description: Descrive il modo in cui Azure Load Balancer viene usato come proxy per la connettività Internet in uscita
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 185bb47677e978a3098f39024995da6399f90658
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241770"
---
# <a name="outbound-proxy-azure-load-balancer"></a>Azure Load Balancer proxy in uscita

Un servizio di bilanciamento del carico di Azure può essere usato come proxy per la connettività Internet in uscita. Il servizio di bilanciamento del carico fornisce la connettività in uscita per le istanze back-end. 

Questa configurazione utilizza la **Network Address Translation di origine (SNAT)** . SNAT riscrive l'indirizzo IP del back-end nell'indirizzo IP pubblico del servizio di bilanciamento del carico. 

SNAT Abilita il **mascheramento IP** dell'istanza back-end. Questo mascheramento impedisce alle origini esterne di avere un indirizzo diretto per le istanze back-end. 

La condivisione di un indirizzo IP tra le istanze di back-end riduce il costo degli indirizzi IP pubblici statici e supporta scenari come la semplificazione degli elenchi di indirizzi IP consentiti con traffico da indirizzi IP pubblici noti. 

## <a name="sharing-ports-across-resources"></a><a name ="snat"></a> Condivisione delle porte tra le risorse

Se le risorse back-end di un servizio di bilanciamento del carico non hanno indirizzi IP pubblici a livello di istanza (ILPIP), stabiliscono connettività in uscita tramite l'IP front-end del servizio di bilanciamento del carico pubblico.

Le porte vengono usate per generare identificatori univoci usati per mantenere flussi distinti. Internet usa una tupla con cinque elementi per fornire questa distinzione.

La tupla con cinque elementi è costituita da:

* IP di destinazione
* Porta di destinazione
* IP di origine
* Porta di origine e protocollo per fornire questa distinzione.

Se per le connessioni in ingresso viene usata una porta, sarà presente un **listener** per le richieste di connessione in ingresso su tale porta e non potrà essere usata per le connessioni in uscita. 

Per stabilire una connessione in uscita, è necessario usare una **porta temporanea** per fornire la destinazione con una porta per la comunicazione e la gestione di un flusso di traffico distinto. 

Ogni indirizzo IP dispone di 65.535 porte. Le prime 1024 porte sono riservate come **porte di sistema** . Ogni porta può essere utilizzata per le connessioni in ingresso o in uscita per TCP e UDP. 

Delle porte rimanenti, Azure fornisce 64.000 per l'uso come **porte** temporanee. Quando si aggiunge un indirizzo IP come configurazione IP front-end, è possibile usare queste porte temporanee per SNAT.

Tramite le regole in uscita, queste porte SNAT possono essere distribuite alle istanze back-end per consentire loro di condividere gli IP pubblici del servizio di bilanciamento del carico per le connessioni in uscita.

La rete nell'host per ogni istanza di back-end eseguirà il SNAT dei pacchetti che fanno parte di una connessione in uscita. L'host riscrive l'indirizzo IP di origine in uno degli indirizzi IP pubblici. L'host riscrive la porta di origine di ogni pacchetto in uscita in una delle porte SNAT.

## <a name="exhausting-ports"></a><a name="scenarios"></a> Porte esaurite

Ogni connessione allo stesso indirizzo IP di destinazione e alla stessa porta di destinazione userà una porta SNAT. Questa connessione mantiene un **flusso di traffico** distinto dall'istanza o dal **client** back-end a un **server** . Questo processo fornisce al server una porta distinta su cui indirizzare il traffico. Senza questo processo, il computer client non è a conoscenza del flusso di cui fa parte un pacchetto.

Si supponga di avere a che fare con più browser https://www.microsoft.com , ovvero:

* IP di destinazione = 23.53.254.142
* Porta di destinazione = 443
* Protocollo = TCP

Senza porte di destinazione diverse per il traffico di ritorno (la porta SNAT utilizzata per stabilire la connessione), il client non sarà in grado di separare il risultato di una query da un altro.

È possibile che le connessioni in uscita si rompano. Un'istanza back-end può essere allocata A porte insufficienti. Senza il **riutilizzo della connessione** abilitato, viene aumentato il rischio di **esaurimento delle porte** SNAT.

Le nuove connessioni in uscita a un IP di destinazione avranno esito negativo quando si verifica l'esaurimento delle porte. Quando una porta diventa disponibile, le connessioni vengono riuscite. Questo esaurimento si verifica quando le porte 64.000 da un indirizzo IP sono distribuite in modo sottile in molte istanze di back-end. Per istruzioni sulla mitigazione dell'esaurimento delle porte SNAT, vedere la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection).  

Per le connessioni TCP, il servizio di bilanciamento del carico userà una singola porta SNAT per ogni indirizzo IP e porta di destinazione. Questo multiuso consente più connessioni allo stesso indirizzo IP di destinazione con la stessa porta SNAT. Questa multiuso è limitata se la connessione non è a porte di destinazione diverse.

Per le connessioni UDP, il servizio di bilanciamento del carico usa un algoritmo **NAT cono con restrizioni di porta** , che usa una porta SNAT per ogni indirizzo IP di destinazione, indipendentemente dalla porta di destinazione. 

Una porta viene riutilizzata per un numero illimitato di connessioni. La porta viene riutilizzata solo se la porta o l'indirizzo IP di destinazione è diverso.

## <a name="port-allocation"></a><a name="preallocatedports"></a> Allocazione porta

A ogni indirizzo IP pubblico assegnato come IP front-end del servizio di bilanciamento del carico vengono concesse 64.000 porte SNAT per i membri del pool back-end. Non è possibile condividere le porte con i membri del pool back-end. Un intervallo di porte SNAT può essere usato solo da una singola istanza di back-end per garantire che i pacchetti restituiti vengano instradati correttamente. 

Si consiglia di usare una regola in uscita esplicita per configurare l'allocazione della porta SNAT. Questa regola massimizza il numero di porte SNAT disponibili per le connessioni in uscita per ogni istanza di back-end. 

Se si utilizza l'allocazione automatica dei SNAT in uscita tramite una regola di bilanciamento del carico, nella tabella di allocazione verrà definita l'allocazione delle porte.

La <a name="snatporttable"></a> tabella seguente mostra le preallocazioni delle porte SNAT per i livelli di dimensioni del pool back-end:

| Dimensioni del pool (istanze VM) | Porte SNAT preallocate per configurazione IP |
| --- | --- |
| 1-50 | 1\.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1.000 | 32 | 

>[!NOTE]
> Se si dispone di un pool back-end con una dimensione massima di 10, ogni istanza può avere 64000/10 = 6.400 porte se si definisce una regola esplicita in uscita. In base alla tabella precedente, ognuno avrà 1.024 solo se si sceglie l'allocazione automatica.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Regole in uscita e NAT della rete virtuale

Azure Load Balancer le regole in uscita e la rete virtuale NAT sono opzioni disponibili per l'uscita da una rete virtuale.

Per ulteriori informazioni sulle regole in uscita, vedere [regole in uscita](outbound-rules.md).

Per ulteriori informazioni sulla rete virtuale di Azure NAT, vedere informazioni su [rete virtuale di Azure NAT](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Vincoli

*   Le porte verranno rilasciate dopo 15 secondi se viene ricevuto o inviato un **RST TCP**
*   Le porte verranno rilasciate dopo 240 secondi se viene ricevuto o inviato un **FINACK**
*   Quando una connessione è inattiva e non viene inviato alcun nuovo pacchetto, le porte verranno rilasciate dopo 4 – 120 minuti.
  * Questa soglia può essere configurata tramite regole in uscita.
*   Ogni indirizzo IP fornisce porte 64.000 che è possibile usare per SNAT.
*   Ogni porta può essere utilizzata per le connessioni TCP e UDP a un indirizzo IP di destinazione
  * È necessario specificare una porta UDP SNAT se la porta di destinazione è univoca o meno. Per ogni connessione UDP a un indirizzo IP di destinazione, viene usata una porta SNAT UDP.
  * Una porta TCP SNAT può essere utilizzata per più connessioni allo stesso indirizzo IP di destinazione purché le porte di destinazione siano diverse.
*   L'esaurimento SNAT si verifica quando un'istanza back-end esaurisce le porte SNAT specificate. Un servizio di bilanciamento del carico può ancora avere porte SNAT inutilizzate. Se le porte SNAT utilizzate da un'istanza back-end superano le porte SNAT specificate, non sarà possibile stabilire nuove connessioni in uscita.

## <a name="next-steps"></a>Passaggi successivi

*   [Risolvere gli errori di connessione in uscita a causa dell'esaurimento SNAT](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)
*   [Esaminare le metriche di SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) e acquisire familiarità con il modo corretto per filtrare, suddividere e visualizzare le metriche.

