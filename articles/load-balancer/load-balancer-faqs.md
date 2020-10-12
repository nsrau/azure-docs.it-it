---
title: Domande frequenti-Azure Load Balancer
description: Risposte alle domande più frequenti sul Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 2b547dbc8671481275952f4c3eae5683e9e3a06c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86207534"
---
# <a name="load-balancer-frequently-asked-questions"></a>Load Balancer domande frequenti

## <a name="what-types-of-load-balancer-exist"></a>Quali sono i tipi di Load Balancer esistenti?
Bilanciamento del carico interno che bilanciano il traffico all'interno di una VNET e di bilanciamento del carico esterno che bilanciano il traffico da e verso un endpoint connesso a Internet. Per ulteriori informazioni, vedere [tipi di Load Balancer](components.md#frontend-ip-configurations). 

Per entrambi questi tipi, Azure offre uno SKU di base e uno SKU standard con funzionalità diverse di rilevamento funzionale, prestazioni, sicurezza e integrità. Queste differenze sono illustrate nell'articolo relativo al [confronto tra SKU](skus.md) .

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Come è possibile eseguire l'aggiornamento da una base a una Load Balancer Standard?
Vedere l'articolo relativo all' [aggiornamento da Basic a standard](upgrade-basic-standard.md) per uno script automatizzato e istruzioni sull'aggiornamento di uno SKU Load Balancer.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Quali sono le diverse opzioni di bilanciamento del carico in Azure?
Vedere la [Guida alla tecnologia](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)  del servizio di bilanciamento del carico per i servizi di bilanciamento del carico disponibili e gli usi consigliati per ognuno di essi.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Dove è possibile trovare Load Balancer modelli ARM?
Vedere l' [elenco dei modelli di avvio rapido Azure Load Balancer](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers#quickstart-templates) per i modelli ARM delle distribuzioni comuni.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>In che modo le regole NAT in ingresso sono diverse dalle regole di bilanciamento del carico?
Le regole NAT vengono usate per specificare una risorsa back-end a cui indirizzare il traffico. Ad esempio, configurazione di una porta di bilanciamento del carico specifica per inviare il traffico RDP a una macchina virtuale specifica. Le regole di bilanciamento del carico vengono usate per specificare un pool di risorse back-end per indirizzare il traffico a, bilanciare il carico in ogni istanza. Una regola di bilanciamento del carico, ad esempio, può instradare i pacchetti TCP sulla porta 80 del servizio di bilanciamento del carico in un pool di server Web.

## <a name="what-is-ip-1686312916"></a>Che cos'è IP 168.63.129.16?
Indirizzo IP virtuale per l'host contrassegnato come infrastruttura di Azure Load Balancer da cui hanno origine i probe di integrità di Azure. Quando si configurano le istanze back-end, è necessario consentire il traffico da questo indirizzo IP per rispondere correttamente ai Probe di integrità. Questa regola non interagisce con l'accesso al front-end Load Balancer. Se non si usa la Azure Load Balancer, è possibile eseguire l'override di questa regola. Altre informazioni sui tag di servizio sono disponibili [qui](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags).

## <a name="can-i-use-global-vnet-peering-with-basic-load-balancer"></a>È possibile usare il peering VNET globale con Load Balancer di base?
No. Il Load Balancer di base non supporta il peering VNET globale. In alternativa, è possibile usare un Load Balancer Standard. Per un aggiornamento semplice, vedere l'articolo relativo all' [aggiornamento da Basic a standard](upgrade-basic-standard.md) .

## <a name="how-can-i-discover-the-public-ip-that-an-azure-vm-uses"></a>Come è possibile individuare l'indirizzo IP pubblico usato da una macchina virtuale di Azure?

Esistono molti modi per determinare l'indirizzo IP di origine pubblica di una connessione in uscita. OpenDNS offre un servizio che consente di visualizzare l'indirizzo IP pubblico della macchina virtuale.
Usando il comando nslookup è possibile inviare una query DNS per il nome myip.opendns.com al resolver OpenDNS. Il servizio restituisce l'indirizzo IP di origine usato per inviare la query. Quando si esegue la query seguente dalla macchina virtuale, la risposta è l'indirizzo IP pubblico usato per tale VM:

 ```nslookup myip.opendns.com resolver1.opendns.com```

## <a name="how-do-connections-to-azure-storage-in-the-same-region-work"></a>Come funzionano le connessioni ad archiviazione di Azure nella stessa area?
La connettività in uscita tramite gli scenari precedenti non è necessaria per connettersi ad Archiviazione nella stessa area della macchina virtuale. Se non lo si desidera, usare i gruppi di sicurezza di rete (NSG) come illustrato in precedenza. Per la connettività ad Archiviazione in altre aree, è necessaria la connettività in uscita. Si noti che quando ci si connette ad Archiviazione da una macchina virtuale nella stessa area, l'indirizzo IP di origine nei log di diagnostica di Archiviazione sarà un indirizzo di provider interno e non l'indirizzo IP pubblico della macchina virtuale. Se si vuole limitare l'accesso all'account di Archiviazione alle macchine virtuali in una o più subnet della rete virtuale nella stessa area, usare [endpoint di servizio della rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) e non l'indirizzo IP pubblico quando si configura il firewall dell'account di archiviazione. Una volta configurati gli endpoint di servizio, verrà visualizzato l'indirizzo IP privato della rete virtuale nei log di diagnostica di Archiviazione e non l'indirizzo del provider interno.

## <a name="what-are-best-practises-with-respect-to-outbound-connectivity"></a>Quali sono le procedure consigliate per quanto riguarda la connettività in uscita?
Load Balancer Standard e l'IP pubblico standard introduce funzionalità e comportamenti diversi per la connettività in uscita. Non corrispondono agli SKU di base. Per disporre di connettività in uscita quando si utilizzano SKU standard, è necessario definire questa opzione sia per gli indirizzi IP pubblici standard sia per un'istanza di Load Balancer Standard pubblica. Ciò include la creazione di connettività in uscita quando si usa un'istanza di Load Balancer Standard interna. È consigliabile usare sempre le regole in uscita in un'istanza di Load Balancer Standard pubblica. Ciò significa che quando viene usata un'istanza di Load Balancer Standard interna, è necessario provvedere a creare una connettività in uscita per le macchine virtuali nel pool di back-end se è richiesta la connettività in uscita. Nel contesto della connettività in uscita, una singola VM autonoma, tutte le VM in un set di disponibilità, tutte le istanze in una VMSS si comportano come un gruppo. Ciò significa che, se una singola macchina virtuale in un set di disponibilità è associata a uno SKU standard, tutte le istanze della macchina virtuale all'interno di questo set di disponibilità si comportano allo stesso modo, come se fossero associate allo SKU standard, anche se una singola istanza non è direttamente associata a tale SKU. Questo comportamento viene osservato anche nel caso di una VM autonoma con più schede di interfaccia di rete collegate a un servizio di bilanciamento del carico. Se una scheda di interfaccia di rete viene aggiunta come autonoma, avrà lo stesso comportamento. Rivedere attentamente l'intero documento per comprendere i concetti generali, esaminare [Load Balancer Standard](load-balancer-standard-overview.md) per le differenze tra gli SKU e rivedere le [regole in uscita](load-balancer-outbound-connections.md#outboundrules).
L'uso di regole in uscita offre all'utente un controllo dettagliato su tutti gli aspetti della connettività in uscita.
 
## <a name="next-steps"></a>Passaggi successivi
Se la domanda non è elencata in precedenza, inviare commenti e suggerimenti su questa pagina. Verrà creato un problema di GitHub per consentire al team del prodotto di garantire la risposta a tutte le domande importanti dei clienti.
