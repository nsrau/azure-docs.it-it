---
title: File di inclusione
description: File di inclusione
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 3/26/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: b8842ab4bcaf16b7345b25fa9ac4998981d9c458
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804296"
---
### <a name="what-is-azure-firewall"></a>Informazioni sul firewall di Azure

Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. È un firewall con stato completo distribuito come servizio con disponibilità elevata e scalabilità cloud senza limiti. È possibile creare, applicare e registrare criteri di connettività di applicazione e di rete in modo centralizzato tra le sottoscrizioni e le reti virtuali.

### <a name="what-capabilities-are-supported-in-azure-firewall"></a>Quali funzionalità sono supportate nel Firewall di Azure?

* Firewall con stato come servizio
* Disponibilità elevata e scalabilità cloud senza limiti
* Filtro dei nomi di dominio completi
* Tag FQDN
* Regole di filtro per il traffico di rete
* Supporto SNAT in uscita
* Supporto DNAT in ingresso
* Possibilità di creare, applicare e registrare criteri di connettività di applicazioni e rete in modo centralizzato tra le reti virtuali e le sottoscrizioni di Azure
* Integrazione completa con Monitoraggio di Azure per la registrazione e l'analisi

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Qual è il modello di distribuzione tipico per Firewall di Azure?

È possibile distribuire Firewall di Azure in qualsiasi rete virtuale, ma i clienti distribuiscono in genere Firewall di Azure in una rete virtuale centrale e configurano il peering di altre reti virtuali verso tale rete in un modello di tipo hub-spoke. È quindi possibile impostare la route predefinita dalle reti virtuali con peering verso la rete virtuale centrale del firewall. Peering reti virtuali è supportato, ma non è consigliabile a causa di potenziali problemi di prestazioni e latenza tra le aree. Per prestazioni ottimali, distribuire un firewall per ogni area.

Il vantaggio offerto da questo modello consiste nella possibilità di esercitare il controllo centralizzato su più reti virtuali spoke tramite diverse sottoscrizioni. È inoltre possibile usufruire di un risparmio sui costi perché non è necessario distribuire un firewall separatamente in ogni rete virtuale. Il risparmio sui costi deve essere misurato rispetto al costo di peering associato in base ai modelli di traffico dei clienti.

### <a name="how-can-i-install-the-azure-firewall"></a>Come si installa Firewall di Azure?

È possibile configurare Firewall di Azure tramite il portale di Azure, PowerShell o l'API REST oppure usando modelli. Per istruzioni dettagliate, vedere [Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure](../articles/firewall/tutorial-firewall-deploy-portal.md).

### <a name="what-are-some-azure-firewall-concepts"></a>Su quali concetti si basa Firewall di Azure?

Firewall di Azure supporta regole e raccolte di regole. Una raccolta di regole è un set di regole con lo stesso ordine e la stessa priorità. Le raccolte di regole vengono eseguite in ordine di priorità. Quelle di rete hanno maggiore priorità rispetto a quelle di applicazione e tutte le regole sono di terminazione.

Esistono tre tipi di raccolte di regole:

* *Regole di applicazione*: Configurare i nomi di dominio completo (FQDN) che sono accessibili da una subnet.
* *Regole di rete*: Configurare le regole che contengono gli indirizzi di origine, protocolli, le porte di destinazione e gli indirizzi di destinazione.
* *Le regole NAT*: Configurare regole DNAT per consentire le connessioni in ingresso.

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Firewall di Azure supporta il filtraggio del traffico in ingresso?

Firewall di Azure supporta i filtri in ingresso e in uscita. La protezione in ingresso è per i protocolli non HTTP/S. Ad esempio i protocolli RDP, SSH e FTP.

### <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Quali servizi di registrazione e analisi sono supportati da Firewall di Azure?

Firewall di Azure è integrato con Monitoraggio di Azure per la visualizzazione e l'analisi dei log di firewall. I log possono essere inviati a Log Analytics, Archiviazione di Azure o Hub eventi e possono essere analizzati in Log Analytics o con strumenti diversi, ad esempio Excel e Power BI. Per altre informazioni, vedere [Esercitazione: Monitorare i log di Firewall di Azure](../articles/firewall/tutorial-diagnostics.md).

### <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Come funziona Firewall di Azure rispetto ad altri servizi esistenti, come le appliance virtuali di rete, nel marketplace?

Firewall di Azure è un servizio firewall di base che consente di risolvere determinati scenari dei clienti. Si presuppone che ci sia una combinazione di appliance virtuali di rete di terze parti e Firewall di Azure. L'integrazione è una priorità fondamentale.

### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Qual è la differenza tra la funzionalità Web application firewall del gateway applicazione e Firewall di Azure?

Web application firewall (WAF) è una funzionalità del gateway applicazione che offre una protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. Firewall di Azure fornisce la protezione in ingresso per i protocolli non HTTP/S (ad esempio, RDP, SSH, FTP), la protezione a livello di rete in uscita per tutte le porte e tutti i protocolli e la protezione a livello di applicazione per HTTP/S in uscita.

### <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Qual è la differenza tra i gruppi di sicurezza di rete e Firewall di Azure?

Il servizio Firewall di Azure si integra con la funzionalità dei gruppi sicurezza di rete offrendo una migliore sicurezza di rete con strategie di difesa avanzate. I gruppi di sicurezza di rete forniscono un filtraggio del traffico distribuito a livello di rete per limitare il traffico verso le risorse all'interno delle reti virtuali in ogni sottoscrizione. Firewall di Azure è un firewall di rete centralizzato con stato completo, distribuito come servizio, che fornisce protezione a livello di rete e di applicazione in diverse sottoscrizioni e reti virtuali.

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Come si configura Firewall di Azure con gli endpoint di servizio?

Per l'accesso sicuro ai servizi PaaS, è consigliabile usare gli endpoint di servizio. È possibile scegliere di abilitare gli endpoint di servizio nella subnet di Firewall di Azure e disabilitarli nelle reti virtuali spoke connesse. In questo modo si possono sfruttare entrambe le funzionalità: sicurezza degli endpoint di servizio e registrazione centrale per tutto il traffico.

### <a name="what-is-the-pricing-for-azure-firewall"></a>Quali prezzi vengono applicati per Firewall di Azure?

Firewall di Azure ha un costo fisso e un costo variabile:

* Tariffa fissa: 0,125 USD all'ora per unità firewall
* Tariffa variabile: 0,03 USD per GB elaborato dal firewall (traffico dati in ingresso o in uscita)

Non sono previsti costi per un firewall deallocato.

Per altre informazioni, vedere [Prezzi di Firewall di Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

### <a name="how-can-i-stop-and-start-azure-firewall"></a>Come è possibile arrestare e avviare il Firewall di Azure?

È possibile usare i metodi di *deallocazione* e *allocazione* di Azure PowerShell.

Ad esempio: 

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
#Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> È necessario riassegnare un firewall e un indirizzo IP pubblico al gruppo di risorse e alla sottoscrizione originali.

### <a name="what-are-the-known-service-limits"></a>Quali sono i limiti noti del servizio?

Per i limiti del servizio Firewall di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../articles/azure-subscription-service-limits.md#azure-firewall-limits)

### <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Firewall di Azure in una rete virtuale hub può inoltrare e filtrare il traffico di rete tra due reti virtuali spoke?

Sì, è possibile usare il Firewall di Azure in una rete virtuale hub per instradare e filtrare il traffico tra due reti virtuali spoke. Le subnet in ognuna delle reti virtuali spoke devono avere il routing definito dall'utente che punta verso il Firewall di Azure come gateway predefinito per il corretto funzionamento di questo scenario.

### <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Firewall di Azure può inoltrare e filtrare il traffico di rete tra subnet della stessa rete virtuale o di reti virtuali con peering?

Sì. Tuttavia, la configurazione di route definite dall'utente per reindirizzare il traffico tra subnet nella stessa rete virtuale richiede ulteriore attenzione. Anche se l'uso dell'intervallo di indirizzi della rete virtuale come prefisso di destinazione per il routing definito dall'utente è sufficiente, in questo modo si instrada tutto il traffico da un computer a un altro computer nella stessa subnet tramite l'istanza di Firewall di Azure. Per evitare questo problema, includere una route per la subnet nel routing definito dall'utente con un hop successivo di tipo **VNET**. La gestione di queste route può essere complessa e soggetta a errori. Il metodo consigliato per la segmentazione della rete interna prevede l'uso di gruppi di sicurezza di rete, che non richiedono routing definiti dall'utente.

### <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Viene forzato il tunneling/concatenamento a un'Appliance virtuale di rete supportati?

Sì.

Firewall di Azure deve avere connettività Internet diretta. Per impostazione predefinita, AzureFirewallSubnet dispone di una route 0.0.0.0/0 con il valore NextHopType impostata **Internet**.

Se si abilita il tunneling forzato in locale tramite Gateway VPN o ExpressRoute, si potrebbe essere necessario configurare una route definita dall'utente di 0.0.0.0/0 (UDR) con il valore impostato NextHopType come Internet e associarla con i AzureFirewallSubnet in modo esplicito. Sostituisce un gateway predefinito potenziali annuncio BGP alla rete locale. Se l'organizzazione richiede che il tunneling forzato per il Firewall di Azure indirizzare il traffico del gateway predefinito tramite la rete locale, contattare il supporto tecnico. È possibile elenco elementi consentiti viene mantenuta la sottoscrizione per assicurarsi che il firewall necessario la connettività Internet.

### <a name="are-there-any-firewall-resource-group-restrictions"></a>Vi sono restrizioni relative al gruppo di risorse del firewall?

Sì. Il firewall, la subnet, la rete virtuale e l'indirizzo IP pubblico devono trovarsi nello stesso gruppo di risorse.

### <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Quando si configura DNAT per il traffico di rete in ingresso, è necessario anche configurare una regola di rete corrispondente per consentire tale traffico?

 No. Le regole NAT aggiungono in modo implicito una regola di rete corrispondente per consentire il traffico convertito. È possibile sostituire questo comportamento aggiungendo in modo esplicito una raccolta regole di rete con regole di negazione corrispondenti al traffico convertito. Per altre informazioni sulla logica di elaborazione delle regole di Firewall di Azure, vedere [Azure Firewall rule processing logic](../articles/firewall/rule-processing.md) (Logica di elaborazione delle regole di Firewall di Azure).

### <a name="how-to-wildcards-work-in-an-application-rule-target-fqdn"></a>Per i caratteri jolly funzionamento in una nome di dominio completo di destinazione regola applicazione?

Se si configura ***. contoso.com**, consente *anyvalue*. contoso.com, ma non contoso.com (dominio dominio radice). Se si desidera consentire il vertice di dominio, è necessario configurarlo in modo esplicito come un nome di dominio completo di destinazione.
