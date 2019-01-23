---
title: Informazioni su Load Balancer Standard di Azure
titlesuffix: Azure Load Balancer
description: Panoramica delle funzionalità di Azure Load Balancer Standard
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/11/2019
ms.author: kumud
ms.openlocfilehash: 2eb2fbb1d184bf58923748278d4989a271adf434
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352581"
---
# <a name="azure-standard-load-balancer-overview"></a>Panoramica di Azure Load Balancer Standard

Azure Load Balancer consente di ridimensionare le applicazioni e di creare disponibilità elevata per i servizi. Load Balancer supporta scenari in ingresso e in uscita, offre bassa latenza, elevata produttività e una scalabilità fino a milioni di flussi per tutte le applicazioni TCP e UDP. 

Questo articolo illustra Load Balancer Standard.  Per una panoramica più generale di Load Balancer di Azure, consultare anche la [Panoramica di Load Balancer](load-balancer-overview.md).

## <a name="what-is-standard-load-balancer"></a>Cos’è Load Balancer Standard?

Load Balancer Standard è un nuovo prodotto Load Balancer per tutte le applicazioni TCP e UDP, con un set di funzionalità più espanso e granulare di Load Balancer Basic.  Nonostante siano presenti molte somiglianze, è importante acquisire familiarità con le differenze, come viene suggerito da questo articolo.

È possibile usare il servizio Load Balancer Standard come servizio di bilanciamento del carico pubblico o interno. E una macchina virtuale può essere connessa a una risorsa Load Balancer interna e pubblica.

Le funzioni della risorsa di Load Balancer sono espresse come un front-end, una regola, una probe di integrità e una definizione di pool back-end.  Una risorsa può contenere più regole. Le macchine virtuali vengono inserite nel pool back-end specificando il pool back-end dalla risorsa di scheda di interfaccia di rete.  Se si usa un set di scalabilità della macchina virtuale, questo parametro viene passato tramite il profilo di rete ed espanso.

Un aspetto chiave è l'ambito della rete virtuale per la risorsa.  Mentre Basic Load Balancer esiste all'interno dell'ambito di un set di disponibilità, un Load Balancer Standard è completamente integrato con l'ambito di una rete virtuale e si applicano tutti i concetti di rete virtuale.

Le risorse di Load Balancer sono oggetti all'interno dei quali è possibile esprimere in che modo Azure deve programmare la propria infrastruttura multi-tenant per ottenere lo scenario che si vuole creare.  Non esiste una relazione diretta tra le risorse di Load Balancer e l'infrastruttura effettiva; la creazione di un bilanciamento del carico non crea un'istanza e la capacità è sempre disponibile. Inoltre, non ci sono start up o ritardi di scalabilità da considerare. 

>[!NOTE]
> Azure offre una suite di soluzioni di bilanciamento del carico completamente gestite per tutti gli scenari.  Se si desidera terminare TLS ("offload SSL") o elaborare a livello di applicazione per richiesta HTTP/HTTPS, esaminare l'articolo relativo al [gateway applicazione](../application-gateway/application-gateway-introduction.md).  Se si desidera il bilanciamento del carico DNS globale, esaminare l'articolo relativo a [Gestione traffico di Microsoft Azure](../traffic-manager/traffic-manager-overview.md).  Gli scenari end-to-end potrebbero trarre vantaggio dalla combinazione di queste soluzioni in base alle esigenze.

## <a name="why-use-standard-load-balancer"></a>Perché usare Load Balancer Standard?

Load Balancer Standard consente di ridimensionare le applicazioni e di creare disponibilità elevata sia per distribuzioni su scala ridotta sia per architetture grandi e complesse a più zone.

Consultare la tabella seguente per una panoramica delle differenze tra Load Balancer Standard e Load Balancer Basic:

>[!NOTE]
> Per i nuovi progetti è consigliabile adottare Load Balancer Standard. 

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Revisionare i [limiti del servizio di Load Balancer](https://aka.ms/lblimits), nonché i [prezzi](https://aka.ms/lbpricing), e il [contratto di servizio](https://aka.ms/lbsla).


### <a name="backend"></a>Pool back-end

I pool back-end di Load Balancer Standard si espandono a qualsiasi risorsa di macchina virtuale in una rete virtuale.  Può contenere fino a 1000 istanze di back-end.  Un'istanza di back-end è una configurazione IP, che è una proprietà di una risorsa di scheda di rete.

Il pool back-end può contenere le macchine virtuali autonome, set di disponibilità o set di scalabilità di macchine virtuali.  È anche possibile eseguire unire le risorse nel pool di back-end. È possibile combinare fino a 150 risorse nel pool di back-end per ogni risorsa di Load Balancer.

Quando si considera come progettare il pool back-end, è possibile progettare per il minor numero di singole risorse di pool di back-end per ottimizzare ulteriormente la durata delle operazioni di gestione.  Non vi è alcuna differenza nelle prestazioni del piano dati o la scala.

### <a name="probes"></a>Probe di integrità
  
Load Balancer Standard aggiunge il supporto per i [probe di integrità HTTPS](load-balancer-custom-probe-overview.md#httpprobe), ovvero i probe HTTP con wrapper Transport Layer Security (TLS), per monitorare in modo accurato le applicazioni HTTPS.  

Inoltre, in caso di [inattività dei probe](load-balancer-custom-probe-overview.md#probedown) dell'intero pool di back-end, Load Balancer Standard consente a tutte le connessioni TCP stabilite di continuare. Diversamente da questo, Load Balancer Basic terminerà tutte le connessioni TCP a tutte le istanze.

Per informazioni dettagliate, vedere [Probe di integrità di Load Balancer](load-balancer-custom-probe-overview.md).

### <a name="az"></a>Zone di disponibilità

Load Balancer Standard supporta funzionalità aggiuntive in aree in cui sono disponibili zone di disponibilità.  Queste funzionalità sono incrementali rispetto a tutte le funzioni di Load Balancer Standard.  Le configurazioni delle zone di disponibilità sono disponibili per Load Balancer Standard pubblico e interno.

Server front-end non zonali diventano con ridondanza della zona per impostazione predefinita quando vengono distribuiti in un'area con zone di disponibilità.   Un server front-end con ridondanza della zona sopravvive all'errore di zona e viene fornito da parte di infrastrutture dedicate in tutte le zone contemporaneamente. 

Inoltre, è possibile garantire un front-end a una zona specifica. Un server front-end zonale condivide il destino con la rispettiva zona ed è servito solo da un'infrastruttura dedicata in una singola zona.

Il bilanciamento del carico tra zone è disponibile per il pool back-end e qualsiasi risorsa di macchina virtuale in una rete virtuale può far parte di un pool back-end.

Consultare [per ulteriori dettagli sulle abilità associate alle zone di disponibilità](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a> Diagnostica

Load Balancer Standard fornisce le metriche multidimensionali tramite il Monitoraggio di Azure.  Queste metriche possono essere filtrate, raggruppate e suddivise in una determinata dimensione.  Forniscono informazioni dettagliate sulle prestazioni presenti e passate e sullo stato di integrità del servizio.  Integrità risorse di Azure è supportato.  Ecco di seguito una breve panoramica della diagnostica supportata:

| Metrica | DESCRIZIONE |
| --- | --- |
| Disponibilità IP virtuale | Load Balancer Standard esercita continuamente il percorso dati dall'interno di un'area al front-end di Load Balancer e infine allo stack SDN che supporta la macchina virtuale. Finché sono presenti istanze integre, la misurazione segue lo stesso percorso del traffico con bilanciamento del carico dell'applicazione. Viene anche convalidato il percorso dati usato dai clienti. La misurazione è invisibile all'applicazione e non interferisce con altre operazioni.|
| Disponibilità DIP | Load Balancer Standard usa un servizio di probe dell'integrità distribuito che monitora l'integrità dell'endpoint dell'applicazione in base alle impostazioni di configurazione. Questa metrica offre una visualizzazione filtrata, aggregata o per endpoint di ogni singolo endpoint dell'istanza nel pool di Load Balancer.  In questo modo è possibile visualizzare l'integrità dell'applicazione rilevata da Load Balancer, in base alla configurazione del probe di integrità.
| Pacchetti SYN | Load Balancer Standard non termina le connessioni TCP, né interagisce con i flussi di pacchetti TCP o UDP. I flussi e i relativi handshake sono sempre tra l'origine e l'istanza VM. Per risolvere meglio i problemi degli scenari del protocollo TCP, è possibile usare contatori di pacchetti SYN per determinare quanti tentativi di connessione TCP vengono eseguiti. La metrica indica il numero di pacchetti SYN TCP ricevuti.|
| Connessioni SNAT | Load Balancer Standard segnala il numero di flussi in uscita mascherati per il front-end dell'indirizzo IP pubblico. Le porte SNAT sono una risorsa esauribile. Questa metrica può indicare l'uso che l'applicazione fa di SNAT per i flussi originati in uscita.  Vengono segnalati i contatori per i flussi SNAT con esito positivo e negativo, che è possibile usare per risolvere i problemi e comprendere l'integrità dei flussi in uscita.|
| Contatori di byte | Load Balancer Standard restituisce i dati elaborati per ogni front-end.|
| Contatori di pacchetti | Load Balancer Standard restituisce i pacchetti elaborati per ogni front-end.|

Consultare [per ulteriori dettagli sulla diagnostica di Load Balancer Standard](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>Porte a disponibilità elevata

Load Balancer Standard supporta un nuovo tipo di norma.  

È possibile configurare regole di bilanciamento del carico per ridimensionare l'applicazione e aumentarne l'affidabilità. Quando si usa una regola di bilanciamento del carico con porte a disponibilità elevata, Load Balancer Standard fornisce il bilanciamento del carico per ogni flusso su tutte le porte temporanee dell'indirizzo IP di un front-end interno di Load Balancer Standard.  La funzionalità è utile per altri scenari in cui specificare le singole porte è poco pratico o non opportuno.

Una regola di bilanciamento del carico con porte a disponibilità elevata consente di creare scenari attivo-passivo o attivo-attivo n+1 per appliance virtuali di rete e qualsiasi applicazione che richieda ampi intervalli di porte in ingresso.  Un probe di integrità può essere utilizzato per determinare quali back-end dovrebbero ricevere nuovi flussi.  È possibile utilizzare un gruppo di sicurezza di rete per emulare uno scenario di intervallo di porte.

>[!IMPORTANT]
> Se si intende usare un dispositivo di rete virtuale, contattare il fornitore per informazioni aggiuntive oppure per sapere se il prodotto è stato testato con le porte a disponibilità elevata e, quindi, seguire le linee guida specifiche per l'implementazione. 

Consultare [per ulteriori dettagli su porte a disponibilità elevata](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Protezione per impostazione predefinita

Load Balancer Standard è completamente caricato per la rete virtuale.  La rete virtuale è una rete privata, chiusa.  Poiché Load Balancer Standard e gli indirizzi IP pubblici standard sono progettati per consentire l’accesso a questa rete virtuale dall'esterno, queste risorse per impostazione predefinita sono chiuse a meno che non vengono aperte. Ciò significa i gruppi di sicurezza di rete vengono utilizzati per permettere e consentire in modo esplicito il traffico consentito.  È possibile creare l'intero data virtual center e decidere tramite il gruppo di sicurezza di rete quando e quali dati devono essere disponibili.  Se non si ha un gruppo di sicurezza di rete su una subnet o sulla scheda di rete della risorsa macchina virtuale, non è consentito al traffico di raggiungere questa risorsa.

Per altre informazioni sui gruppi di sicurezza di rete e su come applicarli allo scenario, vedere [Gruppi di sicurezza di rete](../virtual-network/security-overview.md).

### <a name="outbound"></a> Connessioni in uscita

Load Balancer supporta scenari in ingresso e in uscita.  Load Balancer Standard presenta differenze sostanziali rispetto a Load Balancer Basic per quanto riguarda le connessioni in uscita.

Origine rete indirizzo traduzione (SNAT) viene utilizzato per eseguire il mapping di indirizzi IP interni e privati nella rete virtuale per gli indirizzi IP pubblici nel server front-end di Load Balancer.

Load Balancer Standard introduce un nuovo algoritmo per un [algoritmo SNAT più solido, scalabile e stimabile](load-balancer-outbound-connections.md#snat) e abilita nuove funzionalità, rimuove ambiguità e forza esplicite configurazioni piuttosto che effetti collaterali. Queste modifiche sono necessarie per consentire alle nuove funzionalità di emergere. 

Di seguito, i concetti chiave da ricordare quando si lavora con Load Balancer Standard:

- Il completamento di una regola guida le risorse di Load Balancer.  Tutti i programmatori di Azure derivano dalla relativa configurazione.
- Quando sono disponibili più server front-end, tutti vengono utilizzati e ognuno moltiplicata il numero di porte SNAT disponibili
- È possibile selezionare e controllare che un server front-end specifico non venga utilizzato per le connessioni in uscita, se lo si desidera.
- Gli scenari in uscita sono espliciti e non esiste connettività in uscita fino a quando non è stata specificata.
- Le regole di bilanciamento del carico deducono il modo in cui SNAT è programmato. Le regole di bilanciamento del carico sono specifiche del protocollo. SNAT è specifico del protocollo e la configurazione deve riflettere questa situazione, anziché creare un effetto collaterale.

#### <a name="multiple-frontends"></a>Più front-end
Se si desiderano più porte SNAT perché è previsto o è già presente un numero elevato di richieste per le connessioni in uscita, è possibile aggiungere anche l'inventario di porta incrementale SNAT tramite la configurazione di ulteriori server front-end, regole e pool back-end per le stesse risorse della macchina virtuale.

#### <a name="control-which-frontend-is-used-for-outbound"></a>Controllo di quale front-end viene utilizzato in uscita
Se si intende vincolare le connessioni in uscita in modo che provengano solo da un indirizzo IP front-end specifico, è possibile disabilitare facoltativamente SNAT in uscita per la regola che esprime il mapping in uscita.

#### <a name="control-outbound-connectivity"></a>Controllo della connettività in uscita
Load Balancer Standard esiste all'interno del contesto della rete virtuale.  Una rete virtuale è una rete isolata privata.  A meno che non esista un'associazione con un indirizzo IP pubblico, la connettività pubblica non è consentita.  È possibile contattare [gli endpoint di servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) perché sono dentro e in locale alla rete virtuale.  Se si desidera stabilire la connettività in uscita verso una destinazione esterna alla rete virtuale, sono disponibili due opzioni:
- assegnare un indirizzo IP pubblico SKU Standard come un indirizzo IP pubblico a livello di istanza per la risorsa di macchina virtuale o
- inserire la risorsa di macchina virtuale nel pool back-end del Load Balancer Standard pubblico.

Entrambe consentiranno la connettività in uscita dalla rete virtuale verso l'esterno della rete virtuale. 

Se si dispone _solo_ di un Load Balancer Standard interno associato al pool back-end in cui si trova la risorsa di macchina virtuale, la macchina virtuale può solamente raggiungere le risorse di rete virtuale e [gli endpoint di servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md).  È possibile seguire i passaggi descritti nel paragrafo precedente per creare una connettività in uscita.

Connettività in uscita di una risorsa di macchina virtuale non associata con SKU Standard rimane come prima.

Verificare la [discussione approfondita di Connessioni in uscita](load-balancer-outbound-connections.md).

### <a name="multife"></a>Front-end multipli
Load Balancer supporta più regole con più front-end.  Load Balancer Standard ne consente l’espansione agli scenari in uscita.  Gli scenari in uscita sono essenzialmente l'inverso di una regola di bilanciamento del carico in ingresso.  Anche la regola di bilanciamento del carico in ingresso crea un collegamento per le connessioni in uscita. Load Balancer Standard usa tutti i front-end associati a una risorsa di macchina virtuale tramite una regola di bilanciamento del carico.  Inoltre, un parametro della regola di bilanciamento del carico consente di eliminare una regola di bilanciamento del carico allo scopo di favorire la connettività in uscita, il che consente la selezione di front-end specifici e persino di nessun front-end.

In confronto, Load Balancer Basic consente di selezionare un unico server front-end in modo casuale e non consente di controllare il server selezionato.

Verificare la [discussione approfondita di Connessioni in uscita](load-balancer-outbound-connections.md).

### <a name="operations"></a> Operazioni di gestione

Le risorse di Load Balancer Standard sono presenti in una piattaforma infrastrutturale completamente nuova.  Ciò consente operazioni di gestione più veloci per gli SKU Standard e i tempi di completamento sono in genere inferiori a 30 secondi per ogni risorsa SKU Standard.  Man mano che i pool back-end aumentano di dimensione, aumenta anche il tempo necessario per le modifiche del pool back-end.

È possibile modificare le risorse di Load Balancer Standard e spostare un indirizzo IP pubblico Standard da una macchina virtuale a un altro molto più veloce.

## <a name="migration-between-skus"></a>Migrazione tra SKU

Gli SKU non sono modificabili. Seguire i passaggi di questa sezione per passare da uno SKU di risorsa a un altro.

>[!IMPORTANT]
>Leggere tutto questo documento per comprendere le differenze tra SKU ed esaminare attentamente il proprio scenario.  Potrebbe essere necessario apportare altre modifiche in base allo scenario.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrare dallo SKU Basic a Standard

1. Creare una nuova risorsa Standard, Load Balancer o IP pubblico in base alle esigenze. Ricreare le regole e le definizioni dei probe.  Se in precedenza si è usato un probe TCP su 443/tcp, valutare l'opportunità di sostituirlo con un probe HTTPS e aggiungere un percorso.

2. Creare un nuovo gruppo di sicurezza di rete o aggiornare quello esistente per la NIC o la subnet per avere un elenco elementi consentiti relativo a probe e traffico con carico bilanciato, nonché per qualsiasi altro tipo di traffico che si vuole consentire.

3. Rimuovere le risorse SKU Basic (Load Balancer e IP pubblici, secondo il caso) da tutte le istanze VM. Assicurarsi di rimuovere anche tutte le istanze VM di un set di disponibilità.

4. Collegare tutte le istanze di macchina virtuale alle nuove risorse SKU Standard.

### <a name="migrate-from-standard-to-basic-sku"></a>Migrare dallo SKU Standard a Basic

1. Creare una nuova risorsa Basic, Load Balancer o IP pubblico in base alle esigenze. Ricreare le regole e le definizioni dei probe.  Sostituire un probe HTTPS con un probe TCP su 443/tcp. 

2. Rimuovere le risorse SKU Standard (Load Balancer e IP pubblici, secondo il caso) da tutte le istanze VM. Assicurarsi di rimuovere anche tutte le istanze VM di un set di disponibilità.

3. Collegare tutte le istanze di macchina virtuale alle nuove risorse SKU Basic.

>[!IMPORTANT]
>
>Esistono limitazioni relative all'uso degli SKU Basic e Standard.
>
>Le porte a disponibilità elevata e la diagnostica dello SKU Standard sono disponibili unicamente con lo SKU Standard. Non è possibile eseguire la migrazione da SKU Standard a SKU Basic e mantenere queste funzionalità.
>
>Gli SKU Basic e quelli Standard hanno numerose differenze3, come indicato nel presente articolo.  Assicurarsi di averne la più completa comprensione per capire come gestirli.
>
>Per le risorse di Load Balancer e IP pubblico è necessario usare SKU corrispondenti. Non è possibile avere una combinazione di risorse SKU Basic e risorse SKU Standard. Non è possibile collegare le macchine virtuali autonome, le macchine virtuali in una risorsa del set di disponibilità, o una risorsa di un set di scalabilità di macchina virtuale per entrambi gli SKU contemporaneamente.

## <a name="region-availability"></a>Aree di disponibilità

Load Balancer Standard è attualmente disponibile in tutte le aree di cloud pubblico.

## <a name="sla"></a>Contratto di servizio

I Load Balancer Standard sono disponibili con un contratto di servizio del 99,99%.  Rivedere il [contratto di servizio di Load Balancer Standard](https://aka.ms/lbsla) per informazioni dettagliate.

## <a name="pricing"></a>Prezzi

Il costo dell'utilizzo del servizio Load Balancer Standard viene addebitato in base ai criteri seguenti.

- Numero di regole di bilanciamento del carico e in uscita configurate (le regole NAT in ingresso non vengono conteggiate ai fini del numero totale di regole)
- Quantità di dati elaborati in ingresso e in uscita indipendentemente dalla regola. 

Per informazioni sui prezzi di Load Balancer Standard, visitare la pagina [Prezzi del servizio Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="limitations"></a>Limitazioni

- Gli SKU non sono modificabili. Non è possibile modificare lo SKU di una risorsa esistente.
- Una risorsa autonoma per macchine virtuali, una risorsa per un set di disponibilità o una risorsa per un set di scalabilità di macchine virtuali può essere riferita a uno SKU, non a entrambi.
- Una regola di Load Balancer non può estendersi a due reti virtuali.  I front-end e le relative istanze di back-end devono trovarsi nella stessa rete virtuale.  
- I front-end di Load Balancer non sono accessibili tramite peering di rete virtuale globale.
- Le [operazioni di spostamento delle sottoscrizioni](../azure-resource-manager/resource-group-move-resources.md) non sono supportate per le risorse LB e PIP dello SKU Standard.
- I ruoli di lavoro Web senza una rete virtuale e altri servizi della piattaforma Microsoft possono essere accessibili quando si usa solo Load Balancer Standard interno a causa di un effetto collaterale della modalità di funzionamento dei servizi pre-VNet e altre piattaforme di servizi. Non è necessario basarsi su questo poiché il servizio stesso o la piattaforma sottostante possono essere soggetti a modifiche senza preavviso. Presupporre sempre che è necessario creare una [connettività in uscita](load-balancer-outbound-connections.md) in modo esplicito se desiderato quando si usa solo Load Balancer Standard interno.
- Load Balancer è un prodotto TCP o UDP per il bilanciamento del carico e il port forwarding per questi protocolli IP specifici.  Le regole di bilanciamento del carico e le regole NAT in ingresso sono supportate per TCP e UDP e non per altri protocolli IP, tra cui ICMP. Load Balancer non termina, non risponde o non interagisce in altro modo con il payload di un flusso UDP o TCP. Non è un proxy. La convalida della connettività a un front-end deve avvenire in banda con lo stesso protocollo usato in un bilanciamento del carico o in una regola NAT in ingresso (TCP o UDP) _e_ almeno una delle macchine virtuali deve generare una risposta affinché un client possa vedere una risposta da un front-end.  La mancata ricezione di una risposta in banda da parte del front-end di Load Balancer indica che nessuna macchina virtuale è stata in grado di rispondere.  Non è possibile interagire con un front-end di Load Balancer senza una macchina virtuale in grado di rispondere.  Questo vale anche per le connessioni in uscita in cui lo [SNAT di mascheramento delle porte](load-balancer-outbound-connections.md#snat) è supportato solo per TCP e UDP. Qualsiasi altro protocollo IP, incluso ICMP, non funziona.  Assegnare un indirizzo IP pubblico a livello di istanza per la mitigazione.
- A differenza dei servizi di bilanciamento del carico pubblici che forniscono [connessioni in uscita](load-balancer-outbound-connections.md) quando si passa da indirizzi IP privati all'interno della rete virtuale a indirizzi IP pubblici, i servizi di bilanciamento del carico interni non convertono le connessioni originate in uscita nel front-end di un servizio di bilanciamento del carico perché entrambi si trovano in uno spazio indirizzi IP privato.  In questo modo si evita il rischio di esaurimento SNAT all'interno di uno spazio indirizzi IP interno univoco in cui non è richiesta la conversione.  L'effetto collaterale è che se un flusso in uscita da una VM nel pool di back-end tenta di raggiungere il front-end del servizio di bilanciamento del carico interno in cui risiede il pool _e_ viene rimappato su se stesso, i due lati del flusso non corrispondono e il flusso avrà esito negativo.  Se il flusso non è stato rimappato sulla stessa VM del pool di back-end che ha creato il flusso verso il front-end, il flusso avrà esito positivo.   Quando il flusso viene rimappato su se stesso, il flusso in uscita sembra provenire dalla VM verso il front-end e il flusso in ingresso corrispondente sembra provenire dalla VM verso se stessa. Dal punto di vista del sistema operativo guest, le parti in ingresso e in uscita dello stesso flusso non corrispondono all'interno della macchina virtuale. Lo stack TCP non riconoscerà queste metà del flusso come facenti parte dello stesso flusso perché l'origine e la destinazione non corrispondono.  Quando il flusso viene mappato su qualsiasi altra VM nel pool di back-end, le metà del flusso corrispondono e la VM può rispondere correttamente al flusso stesso.  Il sintomo di questo scenario è il timeout intermittente della connessione. Esistono diverse soluzioni alternative comuni per ottenere questo scenario in modo affidabile (origine dei flussi da un pool back-end verso i pool back-end o il front-end del servizio di bilanciamento del carico interno) che includono l'inserimento di un proxy di terze parti dietro il servizio di bilanciamento del carico interno oppure l'[uso di regole in stile DSR](load-balancer-multivip-overview.md).  Anche se è possibile usare un servizio di bilanciamento del carico pubblico per la mitigazione, lo scenario risultante è soggetto a [esaurimento SNAT](load-balancer-outbound-connections.md#snat) e deve essere evitato a meno che non venga gestito con attenzione.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull'uso di [Load Balancer Standard e zone di disponibilità](load-balancer-standard-availability-zones.md).
- Informazioni sui [probe di integrità](load-balancer-custom-probe-overview.md).
- Altre informazioni sulle [zone di disponibilità](../availability-zones/az-overview.md).
- Altre informazioni sulla diagnostica per [Azure Load Balancer Standard](load-balancer-standard-diagnostics.md).
- Altre informazioni sulle [metriche multidimensionali supportate](../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) per la diagnostica in [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview.md).
- Informazioni sull'uso di [Load Balancer per le connessioni in uscita](load-balancer-outbound-connections.md).
- Altre informazioni sulle [Regole in uscita](load-balancer-outbound-rules-overview.md).
- Informazioni su [Reimpostare TCP in caso di inattività](load-balancer-tcp-reset.md).
- Informazioni su [Load Balancer Standard con regole di bilanciamento del carico di porte a disponibilità elevata](load-balancer-ha-ports-overview.md).
- Informazioni sull'uso di [Load Balancer con più front-end](load-balancer-multivip-overview.md).
- Informazioni sulle [reti virtuali](../virtual-network/virtual-networks-overview.md).
- Vedere altre informazioni sui [gruppi di sicurezza di rete](../virtual-network/security-overview.md).
- Informazioni sugli [endpoint del servizio Rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md).
- Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.
- Vedere altre informazioni su [Azure Load Balancer](load-balancer-overview.md).
