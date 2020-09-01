---
title: Baseline della sicurezza di Azure per app per la logica
description: Baseline della sicurezza di Azure per app per la logica
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2e3b6845b66978b4892303549dabca4034a870b2
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079643"
---
# <a name="azure-security-baseline-for-logic-apps"></a>Baseline della sicurezza di Azure per app per la logica

La linea di base di sicurezza di Azure per le app per la logica contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](../security/benchmarks/overview.md), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere la [Panoramica delle baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Controllo di sicurezza: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: i connettori che vengono eseguiti nel servizio app per la logica multi-tenant "globale" vengono distribuiti e gestiti da Microsoft. Questi connettori forniscono trigger e azioni per l'accesso a servizi cloud, sistemi locali o entrambi, tra cui Office 365, archiviazione BLOB di Azure, SQL Server, Dynamics, Salesforce, SharePoint e altro ancora. È possibile usare il tag del servizio AzureConnectors quando si specificano le regole nel gruppo di sicurezza di rete o nel firewall di Azure per consentire l'accesso alle risorse correlate.

Per le app per la logica che richiedono l'accesso diretto alle risorse in una rete virtuale di Azure, è possibile creare un ambiente del servizio di integrazione (ISE) in cui è possibile compilare, distribuire ed eseguire le app per la logica su risorse dedicate. Alcune reti virtuali di Azure usano endpoint privati (collegamento privato di Azure) per fornire l'accesso ai servizi PaaS di Azure, ad esempio archiviazione di Azure, Azure Cosmos DB, database SQL di Azure, servizi partner o servizi clienti ospitati in Azure. Se le app per la logica necessitano dell'accesso alle reti virtuali che usano endpoint privati, è necessario creare, distribuire ed eseguire tali app per la logica all'interno di ISE.

Quando si crea ISE, è possibile scegliere di usare endpoint di accesso interni o esterni. La selezione determina se i trigger di richiesta o webhook nelle app per la logica in ISE possono ricevere chiamate dall'esterno della rete virtuale. Gli endpoint di accesso interno ed esterno influiscono anche sul fatto che sia possibile visualizzare la cronologia di esecuzione dell'app per la logica, inclusi gli input e gli output per un'esecuzione, dall'interno o dall'esterno della rete virtuale.

Assicurarsi che tutte le distribuzioni di subnet della rete virtuale correlate all'ISE dispongano di un gruppo di sicurezza di rete applicato con controlli di accesso alla rete specifici per le porte e le origini attendibili dell'applicazione. Quando si distribuiscono le app per la logica in ISE, usare il collegamento privato. Il collegamento privato di Azure consente di accedere ai servizi PaaS di Azure e ai servizi di proprietà del cliente/partner ospitati di Azure tramite un endpoint privato nella rete virtuale. In alternativa, se si ha un caso d'uso specifico, è possibile soddisfare questo requisito implementando il firewall di Azure. Per ridurre la complessità quando si configurano le regole di sicurezza, usare i tag di servizio che rappresentano gruppi di prefissi di indirizzi IP per un servizio di Azure specifico.

* [Informazioni sui connettori per le app per la logica](../connectors/apis-list.md)

* [Informazioni sui tag del servizio in Azure](../virtual-network/service-tags-overview.md)

* [Informazioni sull'accesso alle risorse della rete virtuale di Azure da app per la logica di Azure tramite gli ambienti di Integration Services (ISEs)](./connect-virtual-network-vnet-isolated-environment-overview.md)

* [Informazioni sugli endpoint del servizio rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md)

* [Informazioni sul collegamento privato di Azure](../private-link/private-link-overview.md)

* [Informazioni sull'accesso all'endpoint ISE](./connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

* [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

* [Come creare una NSG con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

* [Come distribuire e configurare il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Come abilitare l'accesso per ISE](./connect-virtual-network-vnet-isolated-environment.md#enable-access-for-ise)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Condiviso

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

**Linee guida**: se si eseguono app per la logica in un ambiente Integration Services (ISE) che usa un punto di accesso esterno, è possibile usare un gruppo di sicurezza di rete (NSG) per ridurre il rischio di exfiltration dei dati. Abilitare i log di flusso NSG e inviare i log a un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log di flusso NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

* [Informazioni sull'accesso all'endpoint ISE](./connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

* [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: se l'app per la logica usa un trigger basato su richiesta, che riceve le chiamate o le richieste in ingresso, ad esempio il trigger di richiesta o webhook, è possibile limitare l'accesso in modo che solo i client autorizzati possano chiamare l'app per la logica.

Se si eseguono app per la logica in un ambiente Integration Services (ISE), abilitare protezione DDoS standard nella rete virtuale associata a ISE per proteggersi da attacchi DDoS. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

Distribuire il firewall di Azure in ogni limite di rete dell'organizzazione con l'Intelligence per le minacce abilitata e configurata per "avviso e negazione" per il traffico di rete dannoso.

Usare il Centro sicurezza di Azure accesso alla rete just-in-time per configurare gruppi per limitare l'esposizione degli endpoint agli indirizzi IP approvati per un periodo di tempo limitato.

Usare il Centro sicurezza di Azure Adaptive Network hardening per consigliare configurazioni NSG che limitano le porte e gli indirizzi IP di origine in base al traffico effettivo e all'Intelligence per le minacce.

* [Come proteggere le chiamate in ingresso alle app per la logica](./logic-apps-securing-a-logic-app.md#access-to-request-based-triggers)

* [Come limitare gli indirizzi IP in ingresso](./logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

* [Come configurare la protezione DDoS](../virtual-network/manage-ddos-protection.md)

* [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](../security-center/threat-protection.md)

* [Informazioni sul centro sicurezza di Azure Adaptive Network hardening](../security-center/security-center-adaptive-network-hardening.md)

* [Informazioni sul controllo di accesso di rete just-in-Time del Centro sicurezza di Azure](../security-center/security-center-just-in-time.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: se si eseguono app per la logica in un ambiente Integration Services (ISE) che usa un punto di accesso esterno, è possibile usare un gruppo di sicurezza di rete (NSG) per ridurre il rischio di exfiltration dei dati. Abilitare i log di flusso NSG e inviare i log a un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log di flusso NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Per fornire ulteriori informazioni sulla protezione e sul traffico di rete, è possibile fare riferimento ai log di accesso, che vengono generati solo se sono stati abilitati in ogni istanza del gateway applicazione. È possibile usare questo log per visualizzare i modelli di accesso al gateway applicazione e analizzare informazioni importanti. I dati includono indirizzo IP del chiamante, URL richiesto, latenza della risposta, codice restituito, byte in ingresso e in uscita.

In caso contrario, è possibile sfruttare una soluzione di terze parti dal Marketplace per soddisfare questo requisito.

* [Informazioni sull'accesso all'endpoint ISE](./connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

* [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

* [Come integrare Gestione API in una VNET interna con il gateway applicazione](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

* [Come comprendere i log di accesso di WAF](../web-application-firewall/ag/web-application-firewall-logs.md#access-log)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida**: selezionare un'offerta da Azure Marketplace che supporta la funzionalità di ID/IP con funzionalità di ispezione del payload. Se il rilevamento e/o la prevenzione di intrusioni in base all'ispezione del payload non costituisce un requisito, è possibile usare Firewall di Azure con intelligence sulle minacce. I filtri basati sull'intelligence sulle minacce del Firewall di Azure possono creare avvisi e rifiutare il traffico da o verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

Distribuire la soluzione firewall scelta a ogni limite di rete dell'organizzazione per rilevare e/o negare il traffico dannoso.

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Come configurare gli avvisi con il firewall di Azure](../firewall/threat-intel.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee guida**: se si eseguono app per la logica in un ambiente Integration Services (ISE), distribuire applicazione Azure gateway.

* [Informazioni su come integrare Gestione API in una VNET interna con un gateway applicazione](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

* [Come configurare il gateway applicazione per l'uso di HTTPS](../application-gateway/create-ssl-portal.md)

* [Informazioni sul bilanciamento del carico di livello 7 con i gateway applicazione Web di Azure](../application-gateway/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: per le risorse che richiedono l'accesso alle istanze di app per la logica di Azure, usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, logica, LogicAppsManagement) nel campo di origine o destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

* [Per ulteriori informazioni sull'utilizzo dei tag di servizio](../virtual-network/service-tags-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete correlate alle istanze di app per la logica di Azure con criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. Logic" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle istanze di app per la logica di Azure. È anche possibile usare le definizioni di criteri predefinite, ad esempio:

È consigliabile abilitare i log di diagnostica in App per la logica

Lo standard di protezione DDoS deve essere abilitato

È anche possibile usare i progetti di Azure per semplificare le distribuzioni su larga scala di Azure tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controllo degli accessi in base al ruolo di Azure (RBAC di Azure) e criteri, in una singola definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni**: per le singole regole NSG, usare il campo "Description" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

* [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

* [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

* [Elenco di definizioni di criteri di Azure per app per la logica](./policy-samples.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze di app per la logica di Azure. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

* [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: Registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Linee guida**: Microsoft gestisce l'origine dell'ora usata per le risorse di Azure, ad esempio app per la logica di Azure per i timestamp nei log.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: per ottenere informazioni di debug più complete sulle app per la logica durante la fase di esecuzione, è possibile configurare e usare i log di monitoraggio di Azure per registrare e archiviare le informazioni sui dati e gli eventi di runtime, ad esempio eventi trigger, eventi di esecuzione ed eventi di azione in un'area di lavoro log Analytics. Monitoraggio di Azure consente di monitorare gli ambienti cloud e locali per consentire una gestione semplificata della disponibilità e delle prestazioni. Usando i log di monitoraggio di Azure, è possibile creare query di log che consentono di raccogliere ed esaminare queste informazioni. È anche possibile usare questi dati di diagnostica con altri servizi di Azure, ad esempio archiviazione di Azure e hub eventi di Azure.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

* [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/activity-log.md)

* [Come configurare i log di monitoraggio di Azure e raccogliere i dati di diagnostica per le app per la logica di Azure](./monitor-logic-apps-log-analytics.md)

* [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: per ottenere informazioni di debug più complete sulle app per la logica durante la fase di esecuzione, è possibile configurare e usare i log di monitoraggio di Azure per registrare e archiviare le informazioni sui dati e gli eventi di runtime, ad esempio eventi trigger, eventi di esecuzione ed eventi di azione in un'area di lavoro log Analytics. Monitoraggio di Azure consente di monitorare gli ambienti cloud e locali per consentire una gestione semplificata della disponibilità e delle prestazioni. Usando i log di monitoraggio di Azure, è possibile creare query di log che consentono di raccogliere ed esaminare queste informazioni. È anche possibile usare questi dati di diagnostica con altri servizi di Azure, ad esempio archiviazione di Azure e hub eventi di Azure.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

* [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/activity-log.md)

* [Come configurare i log di monitoraggio di Azure e raccogliere i dati di diagnostica per le app per la logica di Azure](./monitor-logic-apps-log-analytics.md)

* [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni**: dopo la creazione e l'esecuzione di un'app per la logica, è possibile controllare lo stato di esecuzione dell'app per la logica, la cronologia delle esecuzioni, la cronologia dei trigger e le prestazioni. Per il monitoraggio degli eventi in tempo reale e il debug più completo, configurare la registrazione diagnostica per l'app per la logica usando i log di monitoraggio di Azure. Questo servizio di Azure consente di monitorare gli ambienti cloud e locali in modo da mantenere la disponibilità e le prestazioni più semplici. È quindi possibile trovare e visualizzare gli eventi, ad esempio eventi trigger, eventi di esecuzione ed eventi di azione. Archiviando queste informazioni nei log di monitoraggio di Azure, è possibile creare query di log che consentono di trovare e analizzare tali informazioni. È anche possibile usare questi dati di diagnostica con altri servizi di Azure, ad esempio archiviazione di Azure e hub eventi di Azure.

In monitoraggio di Azure impostare il periodo di conservazione del log per i log associati alle istanze di app per la logica di Azure in base alle normative di conformità dell'organizzazione.

* [Come monitorare lo stato di esecuzione, rivedere la cronologia dei trigger e configurare gli avvisi per le app per la logica di Azure](./monitor-logic-apps.md)

* [Come impostare i parametri di conservazione dei log](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida**: per configurare la registrazione per l'app per la logica, è possibile abilitare log Analytics quando si crea l'app per la logica oppure è possibile installare la soluzione di gestione delle app per la logica nell'area di lavoro log Analytics per le app per la logica esistenti. Questa soluzione fornisce informazioni aggregate per le esecuzioni dell'app per la logica e include dettagli specifici, ad esempio stato, tempo di esecuzione, stato di reinvio e ID correlazione. Quindi, per abilitare la registrazione e la creazione di query per queste informazioni, configurare i log di monitoraggio di Azure.

È anche possibile abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro Log Analytics. Eseguire query in Log Analytics per cercare termini, identificare tendenze, analizzare modelli e fornire molte altre informazioni basate sui dati del log attività che potrebbero essere stati raccolti per le app per la logica di Azure.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

* [Come configurare i log di monitoraggio di Azure e raccogliere i dati di diagnostica per le app per la logica di Azure](./monitor-logic-apps-log-analytics.md)

* [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/activity-log.md)

* [Come raccogliere e analizzare i log attività di Azure in Log Analytics di monitoraggio di Azure](../azure-monitor/platform/activity-log.md)

* [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Indicazioni**: usare il Centro sicurezza di Azure con log Analytics per il monitoraggio e l'invio di avvisi su attività anomale rilevate in registri di sicurezza ed eventi.

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure.

* [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Come gestire gli avvisi nel centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md)

* [Come inviare un avviso sui dati del log di log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida**: non applicabile; App per la logica di Azure non elabora o produce log correlati all'anti-malware.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida**: non applicabile; App per la logica di Azure non elabora o produce log correlati a DNS.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Non applicabile

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida**: Azure Active Directory (ad) include ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

Per accedere facilmente alle risorse protette da Azure Active Directory (Azure AD) ed eseguire l'autenticazione dell'identità senza eseguire l'accesso, l'app per la logica può usare un'identità gestita (in precedenza denominata identità del servizio gestita) anziché credenziali o segreti. Azure gestisce questa identità per l'utente e consente di proteggere le proprie credenziali perché non è necessario fornire o ruotare i segreti.

Ogni endpoint di richiesta in un'app per la logica ha una firma di accesso condiviso (SAS) nell'URL dell'endpoint. Se si condivide l'URL dell'endpoint per un trigger di richiesta con altre parti è possibile generare gli URL di callback che usano chiavi specifiche e hanno delle date di scadenza. In questo modo è possibile aggiornare le chiavi senza problemi o limitare l'accesso per attivare l'app per la logica sulla base di un determinato lasso di tempo.

* [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Autenticare l'accesso alle risorse di Azure usando identità gestite in App per la logica di Azure](./create-managed-service-identity.md)

* [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Come proteggere l'accesso e i dati in app per la logica di Azure con SAS](./logic-apps-securing-a-logic-app.md#access-to-request-based-triggers)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: Azure Active Directory e app per la logica di Azure non hanno il concetto di password predefinite.

Se viene usata l'autenticazione di base, sarà necessario specificare un nome utente e una password. Quando si creano queste credenziali, assicurarsi di configurare una password complessa per l'autenticazione.

Se si usa l'infrastruttura come codice, evitare di archiviare le password nel codice e usare invece Azure Key Vault per archiviare e recuperare le credenziali.

* [Come proteggere e accedere ai dati nelle app per la logica](./logic-apps-securing-a-logic-app.md)

* [Come impostare e recuperare un segreto da Azure Key Vault](../key-vault/secrets/quick-create-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio:
- Alla sottoscrizione deve essere assegnato più di un proprietario
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

* [Come usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso (Anteprima)](../security-center/security-center-identity-access.md)

* [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Indicazioni**: usare una registrazione di app di Azure (entità servizio) per recuperare un token che può essere usato per interagire con gli insiemi di credenziali dei servizi di ripristino tramite chiamate API.

Molti connettori richiedono anche la prima volta che si crea una connessione al servizio o al sistema di destinazione e si forniscono le credenziali di autenticazione o altri dettagli di configurazione prima di poter usare un trigger o un'azione nell'app per la logica. Ad esempio, è necessario autorizzare una connessione a un account Twitter per accedere ai dati o pubblicare per conto dell'utente.]

Per i connettori che usano Azure Active Directory (Azure AD) OAuth, la creazione di una connessione significa accedere al servizio, ad esempio Office 365, Salesforce o GitHub, in cui il token di accesso è crittografato e archiviato in modo sicuro in un archivio segreto di Azure. Altri connettori, ad esempio FTP e SQL, richiedono una connessione con i dettagli di configurazione, ad esempio l'indirizzo del server, il nome utente e la password. Questi dettagli di configurazione della connessione vengono inoltre crittografati e archiviati in modo sicuro.

* [Come chiamare le API REST di Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Come registrare l'applicazione client (entità servizio) con Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Informazioni API dei trigger del flusso di lavoro](/rest/api/logic/workflowtriggers)

* [Informazioni sulla configurazione del connettore](../connectors/apis-list.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Indicazioni**: abilitare Azure Active Directory (AD) Multi-Factor Authentication (MFA) e seguire le raccomandazioni sulla gestione delle identità e degli accessi nel Centro sicurezza di Azure.

* [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni**: usare workstation con accesso con privilegi insieme a Multi-Factor Authentication (MFA) configurato per l'accesso e la configurazione delle risorse di Azure.

* [Informazioni sulle workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Indicazioni**: usare Azure Active Directory (AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando nell'ambiente si verifica un'attività sospetta o non sicura.

Inoltre, usare i rilevamenti di rischi di Azure AD per visualizzare gli avvisi e i report sul comportamento utente rischioso.

* [Come distribuire Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Informazioni sui rilevamenti di rischi di Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare le località denominate di accesso condizionale per consentire l'accesso al portale di Azure solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

Ogni endpoint di richiesta in un'app per la logica dispone inoltre di una firma di accesso condiviso (SAS) nell'URL dell'endpoint. È possibile limitare l'app per la logica affinché accetti richieste solo da determinati indirizzi IP.

* [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

* [Informazioni su come limitare gli indirizzi IP in ingresso nelle app per la logica](./logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (ad) come sistema di autenticazione e autorizzazione centrale per le istanze di app per la logica di Azure. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

Quando è disponibile il supporto per le app per la logica, usare un'identità gestita per accedere facilmente ad altre risorse protette da Azure Active Directory (Azure AD) e autenticare l'identità senza accedere, anziché con credenziali o segreti. Azure gestisce questa identità per l'utente e consente di proteggere le proprie credenziali perché non è necessario fornire o ruotare i segreti.

App per la logica di Azure supporta le identità gestite sia assegnate dal sistema che dall'utente. L'app per la logica può usare l'identità assegnata dal sistema o una singola identità assegnata dall'utente che è possibile condividere in un gruppo di app per la logica, ma non entrambe. Attualmente, solo trigger e azioni predefiniti specifici supportano le identità gestite, non i connettori gestiti o le connessioni, ad esempio:
- HTTP
- Funzioni di Azure
- Gestione API di Azure
- Servizi app di Azure

* [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* [Autenticare l'accesso alle risorse di Azure usando identità gestite in App per la logica di Azure](./create-managed-service-identity.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Linee guida**: Azure Active Directory (ad) fornisce log che consentono di individuare gli account obsoleti. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

* [Informazioni sulla creazione di report Azure AD](../active-directory/reports-monitoring/index.yml)

* [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: usare Azure Active Directory (ad) come sistema di autenticazione e autorizzazione centrale per le istanze di app per la logica di Azure. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

È possibile accedere alle origini del registro eventi di attività, controllo e rischio Azure AD di accesso, che consentono di eseguire l'integrazione con Azure Sentinel o con SIEM di terze parti.

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi del log desiderati all'interno Log Analytics.

* [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Come caricare dati in Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Avvisare in caso di deviazione dal comportamento di accesso dell'account

**Indicazioni**: Usare le funzionalità di rilevamento dei rischi e protezione delle identità di Azure Active Directory per configurare risposte automatiche ad azioni sospette correlate a identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.

* [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: attualmente non disponibile; Customer Lockbox non è ancora supportata per le app per la logica di Azure.

* [Elenco dei servizi Customer Lockbox supportati](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida**: i connettori che vengono eseguiti nel servizio app per la logica multi-tenant "globale" vengono distribuiti e gestiti da Microsoft. Questi connettori forniscono trigger e azioni per l'accesso a servizi cloud, sistemi locali o entrambi, tra cui Office 365, archiviazione BLOB di Azure, SQL Server, Dynamics, Salesforce, SharePoint e altro ancora.

Per le app per la logica che richiedono l'accesso diretto alle risorse in una rete virtuale di Azure, è possibile creare un ambiente del servizio di integrazione (ISE) in cui è possibile compilare, distribuire ed eseguire le app per la logica su risorse dedicate. Alcune reti virtuali di Azure usano endpoint privati (collegamento privato di Azure) per fornire l'accesso ai servizi PaaS di Azure, ad esempio archiviazione di Azure, Azure Cosmos DB o il database SQL di Azure, i servizi dei partner o i servizi dei clienti ospitati in Azure. Se le app per la logica necessitano dell'accesso alle reti virtuali che usano endpoint privati, è necessario creare, distribuire ed eseguire tali app per la logica all'interno di ISE.

Quando si crea ISE, è possibile scegliere di usare endpoint di accesso interni o esterni. La selezione determina se i trigger di richiesta o webhook nelle app per la logica in ISE possono ricevere chiamate dall'esterno della rete virtuale.

Inoltre, implementare l'isolamento utilizzando sottoscrizioni e gruppi di gestione distinti per singoli domini di sicurezza, ad esempio il tipo di ambiente e il livello di sensibilità dei dati. È possibile limitare il livello di accesso alle risorse di Azure richieste dalle applicazioni e dagli ambienti aziendali. È possibile controllare l'accesso alle risorse di Azure tramite il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).

* [Informazioni sui connettori per le app per la logica](../connectors/apis-list.md)

* [Accedere alle risorse di Rete virtuale di Azure da App per la logica di Azure usando ambienti del servizio di integrazione (ISE)](./connect-virtual-network-vnet-isolated-environment-overview.md)

* [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

* [Come creare gruppi di gestione](../governance/management-groups/create.md)

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: attualmente non disponibile; le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per le app per la logica di Azure.

Sfrutta una soluzione di terze parti da Azure Marketplace sui perimetri di rete che monitora il trasferimento non autorizzato di informazioni riservate e blocca tali trasferimenti mentre invia avvisi ai professionisti della sicurezza delle informazioni.

Microsoft gestisce l'infrastruttura sottostante per le app per la logica di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

* [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsibilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: crittografare tutte le informazioni riservate in transito. In app per la logica di Azure tutti i dati durante l'esecuzione di un'app per la logica vengono crittografati durante il transito con Transport Layer Security (TLS) e inattivi. Quando si visualizza la cronologia di esecuzione di un'app per la logica, App per la logica autentica l'accesso e fornisce i collegamenti agli input e agli output delle richieste e delle risposte per ogni esecuzione. Tuttavia, per le azioni che gestiscono password, segreti, chiavi o altre informazioni riservate, è opportuno impedire ad altri utenti di visualizzare e accedere a tali dati. Ad esempio, se l'app per la logica ottiene un segreto da Azure Key Vault da usare per l'autenticazione di un'azione HTTP, occorre nascondere tale segreto in modo che non venga visualizzato.

Il trigger request supporta solo Transport Layer Security (TLS) 1,2 per le richieste in ingresso. Assicurarsi che tutti i client che si connettono alle risorse di Azure siano in grado di negoziare TLS 1,2 o versione successiva. Le chiamate in uscita che usano il connettore HTTP supportano Transport Layer Security (TLS) 1,0, 1,1 e 1,2.

Seguire le raccomandazioni del Centro sicurezza di Azure per la crittografia dei dati inattivi e la crittografia in transito, ove applicabile.

* [Proteggere l'accesso e i dati in App per la logica di Azure](./logic-apps-securing-a-logic-app.md)

* [Ricevere e rispondere alle richieste HTTPS in ingresso in App per la logica di Azure](../connectors/connectors-native-reqres.md#tls-support)

* [Chiamare gli endpoint di servizio su HTTP o HTTPS da App per la logica di Azure](../connectors/connectors-native-http.md#tls-support)

* [Informazioni sulla crittografia in transito con Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Informazioni sulla crittografia dei dati inattivi con Azure](../security/fundamentals/encryption-atrest.md)

* [Configurare chiavi gestite dal cliente per la crittografia dei dati inattivi per gli ambienti di Integration Services (ISEs) in app per la logica di Azure](./customer-managed-keys-integration-service-environment.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: in app per la logica di Azure, molti trigger e azioni hanno impostazioni che è possibile abilitare per proteggere gli input, gli output o entrambi nascondendo tali dati dalla cronologia di esecuzione di un'app per la logica.

Microsoft gestisce l'infrastruttura sottostante per le app per la logica di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

* [Proteggere l'accesso ai dati della cronologia di esecuzione](./logic-apps-securing-a-logic-app.md#access-to-run-history-data)

* [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Linee guida**: è possibile consentire solo a utenti o gruppi specifici di eseguire attività specifiche, ad esempio la gestione, la modifica e la visualizzazione di app per la logica. Per controllare le autorizzazioni, usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) in modo da poter assegnare ruoli personalizzati o predefiniti ai membri nella sottoscrizione di Azure:
- Collaboratore per app per la logica: consente di gestire le app per la logica, ma non di modificarne l'accesso.
- Operatore per app per la logica: consente di leggere, abilitare e disabilitare le app per la logica, ma non di modificarle o aggiornarle.

Per impedire la modifica o l'eliminazione delle app per la logica da parte di altri utenti è inoltre possibile usare i blocchi per le risorse di Azure. Questa funzionalità impedisce ad altri utenti di modificare o eliminare le risorse di produzione.

* [Proteggere l'accesso alle operazioni di app per la logica di Azure](./logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo. Microsoft gestisce l'infrastruttura sottostante per le app per la logica di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

* [Protezione dei dati dei clienti di Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: app per la logica di Azure si basa su archiviazione di Azure per archiviare e crittografare automaticamente i dati inattivi. Questa crittografia protegge i dati e consente di soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Per impostazione predefinita, archiviazione di Azure usa chiavi gestite da Microsoft per crittografare i dati.

Quando si crea un ambiente Integration Services (ISE) per ospitare le app per la logica e si desidera un maggiore controllo sulle chiavi di crittografia usate da archiviazione di Azure, è possibile configurare, usare e gestire la propria chiave usando Azure Key Vault. Questa funzionalità è nota anche come "Bring Your Own Key" (BYOK) e la chiave è detta "chiave gestita dal cliente".

* [Crittografare i dati inattivi per gli ambienti di Integration Services in app per la logica di Azure](./customer-managed-keys-integration-service-environment.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi quando si verificano modifiche in app per la logica di Azure e altre risorse critiche o correlate.

* [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [Controllo di sicurezza: gestione della vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee guida**: [non applicabile; Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano le app per la logica di Azure.]

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: distribuire la soluzione di gestione delle patch automatizzata per i titoli software di terze parti

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee guida**: non applicabile; Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano app per la logica di Azure.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Linee guida**: non applicabile; Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano app per la logica di Azure.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Indicazioni**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) disponibili nelle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure classiche possano essere individuate tramite Graph di risorse, è consigliabile creare e usare Azure Resource Manager risorse in futuro.

* [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle risorse di Azure. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Usare inoltre i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

* [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

* [Come creare gruppi di gestione](../governance/management-groups/create.md)

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definire e gestire un inventario delle risorse di Azure approvate

**Linee guida**: creare un inventario delle risorse di Azure approvate, ad esempio i connettori, e il software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione.

Nota: a causa dei dati e dei criteri di privacy di Google, è possibile usare il connettore Gmail solo con i servizi approvati da Google. Questa situazione è in continua evoluzione e potrebbe influire su altri connettori Google in futuro.

* [Elenco di tutti i connettori di app per la logica](/connectors/connector-reference/connector-reference-logicapps-connectors)

* [Informazioni sui problemi e sulle limitazioni per i connettori Gmail](/connectors/gmail/#known-issues-and-limitations)

* [Altre informazioni sull'informativa sulla privacy di Google](../connectors/connectors-google-data-security-privacy-policy.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/index.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

* [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: le risorse correlate alle app per la logica necessarie per le operazioni aziendali, ma possono comportare un rischio maggiore per l'organizzazione, devono essere isolate all'interno della propria macchina virtuale e/o della rete virtuale e sufficientemente protette con un firewall di Azure o un gruppo di sicurezza di rete.

Le app per la logica necessarie per le operazioni aziendali, ma possono comportare un rischio maggiore per l'organizzazione, devono essere isolate laddove possibile tramite gruppi di risorse distinti con autorizzazioni specifiche e limiti RBAC di Azure.

* [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

* [Come creare una NSG con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

* [Come creare gruppi di gestione](../governance/management-groups/create.md)

* [Come proteggere l'accesso alle app per la logica tramite RBAC di Azure](./logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Controllo di sicurezza: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le istanze di app per la logica di Azure con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Logic" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze di app per la logica. Ad esempio, è possibile impedire ad altri utenti di creare o usare le connessioni alle risorse in cui si vuole limitare l'accesso.

Inoltre, Azure Resource Manager è in grado di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per garantire che le configurazioni soddisfino o superino i requisiti di sicurezza per l'organizzazione.

Usare anche parametri protetti per proteggere i dati sensibili e i segreti.

* [Come visualizzare gli alias di Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Blocca le connessioni create dai connettori nelle app per la logica di Azure](./block-connections-connectors.md)

* [Esportazione di una singola e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

* [Come distribuire modelli di Azure Resource Manager per app per la logica di Azure](./logic-apps-deploy-azure-resource-manager-templates.md)

* [Informazioni sui parametri di azione protetti](./logic-apps-securing-a-logic-app.md#secure-action-parameters)

* [Raccomandazioni sulla sicurezza per i parametri](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

Definire e implementare configurazioni di sicurezza standard per le istanze di app per la logica di Azure con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Logic" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze di app per la logica. Ad esempio, è possibile impedire ad altri utenti di creare o usare le connessioni alle risorse in cui si vuole limitare l'accesso.

Inoltre, Azure Resource Manager è in grado di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per garantire che le configurazioni soddisfino o superino i requisiti di sicurezza per l'organizzazione.

Assicurarsi inoltre di proteggere i dati nella cronologia di esecuzione usando l'offuscamento.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

* [Blocca le connessioni create dai connettori nelle app per la logica di Azure](./block-connections-connectors.md)

* [Esportazione di una singola e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

* [Come distribuire modelli di Azure Resource Manager per app per la logica di Azure](./logic-apps-deploy-azure-resource-manager-templates.md)

* [Proteggere l'accesso agli input e agli output della cronologia di esecuzione](./logic-apps-securing-a-logic-app.md#obfuscate)

* [Proteggere l'accesso agli input di parametri](./logic-apps-securing-a-logic-app.md#secure-action-parameters)

* [Raccomandazioni sulla sicurezza per i parametri](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Condiviso

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni di criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire il codice in modo sicuro.

Inoltre, Azure Resource Manager è in grado di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per garantire che le configurazioni soddisfino o superino i requisiti di sicurezza per l'organizzazione.

* [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentazione di Azure Repos](/azure/devops/repos/index?view=azure-devops)

* [Esportazione di una singola e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni**: usare le definizioni di criteri di Azure predefinite e gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Logic" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione di rete delle risorse di Azure. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Indicazioni**: usare le definizioni di criteri di Azure predefinite e gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Logic" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [Deploy if not exist] per applicare automaticamente le configurazioni per le risorse di Azure.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: proteggere gli input e gli output nella cronologia di esecuzione dell'app per la logica usando l'offuscamento. Se si esegue la distribuzione in ambienti diversi, provare a parametrizzazione i valori nella definizione del flusso di lavoro dell'app per la logica che variano in base a tali ambienti. In questo modo, è possibile evitare i dati hardcoded usando un modello di Azure Resource Manager per distribuire l'app per la logica, proteggere i dati sensibili definendo parametri protetti e passare i dati come input distinti tramite i parametri del modello usando un file di parametri. È possibile utilizzare Key Vault per archiviare dati sensibili e utilizzare parametri di modello protetti che recuperano tali valori da Key Vault alla distribuzione. È quindi possibile fare riferimento all'insieme di credenziali delle chiavi e ai segreti nel file dei parametri.

Quando si crea un ambiente Integration Services (ISE) per ospitare le app per la logica e si desidera un maggiore controllo sulle chiavi di crittografia usate da archiviazione di Azure, è possibile configurare, usare e gestire la propria chiave usando Azure Key Vault. Questa funzionalità è nota anche come "Bring Your Own Key" (BYOK) e la chiave è detta "chiave gestita dal cliente".

* [Proteggere gli input e gli output nella cronologia di esecuzione in app per la logica di Azure](./logic-apps-securing-a-logic-app.md#obfuscate)

* [Raccomandazioni sulla sicurezza per i parametri](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

* [Proteggere l'accesso agli input di parametri in app per la logica di Azure](./logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

* [Passare i valori di parametro protetti durante la distribuzione usando Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

* [Configurare chiavi gestite dal cliente per la crittografia dei dati inattivi per gli ambienti di Integration Services (ISEs) in app per la logica di Azure](./customer-managed-keys-integration-service-environment.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: per accedere facilmente ad altre risorse protette da Azure Active Directory (Azure ad) e autenticare l'identità senza effettuare l'accesso, l'app per la logica può usare un'identità gestita (in precedenza identità del servizio gestita o MSI), anziché le credenziali o i segreti. Azure gestisce questa identità per l'utente e consente di proteggere le proprie credenziali perché non è necessario fornire o ruotare i segreti.

Attualmente, solo trigger e azioni predefiniti specifici supportano le identità gestite, non i connettori gestiti o le connessioni, ad esempio:
- HTTP
- Funzioni di Azure
- Gestione API di Azure
- Servizi app di Azure

* [Come autenticare l'accesso alle risorse di Azure usando identità gestite in app per la logica di Azure](./create-managed-service-identity.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Linee guida**: proteggere gli input e gli output nella cronologia di esecuzione dell'app per la logica usando l'offuscamento. Se si esegue la distribuzione in ambienti diversi, provare a parametrizzazione i valori nella definizione del flusso di lavoro dell'app per la logica che variano in base a tali ambienti. In questo modo, è possibile evitare i dati hardcoded usando un modello di Azure Resource Manager per distribuire l'app per la logica, proteggere i dati sensibili definendo parametri protetti e passare i dati come input distinti tramite i parametri del modello usando un file di parametri. È possibile utilizzare Key Vault per archiviare dati sensibili e utilizzare parametri di modello protetti che recuperano tali valori da Key Vault alla distribuzione. È quindi possibile fare riferimento all'insieme di credenziali delle chiavi e ai segreti nel file dei parametri.

È anche possibile implementare Credential scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

* [Proteggere gli input e gli output nella cronologia di esecuzione in app per la logica di Azure](./logic-apps-securing-a-logic-app.md#obfuscate)

* [Raccomandazioni sulla sicurezza per i parametri](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

* [Proteggere l'accesso agli input di parametri in app per la logica di Azure](./logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

* [Passare i valori di parametro protetti durante la distribuzione usando Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

* [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Controllo di sicurezza: difesa da malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: usare software antimalware gestito in modo centralizzato

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo. Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio app per la logica di Azure, ma non viene eseguito sui contenuti del cliente.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio backup di Azure, ma non viene eseguito sui contenuti.

Pre-analizza tutti i file caricati in risorse di Azure non di calcolo, ad esempio servizio app, Data Lake Storage, archiviazione BLOB e così via.

Usare il rilevamento delle minacce del Centro sicurezza di Azure per i servizi dati per rilevare il malware caricato negli account di archiviazione.

* [Informazioni su Microsoft anti-malware per servizi cloud e macchine virtuali di Azure](../security/fundamentals/antimalware.md)

* [Informazioni sul rilevamento delle minacce del Centro sicurezza di Azure per i servizi dati](../security-center/threat-protection.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: verificare che le firme e il software antimalware siano aggiornati

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [Controllo di sicurezza: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Linee guida**: implementare una soluzione di ripristino di emergenza per poter proteggere i dati, ripristinare rapidamente le risorse che supportano le funzioni aziendali critiche e mantenere le operazioni in esecuzione per mantenere la continuità aziendale (BC).

Questa strategia di ripristino di emergenza è incentrata sulla configurazione dell'app per la logica primaria per il failover in un'app per la logica di standby o di backup in un percorso alternativo in cui è disponibile anche app per la logica di Azure. In questo modo, se il database primario soffre di perdite, interruzioni o errori, il database secondario può intraprendere il lavoro. Questa strategia richiede che l'app per la logica secondaria e le risorse dipendenti siano già distribuite e pronte nel percorso alternativo.

Inoltre, è necessario espandere la definizione del flusso di lavoro sottostante dell'app per la logica in un modello di Azure Resource Manager. Questo modello definisce l'infrastruttura, le risorse, i parametri e altre informazioni per il provisioning e la distribuzione dell'app per la logica.

* [Altre informazioni sulla continuità aziendale e il ripristino di emergenza per app per la logica di Azure](./business-continuity-disaster-recovery-guidance.md)

* [Come automatizzare la distribuzione per app per la logica di Azure usando modelli di Azure Resource Manager](./logic-apps-azure-resource-manager-templates-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: implementare una soluzione di ripristino di emergenza per poter proteggere i dati, ripristinare rapidamente le risorse che supportano le funzioni aziendali critiche e mantenere le operazioni in esecuzione per mantenere la continuità aziendale (BC).

Questa strategia di ripristino di emergenza è incentrata sulla configurazione dell'app per la logica primaria per il failover in un'app per la logica di standby o di backup in un percorso alternativo in cui è disponibile anche app per la logica di Azure. In questo modo, se il database primario soffre di perdite, interruzioni o errori, il database secondario può intraprendere il lavoro. Questa strategia richiede che l'app per la logica secondaria e le risorse dipendenti siano già distribuite e pronte nel percorso alternativo.

Inoltre, è necessario espandere la definizione del flusso di lavoro sottostante dell'app per la logica in un modello di Azure Resource Manager. Questo modello definisce l'infrastruttura, le risorse, i parametri e altre informazioni per il provisioning e la distribuzione dell'app per la logica.

Ogni endpoint di richiesta in un'app per la logica ha una firma di accesso condiviso (SAS) nell'URL dell'endpoint. Se si usa Azure Key Vault per archiviare i segreti, assicurarsi che vengano eseguiti backup automatici regolari delle chiavi e degli URL.

* [Altre informazioni sulla continuità aziendale e il ripristino di emergenza per app per la logica di Azure](./business-continuity-disaster-recovery-guidance.md)

* [Come automatizzare la distribuzione per app per la logica di Azure usando modelli di Azure Resource Manager](./logic-apps-azure-resource-manager-templates-overview.md)

* [Come proteggere l'accesso e i dati in app per la logica di Azure con SAS](./logic-apps-securing-a-logic-app.md#access-to-request-based-triggers)

* [Come eseguire il backup di chiavi di Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni**: la strategia di ripristino di emergenza deve concentrarsi sulla configurazione dell'app per la logica primaria per eseguire il failover in un'app per la logica di standby o di backup in un percorso alternativo in cui è disponibile anche app per la logica di Azure. In questo modo, se il database primario soffre di perdite, interruzioni o errori, il database secondario può intraprendere il lavoro. Questa strategia richiede che l'app per la logica secondaria e le risorse dipendenti siano già distribuite e pronte nel percorso alternativo.

Testare il ripristino delle chiavi gestite dal cliente sottoposte a backup. Si noti che questo vale solo per le app per la logica eseguite in Integration Service Environments (ISE).

* [Altre informazioni sulla continuità aziendale e il ripristino di emergenza per app per la logica di Azure](./business-continuity-disaster-recovery-guidance.md)

* [Configurare chiavi gestite dal cliente per la crittografia dei dati inattivi per gli ambienti di Integration Services (ISEs) in app per la logica di Azure](./customer-managed-keys-integration-service-environment.md)

* [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni**: la strategia di ripristino di emergenza deve concentrarsi sulla configurazione dell'app per la logica primaria per eseguire il failover in un'app per la logica di standby o di backup in un percorso alternativo in cui è disponibile anche app per la logica di Azure. In questo modo, se il database primario soffre di perdite, interruzioni o errori, il database secondario può intraprendere il lavoro. Questa strategia richiede che l'app per la logica secondaria e le risorse dipendenti siano già distribuite e pronte nel percorso alternativo.

Proteggi le chiavi gestite dal cliente. Si noti che questo vale solo per le app per la logica eseguite in Integration Service Environments (ISE).

Abilitare l'eliminazione temporanea e ripulire la protezione in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.

* [Altre informazioni sulla continuità aziendale e il ripristino di emergenza per app per la logica di Azure](./business-continuity-disaster-recovery-guidance.md)

* [Configurare chiavi gestite dal cliente per la crittografia dei dati inattivi per gli ambienti di Integration Services (ISEs) in app per la logica di Azure](./customer-managed-keys-integration-service-environment.md)

* [Come abilitare l'eliminazione temporanea e ripulire la protezione in Key Vault](../storage/blobs/soft-delete-overview.md?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

* [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un proprio piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione, tramite i tag e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure, in particolare quelle che elaborano i dati sensibili. È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

* [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

* [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare per proteggere le risorse di Azure. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

* [Pubblicazione del NIST - Guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

* [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per contribuire a individuare i rischi per le risorse di Azure. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

* [Come configurare l'esportazione continua](../security-center/continuous-export.md)

* [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nei consigli per proteggere le risorse di Azure.

* [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Controllo di sicurezza: test di penetrazione ed esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: seguire le regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.

* [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)