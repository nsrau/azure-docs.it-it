---
title: Azure Load Balancer regole in uscita
description: Questo articolo illustra come configurare le regole in uscita per controllare l'uscita del traffico Internet con Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 947ecaa2efbfb013f1f3e8203d1c4296b9ca329f
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422162"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Azure Load Balancer regole in uscita

Le regole in uscita consentono di definire in modo esplicito SNAT (Source Network Address Translation) per un Load Balancer standard pubblico. Questa configurazione consente di usare gli IP pubblici del servizio di bilanciamento del carico per fornire connettività Internet in uscita per le istanze back-end.

Questa configurazione consente di:

* Mascheramento IP
* Semplificazione degli elenchi di accesso consentito.
* Riduce il numero di risorse IP pubbliche per la distribuzione.

Con le regole in uscita si ha un controllo dichiarativo completo sulla connettività Internet in uscita. Le regole in uscita consentono di ridimensionare e ottimizzare questa capacità in base alle esigenze specifiche. 

Le regole in uscita verranno seguite solo se la VM back-end non dispone di un indirizzo IP pubblico a livello di istanza (ILPIP).

![Regole in uscita di Load Balancer](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Con le regole in uscita è possibile definire in modo esplicito il comportamento di **SNAT** in uscita.

Le regole in uscita consentono di controllare:

* **Quali macchine virtuali vengono convertite in indirizzi IP pubblici.**
     * Due regole erano il pool back-end A USA l'indirizzo IP A e B. il pool back-end B usa l'indirizzo IP C e D.
* **Come vengono allocate le porte SNAT in uscita.**
     * Il pool back-end B è l'unico pool che effettua connessioni in uscita, assegna tutte le porte SNAT al pool back-end B e nessuna al pool back-end a.
* **Protocolli per i quali fornire la traduzione in uscita.**
     * Il pool back-end B necessita di porte UDP per l'uscita. Il pool back-end A richiede TCP. Assegnare le porte TCP a e a porte UDP a B.
* **La durata da usare per il timeout di inattività della connessione in uscita (4-120 minuti).**
     * Se sono presenti connessioni con esecuzione prolungata con KeepAlive, riservare le porte inattive per le connessioni a esecuzione prolungata per un massimo di 120 minuti. Si supponga che le connessioni non aggiornate vengano abbandonate e rilasciate in 4 minuti per le connessioni recenti 
* **Indica se inviare una reimpostazione TCP al timeout di inattività.**
     * Quando si verifica il timeout delle connessioni inattive, si invia un RST TCP al client e al server in modo che il flusso venga abbandonato?

## <a name="outbound-rule-definition"></a>Definizione della regola in uscita

Le regole in uscita seguono la stessa sintassi familiare come bilanciamento del carico e regole NAT in ingresso **:**  +  **parameters**  +  **pool back-end** dei parametri front-end. 

Una regola in uscita configura NAT in uscita per _tutte le macchine virtuali identificate dal pool back-end_ da convertire in _front-end_.  

I _parametri_ forniscono un controllo granulare aggiuntivo per l'algoritmo NAT in uscita.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Scalabilità di NAT in uscita con più indirizzi IP

Ogni indirizzo IP aggiuntivo fornito da un front-end fornisce altre porte effimere 64.000 per il bilanciamento del carico da usare come porte SNAT. 

Usare più indirizzi IP per pianificare scenari su larga scala. Usare le regole in uscita per attenuare l' [esaurimento SNAT](troubleshoot-outbound-connection.md#snatexhaust). 

È anche possibile usare un [prefisso IP pubblico](https://aka.ms/lbpublicipprefix) direttamente con una regola in uscita. 

Un prefisso IP pubblico aumenta il ridimensionamento della distribuzione. Il prefisso può essere aggiunto all'elenco Consenti dei flussi originati dalle risorse di Azure. È possibile configurare una configurazione IP front-end all'interno del servizio di bilanciamento del carico per fare riferimento a un prefisso di indirizzo IP pubblico.  

Il servizio di bilanciamento del carico ha il controllo sul prefisso IP pubblico. La regola in uscita utilizzerà automaticamente tutti gli indirizzi IP pubblici contenuti nel prefisso IP pubblico per le connessioni in uscita. 

Ognuno degli indirizzi IP all'interno del prefisso IP pubblico fornisce altre porte temporanee 64.000 per ogni indirizzo IP per il servizio di bilanciamento del carico da usare come porte SNAT.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Timeout di inattività del flusso in uscita e ripristino TCP

Le regole in uscita includono un parametro di configurazione per controllare il timeout di inattività per i flussi in uscita in modo da adattarlo alle esigenze dell'applicazione. Il timeout di inattività in uscita è per impostazione predefinita di 4 minuti. Per altre informazioni, vedere [configurare i timeout di inattività](load-balancer-tcp-idle-timeout.md). 

Il comportamento predefinito del servizio di bilanciamento del carico prevede l'eliminazione invisibile del flusso quando è stato raggiunto il timeout di inattività in uscita. Il `enableTCPReset` parametro abilita un comportamento e un controllo dell'applicazione stimabile. Il parametro determina se inviare la reimpostazione TCP (TCP RST) bidirezionale al timeout del timeout di inattività in uscita. 

Esaminare [il timeout di inattività](https://aka.ms/lbtcpreset) per i dettagli, inclusa la disponibilità dell'area.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Sicurezza e controllo della connettività in uscita in modo esplicito

Le regole di bilanciamento del carico forniscono la programmazione automatica del NAT in uscita. Alcuni scenari traggono vantaggio o richiedono la disabilitazione della programmazione automatica del NAT in uscita dalla regola di bilanciamento del carico. La disabilitazione tramite la regola consente di controllare o affinare il comportamento.  

È possibile usare questo parametro in due modi:

1. Prevenzione dell'indirizzo IP in ingresso per SNAT in uscita. Disabilitare SNAT in uscita nella regola di bilanciamento del carico.
  
2. Ottimizzare i parametri di **SNAT** in uscita di un indirizzo IP usato per il traffico in ingresso e in uscita simultaneamente. Il NAT in uscita automatico deve essere disabilitato per consentire a una regola in uscita di assumere il controllo. Per modificare l'allocazione delle porte SNAT di un indirizzo utilizzato anche per il traffico in ingresso, il `disableOutboundSnat` parametro deve essere impostato su true. 

L'operazione di configurazione di una regola in uscita avrà esito negativo se si tenta di ridefinire un indirizzo IP utilizzato per il traffico in ingresso.  Disabilitare prima la NAT in uscita della regola di bilanciamento del carico.

>[!IMPORTANT]
> La macchina virtuale non avrà connettività in uscita se si imposta questo parametro su true e non si dispone di una regola in uscita per definire la connettività in uscita.  Alcune operazioni della macchina virtuale o dell'applicazione possono dipendere dalla disponibilità della connettività in uscita. Assicurarsi di valutare le dipendenze dello scenario e l'impatto di questa modifica.

A volte non è auspicabile che una macchina virtuale crei un flusso in uscita. Potrebbe essere necessario gestire le destinazioni che ricevono i flussi in uscita o le destinazioni che iniziano i flussi in ingresso. Usare i [gruppi di sicurezza di rete](../virtual-network/security-overview.md) per gestire le destinazioni raggiunte dalla macchina virtuale. Usare gruppi per gestire le destinazioni pubbliche che avviano i flussi in ingresso.

Quando si applica un gruppo di sicurezza di rete a una macchina virtuale con carico bilanciato, considerare i [tag del servizio](../virtual-network/security-overview.md#service-tags) e le [regole di sicurezza predefinite](../virtual-network/security-overview.md#default-security-rules). 

Assicurarsi che la macchina virtuale sia in grado di ricevere richieste di probe di integrità da Azure Load Balancer.

Se un NSG blocca le richieste di probe di integrità dal AZURE_LOADBALANCER tag predefinito, il probe di integrità della macchina virtuale ha esito negativo e la macchina virtuale è contrassegnata come Il servizio di bilanciamento del carico interrompe l'invio di nuovi flussi a tale macchina virtuale.

## <a name="scenarios-with-outbound-rules"></a>Scenari con regole in uscita
        

### <a name="outbound-rules-scenarios"></a>Scenari di regole in uscita


* Configurare le connessioni in uscita a un set specifico di indirizzi IP pubblici o prefisso.
* Modificare l'allocazione della porta [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) .
* Abilita solo in uscita.
* NAT in uscita solo per le macchine virtuali (nessun in ingresso).
* NAT in uscita per il servizio di bilanciamento del carico standard interno.
* Abilitare entrambi i protocolli TCP & UDP per la NAT in uscita con un servizio di bilanciamento del carico standard pubblico.


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Scenario 1: configurare le connessioni in uscita a un set specifico di indirizzi IP pubblici o prefisso


#### <a name="details"></a>Dettagli


Usare questo scenario per personalizzare le connessioni in uscita da un set di indirizzi IP pubblici. Aggiungere indirizzi IP pubblici o prefissi a un elenco Consenti o nega in base all'origine.


Il prefisso o l'indirizzo IP pubblico può essere identico a quello usato da una regola di bilanciamento del carico. 


Per usare un indirizzo IP pubblico o un prefisso diverso da quello usato da una regola di bilanciamento del carico: 


1. Creare un prefisso IP pubblico o un indirizzo IP pubblico.
2. Creare un servizio di bilanciamento del carico standard pubblico 
3. Creare un front-end che faccia riferimento al prefisso IP pubblico o all'indirizzo IP pubblico che si vuole usare. 
4. Riutilizzare un pool back-end o creare un pool back-end e inserire le VM in un pool back-end del servizio di bilanciamento del carico pubblico
5. Configurare una regola in uscita per il servizio di bilanciamento del carico pubblico per abilitare la NAT in uscita per le macchine virtuali usando il front-end. Non è consigliabile usare una regola di bilanciamento del carico per le connessioni in uscita, disabilitare SNAT in uscita nella regola di bilanciamento del carico.


### <a name="scenario-2-modify-snat-port-allocation"></a><a name="scenario2out"></a>Scenario 2: modifica dell'allocazione delle porte [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources)


#### <a name="details"></a>Dettagli


È possibile usare le regole in uscita per ottimizzare l'[allocazione automatica delle porte SNAT in base alle dimensioni del pool back-end](load-balancer-outbound-connections.md#preallocatedports). 


Se si verifica l'esaurimento SNAT, aumentare il numero di porte [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) specificate dal valore predefinito 1024. 


Ogni indirizzo IP pubblico contribuisce fino a 64.000 porte effimere. Il numero di macchine virtuali nel pool back-end determina il numero di porte distribuite a ogni macchina virtuale. Una macchina virtuale nel pool back-end ha accesso al numero massimo di 64.000 porte. Per due macchine virtuali, è possibile assegnare un massimo di 32.000 porte [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) con una regola in uscita (2x 32.000 = 64.000). 


È possibile utilizzare le regole in uscita per ottimizzare le porte SNAT specificate per impostazione predefinita. È possibile assegnare più o meno rispetto all'allocazione di porta [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) predefinita. Ogni indirizzo IP pubblico da un front-end di una regola in uscita contribuisce fino a 64.000 porte effimere da usare come porte [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) . 


Il servizio di bilanciamento del carico fornisce porte [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) in multipli di 8. Se si specifica un valore non divisibile per 8, l'operazione di configurazione viene rifiutata. Ogni regola di bilanciamento del carico e regola NAT in ingresso utilizzerà un intervallo di 8 porte. Se una regola NAT di bilanciamento del carico o in ingresso condivide lo stesso intervallo di 8 come un altro, non verranno utilizzate porte aggiuntive.


Se si tenta di assegnare più porte [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) rispetto a quelle disponibili in base al numero di indirizzi IP pubblici, l'operazione di configurazione viene rifiutata. Ad esempio, se si assegnano 10.000 porte per macchina virtuale e sette macchine virtuali in un pool back-end condividono un solo indirizzo IP pubblico, la configurazione viene rifiutata. 7 moltiplicato per 10.000 supera il limite di 64.000 porte. Aggiungere altri indirizzi IP pubblici al front-end della regola in uscita per abilitare lo scenario. 


Ripristinare l' [allocazione di porte predefinita](load-balancer-outbound-connections.md#preallocatedports) specificando 0 per il numero di porte. La prima istanza di VM 50 otterrà 1024 porte, 51-100 istanze di VM otterranno 512 fino al numero massimo di istanze. Per ulteriori informazioni sull'allocazione di porte SNAT predefinite, vedere [tabella di allocazione delle porte SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>Scenario 3: abilitare solo in uscita


#### <a name="details"></a>Dettagli


Usare un servizio di bilanciamento del carico standard pubblico per fornire NAT in uscita per un gruppo di macchine virtuali. In questo scenario, utilizzare una regola in uscita autonomamente, senza regole aggiuntive configurate.


> [!NOTE]
> La **rete virtuale di Azure NAT** può fornire connettività in uscita per le macchine virtuali senza la necessità di un servizio di bilanciamento del carico. Per ulteriori informazioni, vedere informazioni sulla [rete virtuale di Azure NAT](../virtual-network/nat-overview.md) .

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>Scenario 4: NAT in uscita solo per le macchine virtuali (nessun ingresso)


> [!NOTE]
> La **rete virtuale di Azure NAT** può fornire connettività in uscita per le macchine virtuali senza la necessità di un servizio di bilanciamento del carico. Per ulteriori informazioni, vedere informazioni sulla [rete virtuale di Azure NAT](../virtual-network/nat-overview.md) .

#### <a name="details"></a>Dettagli


Per questo scenario: Azure Load Balancer regole in uscita e NAT della rete virtuale sono opzioni disponibili per l'uscita da una rete virtuale.


1. Creare un indirizzo IP pubblico o un prefisso.
2. Creare un servizio di bilanciamento del carico standard pubblico. 
3. Creare un front-end associato all'indirizzo IP pubblico o al prefisso dedicato per l'uscita.
4. Creare un pool back-end per le macchine virtuali.
5. Inserire le macchine virtuali nel pool back-end.
6. Configurare una regola in uscita per abilitare la NAT in uscita.



Usare un prefisso o un indirizzo IP pubblico per scalare le porte [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) . Aggiungere l'origine delle connessioni in uscita a un elenco Consenti o nega.



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>Scenario 5: NAT in uscita per Load Balancer standard interno


> [!NOTE]
> La **rete virtuale di Azure NAT** può fornire connettività in uscita per le macchine virtuali che usano un servizio di bilanciamento del carico standard interno. Per ulteriori informazioni, vedere informazioni sulla [rete virtuale di Azure NAT](../virtual-network/nat-overview.md) .

#### <a name="details"></a>Dettagli


La connettività in uscita non è disponibile per un servizio di bilanciamento del carico standard interno fino a quando non viene dichiarata in modo esplicito tramite indirizzi IP pubblici a livello di istanza o NAT della rete virtuale oppure associando i membri del pool back-end a una configurazione del servizio di bilanciamento del carico solo in uscita. 


Per altre informazioni, vedere Configurazione del servizio di [bilanciamento del carico solo in uscita](https://docs.microsoft.com/azure/load-balancer/egress-only).




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Scenario 6: abilitare protocolli UDP TCP & per la NAT in uscita con un servizio di bilanciamento del carico standard pubblico


#### <a name="details"></a>Dettagli


Quando si usa un servizio di bilanciamento del carico standard pubblico, il valore NAT in uscita automatico corrisponde al protocollo di trasporto della regola di bilanciamento del carico. 


1. Disabilitare [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) in uscita nella regola di bilanciamento del carico. 
2. Configurare una regola in uscita nello stesso servizio di bilanciamento del carico.
3. Riutilizzare il pool back-end già usato dalle macchine virtuali. 
4. Specificare "protocol": "All" come parte della regola in uscita. 


Quando si usano solo regole NAT in ingresso, non viene fornita alcuna conversione NAT in uscita. 


1. Posizionare le macchine virtuali in un pool back-end.
2. Definire una o più configurazioni IP front-end con indirizzi IP pubblici o con prefisso IP pubblico 
3. Configurare una regola in uscita nello stesso servizio di bilanciamento del carico. 
4. Specificare "protocol": "All" come parte della regola in uscita


## <a name="limitations"></a>Limitazioni

- Il numero massimo di porte temporanee utilizzabili per ogni indirizzo IP front-end è 64.000.
- L'intervallo del timeout di inattività in uscita configurabile è compreso tra 4 a 120 minuti (da 240 a 7200 secondi).
- Il servizio di bilanciamento del carico non supporta ICMP per la NAT in uscita.
- Le regole in uscita possono essere applicate solo alla configurazione IP primaria di una scheda di interfaccia di rete.  Non è possibile creare una regola in uscita per l'IP secondario di una VM o di un appliance virtuale di dispositivo. Sono supportate più schede di rete.
- Tutte le macchine virtuali all'interno di un **set di disponibilità** devono essere aggiunte al pool back-end per la connettività in uscita. 
- Tutte le macchine virtuali all'interno di un **set di scalabilità di macchine virtuali** devono essere aggiunte al pool back-end per la connettività in uscita.

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più su [Azure Load Balancer standard](load-balancer-overview.md)
- Vedere le [domande frequenti su Azure Load Balancer](load-balancer-faqs.md)

