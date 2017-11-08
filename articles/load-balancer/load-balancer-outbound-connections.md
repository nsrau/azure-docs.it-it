---
title: Informazioni sulle connessioni in uscita in Azure | Microsoft Docs
description: In questo articolo viene spiegato come Azure consente alle macchine virtuali di comunicare con i servizi Internet pubblici.
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 93e6c87a9d445ca448509a256247fb5e4749ec1c
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2017
---
# <a name="understanding-outbound-connections-in-azure"></a>Informazioni sulle connessioni in uscita in Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Una macchina virtuale (VM) in Azure può comunicare con endpoint all'esterno di Azure nello spazio di indirizzi IP pubblici. Quando una VM avvia un flusso in uscita verso una destinazione nello spazio di indirizzi IP pubblici, Azure esegue il mapping dell'indirizzo IP privato della macchina virtuale a un indirizzo IP pubblico e consente il traffico di ritorno per raggiungere la macchina virtuale.

Azure offre tre metodi diversi per stabilire connettività in uscita. Ogni metodo ha funzionalità e vincoli specifici. Esaminare ogni metodo attentamente e scegliere quello che soddisfa le proprie esigenze.

| Scenario | Metodo | Nota |
| --- | --- | --- |
| Macchina virtuale autonoma (nessun bilanciamento del carico, nessun indirizzo IP pubblico a livello di istanza) |SNAT predefinito |Azure associa un indirizzo IP pubblico per SNAT |
| Macchina virtuale con carico bilanciato (nessun indirizzo IP pubblico a livello di istanza nella VM) |SNAT con il bilanciamento del carico |Azure usa un indirizzo IP pubblico di Load Balancer per SNAT |
| Macchina virtuale con indirizzo IP pubblico a livello di istanza (con o senza bilanciamento del carico) |SNAT non usato |Azure usa l'IP pubblico assegnato alla macchina virtuale |

Se non si vuole che una macchina virtuale comunichi con gli endpoint all'esterno di Azure nello spazio di indirizzi IP pubblici, è possibile usare i gruppi di sicurezza di rete per bloccare l'accesso. Per informazioni più dettagliate sull'uso dei gruppi di sicurezza di rete, vedere [Prevenzione della connettività pubblica](#preventing-public-connectivity).

## <a name="standalone-vm-with-no-instance-level-public-ip-address"></a>Macchina virtuale autonoma senza indirizzo IP pubblico a livello di istanza

In questo scenario la macchina virtuale non fa parte di un pool di Azure Load Balancer e non ha assegnato un indirizzo IP pubblico a livello di istanza. Quando la macchina virtuale crea un flusso in uscita, Azure converte l'indirizzo IP di origine privata del flusso in uscita in un indirizzo IP di origine pubblica. L'indirizzo IP pubblico usato per questo flusso in uscita non è configurabile e non interferisce con il limite della risorsa IP pubblico della sottoscrizione. Azure usa SNAT (Source Network Address Translation) per eseguire questa funzione. Per distinguere i singoli flussi originati dalla macchina virtuale vengono usate porte temporanee dell'indirizzo IP pubblico. SNAT assegna dinamicamente le porte temporanee dopo che sono stati creati i flussi. In questo contesto le porte temporanee usate per SNAT sono dette porte SNAT.

Le porte SNAT sono una risorsa limitata che può esaurirsi. L'importante è capire come vengono usate. Viene usata una porta SNAT per flusso verso un singolo indirizzo IP di destinazione. In caso di più flussi verso lo stesso indirizzo IP di destinazione, viene usata una singola porta SNAT per ogni flusso. Si garantisce così che i flussi siano univoci quando hanno origine dallo stesso indirizzo IP pubblico e quando sono destinati allo stesso indirizzo IP. Più flussi destinati ognuno a un indirizzo IP di destinazione diverso condividono una singola porta SNAT. L'indirizzo IP di destinazione crea i flussi univoci.

Vedere [Log Analytics per Load Balancer](load-balancer-monitor-log.md) e [Log eventi di avviso](load-balancer-monitor-log.md#alert-event-log) per monitorare l'integrità delle connessioni in uscita. Quando si esauriscono le risorse di porte SNAT, i flussi in uscita vengono completati dopo che le porte SNAT sono state rilasciate da flussi esistenti. Il timeout di inattività di Load Balancer è di 4 minuti per il recupero di porte SNAT.  Consultare la sezione seguente [Macchina virtuale con indirizzo IP pubblico a livello di istanza (con o senza Load Balancer)](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer) e [Gestione dell'esaurimento SNAT](#snatexhaust).

## <a name="load-balanced-vm-with-no-instance-level-public-ip-address"></a>Macchina virtuale con carico bilanciato senza indirizzo IP pubblico a livello di istanza

In questo scenario la macchina virtuale fa parte di un pool di Azure Load Balancer.  Alla macchina virtuale non è assegnato un indirizzo IP pubblico. La risorsa di bilanciamento del carico deve essere configurata con una regola per collegare il front-end dell'IP pubblico al pool back-end.  Se questa configurazione non viene completata, il comportamento sarà quello descritto nella sezione precedente [Macchina virtuale autonoma senza indirizzo IP pubblico a livello di istanza](load-balancer-outbound-connections.md#standalone-vm-with-no-instance-level-public-ip-address).

Quando la macchina virtuale con carico bilanciato crea un flusso in uscita, Azure converte l'indirizzo IP di origine privata del flusso in uscita nell'indirizzo IP pubblico del front-end pubblico di Load Balancer. Azure usa SNAT (Source Network Address Translation) per eseguire questa funzione. Per distinguere i singoli flussi provenienti dalla macchina virtuale vengono usate le porte temporanee dell'indirizzo IP pubblico di Load Balancer. SNAT assegna dinamicamente le porte temporanee dopo che sono stati creati i flussi in uscita. In questo contesto le porte temporanee usate per SNAT sono dette porte SNAT.

Le porte SNAT sono una risorsa limitata che può esaurirsi. L'importante è capire come vengono usate. Viene usata una porta SNAT per flusso verso un singolo indirizzo IP di destinazione. In caso di più flussi verso lo stesso indirizzo IP di destinazione, viene usata una singola porta SNAT per ogni flusso. Si garantisce così che i flussi siano univoci quando hanno origine dallo stesso indirizzo IP pubblico e quando sono destinati allo stesso indirizzo IP. Più flussi destinati ognuno a un indirizzo IP di destinazione diverso condividono una singola porta SNAT. L'indirizzo IP di destinazione crea i flussi univoci.

Vedere [Log Analytics per Load Balancer](load-balancer-monitor-log.md) e [Log eventi di avviso](load-balancer-monitor-log.md#alert-event-log) per monitorare l'integrità delle connessioni in uscita. Quando si esauriscono le risorse di porte SNAT, i flussi in uscita vengono completati dopo che le porte SNAT sono state rilasciate da flussi esistenti. Il timeout di inattività di Load Balancer è di 4 minuti per il recupero di porte SNAT.  Consultare la sezione seguente e [Gestione dell'esaurimento SNAT](#snatexhaust).

## <a name="vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer"></a>Macchina virtuale con indirizzo IP pubblico a livello di istanza (con o senza bilanciamento del carico)

In questo scenario la macchina virtuale ha assegnato un IP pubblico a livello di istanza. Non è importante se la macchina virtuale abbia un carico bilanciato o meno. Quando si usa un IP pubblico a livello di istanza, non si applica SNAT. La macchina virtuale usa un indirizzo IP pubblico a livello di istanza per tutti i flussi in uscita. Se l'applicazione avvia numerosi flussi in uscita e si assiste a un esaurimento SNAT, è necessario considerare l'assegnazione di un indirizzo IP pubblico a livello di istanza per ridurre i vincoli SNAT.

## <a name="discovering-the-public-ip-used-by-a-given-vm"></a>Individuazione dell'indirizzo IP pubblico usato da una macchina virtuale specifica

Esistono molti modi per determinare l'indirizzo IP di origine pubblica di una connessione in uscita. OpenDNS offre un servizio che consente di visualizzare l'indirizzo IP pubblico della macchina virtuale. Con il comando nslookup è possibile inviare una query DNS per il nome myip.opendns.com al resolver OpenDNS. Il servizio restituisce l'indirizzo IP di origine usato per inviare la query. Quando si esegue la query seguente dalla macchina virtuale, la risposta è l'indirizzo IP pubblico usato per tale VM.

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-public-connectivity"></a>Prevenzione della connettività pubblica

A volte può succedere di non voler consentire a una macchina virtuale di creare un flusso in uscita o può essere necessario gestire le destinazioni che possono essere raggiunte da flussi in uscita oppure le destinazioni possono iniziare flussi in entrata. In questo caso usare i [gruppi di sicurezza di rete (NSG)](../virtual-network/virtual-networks-nsg.md) per gestire le destinazioni che la macchina virtuale può raggiungere e la destinazione pubblica che può avviare i flussi in entrata. Quando si applica un gruppo di sicurezza di rete a una macchina virtuale con carico bilanciato, è necessario considerare i [tag predefiniti](../virtual-network/virtual-networks-nsg.md#default-tags) e le [regole predefinite](../virtual-network/virtual-networks-nsg.md#default-rules).

È necessario assicurarsi che la macchina virtuale riceva richieste di probe di integrità da Azure Load Balancer. Se un gruppo di sicurezza di rete blocca le richieste di probe di integrità dal tag AZURE_LOADBALANCER predefinito, il probe di integrità della macchina virtuale avrà esito negativo e la macchina virtuale sarà contrassegnata come non attiva. Load Balancer interrompe l'invio di nuovi flussi a tale macchina virtuale.

## <a name="snatexhaust"></a>Gestione dell'esaurimento SNAT

Le porte temporanee usate per SNAT sono una risorsa esauribile, come descritto in [Macchina virtuale autonoma senza indirizzo IP pubblico a livello di istanza](#standalone-vm-with-no-instance-level-public-ip-address) e [Macchina virtuale con carico bilanciato senza indirizzo IP pubblico a livello di istanza](#standalone-vm-with-no-instance-level-public-ip-address).  

Sono disponibili diverse opzioni di mitigazione dei rischi, se si è consapevoli che verranno avviate numerose connessioni in uscita alla stessa destinazione, se si riscontrano connessioni in uscita con errore o se il supporto tecnico ha comunicato il possibile esaurimento di porte SNAT.  Esaminare queste opzioni e scegliere quella ottimale per lo scenario in uso.  È possibile che una o più opzioni risultino utili per gestire questo scenario.

### <a name="assign-an-instance-level-public-ip-to-each-vm"></a>Assegnare un indirizzo IP pubblico a livello di istanza a ogni macchina virtuale
In questo modo lo scenario cambia in [Indirizzo IP pubblico a livello di istanza a una macchina virtuale](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer).  Tutte le porte temporanee dell'indirizzo IP pubblico usato per ogni macchina virtuale sono disponibili per la macchina virtuale, a differenza degli scenari in cui le porte temporanee di un indirizzo IP pubblico vengono condivise con tutte le macchine virtuali associate al pool back-end corrispondente.

### <a name="modify-application-to-use-connection-pooling"></a>Modificare l'applicazione in modo da usare il pool di connessioni
È possibile ridurre la richiesta di porte temporanee usate per SNAT usando il pool di connessioni nell'applicazione.  I flussi aggiuntivi alla stessa destinazione useranno le porte aggiuntive.  Se si riusa lo stesso flusso per più richieste, le richieste useranno una singola porta.

### <a name="modify-application-to-use-less-aggressive-retry-logic"></a>Modificare l'applicazione in modo da usare una logica di ripetizione meno aggressiva
È possibile ridurre la richiesta di porte temporanee usando una logica di ripetizione meno aggressiva.  Quando vengono esaurite le porte temporanee per SNAT, i tentativi di ripetizione aggressivi o di forza bruta senza logica di backoff e decadenza causano la persistenza dell'esaurimento.  Le porte temporanee hanno un timeout di inattività 4 minuti (non modificabile) e se i tentativi di ripetizione sono troppo aggressivi, l'esaurimento delle porte non viene eliminato autonomamente.

## <a name="limitations"></a>Limitazioni

Se [a un servizio di bilanciamento del carico sono associati più indirizzi IP (pubblici)](load-balancer-multivip-overview.md), uno qualsiasi di questi IP indirizzi pubblici è idoneo per i flussi in uscita.

Azure usa un algoritmo per determinare il numero di porte SNAT disponibili in base alla dimensione del pool.  Al momento questo parametro non è configurabile.

Il timeout di inattività delle connessioni in uscita è di 4 minuti.  Questo valore non è modificabile.

È importante ricordare che il numero di porte SNAT disponibili non vengono traslate direttamente sul numero di connessioni. Vedere le informazioni precedenti per dettagli su come e quando vengono allocate le porte SNAT e su come gestire questa risorsa esauribile.
