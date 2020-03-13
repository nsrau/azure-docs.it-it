---
title: Panoramica sui gruppi di sicurezza di rete di Azure
titlesuffix: Azure Virtual Network
description: Informazioni sui gruppi di sicurezza di rete. I gruppi di sicurezza di rete consentono di filtrare il traffico di rete tra le risorse di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 3837b2af31ddab3c35abf877a74f980bd34e933d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280222"
---
# <a name="network-security-groups"></a>Gruppi di sicurezza di rete
<a name="network-security-groups"></a>

È possibile filtrare il traffico di rete da e verso le risorse di Azure in una rete virtuale di Azure con un gruppo di sicurezza di rete. Un gruppo di sicurezza di rete contiene regole di sicurezza che consentono o negano il traffico di rete in ingresso o il traffico di rete in uscita rispettivamente verso o da diversi tipi di risorse di Azure. Per informazioni sulle risorse di Azure che possono essere distribuite in una rete virtuale e sui gruppi di sicurezza di rete ad esse associati, vedere [Integrazione della rete virtuale per i servizi di Azure](virtual-network-for-azure-services.md). Per ogni regola è possibile specificare l'origine e la destinazione, la porta e il protocollo.

Questo articolo spiega i concetti dei gruppi di sicurezza di rete per un uso efficace. Se non è mai stato creato un gruppo di sicurezza di rete, è possibile completare una breve [esercitazione](tutorial-filter-network-traffic.md) per acquisire esperienza nella creazione di tale gruppo. Se si ha familiarità con i gruppi di sicurezza di rete e si ha la necessità di gestirli, vedere [Gestire un gruppo di sicurezza di rete](manage-network-security-group.md). Se si verificano problemi di comunicazione ed è necessario risolvere i problemi dei gruppi di sicurezza di rete, vedere [Diagnosticare problemi di filtro del traffico di rete di una macchina virtuale](diagnose-network-traffic-filter-problem.md). È possibile abilitare i [log di flusso dei gruppi di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per analizzare il traffico di rete da e verso le risorse a cui è associato un gruppo di sicurezza di rete.

## <a name="security-rules"></a>Regole di sicurezza

Un gruppo di sicurezza di rete può contenere zero regole o il numero di regole desiderato, entro i [limiti](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) della sottoscrizione di Azure. Ogni regola specifica le proprietà seguenti:

|Proprietà  |Spiegazione  |
|---------|---------|
|Nome|Nome univoco all'interno del gruppo di sicurezza di rete.|
|Priorità | Numero compreso tra 100 e 4096. Le regole vengono elaborate in ordine di priorità. I numeri più bassi vengono elaborati prima di quelli più elevati perché hanno priorità più alta. Quando il traffico corrisponde a una regola, l'elaborazione viene interrotta. Di conseguenza, le regole con priorità più bassa (numeri più elevati) che hanno gli stessi attributi di regole con priorità più elevata non vengono elaborate.|
|Origine o destinazione| Qualsiasi indirizzo IP, blocco CIDR (Classless Inter-Domain Routing), ad esempio 10.0.0.0/24, [tag di servizio](service-tags-overview.md) o [gruppo di sicurezza delle applicazioni](#application-security-groups). Se si specifica un indirizzo per una risorsa di Azure, specificare l'indirizzo IP privato assegnato alla risorsa. I gruppi di sicurezza della rete vengono elaborati dopo che Azure ha convertito un indirizzo IP pubblico in un indirizzo IP privato per il traffico in ingresso e prima che Azure converta un indirizzo IP privato in un indirizzo IP pubblico per il traffico in uscita. Vedere altre informazioni sugli [indirizzi IP](virtual-network-ip-addresses-overview-arm.md) di Azure. Specificando un intervallo, un tag di servizio o un gruppo di sicurezza delle applicazioni è possibile creare un minor numero di regole di sicurezza. La possibilità di specificare più intervalli e indirizzi IP singoli in una regola è detta [regola di sicurezza ottimizzata](#augmented-security-rules). Non si possono specificare più tag di servizio o gruppi di applicazioni. È possibile creare regole di sicurezza ottimizzate solo in gruppi di sicurezza di rete creati tramite il modello di distribuzione Resource Manager. Non si possono specificare più indirizzi IP e intervalli di indirizzi IP nei gruppi di sicurezza di rete creati tramite il modello di distribuzione classica. Vedere altre informazioni sui [modelli di distribuzione](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure.|
|Protocollo     | TCP, UDP, ICMP o any.|
|Direction| Definisce se la regola si applica al traffico in ingresso o in uscita.|
|Intervallo di porte     |È possibile specificare una singola porta o un intervallo di porte. Ad esempio, è possibile specificare 80 oppure 10000-10005. Specificando intervalli è possibile creare un minor numero di regole di sicurezza. È possibile creare regole di sicurezza ottimizzate solo in gruppi di sicurezza di rete creati tramite il modello di distribuzione Resource Manager. Non si possono specificare più porte o intervalli di porte nella stessa regola di sicurezza nei gruppi di sicurezza di rete creati tramite il modello di distribuzione classica.   |
|Azione     | Consentire o impedire.        |

Le regole di sicurezza del gruppo di sicurezza di rete vengono valutate in base alla priorità, usando informazioni a 5 tuple (origine, porta di origine, destinazione, porta di destinazione e protocollo) per consentire o negare il traffico. Viene creato un record di flusso per le connessioni esistenti. La comunicazione è consentita o negata in base allo stato di connessione del record di flusso. Il record di flusso consente al gruppo di sicurezza di avere uno stato. Se si specifica una regola di sicurezza in uscita per qualsiasi indirizzo sulla porta 80, ad esempio, non è necessario specificare una regola di sicurezza in ingresso per la risposta al traffico in uscita. È necessario specificare una regola di sicurezza in ingresso solo se la comunicazione viene avviata all'esterno. Questa considerazione si applica anche al contrario. Se il traffico in ingresso è consentito su una porta, non è necessario specificare una regola di sicurezza in uscita per rispondere al traffico sulla porta.
Le connessioni esistenti non possono essere interrotte quando si rimuove una regola di sicurezza che abilita il flusso. I flussi di traffico vengono interrotti quando le connessioni vengono arrestate e non è presente alcun flusso di traffico in entrambe le direzioni almeno per alcuni minuti.

Il numero di regole di sicurezza che è possibile creare in un gruppo di sicurezza di rete è limitato. Per informazioni dettagliate, vedere [Limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

### <a name="default-security-rules"></a>Regole di sicurezza predefinite

Azure crea le regole predefinite seguenti in ogni gruppo di sicurezza di rete creato:

#### <a name="inbound"></a>In ingresso

##### <a name="allowvnetinbound"></a>AllowVNetInBound

|Priorità|Source (Sorgente)|Porte di origine|Destination|Porte di destinazione|Protocollo|Accesso|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Qualsiasi|Allow|

##### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Priorità|Source (Sorgente)|Porte di origine|Destination|Porte di destinazione|Protocollo|Accesso|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Qualsiasi|Allow|

##### <a name="denyallinbound"></a>DenyAllInbound

|Priorità|Source (Sorgente)|Porte di origine|Destination|Porte di destinazione|Protocollo|Accesso|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Qualsiasi|Nega|

#### <a name="outbound"></a>In uscita

##### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Priorità|Source (Sorgente)|Porte di origine| Destination | Porte di destinazione | Protocollo | Accesso |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Qualsiasi | Allow |

##### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Priorità|Source (Sorgente)|Porte di origine| Destination | Porte di destinazione | Protocollo | Accesso |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Qualsiasi | Allow |

##### <a name="denyalloutbound"></a>DenyAllOutBound

|Priorità|Source (Sorgente)|Porte di origine| Destination | Porte di destinazione | Protocollo | Accesso |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Qualsiasi | Nega |

Nelle colonne **Origine** e **Destinazione**, *VirtualNetwork*, *AzureLoadBalancer* e *Internet* sono [tag di servizio](service-tags-overview.md), anziché indirizzi IP. Nella colonna protocollo, **any** include TCP, UDP e ICMP. Quando si crea una regola, è possibile specificare TCP, UDP, ICMP o any. Nelle colonne *Origine* e **Destinazione**, **0.0.0.0/0** rappresenta tutti gli indirizzi. I client come portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell possono usare * o any per questa espressione.
 
Non è possibile rimuovere le regole predefinite, ma è possibile eseguirne l'override creando regole con priorità più alta.

### <a name="augmented-security-rules"></a>Regole di sicurezza ottimizzate

Le regole di sicurezza ottimizzate semplificano la definizione della sicurezza per le reti virtuali, perché consentono di definire criteri di sicurezza di rete più estesi e complessi con un minor numero di regole. È possibile combinare più porte e più indirizzi e intervalli IP espliciti in un'unica regola di sicurezza facilmente comprensibile. Usare regole ottimizzate nei campi relativi a origine, destinazione e porte di una regola. Per semplificare la gestione della definizione delle regole di sicurezza, combinare le regole di sicurezza ottimizzate con [tag di servizio](service-tags-overview.md) o [gruppi di sicurezza delle applicazioni](#application-security-groups). Il numero di indirizzi, intervalli e porte che è possibile specificare in una regola è limitato. Per informazioni dettagliate, vedere [Limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

#### <a name="service-tags"></a>Tag di servizio

Un tag di servizio rappresenta un gruppo di prefissi di indirizzi IP da un determinato servizio di Azure. Consente di ridurre al minimo la complessità degli aggiornamenti frequenti sulle regole di sicurezza di rete.

Per altre informazioni, vedere [tag dei servizi di Azure](service-tags-overview.md). Per un esempio su come usare il tag del servizio di archiviazione per limitare l'accesso alla rete, vedere [limitare l'accesso di rete alle risorse PaaS](tutorial-restrict-network-access-to-resources.md).

#### <a name="application-security-groups"></a>Gruppi di sicurezza delle applicazioni

I gruppi di sicurezza delle applicazioni consentono di configurare la sicurezza di rete come un'estensione naturale della struttura di un'applicazione, raggruppando le macchine virtuali e definendo i criteri di sicurezza di rete in base a tali gruppi. È possibile riusare i criteri di sicurezza su larga scala senza gestire manualmente indirizzi IP espliciti. Per altre informazioni, vedere [gruppi di sicurezza delle applicazioni](application-security-groups.md).

## <a name="how-traffic-is-evaluated"></a>Modalità di valutazione del traffico

È possibile distribuire le risorse di diversi servizi di Azure in una rete virtuale di Azure. Per un elenco completo, vedere [Servizi distribuibili in una rete virtuale](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). È possibile associare zero o un gruppo di sicurezza di rete a ogni [subnet](virtual-network-manage-subnet.md#change-subnet-settings) e [interfaccia di rete](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) della rete virtuale in una macchina virtuale. Lo stesso gruppo di sicurezza di rete può essere associato a un numero qualsiasi di subnet e interfacce di rete.

L'immagine seguente illustra diversi scenari per l'implementazione dei gruppi di sicurezza di rete per consentire il traffico di rete da e verso Internet tramite la porta TCP 80:

![Elaborazione dei gruppi di sicurezza di rete](./media/security-groups/nsg-interaction.png)

Vedere l'immagine precedente e il testo seguente per comprendere come Azure elabora le regole in ingresso e in uscita per i gruppi di sicurezza di rete:

### <a name="inbound-traffic"></a>Traffico in ingresso

Per il traffico in ingresso, Azure elabora prima le regole di un gruppo di sicurezza di rete associato a una subnet, se presente, quindi le regole di un gruppo di sicurezza di rete associato all'interfaccia di rete, se presente.

- **VM1**: le regole di sicurezza in *NSG1* vengono elaborate perché associate a *Subnet1* e *VM1* si trova in *Subnet1*. A meno che non sia stata creata una regola che consenta l'ingresso alla porta 80, il traffico viene negato dalla regola di sicurezza predefinita [DenyAllInbound](#denyallinbound) e non viene mai valutato da *NSG2*, perché *NSG2* è associato all'interfaccia di rete. Se *NSG1* ha una regola di sicurezza che consente la porta 80, il traffico verrà quindi elaborato da *NSG2*. Per consentire la porta 80 per la macchina virtuale, sia *NSG1* che *NSG2* devono avere una regola che consenta la porta 80 da Internet.
- **VM2**: le regole in *NSG1* vengono elaborate perché *VM2* si trova anche in *Subnet1*. Poiché *VM2* non ha un gruppo di sicurezza di rete associato alla propria interfaccia di rete, riceve tutto il traffico consentito attraverso *NSG1* oppure tutto il traffico viene negato da *NSG1*. Quando un gruppo di sicurezza di rete è associato a una subnet, il traffico è consentito o negato a tutte le risorse della stessa subnet.
- **VM3**: poiché a *Subnet2* non è associato alcun gruppo di sicurezza di rete, il traffico è consentito nella subnet ed elaborato da *NSG2*, perché *NSG2* è associato all'interfaccia di rete collegata a *VM3*.
- **VM4**: il traffico è consentito in *VM4,* perché a *Subnet3*o all'interfaccia di rete della macchina virtuale non è associato un gruppo di sicurezza di rete. È consentito tutto il traffico di rete attraverso una subnet e un'interfaccia di rete se questi componenti non hanno un gruppo di sicurezza di rete associato.

### <a name="outbound-traffic"></a>Traffico in uscita

Per il traffico in uscita, Azure elabora prima le regole di un gruppo di sicurezza di rete associato a un'interfaccia di rete, se presente, quindi le regole di un gruppo di sicurezza di rete associato alla subnet, se presente.

- **VM1**: vengono elaborate le regole di sicurezza presenti in *NSG2*. A meno che non si crei una regola di sicurezza che nega la porta 80 in uscita verso Internet, il traffico è consentito dalla regola di sicurezza predefinita [AlllowInternetOutbound](#allowinternetoutbound) in *NSG1* e *NSG2*. Se *NSG2* ha una regola di sicurezza che nega la porta 80, il traffico viene negato e mai valutato da *NSG1*. Per negare la porta 80 dalla macchina virtuale, uno o entrambi i gruppi di sicurezza di rete devono avere una regola che nega la porta 80 verso Internet.
- **VM2**: tutto il traffico viene inviato attraverso l'interfaccia di rete alla subnet perché l'interfaccia di rete collegata a *VM2* non ha un gruppo di sicurezza di rete associato. Vengono elaborate le regole in *NSG1*.
- **VM3**: se *NSG2* ha una regola di sicurezza che nega la porta 80, il traffico viene negato. Se *NSG2* ha una regola di sicurezza che consente la porta 80, la porta 80 è consentita in uscita verso Internet perché a *Subnet2* non è associato un gruppo di sicurezza di rete.
- **VM4**: tutto il traffico di rete è consentito da *VM4* perché a *Subnet3* o all'interfaccia di rete collegata alla macchina virtuale non è associato un gruppo di sicurezza di rete.


### <a name="intra-subnet-traffic"></a>Traffico all'interno della subnet

È importante notare che le regole di sicurezza in un NSG associato a una subnet possono influire sulla connettività tra le macchine virtuali. Se, ad esempio, viene aggiunta una regola a *NSG1* che nega tutto il traffico in ingresso e in uscita, *VM1* e *VM2* non saranno più in grado di comunicare tra loro. È necessario aggiungere un'altra regola in modo specifico per consentire questa operazione. 



Le regole di aggregazione applicate a un'interfaccia di rete possono essere verificate facilmente visualizzando le [regole di sicurezza effettive](virtual-network-network-interface.md#view-effective-security-rules) per un'interfaccia di rete. È anche possibile usare la funzionalità di [verifica del flusso IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) in Azure Network Watcher per determinare se è consentita la comunicazione da o verso un'interfaccia di rete. La verifica del flusso IP indica se la comunicazione è consentita o negata e quale regola di sicurezza di rete consente o nega il traffico.

> [!NOTE]
> I gruppi di sicurezza di rete sono associati a subnet o a macchine virtuali e servizi cloud distribuiti nel modello di distribuzione classica e a subnet o interfacce di rete nel modello di distribuzione Gestione risorse. Per altre informazioni in proposito, vedere le [informazioni sui modelli di distribuzione di Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> A meno che non ci siano motivi specifici, è consigliabile associare un gruppo di sicurezza di rete a una subnet o a un'interfaccia di rete, ma non a entrambe. Dato che le regole di un gruppo di sicurezza di rete associato a una subnet possono entrare in conflitto con quelle di un gruppo di sicurezza di rete associato a un'interfaccia di rete, è possibile che si verifichino problemi di comunicazione imprevisti che devono essere risolti.

## <a name="azure-platform-considerations"></a>Considerazioni sulla piattaforma Azure

- **IP virtuale del nodo host**: i servizi di infrastruttura di base come DHCP, DNS, IMDS e il monitoraggio dello stato vengono forniti tramite gli indirizzi IP host virtualizzati 168.63.129.16 e 169.254.169.254. Questi indirizzi IP appartengono a Microsoft e sono gli unici indirizzi IP virtualizzati usati in tutte le aree a questo scopo.
- **Licenze (servizio di gestione delle chiavi):** le immagini Windows in esecuzione nelle macchine virtuali devono essere concesse in licenza. Per verificare la concessione della licenza, viene inviata una richiesta ai server host del Servizio di gestione delle chiavi che gestiscono le query di questo tipo. La richiesta viene inviata in uscita tramite la porta 1688. Per le distribuzioni tramite la configurazione di [route predefinita 0.0.0.0/0](virtual-networks-udr-overview.md#default-route), questa regola di piattaforma sarà disabilitata.
- **Macchine virtuali in pool con carico bilanciato**: l'intervallo di porte e indirizzi di origine applicato è quello del computer di origine e non quello del servizio di bilanciamento del carico. L'intervallo di porte e indirizzi di destinazione riguarda il computer di destinazione e non il servizio di bilanciamento del carico.
- **Istanze di servizi di Azure**: nelle subnet delle reti virtuali vengono distribuite istanze di diversi servizi di Azure, ad esempio HDInsight, ambienti del servizio app e set di scalabilità di macchine virtuali. Per un elenco completo dei servizi che è possibile distribuire nelle reti virtuali, vedere l'articolo relativo alla [rete virtuale per i servizi di Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Assicurarsi di acquisire familiarità con i requisiti relativi alle porte per ogni servizio prima di applicare un gruppo di sicurezza di rete alla subnet in cui è distribuita la risorsa. Se si bloccano le porte richieste dal servizio, il servizio non funzionerà correttamente.
- **Invio di messaggi di posta elettronica in uscita**: per inviare posta elettronica da macchine virtuali di Azure è consigliabile usare servizi di inoltro SMTP autenticato, in genere connessi tramite la porta TCP 587 ma spesso anche con altre. Sono disponibili servizi di inoltro SMTP specializzati per la reputazione del mittente, per ridurre al minimo la possibilità che provider di posta elettronica di terze parti rifiutino i messaggi. Tali servizi di inoltro SMTP includono, ad esempio, Exchange Online Protection e SendGrid. L'uso di servizi di inoltro SMTP non è soggetto ad alcuna restrizione in Azure, indipendentemente dal tipo di sottoscrizione. 

  Se la sottoscrizione di Azure è stata creata prima del 15 novembre 2017, oltre a poter usare servizi di inoltro SMTP è possibile inviare posta elettronica direttamente sulla porta TCP 25. Se la sottoscrizione è stata creata dopo il 15 novembre 2017, potrebbe non essere possibile inviare posta elettronica direttamente sulla porta 25. Il comportamento della comunicazione in uscita sulla porta 25 dipende dal tipo di sottoscrizione, come illustrato di seguito.

     - **Enterprise Agreement**: la comunicazione in uscita sulla porta 25 è consentita. È possibile inviare messaggi di posta elettronica in uscita direttamente dalle macchine virtuali a provider di posta elettronica esterni, senza restrizioni dalla piattaforma Azure. 
     - **Pagamento in base al consumo:** la comunicazione in uscita sulla porta 25 è bloccata per tutte le risorse. Se è necessario inviare posta elettronica direttamente da un macchina virtuale a provider di posta elettronica esterni, senza un inoltro SMTP autenticato, è necessario richiedere la rimozione della restrizione. Le richieste vengono esaminate e approvate a discrezione di Microsoft e vengono soddisfatte solo in seguito a controlli anti-frode. Per effettuare una richiesta, aprire un caso di supporto con il tipo di problema *Tecnico*, *Virtual Network Connectivity* (Connettività di rete virtuale), *Cannot send e-mail (SMTP/Port 25)* (Impossibile inviare posta elettronica - SMTP/porta 25). Nel caso di supporto includere informazioni dettagliate sui motivi per cui è necessario inviare posta elettronica dalla sottoscrizione a provider di posta direttamente anziché tramite un inoltro SMTP autenticato. Se la sottoscrizione viene esentata, potranno comunicare in uscita sulla porta 25 solo le macchine virtuali create dopo la data di esenzione.
     - **MSDN, Azure Pass, Azure in Open, Education, BizSpark e versione di prova gratuita**: la comunicazione in uscita sulla porta 25 è bloccata per tutte le risorse. Non è possibile richiedere la rimozione della restrizione, perché le richieste non verranno soddisfatte. Se è necessario inviare e-mail dalla macchina virtuale, è necessario usare un servizio di inoltro SMTP.
     - **Provider di servizi cloud**: i clienti che utilizzano le risorse di Azure tramite un provider di servizi cloud possono creare un caso di supporto presso tale provider e richiedergli di creare un caso di sblocco per loro conto, se non può essere usato un inoltro SMTP sicuro.

  Se Azure consente di inviare posta elettronica sulla porta 25, Microsoft non può garantire che i provider di posta elettronica accetteranno i messaggi in ingresso provenienti dalla macchina virtuale. Se un provider specifico rifiuta la posta dalla macchina virtuale, collaborare direttamente con il provider per risolvere eventuali problemi di invio dei messaggi o di filtro antispam oppure usare un servizio di inoltro SMTP autenticato.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un gruppo di sicurezza di rete](tutorial-filter-network-traffic.md).
