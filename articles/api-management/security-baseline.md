---
title: Baseline di sicurezza di Azure per Gestione API
description: Baseline di sicurezza di Azure per Gestione API
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6ac568e56d201ef43e40428085b226608e83973a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024569"
---
# <a name="azure-security-baseline-for-api-management"></a>Baseline di sicurezza di Azure per Gestione API

La baseline di sicurezza di Azure per Gestione API contiene raccomandazioni utili per migliorare il comportamento di sicurezza della distribuzione.

La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](../security/benchmarks/overview.md), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere la [Panoramica delle baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Controllo di sicurezza: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

**Indicazioni**: Gestione API di Azure può essere distribuito all'interno di una rete virtuale di Azure (VNET) in modo che possa accedere ai servizi di back-end all'interno della rete. Il portale per sviluppatori e il gateway di Gestione API possono essere configurati in modo che siano accessibili da Internet (esterno) o solo nell'ambito della VNET (interno).
- Esterno: il gateway di Gestione API e il portale per gli sviluppatori sono accessibili dalla rete internet pubblica tramite un servizio di bilanciamento del carico esterno. Il gateway può accedere alle risorse all'interno della rete virtuale.
- Interno: il gateway di Gestione API e il portale per sviluppatori sono accessibili soltanto dalla rete virtuale, tramite un servizio di bilanciamento del carico interno. Il gateway può accedere alle risorse all'interno della rete virtuale.

Il traffico in ingresso e in uscita nella subnet in cui viene distribuita la gestione delle API può essere controllato usando il gruppo di sicurezza di rete.

* [Come usare Gestione API di Azure con le reti virtuali](./api-management-using-with-vnet.md)

* [Uso del servizio Gestione API di Azure con una rete virtuale interna](./api-management-using-with-internal-vnet.md)

* [Integrare Gestione API in una rete virtuale interna con un gateway applicazione](./api-management-howto-integrate-internal-vnet-appgateway.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Indicazioni**: il traffico in ingresso e in uscita nella subnet in cui viene distribuita Gestione API può essere controllato usando i gruppi di sicurezza di rete (NSG). Distribuire un NSG nella subnet di Gestione API, abilitare i log del flusso dei gruppi di sicurezza e inviare i log a un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Attenzione: Quando si configura un NSG nella subnet di Gestione API è necessario aprire un set di porte. Se una qualsiasi di queste porte non è disponibile, Gestione API potrebbe non funzionare correttamente e potrebbe diventare inaccessibile.

* [Informazioni sulle configurazioni di NSG per Gestione API di Azure](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Indicazioni**: per proteggere le API Web/HTTP critiche, configurare Gestione API in una rete virtuale (VNET) in modalità interna e configurare un gateway applicazione di Azure. Il gateway applicazione è un servizio PaaS. Funge da proxy inverso e fornisce bilanciamento del carico, routing, web application firewall (WAF) e altri servizi del protocollo L7.

La combinazione di Gestione API in una rete VNET interna con il Gateway applicazione come front-end consente gli scenari seguenti:
- uso di un'unica risorsa di Gestione API per l'esposizione di tutte le API sia agli utenti interni che a quelli esterni;
- uso di un'unica risorsa di Gestione API per l'esposizione di un subset di API agli utenti esterni;
- predisposizione di un modo per attivare e disattivare l'accesso a Gestione API dalla rete Internet pubblica.

Nota: Questa funzionalità è disponibile nei livelli Premium e Developer di Gestione API.

* [Informazioni su come integrare Gestione API in una VNET interna con un gateway applicazione](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Informazioni sul gateway applicazione di Azure](../application-gateway/index.yml)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni**: configurare Gestione API in una rete virtuale (VNET) in modalità interna e configurare un gateway applicazione di Azure. Il gateway applicazione è un servizio PaaS. Funge da proxy inverso e fornisce bilanciamento del carico, routing, web application firewall (WAF) e altri servizi del protocollo L7.

La combinazione di Gestione API in una rete VNET interna con il Gateway applicazione come front-end consente gli scenari seguenti:
- uso di un'unica risorsa di Gestione API per l'esposizione di tutte le API sia agli utenti interni che a quelli esterni;
- uso di un'unica risorsa di Gestione API per l'esposizione di un subset di API agli utenti esterni;
- predisposizione di un modo per attivare e disattivare l'accesso a Gestione API dalla rete Internet pubblica.

Nota: Questa funzionalità è disponibile nei livelli Premium e Developer di Gestione API.

Abilitare la protezione DDoS di Azure standard nella VNET associata alla distribuzione di Gestione API per la protezione da attacchi di tipo Denial of Service (DDoS) distribuiti.

Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

* [Informazioni su come integrare Gestione API in una VNET interna con un gateway applicazione](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Informazioni sul gateway applicazione di Azure](../application-gateway/index.yml)

* [Come configurare la protezione DDoS di Azure standard](../virtual-network/manage-ddos-protection.md)

* [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](../security-center/azure-defender.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: registrare i pacchetti di rete e i log dei flussi

**Indicazioni**: il traffico in ingresso e in uscita nella subnet in cui viene distribuita Gestione API può essere controllato usando i gruppi di sicurezza di rete (NSG). Distribuire un NSG nella subnet di Gestione API, abilitare i log del flusso dei gruppi di sicurezza e inviare i log a un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Attenzione: Quando si configura un NSG nella subnet di Gestione API è necessario aprire un set di porte. Se una qualsiasi di queste porte non è disponibile, Gestione API potrebbe non funzionare correttamente e potrebbe diventare inaccessibile.

* [Informazioni sulle configurazioni di NSG per Gestione API di Azure](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: distribuire sistemi di rilevamento intrusioni/prevenzione intrusioni (IDS/IPS) basati sulla rete

**Indicazioni**: configurare Gestione API in una rete virtuale (VNET) in modalità interna e configurare un gateway applicazione di Azure. Il gateway applicazione è un servizio PaaS. Funge da proxy inverso e fornisce bilanciamento del carico, routing, web application firewall (WAF) e altri servizi del protocollo L7. Il WAF del gateway applicazione offre protezione da exploit e vulnerabilità comuni per la sicurezza e può essere eseguito nelle due modalità riportate di seguito.
- Modalità di rilevamento: monitora e registra tutti gli avvisi sulle minacce. È possibile attivare la registrazione diagnostica per il gateway applicazione usando la sezione Diagnostica. È necessario assicurarsi che il log di WAF sia selezionato e attivato. Quando viene eseguito in modalità di rilevamento, Web Application Firewall non blocca le richieste in ingresso.
- Modalità di prevenzione: blocca le intrusioni e gli attacchi rilevati dalle regole. L'autore dell'attacco riceve un'eccezione di "accesso non autorizzato 403" e la connessione viene chiusa. La modalità di prevenzione registra tali attacchi nei log di WAF.

La combinazione di Gestione API in una rete VNET interna con il Gateway applicazione come front-end consente gli scenari seguenti:
- uso di un'unica risorsa di Gestione API per l'esposizione di tutte le API sia agli utenti interni che a quelli esterni;
- uso di un'unica risorsa di Gestione API per l'esposizione di un subset di API agli utenti esterni;
- predisposizione di un modo per attivare e disattivare l'accesso a Gestione API dalla rete Internet pubblica.

Nota: Questa funzionalità è disponibile nei livelli Premium e Developer di Gestione API.

* [Informazioni su come integrare Gestione API in una VNET interna con un gateway applicazione](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [Informazioni sul WAF del gateway applicazione di Azure](../web-application-firewall/ag/ag-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Indicazioni**: Per gestire il flusso del traffico verso le API Web/HTTP distribuire Gestione API a una rete virtuale (VNET) associata all'ambiente del servizio app in modalità esterna o interna.

In modalità interna, configurare un gateway applicazione di Azure davanti a Gestione API. Il gateway applicazione è un servizio PaaS. Funge da proxy inverso e fornisce bilanciamento del carico, routing, web application firewall (WAF) e altri servizi del protocollo L7. Il WAF del gateway applicazione offre protezione da exploit e vulnerabilità comuni per la sicurezza.

La combinazione di Gestione API in una rete VNET interna con il Gateway applicazione come front-end consente gli scenari seguenti:
- uso di un'unica risorsa di Gestione API per l'esposizione di tutte le API sia agli utenti interni che a quelli esterni;
- uso di un'unica risorsa di Gestione API per l'esposizione di un subset di API agli utenti esterni;
- predisposizione di un modo per attivare e disattivare l'accesso a Gestione API dalla rete Internet pubblica.

Nota: Questa funzionalità è disponibile nei livelli Premium e Developer di Gestione API.

* [Come esporre le API private a utenti esterni](/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

* [Come usare Gestione API in una VNET](./api-management-using-with-vnet.md)

* [Web application firewall di Azure nel gateway applicazione di Azure](../web-application-firewall/ag/ag-overview.md)

* [Informazioni sul gateway applicazione di Azure](../application-gateway/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni**: usare i tag di servizio della rete virtuale (VNET) per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete (NSG) usati nelle subnet di Gestione API. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

Attenzione: Quando si configura un NSG nella subnet di Gestione API è necessario aprire un set di porte. Se una qualsiasi di queste porte non è disponibile, Gestione API potrebbe non funzionare correttamente e potrebbe diventare inaccessibile.

* [Comprensione e uso dei tag del servizio](../virtual-network/service-tags-overview.md)

* [Porte necessarie per il servizio Gestione API](./api-management-using-with-vnet.md#-common-network-configuration-issues)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Indicazioni**: definire e implementare configurazioni di sicurezza standard per le impostazioni di rete correlate alle distribuzioni di Gestione API di Azure. Usare gli alias di Criteri di Azure negli spazi dei nomi "Microsoft.ApiManagement" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle distribuzioni di Gestione API di Azure e delle risorse correlate. È inoltre possibile usare le definizioni di criteri predefiniti per le reti virtuali di Azure, ad esempio:
- Lo standard di protezione DDoS deve essere abilitato

È anche possibile usare i progetti di Azure per semplificare le distribuzioni su larga scala di Azure tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controllo degli accessi in base al ruolo di Azure (RBAC di Azure) e criteri in una singola definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni**: usare i tag per i gruppi di sicurezza di rete (NSG) e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le regole dei singoli gruppi di sicurezza di rete è possibile usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

* [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

* [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche alle risorse di rete associate alle distribuzioni di Gestione API di Azure. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

* [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: Registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Indicazioni**: Microsoft gestisce le origini temporali per la Gestione API di Azure.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni**: nel Monitoraggio di Azure usare l'area o le aree di lavoro Log Analytics per eseguire query e analisi, inviare log ad Archiviazione di Azure per l'archiviazione a lungo termine o l'analisi offline oppure esportare i log in un'altra soluzione di analisi in Azure e altrove usando Hub eventi di Azure. Per impostazione predefinita Gestione API di Azure restituisce log e metriche al Monitoraggio di Azure. Il livello di dettaglio della registrazione può essere configurato in base al servizio e alle API.

Oltre al Monitoraggio di Azure, Gestione API di Azure può essere integrato con uno o più servizi di Azure Application Insights. Le impostazioni di registrazione per Application Insights possono essere configurate in base al servizio o all'API.

Facoltativamente, abilitare e caricare i dati in Azure Sentinel o in una integrazione di Informazioni di sicurezza e gestione degli eventi (SIEM) di terze parti.

* [Come configurare le impostazioni di diagnostica](../azure-monitor/platform/diagnostic-settings.md#create-in-azure-portal)

* [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Come creare una pipeline di analisi e registrazioni personalizzate](./api-management-howto-log-event-hubs.md)

* [Come eseguire l'integrazione con Azure Application Insights](./api-management-howto-app-insights.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni**: per la registrazione di controllo del piano di controllo, abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log attività a un'area di lavoro Log Analytics per la creazione di report e l'analisi, ad archiviazione di Azure per la sicurezza a lungo termine, ad Hub eventi di Azure per l'esportazione in altre soluzioni di analisi in Azure e altrove. Usando i dati del log attività di Azure è possibile acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita al livello del piano di controllo per le risorse di Gestione API di Azure.

Per la registrazione dell'audit del piano dati, i log di diagnostica offrono informazioni dettagliate sulle operazioni e sugli errori importanti per il controllo e la risoluzione dei problemi. I log di diagnostica differiscono dai log attività. I log attività offrono informazioni approfondite sulle operazioni eseguite nelle risorse di Azure. I log di diagnostica forniscono informazioni dettagliate sulle operazioni eseguite dalla risorsa.

* [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/activity-log.md)

* [Come abilitare le impostazioni di diagnostica per Gestione API di Azure](./api-management-howto-use-azure-monitor.md#activity-logs)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni**: In monitoraggio di Azure, impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine o l'archivio.

* [Come impostare i parametri di conservazione dei log per le aree di lavoro Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [Come archiviare i log in un account di archiviazione di Azure](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Indicazioni**: Gestione API di Azure genera continuamente log e metriche in Monitoraggio di Azure, offrendo una visibilità quasi in tempo reale dello stato e dell'integrità delle API. Con Monitoraggio di Azure e le aree di lavoro Log Analytics è possibile esaminare, eseguire query, visualizzare, indirizzare, archiviare, configurare gli avvisi ed eseguire azioni su metriche e log provenienti da Gestione API e risorse correlate. Inoltre, è possibile analizzare e monitorare i log per i comportamenti anomali e controllare regolarmente i risultati.

Facoltativamente, è possibile integrare Gestione API con Azure Application Insights e usarlo come strumento di monitoraggio, analisi, creazione di report e avvisi primario o secondario.

* [Come monitorare ed esaminare i log per Gestione API di Azure](./api-management-howto-use-azure-monitor.md)

* [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md)

* [Informazioni sull'area di lavoro Log Analytics](../azure-monitor/log-query/get-started-portal.md)

* [Come eseguire l'integrazione con Azure Application Insights](./api-management-howto-app-insights.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: abilitare gli avvisi per le attività anomale

**Indicazioni**: abilitare le impostazioni di diagnostica del log attività di Azure, nonché le impostazioni di diagnostica per le istanze di Gestione API di Azure e inviare i log a un'area di lavoro Log Analytics. Eseguire query in Log Analytics per cercare termini, identificare le tendenze, analizzare i modelli e ottenere molte altre informazioni dettagliate basate sui dati raccolti. È possibile creare avvisi basati sulle query dell'area di lavoro Log Analytics.

È anche possibile creare avvisi collegati alle metriche che si attivano se si verifica qualcosa di imprevisto, ad esempio, per ricevere notifiche quando l'istanza di Gestione API di Azure supera la capacità di picco prevista in un determinato periodo di tempo o se è stato specificato un certo numero di richieste del gateway non autorizzate o di errori in un periodo di tempo predefinito.

Facoltativamente, è possibile integrare Gestione API con Azure Application Insights e usarlo come strumento di monitoraggio, analisi, creazione di report e avvisi primario o secondario.

Facoltativamente, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o nelle informazioni di sicurezza e gestione degli eventi di terze parti.

* [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/activity-log.md)

* [Come abilitare le impostazioni di diagnostica per Gestione API di Azure](./api-management-howto-use-azure-monitor.md#activity-logs)

* [Come configurare una regola di avviso per gestione API di Azure](./api-management-howto-use-azure-monitor.md#set-up-an-alert-rule)

* [Come visualizzare le metriche della capacità di un'istanza di Gestione API di Azure](./api-management-capacity.md)

* [Come eseguire l'integrazione con Azure Application Insights](./api-management-howto-app-insights.md)

* [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Indicazioni**: non applicabile; Gestione API di Azure non elabora né produce log correlati all'antimalware.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Indicazioni**: non applicabile; Gestione API di Azure non elabora o produce log correlati a DNS accessibili dagli utenti.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni**: mantenere un inventario degli account con accesso amministrativo al piano di controllo di Gestione API di Azure (portale di Azure).

Azure Active Directory (AD) include ruoli predefiniti che devono essere assegnati in modo esplicito e sono disponibili per query. Gestione API si basa su questi ruoli e sul controllo degli accessi in base al ruolo per abilitare la gestione degli accessi con granularità fine per i servizi e le entità di Gestione API.

Gestione API contiene inoltre un gruppo Administrators predefinito nel sistema utente di Gestione API. In Gestione API i gruppi controllano la visibilità delle API nel portale per sviluppatori e i membri del gruppo Administrators possono visualizzare tutte le API.

Seguire le raccomandazioni del Centro sicurezza di Azure per la gestione e la manutenzione degli account amministrativi.

* [Come usare il controllo degli accessi in base al ruolo in Gestione API di Azure](./api-management-role-based-access-control.md)

* [Come ottenere un elenco di utenti in un'istanza di Gestione API di Azure](/powershell/module/az.apimanagement/get-azapimanagementuser)

* [Come ottenere un elenco di utenti assegnati a un ruolo della directory in Azure AD con PowerShell](/powershell/module/az.resources/get-azroleassignment)

* [Come ottenere una definizione del ruolo della directory in Azure AD con PowerShell](/powershell/module/az.resources/get-azroledefinition)

* [Informazioni sulle raccomandazioni relative a identità e accesso del Centro sicurezza di Azure](../security-center/recommendations-reference.md#recs-identity)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Indicazioni**: in Gestione API di Azure non è previsto il concetto di password/chiavi predefinite.

Le sottoscrizioni di Gestione API di Azure, che sono un modo per proteggere l'accesso alle API, sono tuttavia dotate di una coppia di chiavi di sottoscrizione generate. I clienti possono rigenerare le chiavi di sottoscrizione in qualsiasi momento.

* [Informazioni sulle sottoscrizioni di Gestione API di Azure](./api-management-subscriptions.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio:
- Alla sottoscrizione deve essere assegnato più di un proprietario
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

* [Come usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso (Anteprima)](../security-center/security-center-identity-access.md)

* [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Indicazioni**: Gestione API di Azure può essere configurato per sfruttare Azure Active Directory come provider di identità per l'autenticazione degli utenti nel portale per sviluppatori per trarre vantaggio dalle funzionalità SSO offerte da Azure AD. Una volta configurata, i nuovi utenti del portale per sviluppatori possono scegliere di seguire il processo di iscrizione predefinito eseguendo prima l'autenticazione tramite Azure AD e quindi completando il processo di iscrizione nel portale dopo l'autenticazione.

* [Autorizzare gli account per sviluppatori usando Azure Active Directory in Gestione API di Azure](./api-management-howto-aad.md)

In alternativa, è possibile personalizzare ulteriormente il processo di accesso/iscrizione tramite la delega. La delega consente di usare il sito Web esistente per gestire l'accesso e l'iscrizione degli sviluppatori e la sottoscrizione ai prodotti invece di usare la funzionalità incorporata nel portale per sviluppatori. Ciò consente al sito Web di avere la proprietà dei dati utente e di eseguire la convalida di questi passaggi in modo personalizzato.

* [Come delegare la registrazione utente e la sottoscrizione ai prodotti](./api-management-howto-setup-delegation.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Indicazioni**: abilitare Azure Active Directory (AD) Multi-Factor Authentication (MFA) e seguire le raccomandazioni sulla gestione delle identità e degli accessi nel Centro sicurezza di Azure.

* [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni**: usare workstation con accesso con privilegi insieme a Multi-Factor Authentication (MFA) configurato per l'accesso e la configurazione delle risorse di Azure.

* [Informazioni sulle workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: registrare e inviare avvisi per le attività sospette dagli account amministrativi

**Indicazioni**: usare Azure Active Directory (AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando nell'ambiente si verifica un'attività sospetta o non sicura.

Inoltre, usare i rilevamenti di rischi di Azure AD per visualizzare gli avvisi e i report sul comportamento utente rischioso.

* [Come distribuire Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Informazioni sui rilevamenti di rischi di Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare le località denominate di accesso condizionale per consentire l'accesso al portale di Azure solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

* [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Indicazioni**: quando possibile, usare Azure AD come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

Configurare il portale per sviluppatori di Gestione API di Azure per autenticare gli account per sviluppatori tramite Azure Active Directory.

Configurare l'istanza di Gestione API di Azure per proteggere le API usando il protocollo OAuth 2.0 con Azure Active Directory (AD).

* [Come autorizzare gli account per sviluppatori usando Azure Active Directory in Gestione API di Azure](./api-management-howto-aad.md)

* [Come proteggere un'API usando OAuth 2.0 con Azure Active Directory e Gestione API](./api-management-howto-protect-backend-with-aad.md)

* [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure Active Directory mette a disposizione i log necessari per individuare gli account obsoleti. Gli utenti possono usare anche le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruoli. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo quelli appropriati dispongano di accesso continuo.

I clienti possono gestire l'inventario degli account utente di Gestione API e riconciliare l'accesso in base alle esigenze. In Gestione API gli sviluppatori sono i consumatori delle API esposte con Gestione API. Per impostazione predefinita, i nuovi account sviluppatore creati sono attivi e associati al gruppo Sviluppatori. Gli account sviluppatore con stato attivo possono essere usati per accedere a tutte le API per cui dispongono di sottoscrizioni.

Gli amministratori possono creare gruppi personalizzati oppure sfruttare i gruppi esterni nei tenant di Azure Active Directory associati. È possibile usare gruppi personalizzati ed esterni assieme ai gruppi di sistema per offrire agli sviluppatori visibilità e accesso ai prodotti API.

* [Come gestire gli account utente in Gestione API di Azure](./api-management-howto-create-or-invite-developers.md)

* [Come ottenere un elenco di utenti di Gestione API](/powershell/module/az.apimanagement/get-azapimanagementuser)

* [Come creare e usare i gruppi per gestire gli account sviluppatore in Gestione API di Azure](./api-management-howto-create-groups.md)

* [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: monitorare i tentativi di accesso agli account disattivati

**Indicazioni**: Configurare l'istanza di Gestione API di Azure per autenticare gli account per sviluppatori tramite Azure Active Directory quale provider di identità in Gestione API di Azure.

Configurare l'istanza di Gestione API di Azure per proteggere le API usando il protocollo OAuth 2.0 con Azure Active Directory (AD).

Configurare i criteri di convalida JWT per le richieste API in ingresso per imporre l'esistenza e la validità di un token valido.

Creare le impostazioni di diagnostica per gli account utente di Azure AD e inviare i log di controllo e di accesso a un'area di lavoro Log Analytics. Configurare gli avvisi desiderati in Log Analytics. Inoltre, è possibile caricare l'area di lavoro Log Analytics in Azure Sentinel o in un SIEM di terze parti.

Configurare il monitoraggio avanzato con Gestione API usando il criterio `log-to-eventhub`, acquisire eventuali informazioni aggiuntive sul contesto necessarie per l'analisi della sicurezza e inviarle ad Azure Sentinel o a SIEM di terze parti.

* [Come autorizzare gli account per sviluppatori usando Azure Active Directory in Gestione API di Azure](./api-management-howto-aad.md)

* [Come proteggere un'API usando OAuth 2.0 con Azure Active Directory e Gestione API](./api-management-howto-protect-backend-with-aad.md)

* [Criteri di limitazione dell'accesso di Gestione API](./api-management-access-restriction-policies.md)

* [Come integrare i log di Azure AD in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Come caricare dati in Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Monitoraggio avanzato delle API](./api-management-log-to-eventhub-sample.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Indicazioni**: per la deviazione del comportamento di accesso dell'account nel piano di controllo (il portale di Azure), usare le funzionalità di protezione delle identità e di rilevamento dei rischi di Azure Active Directory (AD) per configurare le risposte automatiche alle azioni sospette rilevate correlate alle identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.

* [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Indicazioni**: attualmente non disponibile; Customer Lockbox non è attualmente supportata per Gestione API di Azure.

* [Elenco dei servizi Customer Lockbox supportati](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Le istanze di Gestione API di Azure devono essere separate dalla rete virtuale (VNet)/subnet e contrassegnate in modo appropriato.

* [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

* [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

* [Come usare Gestione API di Azure con le reti virtuali](./api-management-using-with-vnet.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Indicazioni**: attualmente non disponibili; le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per Gestione API di Azure.

Microsoft gestisce l'infrastruttura sottostante per Gestione API di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

* [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Indicazioni**: le chiamate del piano di gestione vengono eseguite tramite Azure Resource Manager su TLS. È necessario un token JSON Web (JWT) valido. Le chiamate del piano dati possono essere protette con TLS e uno dei meccanismi di autenticazione supportati (ad esempio, il certificato client o JWT).

* [Informazioni sulla protezione dei dati in Gestione API di Azure](./api-management-security-controls.md#data-protection)

* [Gestire le impostazioni TLS in Gestione API di Azure](./api-management-howto-manage-protocols-ciphers.md)

* [Proteggere le API in Gestione API di Azure con Azure Active Directory](./api-management-howto-protect-backend-with-aad.md)

* [Proteggere le API in Gestione API di Azure con Azure Active Directory B2C](./howto-protect-backend-frontend-azure-ad-b2c.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni**: attualmente non disponibili; le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per Gestione API di Azure. Contrassegnare i servizi di Gestione API di Azure che potrebbero elaborare informazioni riservate come tali e implementare una soluzione di terze parti, se necessario ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

* [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Indicazioni**: usare il controllo degli accessi in base al ruolo per controllare gli accessi a Gestione API di Azure. Gestione API di Azure si basa sul controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per consentire la gestione degli accessi con granularità fine per i servizi e le entità di gestione API (ad esempio, API e criteri).

* [Come usare il controllo degli accessi in base al ruolo in Gestione API di Azure](./api-management-role-based-access-control.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per Gestione API di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

* [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Indicazioni**: i dati sensibili, ad esempio i certificati, le chiavi e i valori denominati segreti vengono crittografati con chiavi di istanza per servizio, gestite dallo stesso. Tutte le chiavi di crittografia sono in base all'istanza del servizio e sono gestite dallo stesso.

* [Informazioni sulla protezione/crittografia dei dati inattivi con Gestione API di Azure](./api-management-security-controls.md#data-protection)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Indicazioni**: usare Monitoraggio di Azure con il log attività di Azure per creare avvisi per il momento in cui le modifiche vengono apportate alle app per le funzioni di Azure e ad altre risorse critiche o correlate.

* [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/platform/alerts-activity-log.md)

* [Come usare Monitoraggio di Azure e il log attività di Azure in Gestione API di Azure](./api-management-howto-use-azure-monitor.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [Controllo di sicurezza: gestione della vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni**: attualmente non disponibile; la valutazione della vulnerabilità nel Centro sicurezza di Azure non è attualmente disponibile per Gestione API di Azure.

Piattaforma sottostante analizzata e munita di patch applicata da Microsoft. Esaminare i controlli di sicurezza disponibili per ridurre le vulnerabilità correlate alla configurazione del servizio.

* [Informazioni sui controlli di sicurezza disponibili per Gestione API di Azure](./api-management-security-controls.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: distribuire una soluzione di gestione delle patch automatizzata per il software di terze parti

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: attualmente non disponibile; la valutazione della vulnerabilità nel Centro sicurezza di Azure non è attualmente disponibile per Gestione API di Azure.

Piattaforma sottostante analizzata e munita di patch applicata da Microsoft. I clienti devono esaminare i controlli di sicurezza cui hanno accesso per ridurre le vulnerabilità correlate alla configurazione del servizio.

* [Informazioni sui controlli di sicurezza disponibili per Gestione API di Azure](./api-management-security-controls.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: usare l'individuazione di asset in Azure

**Indicazioni**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) disponibili nelle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

* [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription)

* [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle risorse di Azure. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Usare inoltre Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

* [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

* [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: mantenere un inventario delle risorse di Azure approvate e dei titoli software

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle proprie sottoscrizioni mediante le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/index.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="610-implement-approved-application-list"></a>6.10: implementare l'elenco di applicazioni approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: limitare la capacità degli utenti di interagire con Azure Resource Manager tramite script

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

* [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

* [Controllo degli accessi in base al ruolo in Gestione API di Azure](./api-management-role-based-access-control.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Controllo di sicurezza: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Indicazioni**: definire e implementare configurazioni di sicurezza standard per il servizio di Gestione API di Azure con Criteri di Azure. Usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.ApiManagement" per creare criteri personalizzati per controllare o applicare la configurazione dei servizi di Gestione API di Azure.

* [Come visualizzare gli alias di Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: definire e implementare configurazioni di sicurezza standard per i servizi di Gestione API di Azure con Criteri di Azure. Usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.ApiManagement" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze di Gestione API di Azure. Usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni**: se si usano definizioni di criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire la configurazione del servizio di Gestione API di Azure in modo sicuro.

* [Come archiviare i file in Azure DevOps](/azure/devops/repos/git/gitworkflow)

* [Documentazione di Azure Repos](/azure/devops/repos/index)

* [Informazioni sul Resource Kit di Gestione API di Azure DevOps](./api-management-security-controls.md#configuration-management)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: distribuire strumenti di gestione della configurazione di sistema

**Indicazioni**: definire e implementare configurazioni di sicurezza standard per i servizi di Gestione API di Azure con Criteri di Azure. Usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.ApiManagement" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze di Gestione API di Azure. Usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

**Indicazioni**: usare il Resource Kit DevOps di Gestione API di Azure per eseguire la gestione della configurazione per Gestione API di Azure.

Inoltre, definire e implementare configurazioni di sicurezza standard per i servizi di Gestione API di Azure con Criteri di Azure. Usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.ApiManagement" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze di Gestione API di Azure. Usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

* [Informazioni sul Resource Kit di Gestione API di Azure DevOps](./api-management-security-controls.md#configuration-management)

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Indicazioni**: usare Key Vault per la gestione dei certificati e impostarli per la rotazione automatica. Se si usa Azure Key Vault per gestire il certificato SSL del dominio personalizzato, assicurarsi che il certificato venga inserito in Key Vault come certificato, non come segreto.

* [Come impostare i nomi di dominio personalizzati con le linee guida per la rotazione delle chiavi di Key Vault](./configure-custom-domain.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Microsoft

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Indicazioni**: usare l'identità del servizio gestito generata da Azure Active Directory (AD) per consentire all'istanza di Gestione API di accedere con facilità e sicurezza ad altre risorse protette da Azure AD, ad esempio Azure Key Vault.

* [Come creare un'identità gestita per un'istanza di Gestione API](./api-management-howto-use-managed-service-identity.md)

* [Criterio per eseguire l'autenticazione con un'identità gestita](./api-management-authentication-policies.md#ManagedIdentity)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

* [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Controllo di sicurezza: difesa da malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: usare software antimalware gestito in modo centralizzato

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

Il software antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio Gestione API di Azure, ma non viene eseguito sui contenuti del cliente.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse non di calcolo destinate ad archiviare dati.

Il software antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio Gestione API di Azure, ma non viene eseguito sui contenuti del cliente.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: verificare che le firme e il software antimalware siano aggiornati

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse non di calcolo destinate ad archiviare dati.

Il software antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio Gestione API di Azure, ma non viene eseguito sui contenuti del cliente.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [Controllo di sicurezza: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Indicazioni**: Pubblicando e gestendo le API tramite Gestione API di Azure è possibile sfruttare funzionalità di tolleranza di errore e di infrastruttura che sarebbe altrimenti necessario progettare, implementare e gestire manualmente. Gestione API supporta la distribuzione in più aree che rende il piano dati impermeabile agli errori a livello di area senza aggiungere alcun sovraccarico operativo.

Le funzionalità di backup e ripristino dei servizi di Gestione API fornisce i blocchi predefiniti necessari per implementare una strategia di ripristino di emergenza. Le operazioni di backup e ripristino possono essere eseguite manualmente o essere automatizzate.

* [Come distribuire un piano dati di Gestione API in più aree](./api-management-howto-deploy-multi-region.md)

* [Come implementare il ripristino di emergenza usando il backup e il ripristino dei servizi in Gestione API di Azure](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [Come chiamare l'operazione di backup di Gestione API](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup)

* [Come chiamare l'operazione di ripristino di Gestione API](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: eseguire backup completi del sistema e il backup di tutte le chiavi gestite dal cliente

**Indicazioni**: le operazioni di backup e ripristino fornite da Gestione API di Azure eseguono il backup e il ripristino completo del sistema.

Le identità gestite possono essere usate per ottenere i certificati da Azure Key Vault per i nomi di dominio personalizzati di Gestione API. Eseguire il backup di tutti i certificati archiviati all'interno di Azure Key Vault.

* [Come implementare il ripristino di emergenza usando il backup e il ripristino dei servizi in Gestione API di Azure](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [Come eseguire il backup dei certificati di Azure Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni**: convalidare i backup eseguendo un ripristino di prova del servizio e dei certificati dai backup.

* [Come chiamare l'operazione di ripristino di Gestione API](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

* [Come ripristinare i certificati di Azure Key Vault](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni**: Gestione API di Azure scrive i backup negli account di archiviazione di Azure di proprietà dei clienti. Seguire le raccomandazioni sulla sicurezza di Archiviazione di Azure per proteggere il backup.

* [Come implementare il ripristino di emergenza usando il backup e il ripristino dei servizi in Gestione API di Azure](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [Raccomandazione sulla sicurezza per archiviazione BLOB](../storage/blobs/security-recommendations.md)

Abilitare l'eliminazione temporanea in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.

* [Come abilitare l'eliminazione temporanea in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

* [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un proprio piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione, tramite i tag e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure, in particolare quelle che elaborano i dati sensibili. È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

* [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

* [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: a intervalli regolari, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi, per contribuire a proteggere le risorse di Azure. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

* [Pubblicazione del NIST - Guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

* [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per contribuire a individuare i rischi per le risorse di Azure. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

* [Come configurare l'esportazione continua](../security-center/continuous-export.md)

* [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

* [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Controllo di sicurezza: test di penetrazione ed esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: eseguire con regolarità test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici in termini di sicurezza entro 60 giorni

**Indicazioni**: * [seguire le regole di engagement Microsoft per assicurarsi che i propri test di penetrazione non violino i criteri Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [È possibile trovare altre informazioni sulla strategia Microsoft e sull'esecuzione dei test di penetrazione Red Teaming sull'infrastruttura cloud gestita da Microsoft, sui servizi e sulle applicazioni qui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)
