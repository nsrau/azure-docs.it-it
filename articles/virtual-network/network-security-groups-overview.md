---
title: Panoramica sui gruppi di sicurezza di rete di Azure
titlesuffix: Azure Virtual Network
description: Informazioni sui gruppi di sicurezza di rete. I gruppi di sicurezza di rete consentono di filtrare il traffico di rete tra le risorse di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: kumud
ms.reviewer: kumud
ms.custom: contperfq1
ms.openlocfilehash: 76f3ba000a9bde4a306d19e8281ebeb41f1616e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335868"
---
# <a name="network-security-groups"></a>Gruppi di sicurezza di rete
<a name="network-security-groups"></a>

È possibile usare un gruppo di sicurezza di rete di Azure per filtrare il traffico di rete da e verso le risorse di Azure in una rete virtuale di Azure. Un gruppo di sicurezza di rete contiene [regole di sicurezza](#security-rules) che consentono o negano il traffico di rete in ingresso o il traffico di rete in uscita da, diversi tipi di risorse di Azure. Per ogni regola è possibile specificare l'origine e la destinazione, la porta e il protocollo.

Questo articolo descrive le proprietà di una regola del gruppo di sicurezza di rete, le [regole di sicurezza predefinite](#default-security-rules) applicate e le proprietà delle regole che è possibile modificare per creare una [regola di sicurezza aumentata](#augmented-security-rules).

## <a name="security-rules"></a><a name="security-rules"></a> Regole di sicurezza

Un gruppo di sicurezza di rete può contenere zero regole o il numero di regole desiderato, entro i [limiti](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) della sottoscrizione di Azure. Ogni regola specifica le proprietà seguenti:

|Proprietà  |Spiegazione  |
|---------|---------|
|Nome|Nome univoco all'interno del gruppo di sicurezza di rete.|
|Priorità | Numero compreso tra 100 e 4096. Le regole vengono elaborate in ordine di priorità. I numeri più bassi vengono elaborati prima di quelli più elevati perché hanno priorità più alta. Quando il traffico corrisponde a una regola, l'elaborazione viene interrotta. Di conseguenza, le regole con priorità più bassa (numeri più elevati) che hanno gli stessi attributi di regole con priorità più elevata non vengono elaborate.|
|Origine o destinazione| Qualsiasi indirizzo IP, blocco CIDR (Classless Inter-Domain Routing), ad esempio 10.0.0.0/24, tag di servizio o gruppo di sicurezza delle applicazioni. Se si specifica un indirizzo per una risorsa di Azure, specificare l'indirizzo IP privato assegnato alla risorsa. I gruppi di sicurezza della rete vengono elaborati dopo che Azure ha convertito un indirizzo IP pubblico in un indirizzo IP privato per il traffico in ingresso e prima che Azure converta un indirizzo IP privato in un indirizzo IP pubblico per il traffico in uscita. . Specificando un intervallo, un tag di servizio o un gruppo di sicurezza delle applicazioni è possibile creare un minor numero di regole di sicurezza. La possibilità di specificare più intervalli e indirizzi IP singoli (non è possibile specificare più tag di servizio o gruppi di applicazioni) in una regola è indicata come [regole di sicurezza aumentata](#augmented-security-rules). È possibile creare regole di sicurezza ottimizzate solo in gruppi di sicurezza di rete creati tramite il modello di distribuzione Resource Manager. Non si possono specificare più indirizzi IP e intervalli di indirizzi IP nei gruppi di sicurezza di rete creati tramite il modello di distribuzione classica.|
|Protocollo     | TCP, UDP, ICMP o any.|
|Direzione| Definisce se la regola si applica al traffico in ingresso o in uscita.|
|Intervallo di porte     |È possibile specificare una singola porta o un intervallo di porte. Ad esempio, è possibile specificare 80 oppure 10000-10005. Specificando intervalli è possibile creare un minor numero di regole di sicurezza. È possibile creare regole di sicurezza ottimizzate solo in gruppi di sicurezza di rete creati tramite il modello di distribuzione Resource Manager. Non si possono specificare più porte o intervalli di porte nella stessa regola di sicurezza nei gruppi di sicurezza di rete creati tramite il modello di distribuzione classica.   |
|Azione     | Consentire o impedire.        |

Le regole di sicurezza del gruppo di sicurezza di rete vengono valutate in base alla priorità, usando informazioni a 5 tuple (origine, porta di origine, destinazione, porta di destinazione e protocollo) per consentire o negare il traffico. Non è possibile creare due regole di sicurezza con la stessa priorità e direzione. Viene creato un record di flusso per le connessioni esistenti. La comunicazione è consentita o negata in base allo stato di connessione del record di flusso. Il record di flusso consente al gruppo di sicurezza di avere uno stato. Se si specifica una regola di sicurezza in uscita per qualsiasi indirizzo sulla porta 80, ad esempio, non è necessario specificare una regola di sicurezza in ingresso per la risposta al traffico in uscita. È necessario specificare una regola di sicurezza in ingresso solo se la comunicazione viene avviata all'esterno. Questa considerazione si applica anche al contrario. Se il traffico in ingresso è consentito su una porta, non è necessario specificare una regola di sicurezza in uscita per rispondere al traffico sulla porta.

Le connessioni esistenti non possono essere interrotte quando si rimuove una regola di sicurezza che abilita il flusso. I flussi di traffico vengono interrotti quando le connessioni vengono arrestate e non è presente alcun flusso di traffico in entrambe le direzioni almeno per alcuni minuti.

Il numero di regole di sicurezza che è possibile creare in un gruppo di sicurezza di rete è limitato. Per informazioni dettagliate, vedere [Limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

### <a name="default-security-rules"></a><a name="default-security-rules"></a> Regole di sicurezza predefinite

Azure crea le regole predefinite seguenti in ogni gruppo di sicurezza di rete creato:

#### <a name="inbound"></a>In entrata

##### <a name="allowvnetinbound"></a>AllowVNetInBound

|Priorità|Source (Sorgente)|Porte di origine|Destination|Porte di destinazione|Protocollo|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Qualsiasi|Allow|

##### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Priorità|Source (Sorgente)|Porte di origine|Destination|Porte di destinazione|Protocollo|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Qualsiasi|Allow|

##### <a name="denyallinbound"></a>DenyAllInbound

|Priorità|Source (Sorgente)|Porte di origine|Destination|Porte di destinazione|Protocollo|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Qualsiasi|Nega|

#### <a name="outbound"></a>In uscita

##### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Priorità|Source (Sorgente)|Porte di origine| Destination | Porte di destinazione | Protocollo | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Qualsiasi | Allow |

##### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Priorità|Source (Sorgente)|Porte di origine| Destination | Porte di destinazione | Protocollo | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Qualsiasi | Allow |

##### <a name="denyalloutbound"></a>DenyAllOutBound

|Priorità|Source (Sorgente)|Porte di origine| Destination | Porte di destinazione | Protocollo | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Qualsiasi | Nega |

Nelle colonne **Origine** e **Destinazione**, *VirtualNetwork*, *AzureLoadBalancer* e *Internet* sono [tag di servizio](service-tags-overview.md), anziché indirizzi IP. Nella colonna protocollo, **any** include TCP, UDP e ICMP. Quando si crea una regola, è possibile specificare TCP, UDP, ICMP o any. Nelle colonne **Origine** e **Destinazione**, *0.0.0.0/0* rappresenta tutti gli indirizzi. I client come portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell possono usare * o any per questa espressione.
 
Non è possibile rimuovere le regole predefinite, ma è possibile eseguirne l'override creando regole con priorità più alta.

### <a name="augmented-security-rules"></a><a name="augmented-security-rules"></a> Regole di sicurezza aumentata

Le regole di sicurezza ottimizzate semplificano la definizione della sicurezza per le reti virtuali, perché consentono di definire criteri di sicurezza di rete più estesi e complessi con un minor numero di regole. È possibile combinare più porte e più indirizzi e intervalli IP espliciti in un'unica regola di sicurezza facilmente comprensibile. Usare regole ottimizzate nei campi relativi a origine, destinazione e porte di una regola. Per semplificare la manutenzione della definizione della regola di sicurezza, combinare regole di sicurezza potenziate con [tag di servizio](service-tags-overview.md) o gruppi di [sicurezza delle applicazioni](#application-security-groups). Il numero di indirizzi, intervalli e porte che è possibile specificare in una regola è limitato. Per informazioni dettagliate, vedere [Limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

#### <a name="service-tags"></a>Tag di servizio

Un tag del servizio rappresenta un gruppo di prefissi di indirizzi IP di un determinato servizio di Azure. Consente di ridurre al minimo la complessità degli aggiornamenti frequenti sulle regole di sicurezza di rete.

Per altre informazioni, vedere [tag dei servizi di Azure](service-tags-overview.md). Per un esempio su come usare il tag del servizio di archiviazione per limitare l'accesso alla rete, vedere [limitare l'accesso di rete alle risorse PaaS](tutorial-restrict-network-access-to-resources.md).

#### <a name="application-security-groups"></a>Gruppi di sicurezza delle applicazioni

I gruppi di sicurezza delle applicazioni consentono di configurare la sicurezza di rete come un'estensione naturale della struttura di un'applicazione, raggruppando le macchine virtuali e definendo i criteri di sicurezza di rete in base a tali gruppi. È possibile riusare i criteri di sicurezza su larga scala senza gestire manualmente indirizzi IP espliciti. Per altre informazioni, vedere [gruppi di sicurezza delle applicazioni](application-security-groups.md).

## <a name="azure-platform-considerations"></a>Considerazioni sulla piattaforma Azure

- **IP virtuale del nodo host**: i servizi di infrastruttura di base come DHCP, DNS, IMDS e il monitoraggio dello stato vengono forniti tramite gli indirizzi IP host virtualizzati 168.63.129.16 e 169.254.169.254. Questi indirizzi IP appartengono a Microsoft e sono gli unici indirizzi IP virtualizzati usati in tutte le aree a questo scopo. Le regole di sicurezza effettive e le route valide non includeranno queste regole della piattaforma. Per eseguire l'override di questa comunicazione di infrastruttura di base, è possibile creare una regola di sicurezza per negare il traffico usando i [tag di servizio](service-tags-overview.md) seguenti nelle regole del gruppo di sicurezza di rete: AzurePlatformDNS, AzurePlatformIMDS, AzurePlatformLKM. Informazioni su come [diagnosticare il filtro del traffico di rete](diagnose-network-traffic-filter-problem.md) e [diagnosticare il routing di rete](diagnose-network-routing-problem.md).
- **Licenze (servizio di gestione delle chiavi):** le immagini Windows in esecuzione nelle macchine virtuali devono essere concesse in licenza. Per verificare la concessione della licenza, viene inviata una richiesta ai server host del Servizio di gestione delle chiavi che gestiscono le query di questo tipo. La richiesta viene inviata in uscita tramite la porta 1688. Per le distribuzioni tramite la configurazione di [route predefinita 0.0.0.0/0](virtual-networks-udr-overview.md#default-route), questa regola di piattaforma sarà disabilitata.
- **Macchine virtuali in pool con carico bilanciato**: l'intervallo di porte e indirizzi di origine applicato è quello del computer di origine e non quello del servizio di bilanciamento del carico. L'intervallo di porte e indirizzi di destinazione riguarda il computer di destinazione e non il servizio di bilanciamento del carico.
- **Istanze di servizi di Azure**: nelle subnet delle reti virtuali vengono distribuite istanze di diversi servizi di Azure, ad esempio HDInsight, ambienti del servizio app e set di scalabilità di macchine virtuali. Per un elenco completo dei servizi che è possibile distribuire nelle reti virtuali, vedere l'articolo relativo alla [rete virtuale per i servizi di Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Assicurarsi di acquisire familiarità con i requisiti relativi alle porte per ogni servizio prima di applicare un gruppo di sicurezza di rete alla subnet in cui è distribuita la risorsa. Se si bloccano le porte richieste dal servizio, il servizio non funzionerà correttamente.
- **Invio di messaggi di posta elettronica in uscita**: per inviare posta elettronica da macchine virtuali di Azure è consigliabile usare servizi di inoltro SMTP autenticato, in genere connessi tramite la porta TCP 587 ma spesso anche con altre. Sono disponibili servizi di inoltro SMTP specializzati per la reputazione del mittente, per ridurre al minimo la possibilità che provider di posta elettronica di terze parti rifiutino i messaggi. Tali servizi di inoltro SMTP includono, ad esempio, Exchange Online Protection e SendGrid. L'uso di servizi di inoltro SMTP non è soggetto ad alcuna restrizione in Azure, indipendentemente dal tipo di sottoscrizione. 

  Se la sottoscrizione di Azure è stata creata prima del 15 novembre 2017, oltre a poter usare servizi di inoltro SMTP è possibile inviare posta elettronica direttamente sulla porta TCP 25. Se la sottoscrizione è stata creata dopo il 15 novembre 2017, potrebbe non essere possibile inviare posta elettronica direttamente sulla porta 25. Il comportamento della comunicazione in uscita sulla porta 25 dipende dal tipo di sottoscrizione, come illustrato di seguito.

     - **Enterprise Agreement**: la comunicazione in uscita sulla porta 25 è consentita. È possibile inviare un messaggio di posta elettronica in uscita direttamente dalle macchine virtuali a provider di posta elettronica esterni, senza restrizioni dalla piattaforma Azure. 
     - **Pagamento in base al consumo:** la comunicazione in uscita sulla porta 25 è bloccata per tutte le risorse. Se è necessario inviare posta elettronica direttamente da un macchina virtuale a provider di posta elettronica esterni, senza un inoltro SMTP autenticato, è necessario richiedere la rimozione della restrizione. Le richieste vengono esaminate e approvate a discrezione di Microsoft e vengono soddisfatte solo in seguito a controlli anti-frode. Per effettuare una richiesta, aprire un caso di supporto con il tipo di problema *Tecnico*, *Virtual Network Connectivity* (Connettività di rete virtuale), *Cannot send e-mail (SMTP/Port 25)* (Impossibile inviare posta elettronica - SMTP/porta 25). Nel caso di supporto includere informazioni dettagliate sui motivi per cui è necessario inviare posta elettronica dalla sottoscrizione a provider di posta direttamente anziché tramite un inoltro SMTP autenticato. Se la sottoscrizione viene esentata, potranno comunicare in uscita sulla porta 25 solo le macchine virtuali create dopo la data di esenzione.
     - **MSDN, Azure Pass, Azure in Open, Education, BizSpark e versione di prova gratuita**: la comunicazione in uscita sulla porta 25 è bloccata per tutte le risorse. Non è possibile richiedere la rimozione della restrizione, perché le richieste non verranno soddisfatte. Se è necessario inviare e-mail dalla macchina virtuale, è necessario usare un servizio di inoltro SMTP.
     - **Provider di servizi cloud**: i clienti che utilizzano le risorse di Azure tramite un provider di servizi cloud possono creare un caso di supporto presso tale provider e richiedergli di creare un caso di sblocco per loro conto, se non può essere usato un inoltro SMTP sicuro.

  Se Azure consente di inviare posta elettronica sulla porta 25, Microsoft non può garantire che i provider di posta elettronica accetteranno i messaggi in ingresso provenienti dalla macchina virtuale. Se un provider specifico rifiuta la posta dalla macchina virtuale, collaborare direttamente con il provider per risolvere eventuali problemi di invio dei messaggi o di filtro antispam oppure usare un servizio di inoltro SMTP autenticato.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle risorse di Azure che possono essere distribuite in una rete virtuale a cui sono associati gruppi di sicurezza di rete, vedere [integrazione della rete virtuale per i servizi di Azure](virtual-network-for-azure-services.md)
* Per informazioni sul modo in cui il traffico viene valutato con i gruppi di sicurezza di rete, vedere funzionamento dei [gruppi di sicurezza di rete](network-security-group-how-it-works.md).
* Se non è mai stato creato un gruppo di sicurezza di rete, è possibile completare una breve [esercitazione](tutorial-filter-network-traffic.md) per acquisire esperienza nella creazione di tale gruppo.
* Se si ha familiarità con i gruppi di sicurezza di rete e si ha la necessità di gestirli, vedere [Gestire un gruppo di sicurezza di rete](manage-network-security-group.md). 
* Se si verificano problemi di comunicazione ed è necessario risolvere i problemi dei gruppi di sicurezza di rete, vedere [Diagnosticare problemi di filtro del traffico di rete di una macchina virtuale](diagnose-network-traffic-filter-problem.md). 
* Informazioni su come abilitare i [log di flusso dei gruppi di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per analizzare il traffico di rete da e verso le risorse a cui è associato un gruppo di sicurezza di rete.
