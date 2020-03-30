---
title: Domande frequenti su Firewall di Azure
description: Domande frequenti su Firewall di Azure.FAQ for Azure Firewall. Servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 60beccc2f2679a18903b74b84f48afebfb3b69da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257752"
---
# <a name="azure-firewall-faq"></a>Domande frequenti su Firewall di Azure

## <a name="what-is-azure-firewall"></a>Informazioni sul firewall di Azure

Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. È un firewall con stato completo distribuito come servizio con disponibilità elevata e scalabilità cloud senza limiti. È possibile creare, applicare e registrare criteri di connettività di applicazione e di rete in modo centralizzato tra le sottoscrizioni e le reti virtuali.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Quali funzionalità sono supportate nel Firewall di Azure?

* Firewall con stato come servizio
* Disponibilità elevata e scalabilità cloud senza limiti
* Filtro dei nomi di dominio completi
* Tag FQDN
* Regole di filtro per il traffico di rete
* Supporto SNAT in uscita
* Supporto DNAT in ingresso
* Possibilità di creare, applicare e registrare criteri di connettività di applicazioni e rete in modo centralizzato tra le reti virtuali e le sottoscrizioni di Azure
* Integrazione completa con Monitoraggio di Azure per la registrazione e l'analisi

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Qual è il modello di distribuzione tipico per Firewall di Azure?

È possibile distribuire Firewall di Azure in qualsiasi rete virtuale, ma i clienti distribuiscono in genere Firewall di Azure in una rete virtuale centrale e configurano il peering di altre reti virtuali verso tale rete in un modello di tipo hub-spoke. È quindi possibile impostare la route predefinita dalle reti virtuali con peering verso la rete virtuale centrale del firewall. Il peering globale della rete virtuale è supportato, ma non è consigliato a causa di potenziali problemi di prestazioni e latenza tra le aree. Per ottenere prestazioni ottimali, distribuire un firewall per area.

Il vantaggio offerto da questo modello consiste nella possibilità di esercitare il controllo centralizzato su più reti virtuali spoke tramite diverse sottoscrizioni. È inoltre possibile usufruire di un risparmio sui costi perché non è necessario distribuire un firewall separatamente in ogni rete virtuale. Il risparmio sui costi deve essere misurato rispetto al costo di peering associato in base ai modelli di traffico dei clienti.

## <a name="how-can-i-install-the-azure-firewall"></a>Come si installa Firewall di Azure?

È possibile configurare Firewall di Azure tramite il portale di Azure, PowerShell o l'API REST oppure usando modelli. Per istruzioni dettagliate, vedere [Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure](tutorial-firewall-deploy-portal.md).

## <a name="what-are-some-azure-firewall-concepts"></a>Su quali concetti si basa Firewall di Azure?

Firewall di Azure supporta regole e raccolte di regole. Una raccolta di regole è un set di regole con lo stesso ordine e la stessa priorità. Le raccolte di regole vengono eseguite in ordine di priorità. Quelle di rete hanno maggiore priorità rispetto a quelle di applicazione e tutte le regole sono di terminazione.

Esistono tre tipi di raccolte di regole:

* *Regole applicazione*: Configurare nomi di dominio completi (FQDN) a cui è possibile accedere da una subnet.
* *Regole di rete*: Configurare le regole che contengono gli indirizzi di origine, i protocolli, le porte di destinazione e gli indirizzi di destinazione.Network rules : Configure rules that contain source addresses, protocols, destination ports, and destination addresses.
* *Regole NAT*: Configurare le regole DNAT per consentire le connessioni Internet in ingresso.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Firewall di Azure supporta il filtraggio del traffico in ingresso?

Firewall di Azure supporta i filtri in ingresso e in uscita. La protezione in ingresso viene in genere utilizzata per i protocolli non HTTP/S. Ad esempio i protocolli RDP, SSH e FTP. Per una migliore protezione HTTP/S in ingresso, usare un firewall dell'applicazione Web, ad esempio [WAF (Web Application Firewall)](../web-application-firewall/overview.md)di Azure.

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Quali servizi di registrazione e analisi sono supportati da Firewall di Azure?

Firewall di Azure è integrato con Monitoraggio di Azure per la visualizzazione e l'analisi dei log di firewall. I log possono essere inviati a Log Analytics, Archiviazione di Azure o Hub eventi e possono essere analizzati in Log Analytics o con strumenti diversi, ad esempio Excel e Power BI. Per altre informazioni, vedere [Esercitazione: Monitorare i log di Firewall di Azure](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Come funziona Firewall di Azure rispetto ad altri servizi esistenti, come le appliance virtuali di rete, nel marketplace?

Firewall di Azure è un servizio firewall di base che consente di risolvere determinati scenari dei clienti. Si prevede che si avrà una combinazione di nVA di terze parti e Firewall di Azure. L'integrazione è una priorità fondamentale.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Qual è la differenza tra la funzionalità Web application firewall del gateway applicazione e Firewall di Azure?

Web application firewall (WAF) è una funzionalità del gateway applicazione che offre una protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. Firewall di Azure fornisce la protezione in ingresso per i protocolli non HTTP/S (ad esempio, RDP, SSH, FTP), la protezione a livello di rete in uscita per tutte le porte e tutti i protocolli e la protezione a livello di applicazione per HTTP/S in uscita.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Qual è la differenza tra i gruppi di sicurezza di rete e Firewall di Azure?

Il servizio Firewall di Azure si integra con la funzionalità dei gruppi sicurezza di rete offrendo una migliore sicurezza di rete con strategie di difesa avanzate. I gruppi di sicurezza di rete forniscono un filtraggio del traffico distribuito a livello di rete per limitare il traffico verso le risorse all'interno delle reti virtuali in ogni sottoscrizione. Firewall di Azure è un firewall di rete centralizzato con stato completo, distribuito come servizio, che fornisce protezione a livello di rete e di applicazione in diverse sottoscrizioni e reti virtuali.

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>I gruppi di sicurezza di rete sono supportati nella subnet di Firewall di Azure?

Firewall di Azure è un servizio gestito con più livelli di protezione, inclusa la protezione della piattaforma con gruppi di protezione rete (non visualizzabili).  I gruppi di sicurezza di rete a livello di subnet non sono necessari nella subnet di Firewall di Azure e sono disabilitati per garantire l'assenza di interruzioni del servizio.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Come si configura Firewall di Azure con gli endpoint di servizio?

Per l'accesso sicuro ai servizi PaaS, è consigliabile usare gli endpoint di servizio. È possibile scegliere di abilitare gli endpoint di servizio nella subnet di Firewall di Azure e disabilitarli nelle reti virtuali spoke connesse. In questo modo si traggono vantaggio da entrambe le funzionalità: sicurezza degli endpoint del servizio e registrazione centrale per tutto il traffico.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Quali prezzi vengono applicati per Firewall di Azure?

Vedere [Prezzi del firewall di Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Come è possibile arrestare e avviare il Firewall di Azure?

È possibile usare i metodi di *deallocazione* e *allocazione* di Azure PowerShell.

Ad esempio:

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> È necessario riassegnare un firewall e un indirizzo IP pubblico al gruppo di risorse e alla sottoscrizione originali.

## <a name="what-are-the-known-service-limits"></a>Quali sono i limiti noti del servizio?

Per i limiti del servizio Firewall di Azure, vedere [Limiti, quote e vincoli](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)di sottoscrizione e servizio di Azure.

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Firewall di Azure in una rete virtuale hub può inoltrare e filtrare il traffico di rete tra due reti virtuali spoke?

Sì, è possibile usare il Firewall di Azure in una rete virtuale hub per instradare e filtrare il traffico tra due reti virtuali spoke. Le subnet in ognuna delle reti virtuali spoke devono avere un UDR che punta al firewall di Azure come gateway predefinito per il corretto funzionamento di questo scenario.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Firewall di Azure può inoltrare e filtrare il traffico di rete tra subnet della stessa rete virtuale o di reti virtuali con peering?

Sì. Tuttavia, la configurazione delle UDR per reindirizzare il traffico tra subnet nella stessa rete disco rigido virtuale richiede ulteriore attenzione. Anche se l'uso dell'intervallo di indirizzi della rete virtuale come prefisso di destinazione per il routing definito dall'utente è sufficiente, in questo modo si instrada tutto il traffico da un computer a un altro computer nella stessa subnet tramite l'istanza di Firewall di Azure. Per evitare questo problema, includere una route per la subnet nel routing definito dall'utente con un hop successivo di tipo **VNET**. La gestione di queste route può essere complessa e soggetta a errori. Il metodo consigliato per la segmentazione della rete interna consiste nell'utilizzare i gruppi di sicurezza di rete, che non richiedono UDR.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Azure Firewall in uscita SNAT tra reti private?

Firewall di Azure non SNAT quando l'indirizzo IP di destinazione è un intervallo IP privato per [IANA RFC 1918.](https://tools.ietf.org/html/rfc1918) Se l'organizzazione usa un intervallo di indirizzi IP pubblici per le reti private, Firewall di Azure scommette il traffico verso uno degli indirizzi IP privati del firewall in AzureFirewallSubnet.If your organization uses a public IP address range for private networks, Azure Firewall SNATs the traffic to one of the firewall private IP addresses in AzureFirewallSubnet. È possibile configurare Firewall di Azure in modo da **non** usare SNAT per l'intervallo di indirizzi IP pubblici. Per altre informazioni, vedere [Intervalli di indirizzi IP privati SNAT di Firewall di Azure](snat-private-range.md).

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>È supportato il tunneling/concatenamento forzato a un appliance virtuale di rete?

Il tunneling forzato è supportato. Per altre informazioni, vedere [Tunneling forzato di Firewall di Azure (anteprima).](forced-tunneling.md) 

Connettività diretta al Firewall di Azure. Se AzureFirewallSubnet apprende una route predefinita alla rete locale tramite BGP è necessario sostituirla con una route UDR 0.0.0.0/0 con il valore **NextHopType** impostato come **Internet** per mantenere connettività diretta a Internet.

Se la configurazione richiede il tunneling forzato in una rete locale ed è possibile determinare i prefissi IP di destinazione per le destinazioni Internet, è possibile configurare questi intervalli con la rete locale come hop successivo tramite una route definita dall'utente in AzureFirewallSubnet. In alternativa, per definire queste route, è possibile usare BGP.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Vi sono restrizioni relative al gruppo di risorse del firewall?

Sì. Il firewall, la rete virtuale e l'indirizzo IP pubblico devono essere tutti nello stesso gruppo di risorse.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Quando si configura DNAT per il traffico di rete Internet in ingresso, è necessario configurare anche una regola di rete corrispondente per consentire tale traffico?

No. Le regole NAT aggiungono in modo implicito una regola di rete corrispondente per consentire il traffico convertito. È possibile sostituire questo comportamento aggiungendo in modo esplicito una raccolta regole di rete con regole di negazione corrispondenti al traffico convertito. Per altre informazioni sulla logica di elaborazione delle regole di Firewall di Azure, vedere [Azure Firewall rule processing logic](rule-processing.md) (Logica di elaborazione delle regole di Firewall di Azure).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Come funzionano i caratteri jolly nel nome di dominio completo della destinazione di una regola dell'applicazione?

Se si configura ,**contoso.com**, *è*possibile che sia possibile .contoso.com, ma non contoso.com (l'apice del dominio). Se si desidera consentire l'apice del dominio, è necessario configurarlo in modo esplicito come FQDN di destinazione.

## <a name="what-does-provisioning-state-failed-mean"></a>Che cosa significa *Stato provisioning: non riuscito?*

Ogni volta che viene applicata una modifica alla configurazione, Firewall di Azure tenta di aggiornare tutte le istanze back-end sottostanti. In rari casi, una di queste istanze back-end potrebbe non riuscire ad aggiornare con la nuova configurazione e il processo di aggiornamento si interrompe con uno stato di provisioning non riuscito. Il firewall di Azure è ancora operativo, ma la configurazione applicata potrebbe essere in uno stato incoerente, in cui alcune istanze hanno la configurazione precedente in cui altre hanno impostato la regola aggiornata. In questo caso, provare ad aggiornare la configurazione ancora una volta fino a quando l'operazione ha esito positivo e il firewall è in stato di provisioning *riuscito.*

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>In che modo Firewall di Azure gestisce la manutenzione pianificata e gli errori non pianificati?
Firewall di Azure è costituito da diversi nodi back-end in una configurazione attivo-attivo.  Per qualsiasi manutenzione pianificata, abbiamo la logica di drenaggio della connessione per aggiornare correttamente i nodi.  Gli aggiornamenti vengono pianificati durante l'orario non di ufficio per ognuna delle aree di Azure per limitare ulteriormente il rischio di interruzione.  Per problemi non pianificati, viene creata un'istanza di un nuovo nodo per sostituire il nodo non riuscito.  La connettività al nuovo nodo viene in genere ristabilita entro 10 secondi dall'ora dell'errore.

## <a name="how-does-connection-draining-work"></a>Come funziona il drenaggio della connessione?

Per qualsiasi manutenzione pianificata, la logica di svuotamento della connessione aggiorna normalmente i nodi back-end. Firewall di Azure attende 90 secondi per la chiusura delle connessioni esistenti. Se necessario, i client possono ristabilire automaticamente la connettività a un altro nodo back-end.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Esiste un limite di caratteri per il nome di un firewall?

Sì. C'è un limite di 50 caratteri per un nome firewall.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Perché Firewall di Azure richiede una dimensione di subnet /26?

Il firewall di Azure deve eseguire il provisioning di più istanze di macchine virtuali durante la scalabilità. Uno spazio degli indirizzi /26 garantisce che il firewall disponga di indirizzi IP sufficienti per contenere il ridimensionamento.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>Le dimensioni della subnet del firewall devono essere modificate durante la scalabilità del servizio?

No. Firewall di Azure non necessita di una subnet più grande di /26.Azure Firewall doesn't need a subnet bigger than /26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Come è possibile aumentare la velocità effettiva del firewall?

La capacità iniziale di Velocità effettiva di Firewall di Azure è da 2,5 a 3 Gbps e la scalabilità orizzontale a 30 Gbps. Scala orizzontalmente in base all'utilizzo della CPU e alla velocità effettiva. Contattare il supporto tecnico per aumentare la capacità di velocità effettiva del firewall.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Quanto tempo è necessario per la scalabilità orizzontale di Firewall di Azure?

La scalabilità orizzontale di Firewall di Azure richiede da cinque a sette minuti. Contattare il supporto tecnico per aumentare la capacità di velocità effettiva iniziale del firewall in caso di burst che richiedono una scalabilità automatica più rapida.

Quando si testa la scalabilità automatica del firewall, tenere conto dei seguenti punti:

- Le prestazioni del flusso TCP singolo sono limitate a 1,4 Gbps. Pertanto, un test delle prestazioni deve stabilire più flussi TCP.
- Gli strumenti per le prestazioni devono stabilire continuamente nuove connessioni per connettersi con le istanze del firewall back-end con scalabilità verticale. Se il test stabilisce le connessioni una sola volta all'inizio, queste si connetteranno solo con le istanze back-end iniziali. Anche se il firewall aumenta, non si noterà alcun aumento delle prestazioni perché le connessioni sono associate alle istanze iniziali.


## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Firewall di Azure consente l'accesso ad Active Directory per impostazione predefinita?

No. Firewall di Azure blocca l'accesso ad Active Directory per impostazione predefinita. Per consentire l'accesso, configurare il tag del servizio AzureActiveDirectory.To allow access, configure the AzureActiveDirectory service tag. Per altre informazioni, vedere Tag del servizio Firewall di Azure.For more information, see [Azure Firewall service tags](service-tags.md).

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>È possibile escludere un FQDN o un indirizzo IP dal filtro basato su Minacce di Azure Firewall?

Sì, è possibile usare Azure PowerShell per eseguire questa operazione:Yes, you can use Azure PowerShell to do it:

```azurepowershell
# Add a Threat Intelligence Whitelist to an Existing Azure Firewall

## Create the Whitelist with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist.FQDNs = @("fqdn1", "fqdn2", …)
$fw.ThreatIntelWhitelist.IpAddress = @("ip1", "ip2", …)

Set-AzFirewall -AzureFirewall $fw
```