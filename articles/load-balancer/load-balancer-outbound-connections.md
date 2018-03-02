---
title: Informazioni sulle connessioni in uscita in Azure | Microsoft Docs
description: In questo articolo viene spiegato come Azure consente alle macchine virtuali di comunicare con i servizi Internet pubblici.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/05/2018
ms.author: kumud
ms.openlocfilehash: e1a2b4c281194ec4c70e4d9fe1bc97c5aa61436e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="understanding-outbound-connections-in-azure"></a>Informazioni sulle connessioni in uscita in Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]


Azure offre connettività in uscita per le distribuzioni dei clienti tramite vari meccanismi diversi.  Questo articolo descrive che cosa sono gli scenari, quando si applicano, come funzionano e come gestirli.

Una distribuzione in Azure può comunicare con endpoint all'esterno di Azure nello spazio indirizzi IP pubblici. Quando un'istanza avvia un flusso in uscita verso una destinazione nello spazio indirizzi IP pubblici, Azure esegue in modo dinamico il mapping dell'indirizzo IP privato a un indirizzo IP pubblico.  Dopo aver creato questo mapping, il traffico di ritorno per questo flusso con origine in uscita può raggiungere l'indirizzo IP privato da cui ha avuto origine il flusso.

Azure usa SNAT (Source Network Address Translation) per eseguire questa funzione.  Quando più indirizzi IP privati si mascherano dietro un singolo indirizzo IP pubblico, Azure usa [PAT (Port Address Translation)](#pat) per il mascheramento degli indirizzi IP privati.  Per PAT vengono usate porte temporanee [preallocate](#preallocatedports) in base alle dimensioni del pool.

Sono presenti più [scenari in uscita](#scenarios). Questi scenari possono essere combinati in base alle esigenze. Esaminarli con attenzione per comprenderne le funzionalità, i vincoli e gli schemi, in riferimento a modelli di distribuzione e scenari di applicazione specifici.  Vedere le linee guida per la [gestione di questi scenari](#snatexhaust).

## <a name="scenarios"></a>Panoramica dello scenario

Azure offre due modelli di distribuzione principali, ovvero Azure Resource Manager e la distribuzione classica. Quando si usano [risorse di Azure Resource Manager](#arm) vengono definiti in modo esplicito il servizio Load Balancer e le risorse correlate.  Le distribuzioni classiche si basano su un'astrazione del concetto di bilanciamento del carico e offrono una funzione simile tramite la definizione di endpoint di un [servizio cloud](#classic). Gli [scenari](#scenarios) applicabili per la distribuzione dipendono dal modello di distribuzione usato.

### <a name="arm"></a>Azure Resource Manager

Attualmente, Azure offre tre diversi metodi per ottenere la connettività in uscita per le risorse di Azure Resource Manager.  Le distribuzioni [classiche](#classic) includono un subset di questi scenari.

| Scenario | Metodo | DESCRIZIONE |
| --- | --- | --- |
| [1. Macchina virtuale con indirizzo IP pubblico a livello di istanza (con o senza bilanciamento del carico)](#ilpip) | SNAT, il mascheramento delle porte non viene usato |Azure usa l'indirizzo IP pubblico assegnato alla configurazione IP della scheda di interfaccia di rete dell'istanza.  L'istanza ha tutte le porte temporanee disponibili. |
| [2. Servizio Load Balancer pubblico associato alla macchina virtuale (nessun indirizzo IP pubblico a livello di istanza per l'istanza)](#lb) | SNAT con mascheramento delle porte (PAT) tramite i front-end di Load Balancer |Azure condivide l'indirizzo IP pubblico dei front-end di Load Balancer pubblici con più IP privati e usa le porte temporanee dei front-end per PAT. |
| [3. Macchina virtuale autonoma (senza servizio di bilanciamento del carico, nessun indirizzo IP pubblico a livello di istanza)](#defaultsnat) | SNAT con mascheramento delle porte (PAT) | Azure designa automaticamente un indirizzo IP pubblico per SNAT, condivide questo indirizzo IP pubblico con più indirizzi IP privati del set di disponibilità e usa le porte temporanee di questo indirizzo IP pubblico.  Questo scenario è uno scenario di fallback per gli scenari precedenti 1 e 2 e non è consigliabile se occorrono visibilità e controllo. |

Se non si vuole che una macchina virtuale comunichi con gli endpoint all'esterno di Azure nello spazio indirizzi IP pubblici, è possibile usare i gruppi di sicurezza di rete per bloccare l'accesso in base alle specifiche esigenze.  Per informazioni più dettagliate sull'uso dei gruppi di sicurezza di rete, vedere [Impedire la connettività in uscita](#preventoutbound).  Esula dagli scopi di questo articolo una descrizione dettagliata dell'implementazione e delle linee guida per la progettazione e gestione di una macchina virtuale senza accesso in uscita.

### <a name="classic"></a>Distribuzione classica (servizi cloud)

Gli scenari disponibili per le distribuzioni classiche sono un subset degli scenari disponibili per le distribuzioni di [Azure Resource Manager](#arm) e del servizio Load Balancer Basic.

Per una macchina virtuale classica esistono gli stessi tre scenari fondamentali descritti per le risorse di Azure Resource Manager ([1](#ilpip), [2](#lb), [3](#defaultsnat)). Un ruolo Web worker classico ha solo due scenari ([2](#lb), [3](#defaultsnat)).  Anche per le [strategie di mitigazione](#snatexhaust) esistono le stesse differenze.

L'algoritmo usato per la [preallocazione di porte temporanee](#ephemeralprots) per PAT per le distribuzioni classiche è lo stesso usato per le distribuzioni di risorse di Azure Resource Manager.  

### <a name="ilpip"></a>Scenario 1: macchina virtuale con un indirizzo IP pubblico a livello di istanza

In questo scenario la macchina virtuale ha assegnato un IP pubblico a livello di istanza. Per quanto riguarda le connessioni in uscita, non è importante se la macchina virtuale abbia un carico bilanciato o meno.  Questo scenario ha la precedenza rispetto agli altri. Quando si usa un indirizzo IP pubblico a livello di istanza, la macchina virtuale usa tale indirizzo IP per tutti i flussi in uscita.  

Il mascheramento delle porte (PAT) non viene usato e tutte le porte temporanee sono disponibili per l'uso per la macchina virtuale.

Se l'applicazione avvia numerosi flussi in uscita e si assiste a un esaurimento delle porte SNAT, è possibile prendere in considerazione l'assegnazione di un [indirizzo IP pubblico a livello di istanza per ridurre i vincoli SNAT](#assignilpip). Leggere per intero [Gestione dell'esaurimento SNAT](#snatexhaust).

### <a name="lb"></a>Scenario 2: macchina virtuale con carico bilanciato senza indirizzo IP pubblico a livello di istanza

In questo scenario la macchina virtuale fa parte di un pool di back-end di Load Balancer pubblico.  Alla macchina virtuale non è assegnato un indirizzo IP pubblico. La risorsa di bilanciamento del carico deve essere configurata con una regola di bilanciamento del carico per creare un collegamento fra il front-end dell'IP pubblico e il pool back-end. Se questa configurazione delle regole non viene completata, il comportamento sarà quello descritto nello scenario [Macchina virtuale autonoma senza indirizzo IP pubblico a livello di istanza](#defaultsnat).  Per la regola non è richiesta la presenza di un listener di lavoro nel pool di back-end o non è necessario che il probe di integrità abbia esito positivo.

Quando la macchina virtuale con carico bilanciato crea un flusso in uscita, Azure converte l'indirizzo IP di origine privata del flusso in uscita nell'indirizzo IP pubblico del front-end pubblico di Load Balancer. Azure usa SNAT (Source Network Address Translation) per questa funzione, oltre a [PAT (Port Address Translation)](#pat) per mascherare più indirizzi IP privati dietro un indirizzo IP pubblico. Per distinguere i singoli flussi provenienti dalla macchina virtuale vengono usate le porte temporanee del front-end dell'indirizzo IP pubblico di Load Balancer. SNAT usa dinamicamente le [porte temporanee preallocate](#preallocatedports) quando vengono creati i flussi in uscita. In questo contesto le porte temporanee usate per SNAT sono dette porte SNAT.

Le porte SNAT vengono preallocate come descritto nella sezione [Informazioni su SNAT e PAT](#snat) e sono una risorsa limitata soggetta a esaurimento. L'importante è capire come vengono [utilizzate](#pat). Vedere [Gestione dell'esaurimento porte SNAT](#snatexhaust) per comprendere i principi di base per la progettazione e la mitigazione di questo aspetto.

Se a un [servizio Load Balancer Basic vengono associati più indirizzi IP (pubblici)](load-balancer-multivip-overview.md), uno qualsiasi di questi IP indirizzi pubblici è un [candidato per i flussi in uscita e ne viene selezionato uno](#multivipsnat).  

È possibile usare [Log Analytics per Load Balancer](load-balancer-monitor-log.md) e il [log eventi di avviso per il monitoraggio degli avvisi relativi all'esaurimento delle porte SNAT](load-balancer-monitor-log.md#alert-event-log) per monitorare l'integrità delle connessioni in uscita con Load Balancer Basic.

### <a name="defaultsnat"></a>Scenario 3: macchina virtuale autonoma senza indirizzo IP pubblico a livello di istanza

La macchina virtuale non fa parte di un pool di Azure Load Balancer e alla macchina virtuale non è assegnato un indirizzo IP pubblico a livello di istanza. Quando la macchina virtuale crea un flusso in uscita, Azure converte l'indirizzo IP di origine privata del flusso in uscita in un indirizzo IP di origine pubblica. L'indirizzo IP pubblico usato per questo flusso in uscita non è configurabile e non interferisce con il limite della risorsa IP pubblico della sottoscrizione. Azure usa SNAT (Source Network Address Translation) con il mascheramento delle porte ([PAT](#pat)) per eseguire questa funzione. Questo scenario è simile allo [scenario 2](#lb), ma non è disponibile alcun controllo sull'indirizzo IP usato.  Si tratta di uno scenario di fallback per quando non esistono gli scenari 1 e 2.  Questo scenario non è consigliato se si desidera avere il controllo dell'indirizzo in uscita.

Le porte SNAT vengono preallocate come descritto nella sezione [Informazioni su SNAT e PAT](#snat) e sono una risorsa limitata soggetta a esaurimento. L'importante è capire come vengono [utilizzate](#pat). Vedere [Gestione dell'esaurimento porte SNAT](#snatexhaust) per comprendere i principi di base per la progettazione e la mitigazione di questo aspetto.

### <a name="combinations"></a>Più scenari combinati

Gli scenari descritti nelle sezioni precedenti possono essere combinati per ottenere un risultato particolare.  Quando esistono più scenari, si applica un ordine di priorità: lo [scenario 1](#ilpip) ha la precedenza sullo [scenario 2](#lb) e sullo [scenario 3](#defaultsnat) (solo Azure Resource Manager) e lo [scenario 2](#lb) è prioritario rispetto allo [scenario 3](#defaultsnat) (distribuzione Azure Resource Manager e classica).

Un esempio è una distribuzione di Azure Resource Manager in cui l'applicazione dipende fortemente da connessioni in uscita a un numero limitato di destinazioni, ma riceve anche flussi in ingresso tramite un front-end di Load Balancer. In questo caso, è possibile combinare gli scenari 1 e 2 per una maggiore efficacia.  Vedere [Gestione dell'esaurimento SNAT](#snatexhaust) per informazioni su altri modelli.

### <a name="multivipsnat"></a> Più front-end per i flussi in uscita

Load Balancer Basic sceglierà un unico front-end da usare per i flussi in uscita quando esistono [più front-end IP (pubblici)](load-balancer-multivip-overview.md) candidati per i flussi in uscita.  Questa selezione non è configurabile e l'algoritmo di selezione deve essere considerato casuale.  È possibile designare un indirizzo IP specifico per i flussi in uscita come descritto in [scenari combinati](#combinations).

## <a name="snat"></a>Informazioni su SNAT e PAT

### <a name="pat"></a>Mascheramento delle porte SNAT (PAT)

Quando una risorsa pubblica di Load Balancer è associata alle istanze VM, ogni origine di connessione in uscita viene riscritta. L'origine viene riscritta dallo spazio dell'indirizzo IP privato della rete virtuale all'indirizzo IP pubblico front-end del bilanciamento del carico. Nello spazio degli indirizzi IP pubblici, le 5 tuple del flusso (indirizzo IP di origine, porta di origine, protocollo di trasporto IP, indirizzo IP di destinazione, porta di destinazione) devono essere univoche.  Per ottenere ciò vengono usate porte temporanee dopo la riscrittura dell'indirizzo IP di origine privato, perché più flussi hanno origine da un singolo indirizzo IP pubblico.  Queste porte temporanee vengono chiamate porte SNAT. 

Viene utilizzata una singola porta SNAT per flusso verso una singola combinazione di indirizzo IP, porta e protocollo di destinazione. Nel caso di più flussi verso la stessa combinazione di indirizzo IP, porta e protocollo di destinazione, ogni flusso utilizza una singola porta SNAT. Si garantisce così che i flussi siano univoci quando hanno origine dallo stesso indirizzo IP pubblico e quando sono destinati allo stesso indirizzo IP, alla stessa porta e allo stesso protocollo di destinazione. 

Più flussi destinati ognuno a un indirizzo IP, una porta e un protocollo diversi condividono una sola porta SNAT. L'indirizzo IP, la porta e il protocollo di destinazione rendono univoci i flussi senza la necessità di usare porte di origine aggiuntive per distinguere i flussi nello spazio indirizzi IP pubblici.

Quando si esauriscono le risorse di porte SNAT, i flussi in uscita vengono completati dopo che le porte SNAT sono state rilasciate da flussi esistenti. Il servizio Load Balancer recupera le porte SNAT alla chiusura del flusso e usa un [timeout per inattività di 4 minuti](#idletimeout) per il recupero delle porte SNAT dai flussi inattivi.

Vedere la sezione [Gestione di SNAT](#snatexhaust) per informazioni sui modelli per la mitigazione delle condizioni che portano comunemente all'esaurimento delle porte SNAT.

### <a name="preallocatedports"></a>Preallocazione di porte temporanee per il mascheramento delle porte SNAT (PAT)

Azure usa un algoritmo per determinare il numero di porte SNAT preallocate disponibili in base alla dimensione del pool back-end quando si usa il mascheramento delle porte SNAT ([PAT](#pat)).  Le porte SNAT sono porte temporanee disponibili per un indirizzo di origine IP pubblico specificato.

Azure prealloca le porte SNAT alla configurazione IP della scheda di interfaccia di rete di ogni macchina virtuale. Quando una configurazione IP viene aggiunta al pool, le porte SNAT vengono preallocate per questa configurazione IP in base alla dimensione del pool back-end. Per il ruolo Web worker classico, l'allocazione avviene per ogni istanza del ruolo.  Quando vengono creati i flussi in uscita, [PAT](#pat) utilizza (fino al limite preallocato) e rilascia in modo dinamico tali porte alla chiusura del flusso o in caso di [timeout per inattività](#ideltimeout).

Nella tabella seguente sono riportate le preallocazioni delle porte SNAT per i livelli di dimensioni del pool back-end:

| Dimensioni del pool (istanze VM) | Porte SNAT preallocate per configurazione IP|
| --- | --- |
| 1 - 50 | 1024 |
| 51 - 100 | 512 |
| 101 - 200 | 256 |
| 201 - 400 | 128 |
| 401 - 800 | 64 |
| 801 - 1.000 | 32 |

È importante ricordare che il numero di porte SNAT disponibili non viene traslato direttamente in un numero di connessioni. È possibile riutilizzare una singola porta SNAT per più destinazioni univoche. Le porte vengono utilizzate solo se è necessario per rendere univoci i flussi. Fare riferimento alle informazioni su [come gestire questa risorsa soggetta a esaurimento](#snatexhaust) per indicazioni sulla progettazione e la mitigazione. Vedere anche la sezione che descrive [PAT](#pat).

La modifica delle dimensioni del pool back-end può influire su alcuni dei flussi stabiliti. Se le dimensioni del pool back-end aumentano e si esegue la transizione al livello successivo, metà delle porte SNAT preallocate vengono recuperate durante la transizione al livello di pool back-end più grande successivo. I flussi associati a una porta SNAT recuperata scadranno e dovranno essere ristabiliti. I nuovi tentativi di connessione hanno esito positivo immediatamente, purché siano disponibili porte preallocate.

Se le dimensioni del pool back-end diminuiscono e si ha una transizione a un livello più basso, il numero di porte SNAT disponibili aumenta. In questo caso, non vi sono effetti per le porte SNAT allocate esistenti e i rispettivi flussi.

## <a name="snatexhaust"></a>Gestione dell'esaurimento delle porte SNAT (PAT)

Le [porte temporanee](#preallocatedports) usate per [PAT](#pat) sono una risorsa esauribile, come descritto in [Macchina virtuale autonoma senza indirizzo IP pubblico a livello di istanza](#defaultsnat) e [Macchina virtuale con carico bilanciato senza indirizzo IP pubblico a livello di istanza](#lb).

Sono disponibili diverse opzioni di mitigazione generali, se si è consapevoli che verranno avviate numerose connessioni in uscita TCP o UDP allo stesso indirizzo IP e alla stessa porta di destinazione e se si riscontrano connessioni in uscita con errore o se il supporto tecnico ha comunicato il possibile esaurimento delle porte SNAT ([porte temporanee](#preallocatedports) preallocate usate da [PAT](#pat)).  Esaminare queste opzioni e scegliere quella disponibile e ottimale per lo scenario specifico.  È possibile che una o più opzioni risultino utili per gestire questo scenario.

In caso di problemi a comprendere il comportamento delle connessioni in uscita, è possibile usare le statistiche dello stack IP (netstat) o può essere utile osservare i comportamenti di connessione tramite le acquisizioni di pacchetti.  È possibile eseguire queste acquisizioni di pacchetti nel sistema operativo guest dell'istanza o usare [Network Watcher per l'acquisizione dei pacchetti](../network-watcher/network-watcher-packet-capture-manage-portal.md).

### <a name="connectionreuse"></a>Modificare l'applicazione per riutilizzare le connessioni 
È possibile ridurre la richiesta di porte temporanee usate per SNAT usando di nuovo le connessioni nell'applicazione.  Ciò vale soprattutto per i protocolli HTTP/1.1 in cui il riuso della connessione è automatico.  Altri protocolli che usano HTTP come trasporto, ad esempio REST, possono trarne vantaggio, a loro volta.  Il riuso è sempre preferibile alle connessioni TCP singole e atomiche per ogni richiesta e comporta transazioni TCP molto efficiente e con prestazioni più elevate.

### <a name="connection pooling"></a>Modificare l'applicazione per usare pool di connessioni
Nella connessione è possibile usare una uno schema di pool della connessione, in cui le richieste sono distribuite internamente in un set predefinito di connessioni, ognuna delle quali viene riutilizzata quando possibile.  Questo limita il numero di porte temporanee in uso e crea un ambiente più prevedibile.  Può anche migliorare la velocità effettiva delle richieste, consentendo più operazioni simultanee, quando una connessione si blocca sulla risposta di un'operazione.  Il pool delle connessioni potrebbe essere già presente nell'ambito usato per sviluppare l'applicazione o le impostazioni di configurazione dell'applicazione.  È possibile combinarlo con il riutilizzo delle connessioni, mentre le richieste multiple usano un numero fisso e prevedibile di porte per lo stesso indirizzo IP e la stessa porta di destinazione sfruttando al contempo anche i vantaggi derivanti da un uso molto efficiente delle transazioni TCP che riduce la latenza e l'uso delle risorse.  Anche le transazioni UDP possono offrire vantaggi, perché gestire il numero di flussi UDP può a sua volta evitare condizioni di esaurimento e consente di gestire l'utilizzo delle porte SNAT.

### <a name="retry logic"></a>Modificare l'applicazione per usare una logica di ripetizione meno aggressiva
Quando vengono esaurite le [porte temporanee preallocate](#preallocatedports) usate per [PAT](#pat) o si verificano errori nell'applicazione, i tentativi di ripetizione aggressivi o di forza bruta senza logica di backoff e decadimento causano il verificarsi o la persistenza dell'esaurimento. È possibile ridurre la richiesta di porte temporanee usando una logica di ripetizione meno aggressiva.   Le porte temporanee hanno un timeout di inattività 4 minuti (non modificabile) e se i tentativi di ripetizione sono troppo aggressivi, l'esaurimento delle porte non viene eliminato autonomamente.  Pertanto, la modalità e la frequenza con cui l'applicazione effettua nuovi tentativi di transazioni è un fattore fondamentale della progettazione.

### <a name="assignilpip"></a>Assegnare un indirizzo IP pubblico a livello di istanza a ogni macchina virtuale
In questo modo lo scenario cambia in [Indirizzo IP pubblico a livello di istanza a una macchina virtuale](#ilpip).  Tutte le porte temporanee dell'indirizzo IP pubblico usato per ogni macchina virtuale sono disponibili per la macchina virtuale, a differenza degli scenari in cui le porte temporanee di un indirizzo IP pubblico vengono condivise con tutte le macchine virtuali associate al pool back-end corrispondente.  Bisogna considerare anche l'altro lato della medaglia, ad esempio i costi aggiuntivi per gli indirizzi IP pubblici e l'impatto potenziale di creare un elenco con un numero elevato di singoli indirizzi IP consentiti.

>[!NOTE] 
>Questa opzione non è disponibile per I ruoli Web worker.

### <a name="idletimeout"></a>Usare keep-alive per reimpostare il timeout di inattività per le connessioni uscita

Il timeout di inattività delle connessioni in uscita è di 4 minuti. Questo valore non è modificabile. È comunque possibile usare keep-alive del livello trasporto (keep-alive TCP) o del livello applicazione per aggiornare un flusso inattivo e reimpostare il timeout di inattività, se necessario.

## <a name="discoveroutbound"></a>Individuazione dell'indirizzo IP pubblico usato da una macchina virtuale specifica
Esistono molti modi per determinare l'indirizzo IP di origine pubblica di una connessione in uscita. OpenDNS offre un servizio che consente di visualizzare l'indirizzo IP pubblico della macchina virtuale. Con il comando nslookup è possibile inviare una query DNS per il nome myip.opendns.com al resolver OpenDNS. Il servizio restituisce l'indirizzo IP di origine usato per inviare la query. Quando si esegue la query seguente dalla macchina virtuale, la risposta è l'indirizzo IP pubblico usato per tale VM.

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Impedire la connettività in uscita
A volte può succedere di non voler consentire a una macchina virtuale di creare un flusso in uscita o può essere necessario gestire le destinazioni che possono essere raggiunte da flussi in uscita oppure le destinazioni possono iniziare flussi in entrata. In questo caso usare i [gruppi di sicurezza di rete (NSG)](../virtual-network/virtual-networks-nsg.md) per gestire le destinazioni che la macchina virtuale può raggiungere e la destinazione pubblica che può avviare i flussi in entrata. Quando si applica un gruppo di sicurezza di rete a una macchina virtuale con carico bilanciato, è necessario considerare i [tag predefiniti](../virtual-network/virtual-networks-nsg.md#default-tags) e le [regole predefinite](../virtual-network/virtual-networks-nsg.md#default-rules).

È necessario assicurarsi che la macchina virtuale riceva richieste di probe di integrità da Azure Load Balancer. Se un gruppo di sicurezza di rete blocca le richieste di probe di integrità dal tag AZURE_LOADBALANCER predefinito, il probe di integrità della macchina virtuale avrà esito negativo e la macchina virtuale sarà contrassegnata come non attiva. Load Balancer interrompe l'invio di nuovi flussi a tale macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Load Balancer Basic](load-balancer-overview.md).
- Vedere altre informazioni sui [gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).
- Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.
