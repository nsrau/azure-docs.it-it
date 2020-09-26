---
title: Connessioni in uscita
titleSuffix: Azure Load Balancer
description: In questo articolo viene spiegato come Azure consente alle macchine virtuali di comunicare con i servizi Internet pubblici.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: contperfq1
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2020
ms.author: allensu
ms.openlocfilehash: 79399d0890f61d723f371528408d226f6a192ce4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336497"
---
# <a name="outbound-connections"></a>Connessioni in uscita

Azure Load Balancer fornisce la connettività in uscita tramite diversi meccanismi. Questo articolo descrive gli scenari e le modalità di gestione. 

## <a name="outbound-connections-scenario-overview"></a><a name="scenarios"></a>Panoramica dello scenario relativo alle connessioni in uscita

Termini usati in questi scenari. Per ulteriori informazioni, vedere [terminologia](#terms):

* [SNAT (Source Network Address Translation)](#snat)
* [Mascheramento delle porte (PAT)](#pat)
* Transmission Control Protocol (TCP)
* UDP (User Datagram Protocol)
* Network Address Translation
* Internet Control Message Protocol
* Incapsulamento del protocollo di sicurezza

### <a name="scenarios"></a>Scenari

* [Scenario 1](#scenario1) -macchina virtuale con indirizzo IP pubblico.
* [Scenario 2](#scenario2) : macchina virtuale senza indirizzo IP pubblico.
* [Scenario 3](#scenario3) : macchina virtuale senza IP pubblico e senza Load Balancer standard.

### <a name="scenario-1---virtual-machine-with-public-ip"></a><a name="scenario1"></a>Scenario 1-macchina virtuale con IP pubblico

| Associazioni | Metodo | Protocolli IP |
| ---------- | ------ | ------------ |
| Servizio di bilanciamento del carico pubblico o autonomo | [SNAT](#snat) </br> [Mascheramento della porta](#pat) non utilizzata. | TCP </br> UDP </br> ICMP </br> ESP |

#### <a name="description"></a>Descrizione

Azure usa l'indirizzo IP pubblico assegnato alla configurazione IP della scheda di interfaccia di rete dell'istanza per tutti i flussi in uscita. L'istanza ha tutte le porte temporanee disponibili. Non importa se la macchina virtuale è con carico bilanciato o meno. Questo scenario ha la precedenza rispetto agli altri. 

Un indirizzo IP pubblico assegnato a una macchina virtuale è una relazione 1:1, anziché 1:molti, e viene implementato come NAT 1:1 senza stato.

### <a name="scenario-2---virtual-machine-without-public-ip"></a><a name="scenario2"></a>Scenario 2: macchina virtuale senza IP pubblico

| Associazioni | Metodo | Protocolli IP |
| ------------ | ------ | ------------ |
| Bilanciamento del carico pubblico | Uso del front-end del servizio di bilanciamento del carico per [SNAT](#snat) con [mascheramento delle porte (Pat)](#pat).| TCP </br> UDP |

#### <a name="description"></a>Descrizione

La risorsa del servizio di bilanciamento del carico è configurata con una regola di bilanciamento del carico. Questa regola viene usata per creare un collegamento tra il front-end IP pubblico e il pool back-end. 

Se non si completa questa configurazione della regola, il comportamento è come descritto nello scenario 3. 

Una regola con un listener non è necessaria affinché il probe di integrità abbia esito positivo.

Quando una macchina virtuale crea un flusso in uscita, Azure converte l'indirizzo IP di origine nell'indirizzo IP pubblico del front-end pubblico del servizio di bilanciamento del carico. Questa traduzione viene eseguita tramite [SNAT](#snat). 

Le porte temporanee dell'indirizzo IP pubblico front-end del servizio di bilanciamento del carico vengono usate per distinguere i singoli flussi originati dalla macchina virtuale. SNAT usa dinamicamente le [porte temporanee preallocate](#preallocatedports) quando vengono creati i flussi in uscita. 

In questo contesto le porte temporanee usate per SNAT sono dette porte SNAT. Le porte SNAT vengono preallocate come descritto nella [tabella di allocazione delle porte SNAT predefinite](#snatporttable).

### <a name="scenario-3---virtual-machine-without-public-ip-and-without-standard-load-balancer"></a><a name="scenario3"></a> Scenario 3: macchina virtuale senza IP pubblico e senza Load Balancer standard

| Associazioni | Metodo | Protocolli IP |
| ------------ | ------ | ------------ |
|nessuno </br> Servizio di bilanciamento del carico di base | [SNAT](#snat) con [mascheramento delle porte (Pat)](#pat)| TCP </br> UDP | 

#### <a name="description"></a>Descrizione

Quando la macchina virtuale crea un flusso in uscita, Azure converte l'indirizzo IP di origine del flusso in uscita in un indirizzo IP di origine pubblica. Questo indirizzo IP pubblico **non è configurabile** e non può essere riservato. Questo indirizzo non viene conteggiato rispetto al limite di risorse IP pubblico della sottoscrizione. 

L'indirizzo IP pubblico verrà rilasciato e verrà richiesto un nuovo indirizzo IP pubblico se si ridistribuisce: 

* Macchina virtuale
* Set di disponibilità
* Set di scalabilità di macchine virtuali  

Non usare questo scenario per aggiungere indirizzi IP a un elenco Consenti. Usare lo scenario 1 o 2 in cui si dichiara in modo esplicito il comportamento in uscita. Le porte [SNAT](#snat) vengono preallocate come descritto nella [tabella di allocazione delle porte SNAT predefinite](#snatporttable).



## <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>Algoritmo di allocazione porte

Azure usa un algoritmo per determinare il numero di porte [SNAT](#snat) preallocate disponibili. L'algoritmo basa il numero di porte sulle dimensioni del pool back-end. 

Per ogni indirizzo IP pubblico associato a un servizio di bilanciamento del carico, sono disponibili 64.000 porte come porte [SNAT](#snat) . Lo stesso numero di porte [SNAT](#snat) è preallocato per UDP e TCP. Le porte vengono utilizzate in modo indipendente per ogni protocollo IP. 

L'utilizzo della porta [SNAT](#snat) è diverso a seconda che il flusso sia UDP o TCP. 

Le porte vengono utilizzate in modo dinamico fino al limite preallocato.  Le porte vengono rilasciate quando il flusso viene chiuso o si verifica un timeout di inattività.

Per ulteriori informazioni sui timeout di inattività, vedere [risolvere i problemi relativi alle connessioni in uscita in Azure Load Balancer](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) 

Le porte vengono usate solo se è necessario per rendere univoci i flussi.

### <a name="dynamic-snat-ports-preallocation"></a><a name="snatporttable"></a> Preallocazione delle porte SNAT dinamiche

La tabella seguente mostra le preallocazioni delle porte [SNAT](#snat) per i livelli di dimensioni del pool back-end:

| Dimensioni del pool (istanze VM) | Porte SNAT preallocate per configurazione IP |
| --- | --- |
| 1-50 | 1\.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1.000 | 32 |

La modifica delle dimensioni del pool back-end potrebbe influire su alcuni dei flussi stabiliti:

- Le dimensioni del pool back-end aumentano le transizioni del trigger nel livello successivo. Metà delle porte [SNAT](#snat) preallocate vengono recuperate durante la transizione al livello successivo. 

- Flussi associati a un timeout della porta [SNAT](#snat) recuperato e alla chiusura. Questi flussi devono essere ristabiliti. Se viene tentato un nuovo flusso, il flusso avrà esito positivo immediatamente, purché siano disponibili porte preallocate.

- Se le dimensioni del pool back-end diminuiscono e si esegue la transizione a un livello inferiore, il numero di porte [SNAT](#snat) disponibili aumenta. Le porte [SNAT](#snat) specificate esistenti e i rispettivi flussi non sono interessati.

## <a name="outbound-rules"></a><a name="outboundrules"></a>Regole in uscita

 Le regole in uscita consentono la configurazione del Network Address Translation in uscita del servizio di [bilanciamento del carico standard](load-balancer-standard-overview.md)pubblico.  

> [!NOTE]
> La **rete virtuale di Azure NAT** può fornire connettività in uscita per le macchine virtuali in una rete virtuale.  Per ulteriori informazioni, vedere informazioni sulla [rete virtuale di Azure NAT](../virtual-network/nat-overview.md) .

Si dispone di un controllo dichiarativo completo sulla connettività in uscita per scalare e ottimizzare questa capacità in base alle esigenze. Questa sezione espande lo scenario 2 come descritto in precedenza.

![Regole in uscita del servizio di bilanciamento del carico](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Con le regole in uscita, è possibile usare il servizio di bilanciamento del carico per definire la NAT in uscita da zero. È anche possibile ridimensionare e ottimizzare il comportamento del NAT in uscita esistente.

Le regole in uscita consentono di controllare:

- Quali macchine virtuali devono essere convertite in quali indirizzi IP pubblici.
- Come assegnare le porte [SNAT](#snat) in uscita.
- Protocolli per i quali fornire la traduzione in uscita.
- La durata da usare per il timeout di inattività della connessione in uscita (4-120 minuti).
- Indica se inviare una reimpostazione TCP al timeout di inattività
- Protocolli di trasporto sia TCP che UDP con una sola regola

### <a name="outbound-rule-definition"></a>Definizione della regola in uscita

Le regole in uscita seguono la stessa sintassi familiare come bilanciamento del carico e regole NAT in ingresso **:**  +  **parameters**  +  **pool back-end**dei parametri front-end. Una regola in uscita configura NAT in uscita per _tutte le macchine virtuali identificate dal pool back-end_ da convertire in _front-end_.  I _parametri_ forniscono un controllo granulare aggiuntivo per l'algoritmo NAT in uscita.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Scalabilità di NAT in uscita con più indirizzi IP

Ogni indirizzo IP aggiuntivo fornito da un front-end fornisce altre porte effimere 64.000 per il bilanciamento del carico da usare come porte SNAT. 

Usare più indirizzi IP per pianificare scenari su larga scala. Usare le regole in uscita per attenuare l' [esaurimento SNAT](troubleshoot-outbound-connection.md#snatexhaust). 

È anche possibile usare un [prefisso IP pubblico](https://aka.ms/lbpublicipprefix) direttamente con una regola in uscita. 

Un prefisso IP pubblico aumenta il ridimensionamento della distribuzione. Il prefisso può essere aggiunto all'elenco Consenti dei flussi originati dalle risorse di Azure. È possibile configurare una configurazione IP front-end all'interno del servizio di bilanciamento del carico per fare riferimento a un prefisso di indirizzo IP pubblico.  

Il servizio di bilanciamento del carico ha il controllo sul prefisso IP pubblico. La regola in uscita utilizzerà automaticamente tutti gli indirizzi IP pubblici contenuti nel prefisso IP pubblico per le connessioni in uscita. 

Ognuno degli indirizzi IP all'interno del prefisso IP pubblico fornisce altre porte temporanee 64.000 per ogni indirizzo IP per il servizio di bilanciamento del carico da usare come porte SNAT.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Timeout di inattività del flusso in uscita e ripristino TCP

Le regole in uscita includono un parametro di configurazione per controllare il timeout di inattività per i flussi in uscita in modo da adattarlo alle esigenze dell'applicazione. Il timeout di inattività in uscita è per impostazione predefinita di 4 minuti. Per altre informazioni, vedere [configurare i timeout di inattività](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout). 

Il comportamento predefinito del servizio di bilanciamento del carico prevede l'eliminazione invisibile del flusso quando è stato raggiunto il timeout di inattività in uscita. Il `enableTCPReset` parametro abilita un comportamento e un controllo dell'applicazione stimabile. Il parametro determina se inviare la reimpostazione TCP (TCP RST) bidirezionale al timeout del timeout di inattività in uscita. 

Esaminare [il timeout di inattività](https://aka.ms/lbtcpreset) per i dettagli, inclusa la disponibilità dell'area.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Impedire la connettività in uscita

Le regole di bilanciamento del carico forniscono la programmazione automatica del NAT in uscita. Alcuni scenari traggono vantaggio o richiedono la disabilitazione della programmazione automatica del NAT in uscita dalla regola di bilanciamento del carico. La disabilitazione tramite la regola consente di controllare o affinare il comportamento.  

È possibile usare questo parametro in due modi:

1. Prevenzione dell'indirizzo IP in ingresso per SNAT in uscita. Disabilitare SNAT in uscita nella regola di bilanciamento del carico.
  
2. Ottimizzare i parametri di [SNAT](#snat) in uscita di un indirizzo IP usato per il traffico in ingresso e in uscita simultaneamente. Il NAT in uscita automatico deve essere disabilitato per consentire a una regola in uscita di assumere il controllo. Per modificare l'allocazione delle porte SNAT di un indirizzo utilizzato anche per il traffico in ingresso, il `disableOutboundSnat` parametro deve essere impostato su true. 

L'operazione di configurazione di una regola in uscita avrà esito negativo se si tenta di ridefinire un indirizzo IP utilizzato per il traffico in ingresso.  Disabilitare prima la NAT in uscita della regola di bilanciamento del carico.

>[!IMPORTANT]
> La macchina virtuale non avrà connettività in uscita se si imposta questo parametro su true e non si dispone di una regola in uscita per definire la connettività in uscita.  Alcune operazioni della macchina virtuale o dell'applicazione possono dipendere dalla disponibilità della connettività in uscita. Assicurarsi di valutare le dipendenze dello scenario e l'impatto di questa modifica.

A volte non è auspicabile che una macchina virtuale crei un flusso in uscita. Potrebbe essere necessario gestire le destinazioni che ricevono i flussi in uscita o le destinazioni che iniziano i flussi in ingresso. Usare i [gruppi di sicurezza di rete](../virtual-network/security-overview.md) per gestire le destinazioni raggiunte dalla macchina virtuale. Usare gruppi per gestire le destinazioni pubbliche che avviano i flussi in ingresso.

Quando si applica un gruppo di sicurezza di rete a una macchina virtuale con carico bilanciato, considerare i [tag del servizio](../virtual-network/security-overview.md#service-tags) e le [regole di sicurezza predefinite](../virtual-network/security-overview.md#default-security-rules). Assicurarsi che la macchina virtuale sia in grado di ricevere richieste di probe di integrità da Azure Load Balancer.

Se un gruppo di sicurezza di rete blocca le richieste di probe di integrità dal tag AZURE_LOADBALANCER predefinito, il probe di integrità della macchina virtuale avrà esito negativo e la macchina virtuale sarà contrassegnata come non attiva. Load Balancer interrompe l'invio di nuovi flussi a tale macchina virtuale.

## <a name="scenarios-with-outbound-rules"></a>Scenari con regole in uscita

### <a name="outbound-rules-scenarios"></a>Scenari di regole in uscita

* [Scenario 1](#scenario1out) : configurare le connessioni in uscita a un set specifico di indirizzi IP pubblici o prefisso.
* [Scenario 2](#scenario2out) : modificare l'allocazione della porta [SNAT](#snat) .
* [Scenario 3](#scenario3out) : abilitare solo in uscita.
* [Scenario 4](#scenario4out) : NAT in uscita solo per le macchine virtuali (nessun in ingresso).
* [Scenario 5](#scenario5out) : NAT in uscita per Load Balancer standard interno.
* [Scenario 6](#scenario6out) : abilitare protocolli UDP TCP & per la NAT in uscita con un servizio di bilanciamento del carico standard pubblico.

### <a name="scenario-1"></a><a name="scenario1out"></a>Scenario 1

| Scenario |
| -------- |
| Configurare le connessioni in uscita a un set specifico di indirizzi IP pubblici o prefisso|

#### <a name="details"></a>Dettagli

Usare questo scenario per personalizzare le connessioni in uscita da un set di indirizzi IP pubblici. Aggiungere indirizzi IP pubblici o prefissi a un elenco Consenti o nega in base all'origine.

Il prefisso o l'indirizzo IP pubblico può essere identico a quello usato da una regola di bilanciamento del carico. 

Per usare un indirizzo IP pubblico o un prefisso diverso da quello usato da una regola di bilanciamento del carico:  

1. Creare un prefisso IP pubblico o un indirizzo IP pubblico.
2. Creare un servizio di bilanciamento del carico standard pubblico 
3. Creare un front-end che faccia riferimento al prefisso IP pubblico o all'indirizzo IP pubblico che si vuole usare. 
4. Riutilizzare un pool back-end o creare un pool back-end e inserire le VM in un pool back-end del servizio di bilanciamento del carico pubblico
5. Configurare una regola in uscita per il servizio di bilanciamento del carico pubblico per abilitare la NAT in uscita per le macchine virtuali usando il front-end. Se non si desidera che la regola di bilanciamento del carico venga utilizzata per le connessioni in uscita, disabilitare SNAT in uscita nella regola di bilanciamento del carico.

### <a name="scenario-2"></a><a name="scenario2out"></a>Scenario 2

| Scenario |
| -------- |
| Modificare l'allocazione delle porte [SNAT](#snat) |

#### <a name="details"></a>Dettagli

È possibile usare le regole in uscita per ottimizzare l'[allocazione automatica delle porte SNAT in base alle dimensioni del pool back-end](load-balancer-outbound-connections.md#preallocatedports). 

Se si verifica l'esaurimento SNAT, aumentare il numero di porte [SNAT](#snat) specificate dal valore predefinito 1024. 

Ogni indirizzo IP pubblico contribuisce fino a 64.000 porte effimere. Il numero di macchine virtuali nel pool back-end determina il numero di porte distribuite a ogni macchina virtuale. Una macchina virtuale nel pool back-end ha accesso al numero massimo di 64.000 porte. Per due macchine virtuali, è possibile assegnare un massimo di 32.000 porte [SNAT](#snat) con una regola in uscita (2x 32.000 = 64.000). 

È possibile utilizzare le regole in uscita per ottimizzare le porte SNAT specificate per impostazione predefinita. È possibile assegnare più o meno rispetto all'allocazione di porta [SNAT](#snat) predefinita. Ogni indirizzo IP pubblico da un front-end di una regola in uscita contribuisce fino a 64.000 porte effimere da usare come porte [SNAT](#snat) .  

Il servizio di bilanciamento del carico fornisce porte [SNAT](#snat) in multipli di 8. Se si specifica un valore non divisibile per 8, l'operazione di configurazione viene rifiutata. 

Se si tenta di assegnare più porte [SNAT](#snat) rispetto a quelle disponibili in base al numero di indirizzi IP pubblici, l'operazione di configurazione viene rifiutata.

Se si assegnano 10.000 porte per macchina virtuale e sette macchine virtuali in un pool back-end condividono un solo indirizzo IP pubblico, la configurazione viene rifiutata. 7 moltiplicato per 10.000 supera il limite di 64.000 porte. Aggiungere altri indirizzi IP pubblici al front-end della regola in uscita per abilitare lo scenario. 

Ripristinare l' [allocazione di porte predefinita](load-balancer-outbound-connections.md#preallocatedports) specificando 0 per il numero di porte. La prima istanza di VM 50 otterrà 1024 porte, 51-100 istanze di VM otterranno 512 fino al numero massimo di istanze.  Per ulteriori informazioni sull'allocazione di porte SNAT predefinite, vedere [sopra](#snatporttable).

### <a name="scenario-3"></a><a name="scenario3out"></a>Scenario 3

| Scenario |
| -------- |
|  Abilitare NAT solo in uscita |

#### <a name="details"></a>Dettagli

È possibile usare un servizio di bilanciamento del carico standard pubblico per fornire NAT in uscita per un gruppo di macchine virtuali. In questo scenario, utilizzare una regola in uscita autonomamente, senza la necessità di regole aggiuntive.

> [!NOTE]
> La **rete virtuale di Azure NAT** può fornire connettività in uscita per le macchine virtuali senza la necessità di un servizio di bilanciamento del carico.  Per ulteriori informazioni, vedere informazioni sulla [rete virtuale di Azure NAT](../virtual-network/nat-overview.md) .

### <a name="scenario-4"></a><a name="scenario4out"></a>Scenario 4

| Scenario |
| -------- |
| Solo NAT in uscita per le macchine virtuali (non in ingresso) |

> [!NOTE]
> La **rete virtuale di Azure NAT** può fornire connettività in uscita per le macchine virtuali senza la necessità di un servizio di bilanciamento del carico.  Per ulteriori informazioni, vedere informazioni sulla [rete virtuale di Azure NAT](../virtual-network/nat-overview.md) .

#### <a name="details"></a>Dettagli

Per questo scenario:

1. Creare un indirizzo IP pubblico o un prefisso.
2. Creare un servizio di bilanciamento del carico standard pubblico. 
3. Creare un front-end associato all'indirizzo IP pubblico o al prefisso dedicato per l'uscita.
4. Creare un pool back-end per le macchine virtuali.
5. Inserire le macchine virtuali nel pool back-end.
6. Configurare una regola in uscita per abilitare la NAT in uscita.

Usare un prefisso o un indirizzo IP pubblico per scalare le porte [SNAT](#snat) . Aggiungere l'origine delle connessioni in uscita a un elenco Consenti o nega.

### <a name="scenario-5"></a><a name="scenario5out"></a>Scenario 5

| Scenario |
| -------- |
| NAT in uscita per Load Balancer standard interno |

> [!NOTE]
> La **rete virtuale di Azure NAT** può fornire connettività in uscita per le macchine virtuali che usano un servizio di bilanciamento del carico standard interno.  Per ulteriori informazioni, vedere informazioni sulla [rete virtuale di Azure NAT](../virtual-network/nat-overview.md) .

#### <a name="details"></a>Dettagli

La connettività in uscita non è disponibile per un servizio di bilanciamento del carico standard interno fino a quando non viene dichiarata in modo esplicito. 

Per altre informazioni, vedere Configurazione del servizio di [bilanciamento del carico solo in uscita](https://docs.microsoft.com/azure/load-balancer/egress-only).


### <a name="scenario-6"></a><a name="scenario6out"></a>Scenario 6

| Scenario |
| -------- |
| Abilitare protocolli UDP TCP & per la NAT in uscita con un servizio di bilanciamento del carico standard pubblico |

#### <a name="details"></a>Dettagli

Quando si usa un servizio di bilanciamento del carico standard pubblico, il valore NAT in uscita automatico corrisponde al protocollo di trasporto della regola di bilanciamento del carico. 

1. Disabilitare [SNAT](#snat) in uscita nella regola di bilanciamento del carico. 
2. Configurare una regola in uscita nello stesso servizio di bilanciamento del carico.
3. Riutilizzare il pool back-end già usato dalle macchine virtuali. 
4. Specificare "protocol": "All" come parte della regola in uscita. 

Quando si usano solo regole NAT in ingresso, non viene fornita alcuna conversione NAT in uscita. 

1. Posizionare le macchine virtuali in un pool back-end.
2. Definire una o più configurazioni IP front-end con indirizzi IP pubblici o con prefisso IP pubblico 
3. Configurare una regola in uscita nello stesso servizio di bilanciamento del carico. 
4. Specificare "protocol": "All" come parte della regola in uscita

## <a name="terminology"></a><a name="terms"></a> Terminologia

### <a name="source-network-address-translation"></a><a name="snat"></a>Network Address Translation di origine

| Protocolli applicabili |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>Dettagli

Una distribuzione in Azure può comunicare con endpoint all'esterno di Azure nello spazio indirizzi IP pubblici.

Quando un'istanza avvia il traffico in uscita verso una destinazione con un indirizzo IP pubblico, Azure esegue in modo dinamico il mapping dell'indirizzo IP privato della risorsa a un indirizzo IP pubblico. 

Dopo aver creato questo mapping, il traffico di ritorno per questo flusso con origine in uscita raggiunge l'indirizzo IP privato da cui ha avuto origine il flusso. 

Azure usa **Network Address Translation di origine (SNAT)** per eseguire questa funzione.

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Mascheramento delle porte SNAT (PAT)

| Protocolli applicabili |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>Dettagli

Quando gli IP privati sono protetti da un singolo indirizzo IP pubblico, Azure usa la **traduzione degli indirizzi di porta (Pat)** per nascondere gli indirizzi IP privati. 

Per PAT vengono usate porte temporanee [preallocate](#preallocatedports) in base alle dimensioni del pool. 

Quando un servizio di bilanciamento del carico pubblico è associato a macchine virtuali senza indirizzi IP pubblici, ogni origine della connessione in uscita viene riscritta. 

L'origine viene riscritta dall'indirizzo IP privato della rete virtuale all'indirizzo IP pubblico front-end del servizio di bilanciamento del carico. 

Nello spazio degli indirizzi IP pubblici, le cinque tuple del flusso di seguito devono essere univoche:

* Indirizzo IP di origine
* Porta di origine
* Protocollo di trasporto IP
* Indirizzo IP di destinazione
* Porta di destinazione 

Il mascheramento delle porte SNAT può essere usato con protocolli TCP o UDP. Le porte SNAT vengono usate dopo la riscrittura dell'indirizzo IP di origine privato, perché più flussi hanno origine da un singolo indirizzo IP pubblico. L'algoritmo SNAT di mascheramento della porta fornisce le porte SNAT in modo diverso per UDP rispetto a TCP.

### <a name="snat-ports-tcp"></a>Porte SNAT (TCP)

| Protocolli applicabili |
|------------------------|
| TCP |

#### <a name="details"></a>Dettagli

Le porte SNAT sono porte temporanee disponibili per un indirizzo di origine IP pubblico. Viene usata una porta SNAT per il flusso verso un singolo indirizzo IP e una porta di destinazione. 

Nel caso di più flussi TCP verso la stessa combinazione di indirizzo IP, porta e protocollo di destinazione, ogni flusso TCP utilizza una singola porta SNAT. 

Questo consumo garantisce che i flussi siano univoci quando hanno origine dallo stesso indirizzo IP pubblico e passano al:

* Stesso indirizzo IP di destinazione
* Porta
* Protocollo 

Più flussi condividono una sola porta SNAT. 

L'indirizzo IP, la porta e il protocollo di destinazione rendono univoci i flussi senza la necessità di porte di origine aggiuntive per distinguere i flussi nello spazio indirizzi IP pubblici.


### <a name="snat-ports-udp"></a>Porte SNAT (UDP)

| Protocolli applicabili |
|------------------------|
| UDP |

#### <a name="details"></a>Dettagli

Le porte SNAT UDP vengono gestite da un algoritmo diverso rispetto alle porte SNAT TCP.  Il servizio di bilanciamento del carico usa un algoritmo denominato NAT con restrizioni di porta per UDP.

Viene usata una porta SNAT per qualsiasi indirizzo IP di destinazione e porta per ogni flusso.


### <a name="exhaustion"></a><a name="exhaustion"></a>Esaurimento

| Protocolli applicabili |
|------------------------|
| N/D |

#### <a name="details"></a>Dettagli

Quando si esauriscono le risorse di porte SNAT, i flussi in uscita vengono completati dopo che i flussi esistenti rilasciano le porte SNAT. Il servizio di bilanciamento del carico recupera le porte SNAT quando il flusso viene chiuso.

Un [timeout di inattività](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) di quattro minuti viene usato dal servizio di bilanciamento del carico per recuperare le porte SNAT.

Le porte SNAT UDP in genere esauriscono molto più velocemente delle porte TCP SNAT a causa della differenza nell'algoritmo usato. Progettazione e scalabilità del test a causa di questa differenza.

### <a name="snat-port-release-behavior-tcp"></a>Comportamento della versione della porta SNAT (TCP)

| Protocolli applicabili |
|------------------------|
| TCP |

#### <a name="details"></a>Dettagli

Quando un server o un client invia un FINACK, una porta SNAT viene rilasciata dopo 240 secondi. Nel caso in cui venga individuata una RST, una porta SNAT verrà rilasciata dopo 15 secondi. Se è stato raggiunto il timeout di inattività, la porta viene rilasciata.

### <a name="snat-port-release-behavior-udp"></a>Comportamento della versione della porta SNAT (UDP)

| Protocolli applicabili |
|------------------------|
| TCP |

#### <a name="details"></a>Dettagli

Se è stato raggiunto il timeout di inattività, la porta viene rilasciata.

### <a name="snat-port-reuse"></a>Riutilizzo delle porte SNAT

| Protocolli applicabili |
|------------------------|
| TCP </br> UDP |

#### <a name="details"></a>Dettagli

Una volta rilasciata una porta, la porta sarà disponibile per il riutilizzo. Le porte SNAT sono una sequenza dal più basso al più alto disponibile per uno scenario specifico e la prima porta SNAT disponibile viene usata per le nuove connessioni.

## <a name="limitations"></a>Limitazioni

- Il numero massimo di porte temporanee utilizzabili per ogni indirizzo IP front-end è 64.000.
- L'intervallo del timeout di inattività in uscita configurabile è compreso tra 4 a 120 minuti (da 240 a 7200 secondi).
- Il servizio di bilanciamento del carico non supporta ICMP per la NAT in uscita.
- Le regole in uscita possono essere applicate solo alla configurazione IP primaria di una scheda di interfaccia di rete.  Non è possibile creare una regola in uscita per l'IP secondario di una VM o di un appliance virtuale di dispositivo. Sono supportate più schede di rete.
- I ruoli Web Worker senza una rete virtuale e altri servizi della piattaforma Microsoft possono essere accessibili quando si usa un servizio di bilanciamento del carico standard interno. Questa accessibilità è a causa di un effetto collaterale del funzionamento dei servizi VNet e di altri servizi di piattaforma. Non fare affidamento su questo effetto collaterale perché il rispettivo servizio stesso o la piattaforma sottostante possono cambiare senza preavviso. Si supponga sempre di dover creare in modo esplicito la connettività in uscita se si vuole usare un servizio di bilanciamento del carico standard interno. Lo scenario 3 descritto in questo articolo non è disponibile.

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi con la connettività in uscita tramite una Azure Load Balancer, vedere la [Guida alla risoluzione dei problemi per le connessioni in uscita](../load-balancer/troubleshoot-outbound-connection.md).

- Altre informazioni su [Load Balancer standard](load-balancer-standard-overview.md).
- Vedere le [domande frequenti su Azure Load Balancer](load-balancer-faqs.md).
- Altre informazioni sulle [regole in uscita](load-balancer-outbound-rules-overview.md) per il servizio di bilanciamento del carico pubblico standard.

