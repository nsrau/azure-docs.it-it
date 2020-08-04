---
title: Concetti di Azure Load Balancer
description: Panoramica dei concetti di Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 40b738c0f074f06b2f15a260cacda876aa78daf6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87060801"
---
# <a name="azure-load-balancer-concepts"></a>Concetti di Azure Load Balancer

Load Balancer offre numerose funzionalità per le applicazioni UDP e TCP. 

## <a name="load-balancing-algorithm"></a>Algoritmo di bilanciamento del carico
È possibile creare una regola di bilanciamento del carico per distribuire il traffico dal pool front-end a un pool back-end. Azure Load Balancer usa un algoritmo hash per la distribuzione dei flussi in ingresso (non i byte). Load Balancer riscrive le intestazioni dei flussi nelle istanze del pool back-end. Un server è disponibile per ricevere i nuovi flussi quando un probe di integrità indica un endpoint di back-end integro.

Per impostazione predefinita, Load Balancer usa un hash a 5 tuple.

L'hash include:

- **Indirizzo IP di origine**
- **Porta di origine**
- **Indirizzo IP di destinazione**
- **Porta di destinazione**
- **Numero di protocollo IP per il mapping dei flussi ai server disponibili**

L'affinità con un indirizzo IP di origine viene creata tramite un hash a due o tre tuple. Tutti i pacchetti dello stesso flusso arrivano nella stessa istanza dietro il front-end con carico bilanciato. 

Quando il client avvia un nuovo flusso dallo stesso indirizzo IP di origine, la porta di origine cambia. Di conseguenza, l'hash a 5 tuple può determinare l'instradamento del traffico a un endpoint back-end diverso.
Per altre informazioni, vedere [Configurare la modalità di distribuzione per Azure Load Balancer](./load-balancer-distribution-mode.md).

L'immagine seguente mostra la distribuzione basata su hash:

![Distribuzione basata su hash](./media/load-balancer-overview/load-balancer-distribution.png)

*Figura: Distribuzione basata su hash*

## <a name="application-independence-and-transparency"></a>Indipendenza e trasparenza delle applicazioni

Load Balancer non interagisce direttamente con il protocollo TCP o UDP o con il livello dell'applicazione. Può essere supportato qualsiasi scenario di applicazione TCP o UDP. Load Balancer non chiude né genera flussi o interagisce con il payload del flusso. Load Balancer non fornisce la funzionalità del gateway del livello applicazione. Gli handshake del protocollo si verificano sempre direttamente tra il client e l'istanza del pool back-end. Una risposta a un flusso in ingresso è sempre una risposta da una macchina virtuale. Quando il flusso arriva nella macchina virtuale, viene mantenuto anche l'indirizzo IP di origine.

* A ogni endpoint risponde una macchina virtuale. Ad esempio, si verifica sempre un handshake TCP tra il client e la macchina virtuale back-end selezionata. Una risposta a una richiesta in un front-end è una risposta generata da una macchina virtuale di back-end. Quando si convalida correttamente la connettività a un front-end, si convalida anche la connettività per almeno una macchina virtuale back-end.
* I payload dell'applicazione sono trasparenti per Load Balancer. Qualsiasi applicazione UDP o TCP può essere supportata.
* Poiché Load Balancer non interagisce con il payload TCP e fornisce l'offload TLS, è possibile creare scenari crittografati completi. L'uso di Load Balancer consente di ottenere un'ampia scalabilità orizzontale per le applicazioni TLS terminando la connessione TLS nella macchina virtuale stessa. Ad esempio, la funzionalità di codifica della sessione TLS è limitata solo dal tipo e dal numero di macchine virtuali aggiunte al pool di back-end.

## <a name="outbound-connections"></a>Connessioni in uscita 

I flussi dal pool back-end agli indirizzi IP pubblici vengono mappati al front-end. Azure converte le connessioni in uscita all'indirizzo IP front-end pubblico tramite la regola di bilanciamento del carico in uscita. Questa configurazione presenta i vantaggi seguenti. Semplicità di aggiornamento e di ripristino di emergenza dei servizi, perché è possibile eseguire il mapping dinamico del front-end a un'altra istanza del servizio. Gestione semplificata degli elenchi di controllo di accesso (ACL). Gli ACL espressi in termini di indirizzi IP front-end non si modificano in caso di ridimensionamento o di ridistribuzione dei servizi. La conversione delle connessioni in uscita in un numero di indirizzi IP inferiore a quello delle macchine riduce il carico correlato all'implementazione di elenchi di destinatari sicuri. Per altre informazioni su SNAT (Source Network Address Translation) e Azure Load Balancer, vedere [SNAT e Azure Load Balancer](load-balancer-outbound-connections.md).

## <a name="availability-zones"></a>Zone di disponibilità 

Load Balancer Standard supporta funzionalità aggiuntive in aree in cui sono disponibili zone di disponibilità. Le configurazioni delle zone di disponibilità sono disponibili per il servizio Load Balancer Standard sia pubblico che interno. Un front-end con ridondanza della zona sopravvive all'errore di zona usando l'infrastruttura dedicata in tutte le zone contemporaneamente. Inoltre, è possibile garantire un front-end a una zona specifica. Un front-end di zona viene gestito dall'infrastruttura dedicata in una singola zona. Per il pool back-end è disponibile il bilanciamento del carico tra zone. Tutte le risorse macchina virtuale in una rete virtuale possono far parte di un pool back-end. Il servizio Load Balancer Basic non supporta le zone. Per altre informazioni, vedere la [discussione dettagliata sulle abilità associate alle zone di disponibilità](load-balancer-standard-availability-zones.md) e la [panoramica delle zone di disponibilità](../availability-zones/az-overview.md).

## <a name="ha-ports"></a>Porte a disponibilità elevata

È possibile configurare regole di bilanciamento del carico per le porte con disponibilità elevata per garantire la scalabilità e l'affidabilità elevata dell'applicazione. Il bilanciamento del carico per flusso sulle porte di breve durata dell'indirizzo IP front-end del servizio di bilanciamento del carico interno viene fornito da queste regole. La funzionalità è utile nei casi in cui specificare le singole porte è poco pratico o non opportuno. Una regola per le porte a disponibilità elevata consente di creare scenari con architettura attiva-passiva o attiva-attiva n+1. Questi scenari sono destinati alle appliance di rete virtuale e alle applicazioni che richiedono ampi intervalli di porte in ingresso. È possibile usare un probe di integrità per determinare i back-end che dovranno ricevere nuovi flussi.  È possibile utilizzare un gruppo di sicurezza di rete per emulare uno scenario di intervallo di porte. Il servizio Load Balancer Basic non supporta le porte a disponibilità elevata. Consultare [per ulteriori dettagli su porte a disponibilità elevata](load-balancer-ha-ports-overview.md).

## <a name="multiple-frontends"></a>Più front-end 

Load Balancer supporta più regole con più front-end.  Load Balancer Standard estende questa funzionalità agli scenari in uscita. Le regole in uscita sono l'inverso di una regola in ingresso. La regola in uscita crea un'associazione per le connessioni in uscita. Load Balancer Standard usa tutti i front-end associati a una risorsa macchina virtuale tramite una regola di bilanciamento del carico. Inoltre, un parametro della regola di bilanciamento del carico consente di eliminare una regola di bilanciamento del carico ai fini della connettività in uscita, per consentire la selezione di front-end specifici o di nessun front-end. Per il confronto, Load Balancer Basic seleziona un singolo front-end in modo casuale. Non esiste la possibilità di controllare il front-end selezionato.

## <a name="floating-ip"></a>IP mobile

Alcuni scenari di applicazione preferiscono o richiedono l'uso della stessa porta da parte di più istanze dell'applicazione in una singola macchina virtuale nel pool back-end. Esempi comuni di riutilizzo delle porte includono il clustering per la disponibilità elevata, le appliance virtuali di rete e l'esposizione di più endpoint TLS senza rieseguire la crittografia di rete. Per riutilizzare la porta di back-end tra più regole, è necessario abilitare l'indirizzo IP mobile nella definizione della regola.

Il termine **IP mobile** appartiene alla terminologia di Azure e fa riferimento a una parte della cosiddetta configurazione Direct Server Return (DSR). La configurazione DSR è costituita da due parti: 

- Topologia di flussi
- Schema di mapping degli indirizzi IP

A livello di piattaforma, Azure Load Balancer viene sempre eseguito in una topologia di flussi DSR indipendentemente dal fatto che l'indirizzo IP mobile sia abilitato o meno. Ciò significa che la parte in uscita di un flusso viene sempre correttamente riscritta in un flusso direttamente nell'origine.
Senza IP mobile, per semplicità Azure espone uno schema di mappatura degli indirizzi IP tradizionale per il servizio di bilanciamento del carico (IP delle istanze di macchine virtuali). L'abilitazione dell'IP mobile modifica il mapping degli indirizzi IP con l'IP front-end del servizio di bilanciamento del carico per offrire una maggiore flessibilità. Fare clic [qui](load-balancer-multivip-overview.md) per altre informazioni.


## <a name="limitations"></a><a name = "limitations"></a>Limitazioni

- L'IP mobile non è attualmente supportato nelle configurazioni IP secondarie per gli scenari di bilanciamento del carico interno.

- Una regola di bilanciamento del carico non può estendersi a due reti virtuali.  I front-end e le relative istanze di back-end devono trovarsi nella stessa rete virtuale.  

- I ruoli di lavoro Web senza rete virtuale e altri servizi della piattaforma Microsoft possono essere accessibili dalle istanze solo dietro a un servizio Load Balancer Standard interno. Non fare affidamento su questa accessibilità, in quanto il servizio stesso o la piattaforma sottostante possono essere soggetti a modifiche senza preavviso. Se è necessaria la connettività in uscita quando si usa un servizio di bilanciamento del carico interno Standard, è necessario configurare la [connettività in uscita](load-balancer-outbound-connections.md).

- Load Balancer fornisce funzionalità di bilanciamento del carico e il port forwarding per protocolli TCP o UDP specifici. Le regole di bilanciamento del carico e le regole NAT in ingresso supportano TCP e UDP, ma non altri protocolli IP, ad esempio ICMP.

- Il flusso in uscita da una macchina virtuale back-end a un front-end di un servizio Load Balancer interno non verrà eseguito.

- L'inoltro di frammenti IP non è supportato nelle regole di bilanciamento del carico. né lo è frammentazione IP dei pacchetti UDP e TCP. È possibile usare le regole di bilanciamento del carico per le porte a disponibilità elevata per l'inoltro di frammenti IP esistenti. Per altre informazioni, vedere [Panoramica delle porte a disponibilità elevata](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Creare un servizio Load Balancer Standard pubblico](quickstart-load-balancer-standard-public-portal.md) per iniziare a usare un servizio Load Balancer, crearne uno, creare macchine virtuali con un'estensione IIS personalizzata installata e bilanciare il carico dell'app Web tra le macchine virtuali.
- Informazioni sulle [Connessioni in uscita di Azure Load Balancer](load-balancer-outbound-connections.md).
- Altre informazioni su [Azure Load Balancer](load-balancer-overview.md).
- Informazioni sui [probe di integrità](load-balancer-custom-probe-overview.md).
- Altre informazioni sulla diagnostica per [Azure Load Balancer Standard](load-balancer-standard-diagnostics.md).
- Vedere altre informazioni sui [gruppi di sicurezza di rete](../virtual-network/security-overview.md).
