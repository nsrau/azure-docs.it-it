---
title: Baseline della sicurezza di Azure per gestione API
description: Baseline della sicurezza di Azure per gestione API
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: aa59a37f3a4413c92a483746ed4a08c363ab8457
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796525"
---
# <a name="azure-security-baseline-for-api-management"></a>Baseline della sicurezza di Azure per gestione API

La linea di base di sicurezza di Azure per gestione API contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La linea di base per questo servizio viene creata dalla [versione 1,0 del benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce indicazioni su come proteggere le soluzioni cloud in Azure con le procedure consigliate.

Per altre informazioni, vedere [Panoramica di base sulla sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

**Linee guida**: gestione API di Azure può essere distribuito all'interno di una rete virtuale di Azure (VNET), in modo da poter accedere ai servizi back-end all'interno della rete. Il portale per sviluppatori e il gateway di gestione API possono essere configurati per essere accessibili da Internet (esterno) o solo all'interno di VNET (interno).
- Esterno: il gateway di Gestione API e il portale per gli sviluppatori sono accessibili dalla rete internet pubblica tramite un servizio di bilanciamento del carico esterno. Il gateway può accedere alle risorse all'interno della rete virtuale.
- Interno: il gateway di Gestione API e il portale per gli sviluppatori sono accessibili soltanto dalla rete virtuale tramite un servizio di bilanciamento del carico interno. Il gateway può accedere alle risorse all'interno della rete virtuale.

Il traffico in ingresso e in uscita nella subnet in cui è distribuito gestione API può essere controllato usando un gruppo di sicurezza di rete.

* [Come usare Gestione API di Azure con le reti virtuali](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

* [Uso del servizio Gestione API di Azure con una rete virtuale interna](https://docs.microsoft.com/azure/api-management/api-management-using-with-internal-vnet)

* [Integrare Gestione API in una VNET interna con il gateway applicazione](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

**Linee guida**: il traffico in ingresso e in uscita nella subnet in cui è distribuito gestione API può essere controllato usando gruppi di sicurezza di rete (gruppi). Distribuire un NSG nella subnet di gestione API e abilitare i log di flusso di NSG e inviare i log a un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log dei flussi di NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete non configurate.

Attenzione: quando si configura un NSG nella subnet di gestione API, è necessario aprire un set di porte. Se una qualsiasi di queste porte non è disponibile, Gestione API potrebbe non funzionare correttamente e potrebbe diventare inaccessibile.

* [Informazioni sulle configurazioni di NSG per gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

* [Come abilitare i log di flusso NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Come abilitare e usare Analisi del traffico](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="13-protect-critical-web-applications"></a>1,3: proteggere le applicazioni Web critiche

**Linee guida**: per proteggere le API Web/http critiche, configurare Gestione API in una rete virtuale (VNET) in modalità interna e configurare un gateway applicazione Azure. Il gateway applicazione è un servizio PaaS. Funge da proxy inverso e fornisce bilanciamento del carico, routing, web application firewall (WAF) e altri servizi di L7.

La combinazione di gestione API sottoposta a provisioning in una VNET interna con il front-end del gateway applicazione consente gli scenari seguenti:
- Usare una singola risorsa di gestione API per esporre tutte le API sia a utenti interni che a utenti esterni.
- Usare una singola risorsa di gestione API per esporre un subset di API a utenti esterni.
- Consente di attivare e disattivare l'accesso a gestione API dalla rete Internet pubblica.

Nota: questa funzionalità è disponibile nei livelli Premium e Developer di gestione API.

* [Come integrare Gestione API in una VNET interna con il gateway applicazione](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [Informazioni sul gateway applicazione Azure](https://docs.microsoft.com/azure/application-gateway/)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: configurare Gestione API in una rete virtuale (VNET) in modalità interna e configurare un gateway applicazione Azure. Il gateway applicazione è un servizio PaaS. Funge da proxy inverso e fornisce bilanciamento del carico, routing, web application firewall (WAF) e altri servizi di L7.

La combinazione di gestione API sottoposta a provisioning in una VNET interna con il front-end del gateway applicazione consente gli scenari seguenti:
- Usare una singola risorsa di gestione API per esporre tutte le API sia a utenti interni che a utenti esterni.
- Usare una singola risorsa di gestione API per esporre un subset di API a utenti esterni.
- Consente di attivare e disattivare l'accesso a gestione API dalla rete Internet pubblica.

Nota: questa funzionalità è disponibile nei livelli Premium e Developer di gestione API.

Abilitare la protezione DDoS di Azure standard nel VNET associato alla distribuzione di gestione API per la protezione da attacchi di tipo Denial of Service (DDoS) distribuiti.

Usare il Centro sicurezza di Azure Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP Internet dannosi noti o non usati.

* [Come integrare Gestione API in una VNET interna con il gateway applicazione](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [Informazioni sul gateway applicazione Azure](https://docs.microsoft.com/azure/application-gateway/)

* [Come configurare la protezione DDoS di Azure standard](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Informazioni sull'Intelligence per le minacce integrata nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrare i pacchetti di rete e i log di flusso

**Linee guida**: il traffico in ingresso e in uscita nella subnet in cui è distribuito gestione API può essere controllato usando gruppi di sicurezza di rete (NSG). Distribuire un NSG nella subnet di gestione API e abilitare i log di flusso di NSG e inviare i log a un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log dei flussi di NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete non configurate.

Attenzione: quando si configura un NSG nella subnet di gestione API, è necessario aprire un set di porte. Se una qualsiasi di queste porte non è disponibile, Gestione API potrebbe non funzionare correttamente e potrebbe diventare inaccessibile.

* [Informazioni sulle configurazioni di NSG per gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

* [Come abilitare i log di flusso NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Come abilitare e usare Analisi del traffico](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (IDS/IP)

**Linee guida**: configurare Gestione API in una rete virtuale (VNET) in modalità interna e configurare un gateway applicazione Azure. Il gateway applicazione è un servizio PaaS. Funge da proxy inverso e fornisce bilanciamento del carico, routing, web application firewall (WAF) e altri servizi di L7. Il WAF del gateway applicazione offre protezione da exploit e vulnerabilità comuni per la sicurezza e può essere eseguito nelle due modalità seguenti:
- Modalità di rilevamento: monitora e registra tutti gli avvisi sulle minacce. È possibile attivare la diagnostica della registrazione per il gateway applicazione nella sezione diagnostica. È necessario assicurarsi che il registro WAF sia selezionato e attivato. Quando viene eseguito in modalità di rilevamento, Web Application Firewall non blocca le richieste in ingresso.
- Modalità di prevenzione: blocca le intrusioni e gli attacchi rilevati dalle regole. L'autore dell'attacco riceve un'eccezione di "accesso non autorizzato 403" e la connessione viene chiusa. La modalità di prevenzione registra tali attacchi nei log di WAF.

La combinazione di gestione API sottoposta a provisioning in una VNET interna con il front-end del gateway applicazione consente gli scenari seguenti:
- Usare una singola risorsa di gestione API per esporre tutte le API sia a utenti interni che a utenti esterni.
- Usare una singola risorsa di gestione API per esporre un subset di API a utenti esterni.
- Consente di attivare e disattivare l'accesso a gestione API dalla rete Internet pubblica.

Nota: questa funzionalità è disponibile nei livelli Premium e Developer di gestione API.

* [Come integrare Gestione API in una VNET interna con il gateway applicazione](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [Informazioni su applicazione Azure gateway WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="17-manage-traffic-to-web-applications"></a>1,7: gestire il traffico per le applicazioni Web

**Linee guida**: per gestire il flusso del traffico verso le API Web/http, distribuire Gestione API a una rete virtuale (VNET) associata a ambiente del servizio app in modalità esterna o interna.

In modalità interna configurare un gateway applicazione Azure davanti a gestione API. Il gateway applicazione è un servizio PaaS. Funge da proxy inverso e fornisce bilanciamento del carico, routing, web application firewall (WAF) e altri servizi di L7. Il WAF del gateway applicazione fornisce la protezione dagli exploit e dalle vulnerabilità comuni alla sicurezza.

La combinazione di gestione API sottoposta a provisioning in una VNET interna con il front-end del gateway applicazione consente gli scenari seguenti:
- Usare una singola risorsa di gestione API per esporre tutte le API sia a utenti interni che a utenti esterni.
- Usare una singola risorsa di gestione API per esporre un subset di API a utenti esterni.
- Consente di attivare e disattivare l'accesso a gestione API dalla rete Internet pubblica.

Nota: questa funzionalità è disponibile nei livelli Premium e Developer di gestione API.

* [Come esporre le API private a utenti esterni](https://docs.microsoft.com/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

* [Come usare gestione API in una VNET](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

* [Web Application Firewall di Azure nel gateway applicazione di Azure](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [Informazioni sul gateway applicazione Azure](https://docs.microsoft.com/azure/application-gateway/overview)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ridurre la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag di servizio della rete virtuale (VNET) per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete (gruppi) usati nelle subnet di gestione API. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag di servizio in base alla modifica degli indirizzi.

Attenzione: quando si configura un NSG nella subnet di gestione API, è necessario aprire un set di porte. Se una qualsiasi di queste porte non è disponibile, Gestione API potrebbe non funzionare correttamente e potrebbe diventare inaccessibile.

* [Informazioni e uso di tag di servizio](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [Porte necessarie per gestione API](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: mantenere le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le impostazioni di rete correlate alle distribuzioni di gestione API di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. ApiManagement" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle distribuzioni di gestione API di Azure e delle risorse correlate. È anche possibile usare le definizioni di criteri predefinite per le reti virtuali di Azure, ad esempio:
- Lo standard di protezione DDoS deve essere abilitato

È anche possibile usare i progetti di Azure per semplificare le distribuzioni di Azure su larga scala tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio Azure Resource Manager modelli, il controllo degli accessi in base al ruolo e i criteri in un'unica definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

* [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Come creare un Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="110-document-traffic-configuration-rules"></a>1,10: regole di configurazione del traffico documento

**Indicazioni**: usare i tag per i gruppi di sicurezza di rete (gruppi) e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le singole regole NSG, è possibile usare il campo "Description" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Come creare una rete virtuale](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Come creare una NSG con una configurazione di sicurezza](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usare gli strumenti automatici per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche alle risorse di rete associate alle distribuzioni di gestione API di Azure. Creare avvisi in monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse di rete critiche.

* [Come visualizzare e recuperare gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Come creare avvisi in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: utilizzare origini di sincronizzazione dell'ora approvate

**Linee guida**: Microsoft gestisce le origini temporali per gestione API di Azure.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: configurare la gestione dei log di sicurezza centrale

**Linee guida**: all'interno del monitoraggio di Azure, usare log Analytics aree di lavoro per eseguire query ed eseguire analisi, inviare log ad archiviazione di Azure per l'archiviazione a lungo termine o di archiviazione o l'analisi offline oppure esportare i log in un'altra soluzione di analisi in Azure e altrove usando hub eventi di Azure. Gestione API di Azure restituisce log e metriche in monitoraggio di Azure per impostazione predefinita. Il livello di dettaglio della registrazione può essere configurato in base al servizio e alle API.

Oltre a monitoraggio di Azure, gestione API di Azure può essere integrato con uno o più servizi applicazione Azure Insights. Le impostazioni di registrazione per Application Insights possono essere configurate in base al servizio o all'API.

Facoltativamente, abilitare e caricare i dati in Sentinel di Azure o in un evento di sicurezza e gestione degli eventi di terze parti (SIEM).

* [Come configurare le impostazioni di diagnostica](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Come iniziare a usare monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Come creare una pipeline di analisi e registrazione personalizzata](https://docs.microsoft.com/azure/api-management/api-management-howto-log-event-hubs)

* [Come eseguire l'integrazione con applicazione Azure Insights](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: per la registrazione di controllo del piano di controllo, abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log attività a un'area di lavoro log Analytics per la creazione di report e l'analisi, ad archiviazione di Azure per la sicurezza a lungo termine, in hub eventi di Azure per l'esportazione in altre soluzioni di analisi Usando i dati del log attività di Azure, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita a livello del piano di controllo per il servizio gestione API di Azure.

Per la registrazione del controllo del piano dati, i log di diagnostica forniscono informazioni dettagliate sulle operazioni e sugli errori importanti per il controllo e per la risoluzione dei problemi. I log di diagnostica differiscono dai log attività. I log attività offrono informazioni approfondite sulle operazioni eseguite nelle risorse di Azure. I log di diagnostica forniscono informazioni dettagliate sulle operazioni eseguite dalla risorsa.

* [Come abilitare le impostazioni di diagnostica per log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Come abilitare le impostazioni di diagnostica per gestione API di Azure](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: raccogliere i registri di sicurezza dai sistemi operativi

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2,5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: in monitoraggio di Azure impostare il periodo di conservazione dell'area di lavoro log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archivio.

* [Come impostare i parametri di conservazione dei log per aree di lavoro Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [Come archiviare i log in un account di archiviazione di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: gestione API di Azure genera continuamente log e metriche in monitoraggio di Azure, offrendo una visibilità quasi in tempo reale dello stato e dell'integrità delle API. Con monitoraggio di Azure e le aree di lavoro Log Analytics è possibile esaminare, eseguire query, visualizzare, indirizzare, archiviare, configurare gli avvisi ed eseguire azioni su metriche e log provenienti da gestione API e risorse correlate. Analizza e monitora i log per i comportamenti anomali e controlla regolarmente i risultati.

Facoltativamente, è possibile integrare Gestione API con applicazione Azure Insights e usarlo come strumento di monitoraggio, traccia, creazione di report e avvisi primario o secondario.

* [Come monitorare e verificare i log per gestione API di Azure](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor)

* [Come eseguire query personalizzate in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [Informazioni sull'area di lavoro Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Come eseguire l'integrazione con applicazione Azure Insights](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: abilitare gli avvisi per attività anomale

**Linee guida**: abilitare le impostazioni di diagnostica del log attività di Azure, nonché le impostazioni di diagnostica per le istanze di gestione API di Azure e inviare i log a un'area di lavoro log Analytics. Consente di eseguire query Log Analytics per cercare termini, identificare tendenze, analizzare modelli e fornire molte altre informazioni basate sui dati raccolti. È possibile creare avvisi basati sulle query dell'area di lavoro Log Analytics.

Creare avvisi delle metriche per indicare quando si verifica un evento imprevisto. Ad esempio, ottenere le notifiche quando l'istanza di gestione API di Azure supera la capacità di picco prevista in un determinato periodo di tempo o se è stato specificato un certo numero di richieste del gateway non autorizzate o di errori in un periodo di tempo predefinito.

Facoltativamente, è possibile integrare Gestione API con applicazione Azure Insights e usarlo come strumento di monitoraggio, traccia, creazione di report e avvisi primario o secondario.

Facoltativamente, è possibile abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti.

* [Come abilitare le impostazioni di diagnostica per log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Come abilitare le impostazioni di diagnostica per gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs)

* [Come configurare una regola di avviso per una richiesta non autorizzata](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor#set-up-an-alert-rule-for-unauthorized-request)

* [Come visualizzare le metriche relative alla capacità di un'istanza di gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-capacity)

* [Come eseguire l'integrazione con applicazione Azure Insights](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

* [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizzare la registrazione anti-malware

**Linee guida**: non applicabile; Gestione API di Azure non elabora o produce log correlati all'anti-malware.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="29-enable-dns-query-logging"></a>2,9: abilitare la registrazione delle query DNS

**Linee guida**: non applicabile; Gestione API di Azure non elabora o produce log correlati a DNS accessibili dagli utenti.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2,10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [controllo di sicurezza: identità e controllo di accesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: gestire un inventario degli account amministrativi

**Linee guida**: gestire un inventario degli account con accesso amministrativo al piano di controllo di gestione API di Azure (portale di Azure).

Azure Active Directory (AD) include ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere sottoposte a query. Gestione API si basa su questi ruoli e sul controllo degli accessi in base al ruolo per abilitare la gestione degli accessi con granularità fine per i servizi e le entità di gestione API.

Gestione API contiene inoltre un gruppo Administrators predefinito nel sistema utente di gestione API. I gruppi in gestione API controllano la visibilità delle API nel portale per sviluppatori e i membri del gruppo Administrators possono visualizzare tutte le API.

Seguire le raccomandazioni del Centro sicurezza di Azure per la gestione e la manutenzione degli account amministrativi.

* [Come usare il controllo degli accessi in base al ruolo in gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

* [Come ottenere un elenco di utenti in un'istanza di gestione API di Azure](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [Come ottenere un elenco di utenti assegnati a un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/az.resources/get-azroleassignment?view=azps-3.7.0)

* [Come ottenere una definizione di ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/az.resources/get-azroledefinition?view=azps-3.7.0)

* [Informazioni sulle indicazioni relative a identità e accesso dal centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-identity)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="32-change-default-passwords-where-applicable"></a>3,2: modificare le password predefinite se applicabile

**Linee guida**: gestione API di Azure non ha il concetto di password/chiave predefinite.

Le sottoscrizioni di gestione API di Azure, che sono un modo per proteggere l'accesso alle API, sono tuttavia dotate di una coppia di chiavi di sottoscrizione generate. I clienti possono rigenerare le chiavi di sottoscrizione in qualsiasi momento.

* [Informazioni sulle sottoscrizioni di gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-subscriptions)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: usare account amministrativi dedicati

**Linee guida**: creare procedure operative standard per l'uso di account amministrativi dedicati. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio:
- Alla sottoscrizione deve essere assegnato più di un proprietario
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

* [Come usare il Centro sicurezza di Azure per monitorare identità e accesso (anteprima)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Come usare criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida**: gestione API di Azure può essere configurato per sfruttare Azure Active Directory come provider di identità per l'autenticazione degli utenti nel portale per sviluppatori per trarre vantaggio dalle funzionalità SSO offerte da Azure ad. Una volta configurata, i nuovi utenti del portale per sviluppatori possono scegliere di seguire il processo di iscrizione predefinito eseguendo prima l'autenticazione tramite Azure AD e quindi completando il processo di iscrizione nel portale dopo l'autenticazione.

* [Autorizzare gli account per sviluppatori usando Azure Active Directory in Gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

In alternativa, è possibile personalizzare ulteriormente il processo di accesso/iscrizione tramite la delega. La delega consente di usare il sito Web esistente per gestire l'accesso e l'iscrizione degli sviluppatori e la sottoscrizione ai prodotti, anziché usare la funzionalità incorporata nel portale per sviluppatori. Consente al sito Web di essere proprietario dei dati utente ed eseguire la convalida di questi passaggi in modo personalizzato.

* [Come delegare la registrazione utente e la sottoscrizione ai prodotti](https://docs.microsoft.com/azure/api-management/api-management-howto-setup-delegation)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più fattori (multi-factor authentication Azure Active Directory) e seguire le indicazioni di gestione delle identità e degli accessi del Centro sicurezza di Azure.

* [Come abilitare l'autenticazione a più fattori in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Come monitorare identità e accesso nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: usare macchine virtuali dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida**: usare workstation con accesso con privilegi (Paw) con multi-factor authentication (multi-factor authentication) configurato per accedere e configurare le risorse di Azure.

* [Informazioni sulle workstation con accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Come abilitare l'autenticazione a più fattori in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: registrare e segnalare l'attività sospetta da account amministrativi

**Linee guida**: usare Azure Active Directory (AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verifica un'attività sospetta o non sicura nell'ambiente.

Inoltre, utilizzare Azure AD i rilevamenti dei rischi per visualizzare gli avvisi e i report sul comportamento utente rischioso.

* [Come distribuire Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Informazioni sui rilevamenti del rischio Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: gestire le risorse di Azure solo dalle località approvate

**Linee guida**: usare i percorsi denominati di accesso condizionale per consentire l'accesso al portale di Azure solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

* [Come configurare le località denominate in Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="39-use-azure-active-directory"></a>3,9: usare Azure Active Directory

**Linee guida**: se possibile, usare Azure ad come sistema di autenticazione e autorizzazione centrale. AAD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD anche Salt, hash e archivia in modo sicuro le credenziali utente.

Configurare il portale per sviluppatori di gestione API di Azure per autenticare gli account per sviluppatori usando Azure Active Directory.

Configurare l'istanza di gestione API di Azure per proteggere le API usando il protocollo OAuth 2,0 con Azure Active Directory (AD).

* [Come autorizzare gli account per sviluppatori usando Azure Active Directory in gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

* [Come proteggere un'API usando OAuth 2,0 con Azure Active Directory e gestione API](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [Come creare e configurare un'istanza di AAD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: rivedere e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure Active Directory fornisce i log per individuare gli account obsoleti. I clienti possono usare le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso utente può essere esaminato a intervalli regolari per garantire che solo gli utenti corretti continuino ad avere l'accesso appropriato.

I clienti possono gestire l'inventario degli account utente di gestione API e riconciliare l'accesso in base alle esigenze. In gestione API, gli sviluppatori sono gli utenti delle API esposte con gestione API. Per impostazione predefinita, i nuovi account sviluppatore creati sono attivi e associati al gruppo Sviluppatori. Gli account sviluppatore con stato attivo possono essere usati per accedere a tutte le API per cui dispongono di sottoscrizioni.

Gli amministratori possono creare gruppi personalizzati o sfruttare gruppi esterni nei tenant di Azure Active Directory associati. È possibile usare gruppi personalizzati ed esterni assieme ai gruppi di sistema per offrire agli sviluppatori visibilità e accesso ai prodotti API.

* [Come gestire gli account utente in Gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-create-or-invite-developers)

* [Come ottenere un elenco di utenti di gestione API](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [Come creare e usare i gruppi per gestire gli account sviluppatore in Gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups)

* [Come usare le verifiche di accesso alle identità di Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: il monitoraggio tenta di accedere agli account disattivati

**Linee guida**: configurare l'istanza di gestione API di Azure per autenticare gli account per sviluppatori usando Azure Active Directory come provider di identità in gestione API di Azure.

Configurare l'istanza di gestione API di Azure per proteggere le API usando il protocollo OAuth 2,0 con Azure Active Directory (AD).

Configurare i criteri di convalida JWT per le richieste API in ingresso per consentire l'applicazione dell'esistenza e della validità di un token valido.

Creare le impostazioni di diagnostica per Azure AD account utente e inviare i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. Configurare gli avvisi desiderati entro Log Analytics. Inoltre, è possibile caricare l'area di lavoro Log Analytics in Azure Sentinel o in un SIEM di terze parti.

Configurare il monitoraggio avanzato con gestione API usando il `log-to-eventhub` criterio, acquisire eventuali informazioni aggiuntive sul contesto necessarie per l'analisi della sicurezza e inviare ad Azure Sentinel o Siem di terze parti.

* [Come autorizzare gli account per sviluppatori usando Azure Active Directory in gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

* [Come proteggere un'API usando OAuth 2,0 con Azure Active Directory e gestione API](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [Criteri di limitazione dell'accesso di Gestione API](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies)

* [Come integrare Azure AD log in monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Come caricare Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Monitoraggio avanzato delle API](https://docs.microsoft.com/azure/api-management/api-management-log-to-eventhub-sample)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento dell'account di accesso

**Linee guida**: per la deviazione del comportamento dell'account di accesso nel piano di controllo (il portale di Azure), usare le funzionalità di protezione delle identità di Azure Active Directory (ad) e di rilevamento dei rischi per configurare le risposte automatiche a azioni sospette rilevate correlate alle identità utente. È anche possibile inserire dati in Sentinel di Azure per un'analisi più approfondita.

* [Come visualizzare Azure AD accessi a rischio](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Come configurare e abilitare i criteri di rischio di Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: non attualmente disponibili; Customer Lockbox non è attualmente supportata per gestione API di Azure.

* [Elenco di servizi supportati da Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per semplificare il monitoraggio delle risorse di Azure che archiviano o elaborano informazioni riservate.

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Le istanze di gestione API di Azure devono essere separate da rete virtuale (VNet)/della subnet e contrassegnate in modo appropriato.

* [Come creare sottoscrizioni di Azure aggiuntive](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Come creare Gruppi di gestione](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Come usare Gestione API di Azure con le reti virtuali](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: non attualmente disponibili; le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono attualmente disponibili per gestione API di Azure.

Microsoft gestisce l'infrastruttura sottostante per gestione API di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

* [Informazioni sulla protezione dei dati dei clienti in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: crittografare tutte le informazioni riservate in transito

**Linee guida**: le chiamate del piano di gestione vengono effettuate tramite Azure Resource Manager su TLS. È necessario un token JSON Web (JWT) valido. Le chiamate del piano dati possono essere protette con TLS e uno dei meccanismi di autenticazione supportati (ad esempio, il certificato client o JWT).

* [Informazioni sulla protezione dei dati in gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-security-controls#data-protection)

* [Gestire le impostazioni TLS in gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-manage-protocols-ciphers)

* [Proteggi le API in gestione API di Azure con Azure Active Directory](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [Proteggi le API in gestione API di Azure con Azure Active Directory B2C](https://docs.microsoft.com/azure/api-management/howto-protect-backend-frontend-azure-ad-b2c)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: non attualmente disponibili; le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono attualmente disponibili per gestione API di Azure. Contrassegnare i servizi di gestione API di Azure che potrebbero elaborare informazioni riservate come tali e implementare una soluzione di terze parti, se necessario ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e passa a grandi lunghezze per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e mantiene una suite di controlli e funzionalità affidabili per la protezione dei dati.

* [Informazioni sulla protezione dei dati dei clienti in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: usare RBAC di Azure per controllare l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo per controllare l'accesso a gestione API di Azure. Gestione API di Azure si basa sul controllo degli accessi in base al ruolo di Azure per abilitare la gestione degli accessi con granularità fine per i servizi e le entità di Gestione API (ad esempio, API e criteri).

* [Come usare il controllo degli accessi in base al ruolo in gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per gestione API di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

* [Informazioni sulla protezione dei dati dei clienti in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: crittografare le informazioni riservate inattive

**Linee guida**: i dati sensibili, ad esempio i certificati, le chiavi e i valori denominati del segreto, vengono crittografati con le chiavi di istanza gestite dal servizio. Tutte le chiavi di crittografia sono per istanza del servizio e sono gestite dal servizio.

* [Comprendere la protezione dei dati e la crittografia dei dati inattivi con gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-security-controls#data-protection)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: registrare e segnalare le modifiche apportate alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle app di funzioni di Azure di produzione e ad altre risorse critiche o correlate.

* [Come creare avvisi per gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Come usare monitoraggio di Azure e log attività di Azure in gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: eseguire strumenti di analisi automatica delle vulnerabilità

**Linee guida**: non attualmente disponibili; la valutazione della vulnerabilità nel centro sicurezza di Azure non è attualmente disponibile per gestione API di Azure.

Piattaforma sottostante analizzata e patchata da Microsoft. Esaminare i controlli di sicurezza disponibili per ridurre le vulnerabilità correlate alla configurazione del servizio.

* [Informazioni sui controlli di sicurezza disponibili per gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-security-controls)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: distribuire la soluzione di gestione delle patch per il sistema operativo automatizzata

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: distribuire una soluzione di gestione delle patch software di terze parti automatizzata

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: confrontare le analisi delle vulnerabilità back-to-back

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Linee guida**: non attualmente disponibili; la valutazione della vulnerabilità nel centro sicurezza di Azure non è attualmente disponibile per gestione API di Azure.

Piattaforma sottostante analizzata e patchata da Microsoft. Cliente per esaminare i controlli di sicurezza disponibili per ridurre le vulnerabilità correlate alla configurazione dei servizi.

* [Informazioni sui controlli di sicurezza disponibili per gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-security-controls)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [controllo di sicurezza: inventario e gestione delle risorse](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: usare Azure Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) nelle sottoscrizioni. Verificare le autorizzazioni (lettura) appropriate nel tenant ed enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure classiche possano essere individuate tramite Graph di risorse, è consigliabile creare e usare Azure Resource Manager risorse in futuro.

* [Come creare query con il grafico delle risorse di Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Come visualizzare le sottoscrizioni di Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Informazioni su RBAC di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="62-maintain-asset-metadata"></a>6,2: gestire i metadati dell'asset

**Linee guida**: applicare i tag alle risorse di Azure fornendo metadati per organizzarli in modo logico in una tassonomia.

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: eliminare le risorse di Azure non autorizzate

**Linee guida**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle risorse di Azure. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Usare inoltre i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

* [Come creare sottoscrizioni di Azure aggiuntive](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Come creare Gruppi di gestione](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: gestire un inventario delle risorse di Azure approvate e dei titoli software

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorare le risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare il grafico risorse di Azure per eseguire query e individuare le risorse all'interno della sottoscrizione o delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

* [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Come creare query con Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="68-use-only-approved-applications"></a>6,8: usare solo le applicazioni approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="69-use-only-approved-azure-services"></a>6,9: usare solo i servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

* [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Come negare un tipo di risorsa specifico con criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="610-implement-approved-application-list"></a>6,10: implementare l'elenco di applicazioni approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: limitare la capacità degli utenti di interagire con gestione risorse di Azure tramite script

**Linee guida**: configurare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app "gestione Microsoft Azure".

* [Come configurare l'accesso condizionale per bloccare l'accesso ai Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

* [Controllo degli accessi in base al ruolo in gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle applicazioni Web in esecuzione in app Azure servizio o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [controllo di sicurezza: configurazione sicura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per il servizio gestione API di Azure con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. ApiManagement" per creare criteri personalizzati per controllare o applicare la configurazione dei servizi gestione API di Azure.

* [Come visualizzare gli alias dei criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: stabilire configurazioni del sistema operativo sicure

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: mantenere sicure le configurazioni delle risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per i servizi di gestione API di Azure con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. ApiManagement" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze di gestione API di Azure. Usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure tra le risorse di Azure.

* [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Informazioni sugli effetti dei criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: mantenere sicure le configurazioni del sistema operativo

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: condiviso

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni di criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire in modo sicuro la configurazione del servizio gestione API di Azure.

* [Come archiviare i file in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentazione di Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

* [Informazioni sul Resource Kit di gestione API di Azure DevOps](https://docs.microsoft.com/azure/api-management/api-management-security-controls#configuration-management)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: archiviare in modo sicuro immagini del sistema operativo personalizzate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida**: definire e implementare configurazioni di sicurezza standard per i servizi di gestione API di Azure con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. ApiManagement" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze di gestione API di Azure. Usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure tra le risorse di Azure.

* [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Informazioni sugli effetti dei criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

**Linee guida**: usare Azure API Management DevOps Resource Kit per eseguire la gestione della configurazione per gestione API di Azure.

Inoltre, definire e implementare configurazioni di sicurezza standard per i servizi di gestione API di Azure con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. ApiManagement" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze di gestione API di Azure. Usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure tra le risorse di Azure.

* [Informazioni sul Resource Kit di gestione API di Azure DevOps](https://docs.microsoft.com/azure/api-management/api-management-security-controls#configuration-management)

* [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Informazioni sugli effetti dei criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="711-manage-azure-secrets-securely"></a>7,11: gestire i segreti di Azure in modo sicuro

**Linee guida**: usare Key Vault per la gestione dei certificati e impostarli per la rotazione. Se si usa Azure Key Vault per gestire il certificato SSL del dominio personalizzato, assicurarsi che il certificato venga inserito in Key Vault come certificato, non come segreto.

* [Come impostare i nomi di dominio personalizzati con linee guida per la rotazione delle chiavi Key Vault](https://docs.microsoft.com/azure/api-management/configure-custom-domain)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Microsoft

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: gestire le identità in modo sicuro e automatico

**Linee guida**: usare identità del servizio gestita generati da Azure Active Directory (ad) per consentire all'istanza di gestione API di accedere in modo semplice e sicuro ad altre risorse protette da Azure ad, ad esempio Azure Key Vault.

* [Come creare un'identità gestita per un'istanza di gestione API](https://docs.microsoft.com/azure/api-management/api-management-howto-use-managed-service-identity)

* [Criteri per l'autenticazione con identità gestita](https://docs.microsoft.com/azure/api-management/api-management-authentication-policies#ManagedIdentity)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminare l'esposizione delle credenziali non intenzionali

**Indicazioni**: implementare Credential scanner per identificare le credenziali all'interno del codice. Credential scanner incoraggerà inoltre lo stato di trasferimento delle credenziali individuate a posizioni più sicure, ad esempio Azure Key Vault.

* [Come configurare Credential scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [controllo di sicurezza: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: utilizzo del software antimalware gestito centralmente

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio gestione API di Azure, ma non viene eseguito sui contenuti dei clienti.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: pre-analizzare i file da caricare in risorse di Azure non di calcolo

**Linee guida**: non applicabile; Questa raccomandazione è concepita per le risorse non di calcolo progettate per archiviare i dati.

Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio gestione API di Azure, ma non viene eseguito sui contenuti dei clienti.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: assicurarsi che il software e le firme anti-malware siano aggiornati

**Linee guida**: non applicabile; Questa raccomandazione è concepita per le risorse non di calcolo progettate per archiviare i dati.

Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio gestione API di Azure, ma non viene eseguito sui contenuti dei clienti.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire l'esecuzione di backup automatici regolari

**Linee guida**: la pubblicazione e la gestione delle API tramite gestione API di Azure consentono di sfruttare la tolleranza di errore e le funzionalità dell'infrastruttura che altrimenti verrebbero progettate, implementate e gestite manualmente. Gestione API supporta la distribuzione in più aree che rende il piano dati impermeabile agli errori a livello di area senza aggiungere alcun sovraccarico operativo.

Le funzionalità di backup e ripristino del servizio di gestione API forniscono i blocchi predefiniti necessari per l'implementazione di una strategia di ripristino di emergenza. Le operazioni di backup e ripristino possono essere eseguite manualmente o automatizzate.

* [Come distribuire un piano dati di gestione API in più aree](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region)

* [Come implementare il ripristino di emergenza usando il backup e il ripristino dei servizi in Gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [Come chiamare l'operazione di backup di gestione API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/backup)

* [Come chiamare l'operazione di ripristino di gestione API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: le operazioni di backup e ripristino fornite da gestione API di Azure eseguono il backup e il ripristino completo del sistema.

È possibile usare le identità gestite per ottenere i certificati da Azure Key Vault per i nomi di dominio personalizzati di gestione API. Eseguire il backup di tutti i certificati archiviati all'interno Azure Key Vault.

* [Come implementare il ripristino di emergenza usando il backup e il ripristino dei servizi in Gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [Come eseguire il backup di certificati Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni**: convalidare i backup eseguendo un ripristino di prova del servizio e dei certificati dai backup.

* [Come chiamare l'operazione di ripristino di gestione API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)

* [Come ripristinare Azure Key Vault certificati](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: gestione API di Azure scrive i backup negli account di archiviazione di Azure di proprietà del cliente. Seguire le raccomandazioni sulla sicurezza di archiviazione di Azure per proteggere il backup.

* [Come implementare il ripristino di emergenza usando il backup e il ripristino dei servizi in Gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [Raccomandazione sulla sicurezza per l'archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)

Abilitare l'eliminazione temporanea in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.

* [Come abilitare l'eliminazione temporanea in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: creare una guida alla risposta agli eventi imprevisti

**Linee**guida: creare una guida alla risposta agli eventi imprevisti per la propria organizzazione. Verificare che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione/gestione degli eventi imprevisti dal rilevamento alla verifica post-evento imprevisto.

* [Linee guida per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Utilizzare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per semplificare la creazione del piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: creare una procedura per assegnazione di punteggi e assegnazione di priorità

**Indicazioni**: il Centro sicurezza assegna una gravità a ogni avviso per facilitare la priorità degli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, nonché sul livello di confidenza causato da un intento dannoso dietro l'attività che ha portato all'avviso.

Inoltre, contrassegnare chiaramente le sottoscrizioni (per es. produzione, non prod) con tag e creazione di un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure, in particolare quelle che elaborano i dati sensibili. È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

* [Avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [Usare tag per organizzare le risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="103-test-security-response-procedures"></a>10,3: testare le procedure di risposta alla sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare per proteggere le risorse di Azure. Identificare i punti deboli e i gap e rivedere il piano in base alle esigenze.

* [Pubblicazione del NIST: Guida ai programmi di test, formazione e esercizio per piani e funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: specificare i dettagli del contatto per gli eventi imprevisti della sicurezza e configurare le notifiche di avviso per gli eventi

**Linee guida**: le informazioni di contatto per gli eventi imprevisti di sicurezza verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati da parte di utenti non autorizzati o non autorizzati. Esaminare gli eventi imprevisti dopo il fatto di garantire la risoluzione dei problemi.

* [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per identificare i rischi per le risorse di Azure. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo e continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

* [Come configurare l'esportazione continua](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Come trasmettere avvisi in Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nelle raccomandazioni.

* [Come configurare l'automazione del flusso di lavoro e le app per la logica](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [controllo di sicurezza: test di penetrazione e esercizi del Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e assicurarsi di correggere tutti i risultati critici della sicurezza entro 60 giorni

**Linee guida**: * [attenersi alle regole di engagement Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [È possibile trovare altre informazioni sulla strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere il [benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Scopri di più sulle [linee di base di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
