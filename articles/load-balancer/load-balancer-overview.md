---
title: Panoramica di Azure Load Balancer | Microsoft Docs
description: Panoramica di funzionalità, architettura e implementazione di Azure Load Balancer. Informazioni sul funzionamento del servizio di bilanciamento del carico e su come usarlo nel cloud.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: kumud
ms.openlocfilehash: f4410932f00f8505ae5a894caa002e1223196d95
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2018
---
# <a name="azure-load-balancer-overview"></a>Panoramica del servizio di bilanciamento del carico

Azure Load Balancer consente di ridimensionare le applicazioni e di creare disponibilità elevata per i servizi. Load Balancer supporta scenari in ingresso e in uscita, offre bassa latenza, velocità effettiva elevata e una scalabilità fino a milioni di flussi per tutte le applicazioni TCP e UDP.   

Load Balancer distribuisce i nuovi flussi in ingresso che arrivano sul front-end di Load Balancer nelle istanze del pool back-end in base alle regole e ai probe di integrità.  

In aggiunta il bilanciamento del carico pubblico può anche offrire connessioni in uscita per le macchine virtuali all'interno della rete virtuale convertendo gli indirizzi IP privati in indirizzi IP pubblici.

Azure Load Balancer è disponibile in due SKU diversi: Basic e Standard,  con differenze di scalabilità, funzionalità e prezzo.  Tutti gli scenari possibili con Load Balancer Basic possono essere creati anche con Load Balancer Standard, anche se l'approccio può essere leggermente diverso.  Man mano che si apprendono nuove cose su Load Balancer, è importante acquisire familiarità con le caratteristiche principali e le differenze specifiche per SKU.

## <a name="why-use-load-balancer"></a>Perché usare Azure Load Balancer 

Azure Load Balancer può essere usato per:

* Bilanciare il carico del traffico Internet in ingresso nelle macchine virtuali. Questa configurazione è nota come [bilanciamento del carico pubblico](#publicloadbalancer).
* Bilanciare il carico del traffico tra le macchine virtuali all'interno di una rete virtuale. È anche possibile raggiungere un front-end di Load Balancer da una rete locale in uno scenario ibrido. Entrambi gli scenari usano una configurazione nota come [bilanciamento del carico interno](#internalloadbalancer).
* Inoltrare il traffico a una porta specifica su specifiche macchine virtuali con le regole NAT in ingresso.
* Offrire [connettività in uscita](load-balancer-outbound-connections.md) per le macchine virtuali all'interno della rete virtuale usando il bilanciamento del carico pubblico.


>[!NOTE]
> Azure offre una suite di soluzioni di bilanciamento del carico completamente gestite per tutti gli scenari.  Se si desidera terminare TLS ("offload SSL") o elaborare a livello di applicazione per richiesta HTTP/HTTPS, esaminare l'articolo relativo al [gateway applicazione](../application-gateway/application-gateway-introduction.md).  Se si desidera il bilanciamento del carico DNS globale, esaminare l'articolo relativo a [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Gli scenari end-to-end potrebbero trarre vantaggio dalla combinazione di queste soluzioni in base alle esigenze.

## <a name="what-is-load-balancer"></a>Che cos'è il servizio di bilanciamento del carico?

Una risorsa di Load Balancer può esistere come bilanciamento del carico pubblico o interno. Le funzioni della risorsa di Load Balancer sono espresse come definizione di front-end, regola, probe di integrità e pool back-end.  Le macchine virtuali vengono inserite nel pool back-end specificando il pool back-end nella macchina virtuale.

Le risorse di Load Balancer sono oggetti all'interno dei quali è possibile esprimere in che modo Azure deve programmare la propria infrastruttura multi-tenant per ottenere lo scenario che si vuole creare.  Non esiste una relazione diretta tra le risorse di Load Balancer e l'infrastruttura effettiva; la creazione di un bilanciamento del carico non crea un'istanza e la capacità è sempre disponibile. 

## <a name="fundamental-load-balancer-features"></a>Funzionalità principali di Load Balancer

Load Balancer offre le funzionalità principali seguenti per applicazioni TCP e UDP:

* **Bilanciamento del carico**

    Azure Load Balancer consente di creare una regola di bilanciamento del carico per distribuire il traffico in arrivo presso un front-end nelle istanze del pool back-end.  Usa un algoritmo basato su hash per distribuire i flussi in ingresso e riscrive di conseguenza le intestazioni dei flussi nelle istanze del pool back-end. Un server è disponibile per ricevere i nuovi flussi quando il probe di integrità indica un endpoint di back-end integro.
    
    Per impostazione predefinita, usa un hash a 5 tuple composto da indirizzo IP di origine, porta di origine, indirizzo IP di destinazione, porta di destinazione e numero di protocollo IP per eseguire il mapping dei flussi nei server disponibili.  È possibile scegliere di creare affinità per uno specifico indirizzo IP di origine scegliendo un hash a 2 o a 3 tuple per una determinata regola.  Tutti i pacchetti dello stesso flusso di pacchetti arrivano sulla stessa istanza dietro il front-end con carico bilanciato.  Quando il client avvia un nuovo flusso dallo stesso indirizzo IP di origine, la porta di origine cambia. L'hash a 5 tuple risultante può comportare l'indirizzamento del traffico a un endpoint back-end diverso.

    Per altre informazioni, vedere [Modalità di distribuzione del bilanciamento del carico](load-balancer-distribution-mode.md). Il grafico seguente illustra la distribuzione basata su hash:

    ![Distribuzione basata su hash](./media/load-balancer-overview/load-balancer-distribution.png)

    *Figura: distribuzione basata su hash*

* **Port forwarding**

    Azure Load Balancer consente di creare una regola NAT in ingresso per inoltrare il traffico di una porta specifica di un determinato indirizzo IP front-end a una porta specifica di una determinata istanza di back-end all'interno della rete virtuale. Ciò avviene anche tramite la stessa distribuzione basata su hash del bilanciamento del carico.  Scenari comuni per questa funzionalità sono le sessioni di Remote Desktop Protocol (RDP) o Secure Shell (SSH) per le singole istanze della macchina virtuale all'interno della rete virtuale.  È possibile eseguire il mapping di più endpoint interni per diverse porte sullo stesso indirizzo IP front-end. È possibile usarli per amministrare in remoto le macchine virtuali in Internet senza la necessità di un jumpbox aggiuntivo.

* **Applicazione indipendente e trasparente**

    Load Balancer non interagisce direttamente con TCP o UDP o con il livello dell'applicazione e può supportare qualsiasi scenario dell'applicazione basato su TCP o UDP.  Ad esempio, sebbene Load Balancer non termini TLS direttamente, è possibile compilare e ridimensionare le applicazioni TLS usando Load Balancer e terminare la connessione TLS nella macchina virtuale stessa. Load Balancer non termina un flusso e gli handshake del protocollo si trovano sempre direttamente tra il client e l'istanza del pool back-end selezionata dall'hash. Ad esempio, un handshake TCP si trova sempre tra il client e la macchina virtuale di back-end selezionata.  Una risposta a una richiesta in un front-end è una risposta generata dalla macchina virtuale di back-end.  Le prestazioni di rete in uscita di Load Balancer sono limitate solo dallo SKU scelto della macchina virtuale e i flussi rimarranno attivi per lunghi periodi di tempo, se non si raggiunge mai il timeout di inattività.

* **Riconfigurazione automatica**

    Il servizio di bilanciamento del carico di Azure si riconfigura immediatamente quando si aumentano o si riducono le istanze. L'aggiunta o la rimozione di macchine virtuali dal pool back-end riconfigura il bilanciamento del carico senza operazioni aggiuntive sulla risorsa di Load Balancer.

* **Probe di integrità**

    Azure Load Balancer usa i probe di integrità definiti dall'utente per determinare l'integrità delle istanze nel pool back-end. Se un probe non risponde, il servizio di bilanciamento del carico interrompe l'invio di nuove connessioni alle istanze non integre. Le connessioni esistenti non sono interessate e continueranno fino a quando l'applicazione termina il flusso, si verifica un timeout di inattività o la macchina virtuale viene arrestata.

    Sono supportati tre tipi di probe:

    - **Probe HTTP personalizzato:** è possibile usarlo per creare la propria logica personalizzata e determinare l'integrità dell'istanza del pool back-end. Il servizio di bilanciamento del carico eseguirà regolarmente il probe dell'endpoint (per impostazione predefinita, ogni 15 secondi). L'istanza è considerata integra se risponde con HTTP 200 entro il periodo di timeout, che per impostazione predefinita è di 31 secondi. Qualsiasi stato diverso da HTTP 200 provoca un esito negativo del probe.  Questo è utile anche per implementare la propria logica per la rimozione delle istanze dalla rotazione del bilanciamento del carico. È possibile, ad esempio, configurare l'istanza in modo che restituisca uno stato diverso da 200 se l'istanza usa più del 90% della CPU.   Questo probe esegue l'override del probe dell'agente guest predefinito.

    - **Probe TCP personalizzato:** questo probe si basa sulla corretta attivazione di una sessione TCP in una porta probe definita.  Fino a quando esiste il listener specificato nella macchina virtuale, questo probe avrà esito positivo. Se la connessione viene rifiutata, il probe avrà esito negativo. Questo probe esegue l'override del probe dell'agente guest predefinito.

    - **Probe dell'agente guest (solo su macchine virtuali Platform as a Service):** il bilanciamento del carico può usare anche l'agente guest all'interno della macchina virtuale. L'agente guest è in ascolto e risponde con HTTP 200 OK solo quando l'istanza è in stato Pronto. Se l'agente non risponde con un messaggio HTTP 200 OK, il servizio di bilanciamento del carico contrassegna l'istanza che non risponde e interrompe l'invio di traffico a tale istanza. Il bilanciamento del carico continua a tentare di raggiungere l'istanza. Se l'agente guest risponde con HTTP 200, il servizio di bilanciamento del carico invierà ancora il traffico a tale istanza.  I probe dell'agente guest rappresentano l'ultima possibilità e non devono essere usati quando è possibile applicare le configurazioni del probe HTTP o TCP personalizzate. 
    
* **Connessioni in uscita (Source NAT)**

    Tutti i flussi in uscita dagli indirizzi IP privati all'interno della rete virtuale verso gli indirizzi IP pubblici in Internet possono essere convertiti in indirizzi IP front-end di Load Balancer. Quando un front-end pubblico è associato a una macchina virtuale back-end tramite una regola di bilanciamento del carico, Azure programma le connessioni in uscita da convertire automaticamente nell'indirizzo IP front-end pubblico. Questa operazione viene detta anche Source NAT (SNAT). Questo processo offre tre importanti vantaggi:

    + Consente di eseguire in modo semplice l'aggiornamento e il ripristino di emergenza dei servizi, perché è possibile eseguire il mapping dinamico del front-end in un'altra istanza del servizio.
    + Semplifica la gestione dell'elenco di controllo di accesso (ACL). Gli ACL espressi in termini di indirizzi IP front-end non si modificano in caso di ridimensionamento o ridistribuzione dei servizi.

    Fare riferimento all'articolo sulle [connessioni in uscita](load-balancer-outbound-connections.md) per una descrizione dettagliata di questa capacità.

Load Balancer Standard dispone di capacità specifiche dello SKU aggiuntive oltre alle capacità principali.  Esaminare la restante parte di questo articolo per informazioni dettagliate.

## <a name="skus"></a> Confronto tra gli SKU di Load Balancer

Azure Load Balancer supporta due SKU diversi: Basic e Standard,  con differenze di scalabilità, funzionalità e prezzo.  Tutti gli scenari possibili con Load Balancer Basic possono essere creati anche con Load Balancer Standard.  In effetti, le API per entrambi gli SKU sono simili e vengono richiamate tramite la specifica di uno SKU.  L'API per supportare gli SKU di Load Balancer e l'indirizzo IP pubblico sono disponibili a partire dall'API 2017-08-01.  Entrambi gli SKU hanno la stessa API e la stessa struttura generale.

Tuttavia a seconda dello SKU scelto, i dettagli di configurazione dello scenario completo potrebbero essere leggermente diversi. La documentazione di Load Balancer specifica quando un articolo è applicabile solo a un determinato SKU. Esaminare la tabella seguente per confrontare e comprendere le differenze.  Rivedere [Panoramica di Load Balancer Standard](load-balancer-standard-overview.md) per altri dettagli.

>[!NOTE]
> Per i nuovi progetti è consigliabile prendere in considerazione l'uso di Load Balancer Standard. 

Le macchine virtuali autonome, i set di disponibilità e i set di scalabilità di macchine virtuali possono essere collegati solo a uno SKU, non a entrambi. Se usati con gli indirizzi IP pubblici, lo SKU di Load Balancer e quello dell'indirizzo IP pubblico devono corrispondere. Gli SKU di Load Balancer e quelli degli indirizzi IP pubblici non sono modificabili.

_È consigliabile specificare gli SKU in modo esplicito, anche se non è ancora obbligatorio._  In questo momento le modifiche necessarie vengono mantenute al minimo. Se non si specifica lo SKU, viene interpretata l'intenzione di usare lo SKU Basic nella versione API 2017-08-01.

>[!IMPORTANT]
>Load Balancer Standard è un nuovo prodotto di Load Balancer e in larga misura un soprainsieme di Load Balancer Basic.  Tra i due prodotti ci sono differenze importanti e valutate attentamente.  Tutti gli scenari end-to-end possibili con Load Balancer Basic possono essere creati con Load Balancer Standard.  Se si conosce già Load Balancer Basic, è consigliabile acquisire familiarità con Load Balancer Standard per comprendere le modifiche più importanti nel comportamento tra la versione Standard e Basic e il relativo impatto. Leggere attentamente questa sezione.

| | [SKU standard](load-balancer-standard-overview.md) | SKU Basic |
| --- | --- | --- |
| Dimensioni del pool back-end | fino a 1000 istanze | fino a 100 istanze |
| Endpoint di pool back-end | qualsiasi macchina virtuale in una sola rete virtuale, tra cui un insieme di macchine virtuali, set di disponibilità, set di scalabilità di macchine virtuali. | macchine virtuali in un singolo set di disponibilità o in un set di scalabilità di macchine virtuali |
| Zone di disponibilità | front-end con ridondanza della zona e front-end di zona per connessioni in entrata e in uscita, mapping di flussi in uscita che superano l'errore di zona, bilanciamento del carico tra più zone | / |
| Diagnostica | Monitoraggio di Azure, metriche multidimensionali, inclusi contatori di byte e pacchetti, stato del probe di integrità, tentativi di connessione (TCP SYN), integrità della connessione in uscita (flussi SNAT riusciti e non), misurazioni del piano dati attivo | Log Analytics di Azure solo per il bilanciamento del carico pubblico, avviso di esaurimento SNAT, conteggio integrità del pool back-end |
| Porte a disponibilità elevata | bilanciamento del carico interno | / |
| Protezione per impostazione predefinita | è necessario usare endpoint predefiniti chiusi per Load Balancer e indirizzi IP pubblici e un gruppo di sicurezza di rete da inserire nell'elenco elementi consentiti in modo esplicito e consentire il traffico | gruppo di sicurezza di rete aperto predefinito facoltativo |
| Connessioni in uscita | Front-end multipli con rifiuto esplicito in base alla regola. È _necessario_ creare in modo esplicito uno scenario in uscita affinché la macchina virtuale sia in grado di usare la connettività in uscita.  [Gli endpoint del servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) possono essere raggiunti senza connettività in uscita e non vengono considerati relativamente ai dati elaborati.  Tutti gli indirizzi IP pubblici, inclusi i servizi PaaS di Azure non disponibili come endpoint del servizio di rete virtuale, devono essere raggiunti tramite connettività in uscita e vengono considerati relativamente ai dati elaborati. Quando una macchina virtuale viene servita solo da un bilanciamento del carico interno, le connessioni in uscita tramite SNAT predefinito non sono disponibili. La programmazione SNAT in uscita è il protocollo di trasporto specifico basato sul protocollo della regola di bilanciamento del carico in ingresso. | Front-end singolo selezionato in modo casuale quando sono presenti più front-end.  Quando una macchina virtuale viene servita solo da un bilanciamento del carico interno, viene usato lo SNAT predefinito. |
| Più front-end | In ingresso e in uscita | Solo in ingresso |
| Operazioni di gestione | La maggior parte delle operazioni < 30 secondi | in genere 60-90+ secondi |
| Contratto di servizio | 99,99% per il percorso dei dati con due macchine virtuali integre | Implicito nel contratto di servizio della macchina virtuale | 
| Prezzi | Addebito in base al numero di regole e dati elaborati in ingresso o in uscita associati alla risorsa  | Nessun addebito |

Rivedere [i limiti di servizio del bilanciamento del carico](https://aka.ms/lblimits).  Per Load Balancer Standard esaminare anche la [panoramica](load-balancer-standard-overview.md), i [prezzi](https://aka.ms/lbpricing) e [il contratto di servizio](https://aka.ms/lbsla) in modo più dettagliato.

## <a name="concepts"></a>Concetti

### <a name = "publicloadbalancer"></a>Bilanciamento del carico pubblico

Il bilanciamento del carico pubblico esegue il mapping dell'indirizzo IP pubblico e del numero di porta del traffico in ingresso all'indirizzo IP privato e al numero di porta della macchina virtuale e viceversa per il traffico di risposta proveniente dalla macchina virtuale. Le regole di bilanciamento del carico consentono di distribuire tipi di traffico specifici tra più macchine virtuali o servizi. È ad esempio possibile dividere il carico del traffico delle richieste Web tra più server Web.

La figura seguente mostra un endpoint con carico bilanciato per il traffico Web condiviso tra tre macchine virtuali per la porta TCP 80, pubblica e privata. Queste tre macchine virtuali appartengono a un set con carico bilanciato.

![esempio di bilanciamento del carico pubblico](./media/load-balancer-overview/IC727496.png)

*Figura: bilanciamento del carico del traffico Web tramite un servizio Load Balancer pubblico*

Quando i client Internet inviano richieste di pagine Web all'indirizzo IP pubblico di un'app Web sulla porta TCP 80, Azure Load Balancer distribuisce le richieste tra le tre macchine virtuali del set con carico bilanciato. Per altre informazioni sull'algoritmo di bilanciamento di carico, vedere la [pagina di panoramica del bilanciamento di carico](load-balancer-overview.md#load-balancer-features).

Per impostazione predefinita, Azure Load Balancer distribuisce il traffico di rete in modo uniforme tra più istanze di macchine virtuali. È anche possibile configurare l'affinità di sessione. Per altre informazioni, vedere l'articolo sulla [modalità di distribuzione del bilanciamento del carico](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Bilanciamento del carico interno

Il bilanciamento del carico interno indirizza il traffico solo alle risorse che si trovano all'interno di una rete virtuale o che usano una VPN per accedere all'infrastruttura di Azure. A questo proposito, il bilanciamento del carico interno si differenzia dal bilanciamento del carico pubblico. L'infrastruttura di Azure limita l'accesso agli indirizzi IP front-end con carico bilanciato di una rete virtuale. Gli indirizzi IP front-end e le reti virtuali non sono mai esposti direttamente a un endpoint di Internet. Le applicazioni line-of-business interne vengono eseguite in Azure e sono accessibili dall'interno di Azure o da risorse locali.

Il bilanciamento del carico interno permette di bilanciare i tipi di carico seguenti:

* In una rete virtuale: bilanciamento del carico dalle macchine virtuali nella rete virtuale a un set di macchine virtuali che si trovano nella stessa rete virtuale.
* Per una rete virtuale cross-premise: bilanciamento del carico dai computer locali a un set di macchine virtuali che si trovano nella stessa rete virtuale. 
* Per le applicazioni a più livelli: bilanciamento del carico per le applicazioni multilivello e con connessione a Internet in cui i livelli di back-end non sono esposti a Internet. I livelli di back-end richiedono il bilanciamento del carico per il traffico dal livello con connessione a Internet (vedi figura 2).
* Per le applicazioni line-of-business: bilanciamento del carico per le applicazioni line-of-business ospitate in Azure senza applicazioni software o componenti hardware aggiuntivi per il bilanciamento del carico. Questo scenario include server locali che si trovano nel set di computer il cui traffico ha il carico bilanciato.

![esempio di bilanciamento del carico interno](./media/load-balancer-overview/IC744147.png)

*Figura: bilanciamento del carico di applicazioni multilivello tramite servizi di bilanciamento del carico sia pubblico che interno*

## <a name="pricing"></a>Prezzi
Load Balancer Standard è un prodotto addebitato in base al numero di regole di bilanciamento del carico configurate e a tutti i dati in ingresso e in uscita elaborati. Per informazioni sul prezzo di Load Balancer Standard, visitare la pagina [Prezzi di Bilanciamento del carico](https://azure.microsoft.com/pricing/details/load-balancer/).

Load Balancer Basic è disponibile senza alcun addebito.

## <a name="sla"></a>Contratto di servizio

Per informazioni sul contratto di servizio di Load Balancer Standard, visitare il [Contratto di Servizio per Bilanciamento del carico](https://aka.ms/lbsla). 

## <a name="next-steps"></a>Passaggi successivi

- Rivedere l'articolo contenente [maggiori informazioni sul Bilanciamento del carico Standard](load-balancer-standard-overview.md)
- Informazioni sull'uso di [Load Balancer Standard e delle zone di disponibilità](load-balancer-standard-availability-zones.md)
- Informazioni sull'uso di [Load Balancer per le connessioni in uscita](load-balancer-outbound-connections.md)
- Informazioni sulle [porte a disponibilità elevata di Load Balancer](load-balancer-ha-ports-overview.md)
- Informazioni sull'uso di [Load Balancer con front-end multipli](load-balancer-multivip-overview.md)
- Informazioni sugli [endpoint del servizio Rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md)
- Informazioni su come creare un [bilanciamento del carico Basic pubblico](load-balancer-get-started-internet-portal.md)

