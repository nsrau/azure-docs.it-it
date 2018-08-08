---
title: Usare i probe di integrità di Load Balancer per proteggere un servizio | Microsoft Docs
description: Informazioni su come usare i probe di integrità per monitorare le istanze di Load Balancer
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2018
ms.author: kumud
ms.openlocfilehash: 7366273e30132daf7dc5ea15072c574180d1bc8b
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397286"
---
# <a name="load-balancer-health-probes"></a>Probe di integrità di Load Balancer

Azure Load Balancer usa i probe di integrità per determinare quale istanza del pool back-end deve ricevere nuovi flussi. È possibile utilizzare i probe di integrità per rilevare l'errore di un'applicazione in un'istanza di back-end. È anche possibile generare una risposta personalizzata a un probe di integrità e usare il probe di integrità per il controllo del flusso e per segnalare a Load Balancer se continuare a inviare nuovi flussi o arrestare l'invio di nuovi flussi a un'istanza di back-end. Ciò consente di gestire carichi o tempi di inattività pianificati.

Se il probe di integrità non riesce, Load Balancer interrompe l'invio di nuovi flussi alla rispettiva istanza non integra. Il comportamento dei flussi nuovi ed esistenti dipende dal fatto che si tratti di flussi TCP o UDP, nonché dagli SKU di Load Balancer in uso.  Per informazioni dettagliate, vedere [Comportamento in caso di inattività dei probe](#probedown).

> [!IMPORTANT]
> I probe di integrità di Load Balancer sono originati dall'indirizzo IP 168.63.129.16 e non devono essere bloccati perché possano contrassegnare l'istanza come attiva.  Per informazioni dettagliate, vedere [Indirizzo IP di origine dei probe](#probesource).

## <a name="health-probe-types"></a>Tipi di probe di integrità

I probe di integrità possono osservare qualsiasi porta in un'istanza di back-end, tra cui la porta su cui viene effettivamente fornito il servizio. Il probe di integrità supportano i listener TCP o gli endpoint HTTP. 

Per il bilanciamento del carico UDP, è necessario generare un segnale di probe di integrità personalizzato per l'istanza di back-end tramite un probe di integrità TCP o HTTP.

Quando si usano [regole di bilanciamento del carico per porte a disponibilità elevata](load-balancer-ha-ports-overview.md) con [Load Balancer Standard](load-balancer-standard-overview.md), viene eseguito il bilanciamento del carico di tutte le porte e una singola risposta del probe di integrità deve riflettere lo stato dell'intera istanza.  

È consigliabile non inviare mediante NAT o proxy un probe di integrità tramite l'istanza che riceve il probe di integrità a un'altra istanza nella rete virtuale, perché questo può causare errori a catena nello scenario.

Se si vuole testare un errore del probe di integrità o contrassegnare una singola istanza come inattiva, è possibile usare un gruppo di sicurezza per bloccare esplicitamente il probe di integrità (destinazione o [origine](#probesource)).

### <a name="tcp-probe"></a>Probe TCP

I probe TCP avviano una connessione tramite l'esecuzione di un handshake TCP aperto a tre vie con la porta definita,  seguito da un handshake TCP chiuso a quattro vie.

L'intervallo minimo del probe è di 5 secondi e il numero minimo di risposte non integre è 2.  La durata totale non può superare i 120 secondi.

Un probe TCP ha esito negativo quando:
* Il listener TCP sull'istanza non invia alcuna risposta durante il periodo di timeout.  Un probe viene contrassegnato in base alla configurazione del numero di richieste di probe non riuscite che possono rimanere senza risposta prima che il probe sia contrassegnato come inattivo.
* Il probe riceve un TCP Reset dall'istanza.

### <a name="http-probe"></a>Probe HTTP

I probe HTTP stabiliscono una connessione TCP ed emettono una richiesta HTTP GET con il percorso specificato. I probe HTTP supportano i percorsi relativi per HTTP GET. Il probe di integrità viene contrassegnato come inattivo quando l'istanza risponde con uno stato HTTP 200 entro il periodo di timeout.  Per impostazione predefinita, i probe di integrità HTTP tentano di controllare la porta del probe di integrità configurata ogni 15 secondi. L'intervallo minimo del probe è di 5 secondi. La durata totale non può superare i 120 secondi. 


I probe HTTP possono anche essere utili per implementare una logica personalizzata per rimuovere le istanze dalla rotazione del servizio di bilanciamento del carico. Ad esempio, si potrebbe scegliere di rimuovere un'istanza se indica oltre il 90% di uso della CPU e restituisce uno stato HTTP diverso da 200. 

Se si usano servizi cloud e ruoli Web che usano w3wp.exe, si ottiene anche il monitoraggio automatico del sito Web. Gli errori nel codice del sito Web restituiscono uno stato diverso da 200 al probe del servizio di bilanciamento del carico.  I probe HTTP eseguono l'override del probe dell'agente guest predefinito. 

Un probe HTTP ha esito negativo quando:
* L'endpoint del probe HTTP restituisce un codice di risposta HTTP diverso da 200, ad esempio 403, 404 o 500. In questo caso, il probe di integrità verrà contrassegnato immediatamente come inattivo. 
* L'endpoint del probe HTTP non invia alcuna risposta durante il periodo di timeout di 31 secondi. A seconda del valore di timeout impostato, più richieste di probe potrebbero non ricevere risposta prima che il probe venga contrassegnato come non in esecuzione, ovvero prima dell'invio di probe SuccessFailCount.
* L'endpoint del probe HTTP chiude la connessione tramite l'invio di TCP Reset.

### <a name="guest-agent-probe-classic-only"></a>Probe dell'agente guest (solo servizi classici)

Per impostazione predefinita, i ruoli del servizio cloud, ovvero i ruoli di lavoro e i ruoli Web, usano un agente guest per il monitoraggio probe.   Questa opzione deve essere presa in considerazione solo come ultima risorsa.  È sempre necessario definire un probe di integrità in modo esplicito con un probe TCP o HTTP. Un probe dell'agente guest non è altrettanto efficace dei probe definiti in modo esplicito per la maggior parte degli scenari di applicazione.  

Un probe dell'agente guest è un controllo dell'agente guest all'interno della macchina virtuale. L'agente guest è quindi in ascolto e risponde con HTTP 200 OK solo quando l'istanza è in stato Pronto. Gli altri stati sono Occupato, Riciclo in corso o Arresto.

Per altre informazioni, vedere [Configure the service definition file (csdef) for health probes](https://msdn.microsoft.com/library/azure/ee758710.aspx) (Configurare il file csdef per probe di integrità) o [Get started by creating a public load balancer for cloud services](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services) (Introduzione alla creazione di un servizio di bilanciamento del carico pubblico per i servizi cloud).

Se l'agente guest non risponde con un messaggio HTTP 200 OK, il servizio di bilanciamento del carico contrassegna l'istanza come istanza che non risponde. Il servizio smette quindi di inviare flussi all'istanza. Il servizio di bilanciamento del carico continua a controllare l'istanza. 

Se l'agente guest risponde con un messaggio HTTP 200, il servizio di bilanciamento del carico continua a inviare i nuovi flussi a tale istanza.

Quando si usa un ruolo Web, il codice del sito Web viene in genere eseguito in w3wp.exe, che non è monitorato dall'infrastruttura di Azure o dall'agente guest. Gli errori in w3wp.exe, ad esempio le risposte HTTP 500, non vengono segnalati all'agente guest. Di conseguenza, il servizio di bilanciamento del carico non rimuove l'istanza dalla rotazione.

## <a name="probe-health"></a>Integrità dei probe

I probe di integrità TCP e HTTP sono considerati integri e contrassegnano come integra l'istanza del ruolo quando:

* Il probe di integrità ha esito positivo al primo avvio della macchina virtuale.
* Il numero di SuccessFailCount, descritto in precedenza, definisce il valore dei probe riusciti necessari per contrassegnare l'istanza del ruolo come integra. Se un'istanza del ruolo è stata rimossa, il numero di probe successivi riusciti deve essere uguale o maggiore del valore di SuccessFailCount per contrassegnare l'istanza del ruolo come in esecuzione.

> [!NOTE]
> Se l'integrità di un'istanza del ruolo varia, il servizio di bilanciamento del carico attende più a lungo prima di ripristinarne lo stato di integrità. Questo tempo di attesa aggiuntivo protegge l'utente e l'infrastruttura ed è un criterio voluto.

## <a name="probe-count-and-timeout"></a>Conteggio e timeout dei probe

Il comportamento dei probe dipende dagli elementi seguenti:

* Numero di probe riusciti che consentono di contrassegnare un'istanza come attiva.
* Numero di probe non riusciti che fanno sì che un'istanza sia contrassegnata come inattiva.

I valori di timeout e frequenza impostati in SuccessFailCount determinano se un'istanza è confermata come in esecuzione o non in esecuzione. Nel portale di Azure il timeout è impostato sul doppio del valore della frequenza.

Una regola di bilanciamento del carico ha un singolo probe di integrità definito dal pool back-end corrispondente.

## <a name="probedown"></a>Comportamento in caso di inattività dei probe

### <a name="tcp-connections"></a>Connessioni TCP

Le nuove connessioni TCP avranno esito positivo per l'istanza di back-end se questa è integra e dispone di un sistema operativo guest e di un'applicazione in grado di accettare un nuovo flusso.

Se il probe di integrità dell'istanza di back-end ha esito negativo, le connessioni TCP stabilite a questa istanza di back-end continuano.

Se tutti i probe per tutte le istanze in un pool back-end hanno esito negativo, non verranno inviati nuovi flussi al pool back-end. Load Balancer Standard consente la continuazione dei flussi TCP stabiliti.  Load Balancer Basic terminerà tutti i flussi TCP esistenti verso il pool back-end.
 
Poiché il flusso è sempre tra il client e il sistema operativo guest della macchina virtuale, un pool con tutti i probe inattivi impedirà a un front-end di rispondere ai tentativi di apertura di una connessione TCP, perché non sarà presente un'istanza di back-end integra per la ricezione del flusso.

### <a name="udp-datagrams"></a>Datagrammi UDP

I datagrammi UDP verranno recapitati alle istanze di back-end integre.

UDP è senza connessione e non esiste uno stato di flusso monitorato per l'UDP. Se un probe di integrità dell'istanza di back-end ha esito negativo, i flussi UDP esistenti possono passare a un'altra istanza integra nel pool di back-end.

Se tutti i probe per un pool di back-end hanno esito negativo, i flussi UDP esistenti verranno interrotti per Load Balancer Standard e Basic.


## <a name="probesource"></a>Indirizzo IP di origine dei probe

Tutti i probe di integrità di Load Balancer sono originati dall'indirizzo IP 168.63.129.16.  Quando si usano i propri indirizzi IP nella rete virtuale di Azure, questo indirizzo IP di origine dei probe di integrità è sicuramente univoco, perché è riservato a livello globale per Microsoft.  Questo indirizzo è lo stesso in tutte le aree e non cambia. Non deve essere considerato come un rischio per la sicurezza, perché solo la piattaforma Azure interna può generare un pacchetto da questo indirizzo IP. 

Per consentire al probe di integrità di Load Balancer di contrassegnare l'istanza come attiva, è **necessario** autorizzare questo indirizzo IP in tutti i criteri firewall locali e i [gruppi di sicurezza](../virtual-network/security-overview.md) di Azure.

Se non si consente questo indirizzo IP nei criteri firewall, il probe di integrità avrà esito negativo, perché non sarà in grado di raggiungere l'istanza.  A sua volta, Load Balancer contrassegnerà l'istanza come inattiva a causa dell'errore del probe di integrità.  Ciò può causare l'arresto del servizio con bilanciamento del carico. 

È anche necessario evitare di configurare la rete virtuale con l'intervallo di indirizzi IP di proprietà di Microsoft, che contiene 168.63.129.16.  Questo intervallo è in conflitto con l'indirizzo IP del probe di integrità.

Se si dispone di più interfacce nella macchina virtuale, è necessario assicurarsi di rispondere al probe nell'interfaccia su cui è stato ricevuto.  Ciò può richiedere un processo SNAT (Source NAT) univoco per questo indirizzo nella macchina virtuale in base a ogni singola interfaccia.

## <a name="monitoring"></a>Monitoraggio

[Load Balancer Standard](load-balancer-standard-overview.md) espone lo stato dei probe di integrità come una metrica multidimensionale per ogni istanza tramite Monitoraggio di Azure.

Load Balancer Basic espone lo stato dei probe di integrità per ogni pool back-end tramite Log Analytics.  Questa funzionalità è disponibile solo per i sistemi Load Balancer Basic pubblici (non per quelli interni).  È possibile usare [Log Analytics](load-balancer-monitor-log.md) per verificare lo stato di integrità e il conteggio dei probe del servizio di bilanciamento del carico pubblico. La registrazione può essere usata con Power BI o Azure Operational Insights per fornire statistiche sullo stato di integrità del servizio di bilanciamento del carico.


## <a name="limitations"></a>Limitazioni

-  I probe di integrità HTTP non supportano TLS (HTTPS).  Usare invece un probe TCP sulla porta 443.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione alla creazione di un servizio di bilanciamento del carico pubblico in Resource Manager con PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [API REST per i probe di integrità](https://docs.microsoft.com/en-us/rest/api/load-balancer/loadbalancerprobes/get)

