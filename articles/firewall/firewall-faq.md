---
title: Domande frequenti su Firewall di Azure
description: Domande frequenti per Firewall di Azure. Servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: victorh
ms.openlocfilehash: efb793898da03d2a024b559075a2d55e79b20d65
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208518"
---
# <a name="azure-firewall-faq"></a>Domande frequenti su Firewall di Azure

## <a name="what-is-azure-firewall"></a>Informazioni sul firewall di Azure

Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. È un firewall con stato completo distribuito come servizio con disponibilità elevata e scalabilità cloud senza limiti. È possibile creare, applicare e registrare criteri di connettività di applicazione e di rete in modo centralizzato tra le sottoscrizioni e le reti virtuali.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Quali funzionalità sono supportate nel Firewall di Azure?

Per informazioni sulle funzionalità di Firewall di Azure, vedere [Funzionalità di Firewall di Azure](features.md).

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Qual è il modello di distribuzione tipico per Firewall di Azure?

È possibile distribuire Firewall di Azure in qualsiasi rete virtuale, ma i clienti distribuiscono in genere Firewall di Azure in una rete virtuale centrale e configurano il peering di altre reti virtuali verso tale rete in un modello di tipo hub-spoke. È quindi possibile impostare la route predefinita dalle reti virtuali con peering verso la rete virtuale centrale del firewall. Il peering di rete virtuale globale è supportato, ma non è consigliato a causa di potenziali problemi di prestazioni e di latenza tra le aree. Per ottenere prestazioni ottimali, distribuire un firewall per ogni area.

Il vantaggio offerto da questo modello consiste nella possibilità di esercitare il controllo centralizzato su più reti virtuali spoke tramite diverse sottoscrizioni. È inoltre possibile usufruire di un risparmio sui costi perché non è necessario distribuire un firewall separatamente in ogni rete virtuale. Il risparmio sui costi deve essere misurato rispetto al costo di peering associato in base ai modelli di traffico dei clienti.

## <a name="how-can-i-install-the-azure-firewall"></a>Come si installa Firewall di Azure?

È possibile configurare Firewall di Azure tramite il portale di Azure, PowerShell o l'API REST oppure usando modelli. Vedere [Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure](tutorial-firewall-deploy-portal.md).

## <a name="what-are-some-azure-firewall-concepts"></a>Su quali concetti si basa Firewall di Azure?

Firewall di Azure supporta regole e raccolte di regole. Una raccolta di regole è un set di regole con lo stesso ordine e la stessa priorità. Le raccolte di regole vengono eseguite in ordine di priorità. Quelle di rete hanno maggiore priorità rispetto a quelle di applicazione e tutte le regole sono di terminazione.

Sono disponibili tre tipi di raccolte di regole:

* *Regole di applicazione*: configurare i nomi di dominio completi (FQDN) accessibili da una subnet.
* *Regole di rete*: configurare le regole che contengono indirizzi di origine, protocolli, porte di destinazione e indirizzi di destinazione.
* *Regole NAT*: configurare le regole DNAT per consentire le connessioni Internet in ingresso.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Firewall di Azure supporta il filtraggio del traffico in ingresso?

Firewall di Azure supporta i filtri in ingresso e in uscita. La protezione in ingresso viene in genere usata per i protocolli diversi da HTTP/S. Ad esempio i protocolli RDP, SSH e FTP. Per una migliore protezione di HTTP/S in ingresso, usare un web application firewall, ad esempio [WAF (Web Application Firewall) di Azure](../web-application-firewall/overview.md).

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Quali servizi di registrazione e analisi sono supportati da Firewall di Azure?

Firewall di Azure è integrato con Monitoraggio di Azure per la visualizzazione e l'analisi dei log di firewall. I log possono essere inviati a Log Analytics, Archiviazione di Azure o Hub eventi e possono essere analizzati in Log Analytics o con strumenti diversi, ad esempio Excel e Power BI. Per altre informazioni, vedere [Esercitazione: Monitorare i log di Firewall di Azure](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Come funziona Firewall di Azure rispetto ad altri servizi esistenti, come le appliance virtuali di rete, nel marketplace?

Firewall di Azure è un servizio firewall di base che consente di risolvere determinati scenari dei clienti. Si presuppone la presenza di una combinazione di appliance virtuali di rete di terze parti e Firewall di Azure. L'integrazione è una priorità fondamentale.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Qual è la differenza tra la funzionalità Web application firewall del gateway applicazione e Firewall di Azure?

Web application firewall (WAF) è una funzionalità del gateway applicazione che offre una protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. Firewall di Azure fornisce la protezione in ingresso per i protocolli non HTTP/S (ad esempio, RDP, SSH, FTP), la protezione a livello di rete in uscita per tutte le porte e tutti i protocolli e la protezione a livello di applicazione per HTTP/S in uscita.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Qual è la differenza tra i gruppi di sicurezza di rete e Firewall di Azure?

Il servizio Firewall di Azure si integra con la funzionalità dei gruppi sicurezza di rete offrendo una migliore sicurezza di rete con strategie di difesa avanzate. I gruppi di sicurezza di rete forniscono un filtraggio del traffico distribuito a livello di rete per limitare il traffico verso le risorse all'interno delle reti virtuali in ogni sottoscrizione. Firewall di Azure è un firewall di rete centralizzato con stato completo, distribuito come servizio, che fornisce protezione a livello di rete e di applicazione in diverse sottoscrizioni e reti virtuali.

## <a name="are-network-security-groups-nsgs-supported-on-the-azurefirewallsubnet"></a>I gruppi di sicurezza di rete (NSG) sono supportati in AzureFirewallSubnet?

Firewall di Azure è un servizio gestito con più livelli di protezione, inclusa la protezione della piattaforma con gruppi di sicurezza di rete a livello NIC (non visualizzabili).  I gruppi a livello di subnet non sono necessari in AzureFirewallSubnet e sono disabilitati per evitare interruzioni del servizio.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Come si configura Firewall di Azure con gli endpoint di servizio?

Per l'accesso sicuro ai servizi PaaS, è consigliabile usare gli endpoint di servizio. È possibile scegliere di abilitare gli endpoint di servizio nella subnet di Firewall di Azure e disabilitarli nelle reti virtuali spoke connesse. In questo modo è possibile sfruttare entrambe le funzionalità, ovvero sicurezza degli endpoint di servizio e registrazione centrale per tutto il traffico.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Quali prezzi vengono applicati per Firewall di Azure?

Vedere [Prezzi del servizio Firewall di Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

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
$publicip1 = Get-AzPublicIpAddress -Name "Public IP1 Name" -ResourceGroupName "RG Name"
$publicip2 = Get-AzPublicIpAddress -Name "Public IP2 Name" -ResourceGroupName "RG Name"
$azfw.Allocate($vnet,@($publicip,$publicip2))

Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> È necessario riassegnare un firewall e un indirizzo IP pubblico al gruppo di risorse e alla sottoscrizione originali.

## <a name="what-are-the-known-service-limits"></a>Quali sono i limiti noti del servizio?

Per i limiti del servizio Firewall di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Firewall di Azure in una rete virtuale hub può inoltrare e filtrare il traffico di rete tra due reti virtuali spoke?

Sì, è possibile usare il Firewall di Azure in una rete virtuale hub per instradare e filtrare il traffico tra due reti virtuali spoke. Per il corretto funzionamento di questo scenario, alle subnet in ognuna delle reti virtuali spoke deve essere associato il routing definito dall'utente che punta verso il Firewall di Azure come gateway predefinito.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Firewall di Azure può inoltrare e filtrare il traffico di rete tra subnet della stessa rete virtuale o di reti virtuali con peering?

Sì. La configurazione del routing definito dall'utente per il reindirizzamento del traffico tra subnet della stessa rete virtuale richiede tuttavia particolare attenzione. Anche se l'uso dell'intervallo di indirizzi della rete virtuale come prefisso di destinazione per il routing definito dall'utente è sufficiente, in questo modo si instrada tutto il traffico da un computer a un altro computer nella stessa subnet tramite l'istanza di Firewall di Azure. Per evitare questo problema, includere una route per la subnet nel routing definito dall'utente con un hop successivo di tipo **VNET**. La gestione di queste route può essere complessa e soggetta a errori. Il metodo consigliato per la segmentazione della rete interna prevede l'uso di gruppi di sicurezza di rete, che non richiedono routing definiti dall'utente.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Firewall di Azure usa SNAT in uscita tra le reti private?

Firewall di Azure non usa SNAT quando l'indirizzo IP di destinazione è un intervallo di indirizzi IP privati, in conformità allo standard [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Se l'organizzazione usa un intervallo di indirizzi IP pubblici per le reti private, Firewall di Azure invierà il traffico tramite SNAT a uno degli indirizzi IP privati firewall in AzureFirewallSubnet. È possibile configurare Firewall di Azure in modo da **non** usare SNAT per l'intervallo di indirizzi IP pubblici. Per altre informazioni, vedere [Intervalli di indirizzi IP privati SNAT di Firewall di Azure](snat-private-range.md).

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>Il tunneling forzato o il concatenamento a un'appliance virtuale di rete è supportato?

Il tunneling forzato è supportato quando si crea un nuovo firewall. Non è possibile configurare un firewall esistente per il tunneling forzato. Per altre informazioni, vedere [Tunneling forzato di Firewall di Azure](forced-tunneling.md).

Connettività diretta al Firewall di Azure. Se AzureFirewallSubnet apprende una route predefinita alla rete locale tramite BGP è necessario sostituirla con una route UDR 0.0.0.0/0 con il valore **NextHopType** impostato come **Internet** per mantenere connettività diretta a Internet.

Se la configurazione richiede il tunneling forzato in una rete locale ed è possibile determinare i prefissi IP di destinazione per le destinazioni Internet, è possibile configurare questi intervalli con la rete locale come hop successivo tramite una route definita dall'utente in AzureFirewallSubnet. In alternativa, per definire queste route, è possibile usare BGP.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Vi sono restrizioni relative al gruppo di risorse del firewall?

Sì. Il firewall, la rete virtuale e l'indirizzo IP pubblico devono trovarsi nello stesso gruppo di risorse.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Quando si configura DNAT per il traffico di rete Internet in ingresso, è necessario anche configurare una regola di rete corrispondente per consentire tale traffico?

No. Le regole NAT aggiungono in modo implicito una regola di rete corrispondente per consentire il traffico convertito. È possibile sostituire questo comportamento aggiungendo in modo esplicito una raccolta regole di rete con regole di negazione corrispondenti al traffico convertito. Per altre informazioni sulla logica di elaborazione delle regole di Firewall di Azure, vedere [Azure Firewall rule processing logic](rule-processing.md) (Logica di elaborazione delle regole di Firewall di Azure).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Come funzionano i caratteri jolly in un nome di dominio completo di destinazione di una regola dell'applicazione?

I caratteri jolly attualmente possono essere utilizzati solo sul lato sinistro del nome di dominio completo. Ad esempio, ***. contoso.com** e ***contoso.com**.

Se si configura * **.contoso.com**, è consentito *anyvalue*.contoso.com, ma non contoso.com (radice del dominio). Se si intende consentire la radice del dominio, è necessario configurarla in modo esplicito come nome di dominio completo di destinazione.

## <a name="what-does-provisioning-state-failed-mean"></a>Cosa significa *Stato di provisioning: non riuscito*?

Ogni volta che viene applicata una modifica alla configurazione, Firewall di Azure tenta di aggiornare tutte le istanze back-end sottostanti. In rari casi, una di tali istanze back-end potrebbe non riuscire ad aggiornarsi con la nuova configurazione e il processo di aggiornamento si interrompe con uno stato di provisioning non riuscito. Firewall di Azure è ancora operativo, ma la configurazione applicata potrebbe trovarsi in uno stato incoerente in cui per alcune istanze la configurazione è quella precedente, mentre per altre il set di regole è aggiornato. In tal caso, provare ad aggiornare la configurazione ancora una volta fino a quando l'operazione non ha esito positivo e il firewall si trova in uno stato di provisioning *Riuscito*.

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>In che modo Firewall di Azure gestisce la manutenzione pianificata e gli errori non pianificati?
Firewall di Azure è costituito da diversi nodi back-end in una configurazione attiva-attiva.  Per ogni manutenzione pianificata, è disponibile la logica di svuotamento delle connessioni per aggiornare correttamente i nodi.  Gli aggiornamenti vengono pianificati durante le ore non lavorative per ogni area di Azure per limitare ulteriormente il rischio di interruzioni.  Per i problemi non pianificati, viene creata un'istanza di un nuovo nodo per sostituire il nodo in cui si è verificato l'errore.  La connettività al nuovo nodo viene in genere ristabilita entro 10 secondi dal momento in cui si è verificato l'errore.

## <a name="how-does-connection-draining-work"></a>Come funziona lo svuotamento delle connessioni?

Per ogni manutenzione pianificata, la logica di svuotamento delle connessioni aggiorna correttamente i nodi back-end. Firewall di Azure attende 90 secondi per la chiusura delle connessioni esistenti. Se necessario, i client possono ristabilire automaticamente la connettività a un altro nodo back-end.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Esiste un limite di caratteri per un nome di firewall?

Sì. È previsto un limite di 50 caratteri per il nome di un firewall.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Perché Firewall di Azure ha bisogno di una dimensione della subnet /26?

Firewall di Azure deve effettuare il provisioning di più istanze di macchine virtuali in base alle dimensioni. Uno spazio indirizzi /26 garantisce che il firewall disponga di un numero sufficiente di indirizzi IP per la scalabilità.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>È necessario modificare la dimensione della subnet del firewall in base alla scalabilità del servizio?

No. Firewall di Azure non ha  bisogno di una subnet maggiore di/26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Come è possibile aumentare la velocità effettiva del firewall?

La capacità di velocità effettiva iniziale di Firewall di Azure è 2,5 - 3 Gbps ed è scalabile fino a 30 Gbps. Il ridimensionamento viene eseguito automaticamente in base all'uso della CPU e alla velocità effettiva.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Quanto tempo è necessario per aumentare le istanze di Firewall di Azure?

Firewall di Azure viene ridimensionato gradualmente quando la velocità effettiva media o il consumo medio della CPU è pari al 60%. Una velocità effettiva massima di distribuzione predefinita è approssimativamente 2,5-3 Gbps e inizia a aumentare il numero di unità quando raggiunge il 60% di tale numero. L'aumento delle istanze richiede da cinque a sette minuti. 

Quando si esegue il test delle prestazioni, assicurarsi di eseguire il test per almeno 10-15 minuti e avviare nuove connessioni per sfruttare i vantaggi dei nodi firewall appena creati.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Firewall di Azure consente l'accesso ad Active Directory per impostazione predefinita?

No. Firewall di Azure blocca l'accesso ad Active Directory per impostazione predefinita. Per consentire l'accesso, configurare il tag del servizio AzureActiveDirectory. Per altre informazioni, vedere [Tag del servizio Firewall di Azure](service-tags.md).

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>È possibile escludere un nome di dominio completo o un indirizzo IP dal filtro basato sull'intelligence sulle minacce di Firewall di Azure?

Sì, è possibile usare Azure PowerShell per eseguire questa operazione:

```azurepowershell
# Add a Threat Intelligence Whitelist to an Existing Azure Firewall

## Create the Whitelist with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name $firewallname -ResourceGroupName $RG
$fw.ThreatIntelWhitelist.IpAddresses = @($fw.ThreatIntelWhitelist.IpAddresses + $ipaddresses)
$fw.ThreatIntelWhitelist.fqdns = @($fw.ThreatIntelWhitelist.fqdns + $fqdns)


Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>Perché un ping TCP e strumenti simili si connettono correttamente a un nome di dominio completo di destinazione anche quando nessuna regola in Firewall di Azure consente il traffico?

Un ping TCP non si connette effettivamente al nome di dominio completo di destinazione. Questo problema si verifica perché il proxy trasparente di Firewall di Azure è in ascolto sulla porta 80/443 per il traffico in uscita. Il ping TCP stabilisce una connessione con il firewall, che quindi elimina il pacchetto e registra la connessione. Questo comportamento non ha alcun effetto sulla sicurezza. Per evitare confusione, tuttavia, verranno esaminate le potenziali modifiche apportate a questo comportamento.

## <a name="are-there-limits-for-the-number-of-ip-addresses-supported-by-ip-groups"></a>Sono previsti limiti per il numero di indirizzi IP supportati da gruppi IP?

Sì. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-i-move-an-ip-group-to-another-resource-group"></a>È possibile spostare un gruppo IP in un altro gruppo di risorse?

No, il trasferimento di un gruppo IP a un altro gruppo di risorse non è attualmente supportato.

## <a name="what-is-the-tcp-idle-timeout-for-azure-firewall"></a>Che cos'è il timeout di inattività TCP per il firewall di Azure?

Un comportamento standard di un firewall di rete è garantire che le connessioni TCP vengano mantenute attive e chiuderle immediatamente se non è presente alcuna attività. Il timeout di inattività TCP del firewall di Azure è di quattro minuti. Questa impostazione non è configurabile. Se un periodo di inattività è più lungo del valore di timeout, non vi è alcuna garanzia che la sessione TCP o HTTP venga mantenuta. Una prassi comune consiste nell'usare una connessione TCP keep-alive per mantenere la connessione attiva per un periodo più lungo. Per ulteriori informazioni, vedere gli [esempi di .NET](https://docs.microsoft.com/dotnet/api/system.net.servicepoint.settcpkeepalive?redirectedfrom=MSDN&view=netcore-3.1#System_Net_ServicePoint_SetTcpKeepAlive_System_Boolean_System_Int32_System_Int32_).

## <a name="can-i-deploy-azure-firewall-without-a-public-ip-address"></a>È possibile distribuire il firewall di Azure senza un indirizzo IP pubblico?

No, attualmente è necessario distribuire il firewall di Azure con un indirizzo IP pubblico.

## <a name="where-does-azure-firewall-store-customer-data"></a>Dove Azure firewall archivia i dati dei clienti?

Il firewall di Azure non sposta o archivia i dati dei clienti dall'area in cui è distribuito.