---
title: Connessioni in uscita in Azure
titleSuffix: Azure Load Balancer
description: In questo articolo viene spiegato come Azure consente alle macchine virtuali di comunicare con i servizi Internet pubblici.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/24/2020
ms.author: allensu
ms.openlocfilehash: 4368a025ecc158afa1ee78b8abd86bd6db42ba75
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438666"
---
# <a name="outbound-connections-in-azure"></a>Connessioni in uscita in Azure

Azure Load Balancer fornisce la connettività in uscita tramite diversi meccanismi. Questo articolo descrive gli scenari e le modalità di gestione. Se si verificano problemi con la connettività in uscita tramite una Azure Load Balancer, vedere la [Guida alla risoluzione dei problemi per le connessioni in uscita](../load-balancer/troubleshoot-outbound-connection.md).

>[!NOTE]
>Questo articolo illustra le distribuzioni Gestione risorse. Microsoft consiglia Gestione risorse per i carichi di lavoro di produzione.

## <a name="terminology"></a>Terminologia

| Termine | Protocolli applicabili | Dettagli|
| ---------|---------| -------|
| Network Address Translation di origine (SNAT) | TCP, UDP | Una distribuzione in Azure può comunicare con endpoint all'esterno di Azure nello spazio indirizzi IP pubblici. Quando un'istanza avvia un flusso in uscita verso una destinazione nello spazio indirizzi IP pubblici, Azure esegue in modo dinamico il mapping dell'indirizzo IP privato a un indirizzo IP pubblico. Dopo aver creato questo mapping, il traffico di ritorno per questo flusso con origine in uscita può raggiungere l'indirizzo IP privato da cui ha avuto origine il flusso. Azure usa **Network Address Translation di origine (SNAT)** per eseguire questa funzione.|
| Mascheramento delle porte SNAT (PAT)| TCP, UDP |  Quando più indirizzi IP privati sono mascherati da un singolo indirizzo IP pubblico, Azure usa la **traduzione degli indirizzi di porta (Pat)** per mascherare o nascondere indirizzi IP privati. Per PAT vengono usate porte temporanee [preallocate](#preallocatedports) in base alle dimensioni del pool. Quando una risorsa pubblica di Load Balancer è associata a istanze VM che non hanno indirizzi IP pubblici dedicati ogni origine di connessione in uscita viene riscritta. L'origine viene riscritta dallo spazio dell'indirizzo IP privato della rete virtuale all'indirizzo IP pubblico front-end del bilanciamento del carico. Nello spazio degli indirizzi IP pubblici, le 5 tuple del flusso (indirizzo IP di origine, porta di origine, protocollo di trasporto IP, indirizzo IP di destinazione, porta di destinazione) devono essere univoche. Il mascheramento delle porte SNAT è utilizzabile con i protocolli TCP o UDP IP. Per ottenere ciò vengono usate porte temporanee (porte SNAT) dopo la riscrittura dell'indirizzo IP di origine privato, perché più flussi hanno origine da un singolo indirizzo IP pubblico. L'algoritmo SNAT per il mascheramento delle porte alloca in modo diverso le porte SNAT per il protocollo UDP rispetto al protocollo TCP.|
| Porte SNAT| TCP | Le porte SNAT sono porte temporanee disponibili per un particolare indirizzo di origine IP pubblico. Viene usata una porta SNAT per flusso verso un singolo indirizzo IP e porta di destinazione. Nel caso di più flussi TCP verso la stessa combinazione di indirizzo IP, porta e protocollo di destinazione, ogni flusso TCP utilizza una singola porta SNAT. Si garantisce così che i flussi siano univoci quando hanno origine dallo stesso indirizzo IP pubblico e quando sono destinati allo stesso indirizzo IP, alla stessa porta e allo stesso protocollo di destinazione. Più flussi destinati ognuno a un indirizzo IP, una porta e un protocollo diversi condividono una sola porta SNAT. L'indirizzo IP, la porta e il protocollo di destinazione rendono univoci i flussi senza la necessità di porte di origine aggiuntive per distinguere i flussi nello spazio indirizzi IP pubblici.|
|Porte SNAT | UDP | Le porte SNAT UDP vengono gestite da un algoritmo diverso rispetto alle porte SNAT TCP.  Load Balancer usa un algoritmo noto come "port-restricted cone NAT" per UDP.  Per ogni flusso, viene usata una porta SNAT indipendentemente dall'indirizzo IP e porta di destinazione.|
| Esaurimento | - | Quando si esauriscono le risorse di porte SNAT, i flussi in uscita vengono completati dopo che i flussi esistenti rilasciano le porte SNAT. Il servizio Load Balancer recupera le porte SNAT alla chiusura del flusso e usa un [timeout per inattività di 4 minuti](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) per il recupero delle porte SNAT dai flussi inattivi. Le porte SNAT UDP in genere si esauriscono molto più rapidamente rispetto alle porte SNAT TCP a causa della differenza tra gli algoritmi utilizzati. È necessario progettare e scalare il test tenendo presente questa differenza.|
| Comportamento della versione della porta SNAT | TCP | Se il server o il client inviano richieste FIN/ACK, la porta SNAT viene rilasciata dopo 240 secondi. In presenza di una richiesta RST, la porta SNAT viene rilasciata dopo 15 secondi. Se è stato raggiunto il timeout di inattività, la porta viene rilasciata.|
| Comportamento della versione della porta SNAT | UDP |Se è stato raggiunto il timeout di inattività, la porta viene rilasciata.|
| Riutilizzo delle porte SNAT | TCP, UDP | Una volta rilasciata una porta, la porta sarà disponibile per il riutilizzo in base alle esigenze.  È possibile considerare le porte SNAT come una sequenza dal più basso al più alto disponibile per un determinato scenario e la prima porta SNAT disponibile viene usata per le nuove connessioni.|

### <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>Algoritmo di allocazione porte

Azure usa un algoritmo per determinare il numero di porte SNAT preallocate disponibili in base alla dimensione del pool back-end quando si usa PAT. Per ogni indirizzo IP pubblico associato a un servizio di bilanciamento del carico sono disponibili 64.000 porte come porte SNAT per ogni protocollo di trasporto IP. Lo stesso numero di porte SNAT viene allocato rispettivamente per UDP e TCP e utilizzato indipendentemente per ogni protocollo di trasporto IP.  Tuttavia, l'uso delle porte SNAT è diverso a seconda che il flusso sia UDP o TCP. Quando vengono creati i flussi in uscita, queste porte vengono utilizzate dinamicamente (fino al limite preallocato) e rilasciate quando il flusso viene chiuso o si verificano [timeout di inattività](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) . Le porte vengono usate solo se è necessario per rendere univoci i flussi.

#### <a name="dynamic-snat-ports-allocated"></a><a name="snatporttable"></a> Porte SNAT dinamiche allocate

Nella tabella seguente sono riportate le preallocazioni delle porte SNAT per i livelli di dimensioni del pool back-end:

| Dimensioni del pool (istanze VM) | Porte SNAT preallocate per configurazione IP |
| --- | --- |
| 1-50 | 1\.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1.000 | 32 |

La modifica delle dimensioni del pool back-end potrebbe influire su alcuni dei flussi stabiliti:

- Se le dimensioni del pool back-end aumentano e si esegue la transizione al livello successivo, metà delle porte SNAT preallocate vengono recuperate durante la transizione al livello di pool back-end più grande successivo. I flussi associati a una porta SNAT recuperata raggiungeranno il timeout e dovranno essere ristabiliti. Se viene tentato un nuovo flusso, il flusso avrà esito positivo immediatamente, purché siano disponibili porte preallocate.
- Se le dimensioni del pool back-end diminuiscono e si ha una transizione a un livello più basso, il numero di porte SNAT disponibili aumenta. In questo caso, non vi sono effetti per le porte SNAT allocate esistenti e i rispettivi flussi.

## <a name="outbound-connections-scenario-overview"></a><a name="scenarios"></a>Panoramica dello scenario relativo alle connessioni in uscita

| Scenario | Metodo | Protocolli IP | Descrizione |
|  --- | --- | --- | --- |
|  1. VM con un indirizzo IP pubblico (con o senza Azure Load Balancer | SNAT, il mascheramento delle porte non viene usato | TCP, UDP, ICMP, ESP | Azure usa l'indirizzo IP pubblico assegnato alla configurazione IP della scheda di interfaccia di rete dell'istanza per tutti i flussi in uscita. L'istanza ha tutte le porte temporanee disponibili. Non importa se la macchina virtuale è con carico bilanciato o meno. Questo scenario ha la precedenza rispetto agli altri. Un indirizzo IP pubblico assegnato a una macchina virtuale è una relazione 1:1, anziché 1:molti, e viene implementato come NAT 1:1 senza stato. |
| 2. public Load Balancer associato a una macchina virtuale (nessun indirizzo IP pubblico nella macchina virtuale o nell'istanza) | SNAT con mascheramento delle porte (PAT) tramite i front-end di Load Balancer | TCP, UDP | In questo scenario, la risorsa Load Balancer deve essere configurata con una regola di bilanciamento del carico per creare un collegamento tra il front-end IP pubblico e il pool back-end. Se questa configurazione delle regole non viene completata, il comportamento sarà quello descritto nello scenario 3. Per la regola non è richiesta la presenza di un listener di lavoro nel pool di back-end perché il probe di integrità abbia esito positivo. Quando la macchina virtuale crea un flusso in uscita, Azure converte l'indirizzo IP di origine privato del flusso in uscita nell'indirizzo IP pubblico del Front-End Load Balancer pubblico tramite SNAT. Le porte temporanee dell'indirizzo IP pubblico front-end del servizio di bilanciamento del carico vengono usate per distinguere i singoli flussi originati dalla macchina virtuale. SNAT usa dinamicamente le [porte temporanee preallocate](#preallocatedports) quando vengono creati i flussi in uscita. In questo contesto le porte temporanee usate per SNAT sono dette porte SNAT. Le porte SNAT vengono preallocate come descritto nella [tabella allocata per le porte SNAT predefinite](#snatporttable). |
| 3. VM (nessuna Load Balancer, nessun indirizzo IP pubblico) o macchina virtuale associata al Load Balancer di base interno | SNAT con mascheramento delle porte (PAT) | TCP, UDP | Quando la macchina virtuale crea un flusso in uscita, Azure converte l'indirizzo IP di origine privata del flusso in uscita in un indirizzo IP di origine pubblica. Questo indirizzo IP pubblico **non è configurabile**, non può essere riservato e non viene conteggiato in base al limite di risorse IP pubblico della sottoscrizione. Se si ridistribuisce la macchina virtuale, il set di disponibilità o il set di scalabilità della macchina virtuale, questo indirizzo IP pubblico verrà rilasciato e verrà richiesto un nuovo indirizzo IP pubblico. Non usare questo scenario per creare un elenco di indirizzi IP consentiti. Usare invece lo scenario 1 o 2 in cui si dichiara in modo esplicito il comportamento in uscita. Le porte SNAT vengono preallocate come descritto nella [tabella allocata per le porte SNAT predefinite](#snatporttable).

## <a name="outbound-rules"></a><a name="outboundrules"></a>Regole in uscita

 Le regole in uscita semplificano la configurazione della conversione degli indirizzi di rete in uscita di [Load Balancer Standard](load-balancer-standard-overview.md) pubblico.  È disponibile il controllo dichiarativo completo sulla connettività in uscita per ridimensionare e ottimizzare questa capacità in base alle esigenze specifiche. Questa sezione espande lo scenario 2 (B) descritto in precedenza.

![Regole in uscita di Load Balancer](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Con le regole in uscita, è possibile usare Load Balancer per definire la NAT in uscita da zero. È anche possibile ridimensionare e ottimizzare il comportamento del NAT in uscita esistente.

Le regole in uscita consentono di controllare:

- Quali macchine virtuali devono essere convertite in quali indirizzi IP pubblici.
- La modalità di allocazione delle porte SNAT in uscita.
- Per quali protocolli implementare la conversione in uscita.
- Quale durata usare per il timeout di inattività delle connessioni in uscita (4-120 minuti).
- indica se inviare una reimpostazione TCP al timeout di inattività
- protocolli di trasporto sia TCP che UDP con una sola regola

### <a name="outbound-rule-definition"></a>Definizione della regola in uscita

Come tutte le regole di Load Balancer, le regole in uscita seguono la stessa sintassi familiare del bilanciamento del carico e delle regole NAT in **ingresso: il**  +  **parameters**  +  **pool back-end**dei parametri front-end. Una regola in uscita configura NAT in uscita per _tutte le macchine virtuali identificate dal pool back-end_ da convertire in _front-end_.  I _parametri_ forniscono un controllo granulare aggiuntivo per l'algoritmo NAT in uscita.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Scalabilità di NAT in uscita con più indirizzi IP

Ogni indirizzo IP aggiuntivo fornito da un front-end fornisce altre porte temporanee di 64.000 per Load Balancer da usare come porte SNAT. È possibile usare più indirizzi IP per gestire scenari su larga scala e le regole in uscita per risolvere i problemi dei modelli soggetti a [esaurimento delle porte SNAT](troubleshoot-outbound-connection.md#snatexhaust).  

È anche possibile usare un [prefisso IP pubblico](https://aka.ms/lbpublicipprefix) direttamente con una regola in uscita.  L'uso del prefisso IP pubblico consente di gestire più facilmente la scalabilità e l'elenco degli elementi consentiti per i flussi provenienti dalla distribuzione di Azure. È possibile configurare una configurazione IP front-end all'interno della risorsa Load Balancer in modo da fare riferimento direttamente a un prefisso di indirizzo IP pubblico.  In questo modo Load Balancer ha il controllo esclusivo sul prefisso IP pubblico e la regola in uscita userà automaticamente tutti gli indirizzi IP pubblici compresi nel prefisso per le connessioni in uscita.  Ognuno degli indirizzi IP all'interno di un prefisso IP pubblico fornisce altre porte temporanee 64.000 per ogni indirizzo IP per Load Balancer da usare come porte SNAT.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Timeout di inattività del flusso in uscita e ripristino TCP

Le regole in uscita includono un parametro di configurazione per controllare il timeout di inattività per i flussi in uscita in modo da adattarlo alle esigenze dell'applicazione. Il timeout di inattività in uscita è per impostazione predefinita di 4 minuti. È possibile imparare a [configurare i timeout di inattività](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout). Il comportamento predefinito di Load Balancer prevede l'eliminazione automatica del flusso quando viene raggiunto il timeout di inattività in uscita.  Con il `enableTCPReset` parametro, è possibile abilitare un comportamento più prevedibile dell'applicazione e controllare se inviare la reimpostazione TCP (TCP RST) bidirezionale al momento del timeout di inattività in uscita. Esaminare [il timeout di inattività](https://aka.ms/lbtcpreset) per i dettagli, inclusa la disponibilità dell'area.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Impedire la connettività in uscita

Le regole di bilanciamento del carico forniscono la programmazione automatica del NAT in uscita. Tuttavia, per alcuni scenari è preferibile o necessario disabilitare la programmazione automatica di NAT in uscita con la regola di bilanciamento del carico, per controllare o mettere a punto il comportamento.  
È possibile usare questo parametro in due modi:

1. Eliminazione facoltativa dell'uso dell'indirizzo IP in ingresso per SNAT in uscita tramite la disabilitazione di SNAT in uscita per una regola di bilanciamento del carico
  
2. Ottimizzare i parametri di SNAT in uscita di un indirizzo IP usato per il traffico in ingresso e in uscita simultaneamente.  La programmazione NAT in uscita automatica deve essere disabilitata per consentire a una regola in uscita di assumere il controllo.  Per modificare, ad esempio, l'allocazione delle porte SNAT di un indirizzo utilizzato anche per il traffico in ingresso, il `disableOutboundSnat` parametro deve essere impostato su true.  Se si prova a usare una regola in uscita per ridefinire i parametri di un indirizzo IP usato anche in ingresso e la programmazione NAT in uscita automatica non viene disabilitata per la regola di bilanciamento del carico, l'operazione di configurazione di una regola in uscita avrà esito negativo.

>[!IMPORTANT]
> La macchina virtuale non avrà connettività in uscita se si imposta questo parametro su true e non si dispone di una regola in uscita per definire la connettività in uscita.  Alcune operazioni della macchina virtuale o dell'applicazione possono dipendere dalla disponibilità della connettività in uscita. Assicurarsi di valutare le dipendenze dello scenario e l'impatto di questa modifica.

A volte è preferibile che a una VM non sia consentito creare un flusso in uscita oppure potrebbe essere necessario gestire le destinazioni che possono essere raggiunte con i flussi in uscita o le destinazioni che possono iniziare flussi in ingresso. In questo caso è possibile usare i [gruppi di sicurezza di rete](../virtual-network/security-overview.md) per gestire le destinazioni che la macchina virtuale può raggiungere. È anche possibile usare i gruppi di sicurezza di rete per gestire la destinazione pubblica che può avviare i flussi in ingresso.

Quando si applica un gruppo di sicurezza di rete a una macchina virtuale con carico bilanciato, considerare i [tag del servizio](../virtual-network/security-overview.md#service-tags) e le [regole di sicurezza predefinite](../virtual-network/security-overview.md#default-security-rules). È necessario assicurarsi che la macchina virtuale riceva richieste di probe di integrità da Azure Load Balancer.

Se un gruppo di sicurezza di rete blocca le richieste di probe di integrità dal tag AZURE_LOADBALANCER predefinito, il probe di integrità della macchina virtuale avrà esito negativo e la macchina virtuale sarà contrassegnata come non attiva. Load Balancer interrompe l'invio di nuovi flussi a tale macchina virtuale.

## <a name="scenarios-with-outbound-rules"></a>Scenari con regole in uscita

| # | Scenario| Dettagli|
|---|---|---|
| I |  Limitare le connessioni in uscita a un set specifico di indirizzi IP pubblici| È possibile usare una regola in uscita per limitare le connessioni in uscita in modo che risultino provenienti da un set specifico di indirizzi IP pubblici per semplificare gli scenari di creazione di elenchi di elementi consentiti.  Questo indirizzo IP pubblico di origine può essere lo stesso usato da una regola di bilanciamento del carico o un set diverso di indirizzi IP pubblici usato da una regola di bilanciamento del carico.  1. Creare un [prefisso IP pubblico](https://aka.ms/lbpublicipprefix) o indirizzi IP pubblici dal prefisso IP pubblico 2. Creare un Load Balancer Standard pubblico 3. Creare front-end che fanno riferimento al prefisso IP pubblico (o indirizzi IP pubblici) che si desidera utilizzare 4. Riutilizzare un pool back-end o creare un pool back-end e collocare le macchine virtuali in un pool back-end del Load Balancer 5 pubblico. Configurare una regola in uscita nel Load Balancer pubblico per programmare la NAT in uscita per queste VM usando i front-end. Se non si desidera che la regola di bilanciamento del carico venga usata per il traffico in uscita, è necessario disabilitare SNAT in uscita nella regola di bilanciamento del carico.
| II |  Modificare l'allocazione delle porte SNAT| È possibile usare le regole in uscita per ottimizzare l'[allocazione automatica delle porte SNAT in base alle dimensioni del pool back-end](load-balancer-outbound-connections.md#preallocatedports). Ad esempio, con due macchine virtuali che condividono un singolo indirizzo IP pubblico per NAT in uscita, potrebbe essere utile aumentare il numero di porte SNAT allocate rispetto alle 1024 porte predefinite, se si verifica l'esaurimento delle porte SNAT. Ogni indirizzo IP pubblico può rendere disponibili fino a 64.000 porte temporanee.  Se si configura una regola in uscita con un singolo front-end per indirizzi IP pubblici, è possibile distribuire un totale di 64.000 porte SNAT alle macchine virtuali nel pool back-end.  Per due macchine virtuali, è possibile allocare un massimo di 32.000 porte SNAT con una regola in uscita (2 x 32.000 = 64.000). È possibile utilizzare le regole in uscita per ottimizzare le porte SNAT allocate per impostazione predefinita. È necessario allocare un numero maggiore o minore dell'allocazione di porta SNAT predefinita. Ogni indirizzo IP pubblico da tutti i front-end di una regola in uscita contribuisce fino a 64.000 porte effimere da usare come porte SNAT.  Load Balancer alloca le porte SNAT in multipli di 8. Se si specifica un valore non divisibile per 8, l'operazione di configurazione viene rifiutata.  Se si tenta di allocare più porte SNAT di quante ne siano disponibili in base al numero di indirizzi IP pubblici, l'operazione di configurazione viene rifiutata.  Se ad esempio si allocano 10.000 porte per macchina virtuale e 7 macchine virtuali in un pool back-end condividono un solo indirizzo IP pubblico, la configurazione viene rifiutata (7 x 10.000 porte SNAT > 64.000 porte SNAT).  È possibile aggiungere altri indirizzi IP al front-end della regola in uscita per abilitare questo scenario. È possibile ripristinare l' [allocazione di porte SNAT predefinite in base alle dimensioni del pool back-end](load-balancer-outbound-connections.md#preallocatedports) specificando 0 per il numero di porte. In tal caso, la prima istanza di VM 50 otterrà 1024 porte, 51-100 istanze di VM otterranno 512 e così via in base alla [tabella](#snatporttable).|
| III|  Abilitare NAT solo in uscita | È possibile usare un Load Balancer Standard pubblico per rendere disponibile la conversione NAT in uscita per un gruppo di macchine virtuali. In questo scenario, è possibile usare esclusivamente una regola in uscita, senza che siano necessarie regole aggiuntive.|
| IV | Solo NAT in uscita per le macchine virtuali (non in ingresso) | Definire un Load Balancer Standard pubblico, posizionare le macchine virtuali nel pool back-end e configurare una regola in uscita per programmare NAT in uscita e limitare le connessioni in uscita in modo che provengano da uno specifico indirizzo IP pubblico. È anche possibile usare un prefisso IP pubblico per semplificare l'inserimento dell'origine delle connessioni in uscita nell'elenco elementi consentiti. 1. Creare un Load Balancer Standard pubblico. 2. Creare un pool back-end e posizionare le macchine virtuali in un pool back-end del Load Balancer pubblico. 3. Configurare una regola in uscita per il Load Balancer pubblico per programmare la conversione NAT in uscita per queste macchine virtuali.
| V| NAT in uscita per scenari con Load Balancer Standard interno| Quando si usa un Load Balancer Standard interno, NAT in uscita non è disponibile se non è stata dichiarata in modo esplicito una connettività in uscita. È possibile definire la connettività in uscita usando una regola in uscita per creare una connettività in uscita per le macchine virtuali dietro una Load Balancer Standard interna con i passaggi seguenti: 1. Creare un Load Balancer Standard pubblico. 2. Creare un pool back-end e posizionare le macchine virtuali in un pool back-end del Load Balancer pubblico oltre al Load Balancer interno. 3. Configurare una regola in uscita per il Load Balancer pubblico per programmare la conversione NAT in uscita per queste macchine virtuali. Per ulteriori informazioni su questo scenario, vedere [questo esempio](https://docs.microsoft.com/azure/load-balancer/egress-only). |
| VI | Abilitare entrambi i protocolli TCP e UDP per NAT in uscita con un Load Balancer Standard pubblico | Quando si usa un Load Balancer Standard pubblico, la programmazione NAT in uscita automatica fornita corrisponde al protocollo di trasporto della regola di bilanciamento del carico. 1. Disabilitare SNAT in uscita per la regola di bilanciamento del carico. 2. Configurare una regola in uscita per lo stesso Load Balancer. 3. Riutilizzare il pool back-end già usato dalle macchine virtuali. 4. Specificare "protocol": "All" come parte della regola in uscita. Quando si usano solo regole NAT in ingresso, non viene fornita alcuna conversione NAT in uscita. 1. Posizionare le macchine virtuali in un pool back-end. 2. Definire una o più configurazioni IP front-end con indirizzi IP pubblici o con prefisso IP pubblico 3. Configurare una regola in uscita per lo stesso Load Balancer. 4. Specificare "protocol": "All" come parte della regola in uscita |


## <a name="limitations"></a>Limitazioni

- Il numero massimo di porte temporanee utilizzabili per ogni indirizzo IP front-end è 64.000.
- L'intervallo del timeout di inattività in uscita configurabile è compreso tra 4 a 120 minuti (da 240 a 7200 secondi).
- Load Balancer non supporta ICMP per NAT in uscita.
- Le regole in uscita possono essere applicate solo alla configurazione IP primaria di una scheda di interfaccia di rete.  Non è possibile creare una regola in uscita per l'IP secondario di una VM o di un appliance virtuale di dispositivo. Sono supportate più schede di rete.
- I ruoli di lavoro Web senza una rete virtuale e altri servizi della piattaforma Microsoft possono essere accessibili quando si usa solo Load Balancer Standard interno a causa di un effetto collaterale della modalità di funzionamento dei servizi pre-VNet e altre piattaforme di servizi. Non è necessario basarsi su questo effetto collaterale poiché il servizio stesso o la piattaforma sottostante sono soggetti a modifiche senza preavviso. Presupporre sempre che è necessario creare una connettività in uscita in modo esplicito se desiderato quando si usa solo Load Balancer Standard interno. Lo scenario 3 descritto in questo articolo non è disponibile.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Load Balancer Standard](load-balancer-standard-overview.md).
- Vedere le [domande frequenti su Azure Load Balancer](load-balancer-faqs.md).
- Altre informazioni sulle [regole in uscita](load-balancer-outbound-rules-overview.md) per un'istanza di Load Balancer Standard pubblica.
- Vedere altre informazioni su [Azure Load Balancer](load-balancer-overview.md).
- Vedere altre informazioni sui [gruppi di sicurezza di rete](../virtual-network/security-overview.md).
- Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.
