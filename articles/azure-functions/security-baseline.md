---
title: Baseline della sicurezza di Azure per funzioni di Azure
description: Baseline della sicurezza di Azure per funzioni di Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 040eeda3edc8aa1165915a157cb7e1bdd1594740
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796499"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Baseline della sicurezza di Azure per funzioni di Azure

La linea di base di sicurezza di Azure per funzioni di Azure contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La linea di base per questo servizio viene creata dalla [versione 1,0 del benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce indicazioni su come proteggere le soluzioni cloud in Azure con le procedure consigliate.

Per altre informazioni, vedere [Panoramica di base sulla sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

**Linee guida**: integrare le app di funzioni di Azure con una rete virtuale di Azure. Le app per le funzioni in esecuzione nel piano Premium hanno le stesse funzionalità di hosting di app Web nel servizio app Azure, che include la funzionalità "integrazione VNet".  Le reti virtuali di Azure consentono di inserire molte delle risorse di Azure, ad esempio funzioni di Azure, in una rete instradabile non Internet.

- [Come integrare funzioni con una rete virtuale di Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-vnet)

- [Informazioni sull'integrazione di VNET per funzioni di Azure e servizio app Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

**Linee guida**: usare il Centro sicurezza di Azure e seguire le raccomandazioni per la protezione della rete per proteggere le risorse di rete e le configurazioni di rete correlate alle app di funzioni di Azure.

Se si usano i gruppi di sicurezza di rete (gruppi) con l'implementazione di funzioni di Azure, abilitare i log di flusso di NSG e inviare i log a un account di archiviazione di Azure per i controlli del traffico. È anche possibile inviare i log dei flussi di NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete non configurate.

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [Come abilitare i log di flusso NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Come abilitare e usare Analisi del traffico](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="13-protect-critical-web-applications"></a>1,3: proteggere le applicazioni Web critiche

**Linee guida**: per proteggere completamente gli endpoint funzione di Azure nell'ambiente di produzione, è consigliabile implementare una delle seguenti opzioni di sicurezza a livello di app per le funzioni:
- Attivare l'autenticazione/autorizzazione del servizio app per l'app per le funzioni,
- Usare gestione API di Azure (gestione API) per autenticare le richieste o
- Distribuire l'app per le funzioni in un ambiente del servizio app di Azure.

Assicurarsi inoltre che il debug remoto sia stato disabilitato per le funzioni di Azure di produzione. Inoltre, la condivisione di risorse tra le origini (CORS) non deve consentire a tutti i domini di accedere all'app per le funzioni di Azure. Consente solo ai domini richiesti di interagire con l'app per le funzioni di Azure.

Prendere in considerazione la distribuzione di Web Application Firewall (WAF) di Azure come parte della configurazione di rete per un ulteriore controllo del traffico in ingresso. Abilitare l'impostazione di diagnostica per WAF e inserire i log in un account di archiviazione, un hub eventi o un'area di lavoro Log Analytics. 

- [Come proteggere gli endpoint delle funzioni di Azure nell'ambiente di produzione](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production)

- [Come distribuire Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare protezione DDoS standard nelle reti virtuali associate alle app per le funzioni per proteggersi da attacchi DDoS. Usare il Centro sicurezza di Azure Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP pubblici dannosi o inutilizzati noti.
Inoltre, configurare un gateway front-end, ad esempio Web Application Firewall di Azure, per autenticare tutte le richieste in ingresso e filtrare il traffico dannoso. Il Web Application Firewall di Azure consente di proteggere le app per le funzioni di Azure controllando il traffico Web in ingresso per bloccare gli attacchi SQL injection, gli script tra siti, i caricamenti di malware e gli attacchi DDoS. L'introduzione di un WAF richiede un ambiente del servizio app o l'uso di endpoint privati (anteprima). Assicurarsi che gli endpoint privati non siano più in anteprima prima di usarli con i carichi di lavoro di produzione.

- [Opzioni di rete di Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Piano Premium di funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Introduzione agli ambienti del servizio app](https://docs.microsoft.com/azure/app-service/environment/intro)

- [Considerazioni sulla rete per un ambiente del servizio app](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [Come configurare la protezione DDoS](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Come distribuire il firewall di Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Informazioni sull'Intelligence per le minacce integrata nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [Informazioni sul centro sicurezza di Azure Adaptive Network hardening](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Informazioni sul controllo di accesso di rete just-in-Time del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

- [Uso di endpoint privati per funzioni di Azure](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrare i pacchetti di rete e i log di flusso

**Linee guida**: se si usano i gruppi di sicurezza di rete (gruppi) con l'implementazione di funzioni di Azure, abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log a un account di archiviazione per il controllo È anche possibile inviare i log di flusso a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete non configurate.

- [Come abilitare i log di flusso NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Come abilitare e usare Analisi del traffico](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Come abilitare Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (IDS/IP)

**Indicazioni**: configurare un gateway front-end, ad esempio Web Application Firewall di Azure per autenticare tutte le richieste in ingresso e filtrare il traffico dannoso. Il Web Application Firewall di Azure consente di proteggere le app per le funzioni controllando il traffico Web in ingresso per bloccare gli attacchi SQL injection, gli script tra siti, i caricamenti di malware e gli attacchi DDoS. L'introduzione di un WAF richiede un ambiente del servizio app o l'uso di endpoint privati (anteprima). Assicurarsi che gli endpoint privati non siano più in anteprima prima di usarli con i carichi di lavoro di produzione.

In alternativa, sono disponibili più opzioni di Marketplace come Barracuda WAF per Azure disponibili in Azure Marketplace che includono le funzionalità di ID/IP.

- [Opzioni di rete di Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Piano Premium di funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Introduzione agli ambienti del servizio app](https://docs.microsoft.com/azure/app-service/environment/intro)

- [Considerazioni sulla rete per un ambiente del servizio app](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [Informazioni sul firewall applicazione Web di Azure](https://docs.microsoft.com/azure/application-gateway/overview#web-application-firewall)

- [Uso di endpoint privati per funzioni di Azure](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

- [Informazioni su Barracuda WAF Cloud Service](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-web-application-firewall#configuring-your-barracuda-waf-cloud-service)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="17-manage-traffic-to-web-applications"></a>1,7: gestire il traffico per le applicazioni Web

**Linee guida**: configurare un gateway front-end per la rete, ad esempio il firewall applicazione Web di Azure con la crittografia TLS end-to-end. L'introduzione di un WAF richiede un ambiente del servizio app o l'uso di endpoint privati (anteprima). Assicurarsi che gli endpoint privati non siano più in anteprima prima di usarli con i carichi di lavoro di produzione.

- [Opzioni di rete di Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Piano Premium di funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Introduzione agli ambienti del servizio app](https://docs.microsoft.com/azure/app-service/environment/intro)

- [Considerazioni sulla rete per un ambiente del servizio app](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [Informazioni sul firewall applicazione Web di Azure](https://docs.microsoft.com/azure/application-gateway/overview#web-application-firewall)

- [Come configurare TLS end-to-end usando il gateway applicazione con il portale](https://docs.microsoft.com/azure/application-gateway/end-to-end-ssl-portal)

- [Uso di endpoint privati per funzioni di Azure](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ridurre la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, AzureAppService) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag di servizio in base alla modifica degli indirizzi.

- [Per ulteriori informazioni sull'utilizzo dei tag di servizio](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: mantenere le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le impostazioni di rete correlate alle funzioni di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. Web" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle funzioni di Azure. È anche possibile usare le definizioni di criteri predefinite per funzioni di Azure, ad esempio:
- CORS non deve consentire a tutte le risorse di accedere alle app per le funzioni
- L'app per le funzioni deve essere accessibile solo tramite HTTPS
- Nell'app per le funzioni è necessario usare la versione più recente di TLS

È anche possibile usare i progetti di Azure per semplificare le distribuzioni di Azure su larga scala tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio Azure Resource Manager modelli, il controllo degli accessi in base al ruolo e i criteri in un'unica definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Come creare un Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="110-document-traffic-configuration-rules"></a>1,10: regole di configurazione del traffico documento

**Linee guida**: se si usano i gruppi di sicurezza di rete (gruppi) con l'implementazione di funzioni di Azure, usare i tag per gruppi e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le singole regole NSG, usare il campo "Description" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usare gli strumenti automatici per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le impostazioni di rete e le risorse correlate alle distribuzioni di funzioni di Azure. Creare avvisi in monitoraggio di Azure che si attiveranno quando si verificano modifiche alle impostazioni o alle risorse di rete critiche. 

- [Come visualizzare e recuperare gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Come creare avvisi in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: utilizzare origini di sincronizzazione dell'ora approvate

**Linee guida**: Microsoft gestisce l'origine dell'ora usata per le risorse di Azure, ad esempio funzioni di Azure per i timestamp nei log.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: configurare la gestione dei log di sicurezza centrale

**Indicazioni**: per la registrazione di controllo del piano di controllo, abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro log Analytics, a hub eventi di Azure o all'account di archiviazione di Azure per l'archiviazione Usando i dati del log attività di Azure, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita a livello di piano di controllo per le risorse di Azure.

Funzioni di Azure offre anche l'integrazione incorporata con applicazione Azure Insights per monitorare le funzioni. Application Insights raccoglie i dati di log, delle prestazioni e degli errori. Rileva automaticamente le anomalie delle prestazioni e include potenti strumenti di analisi che consentono di diagnosticare i problemi e di comprendere come vengono usate le funzioni.

Se l'app per le funzioni di Azure include registrazioni di sicurezza/controllo personalizzate predefinite, abilitare l'impostazione di diagnostica "FunctionAppLogs" e inviare i log a un'area di lavoro Log Analytics, a un hub eventi di Azure o a un account di archiviazione di Azure per l'archiviazione. 

Facoltativamente, è possibile abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti. 

- [Come abilitare le impostazioni di diagnostica per log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Come configurare funzioni di Azure con applicazione Azure Insights](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)

- [Come abilitare le impostazioni di diagnostica (log generati dall'utente) per funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni**: per la registrazione di controllo del piano di controllo, abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro log Analytics, a hub eventi di Azure o all'account di archiviazione di Azure per l'archiviazione Usando i dati del log attività di Azure, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita a livello di piano di controllo per le risorse di Azure.

Se l'app per le funzioni di Azure include registrazioni di sicurezza/controllo personalizzate predefinite, abilitare l'impostazione di diagnostica "FunctionAppLogs" e inviare i log a un'area di lavoro Log Analytics, a un hub eventi di Azure o a un account di archiviazione di Azure per l'archiviazione. 

- [Come abilitare le impostazioni di diagnostica per log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Come abilitare le impostazioni di diagnostica (log generati dall'utente) per funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: raccogliere i registri di sicurezza dai sistemi operativi

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo IaaS.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="25-configure-security-log-storage-retention"></a>2,5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: in monitoraggio di Azure impostare il periodo di conservazione del log per le aree di lavoro log Analytics associate alle app di funzioni di Azure in base alle normative di conformità dell'organizzazione.

- [Come impostare i parametri di conservazione dei log](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: abilitare le impostazioni di diagnostica del log attività di Azure, nonché le impostazioni di diagnostica per l'app funzioni di Azure e inviare i log a un'area di lavoro log Analytics. Consente di eseguire query Log Analytics per cercare termini, identificare tendenze, analizzare modelli e fornire molte altre informazioni basate sui dati raccolti.

Abilitare Application Insights per le app di funzioni di Azure per raccogliere dati di log, prestazioni ed errori. È possibile visualizzare i dati di telemetria raccolti da Application Insights all'interno del portale di Azure.

Se l'app per le funzioni di Azure include registrazioni di sicurezza/controllo personalizzate predefinite, abilitare l'impostazione di diagnostica "FunctionAppLogs" e inviare i log a un'area di lavoro Log Analytics, a un hub eventi di Azure o a un account di archiviazione di Azure per l'archiviazione. 

Facoltativamente, è possibile abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti. 

- [Come abilitare le impostazioni di diagnostica per log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Come abilitare le impostazioni di diagnostica per funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [Come configurare funzioni di Azure con applicazione Azure Insights e visualizzare i dati di telemetria](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)

- [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: abilitare gli avvisi per attività anomale

**Linee guida**: abilitare le impostazioni di diagnostica del log attività di Azure, nonché le impostazioni di diagnostica per l'app funzioni di Azure e inviare i log a un'area di lavoro log Analytics. Consente di eseguire query Log Analytics per cercare termini, identificare tendenze, analizzare modelli e fornire molte altre informazioni basate sui dati raccolti. È possibile creare avvisi basati sulle query dell'area di lavoro Log Analytics.

Abilitare Application Insights per le app di funzioni di Azure per raccogliere dati di log, prestazioni ed errori. È possibile visualizzare i dati di telemetria raccolti da Application Insights e creare avvisi all'interno del portale di Azure.

Facoltativamente, è possibile abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti. 

- [Come abilitare le impostazioni di diagnostica per log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Come abilitare le impostazioni di diagnostica per funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [Come abilitare Application Insights per funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#enable-application-insights-integration)

- [Come creare avvisi in Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

- [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizzare la registrazione anti-malware

**Linee guida**: non applicabile; Le app di funzioni di Azure non elaborano o producono log correlati all'anti-malware.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="29-enable-dns-query-logging"></a>2,9: abilitare la registrazione delle query DNS

**Linee guida**: non applicabile; Le app di funzioni di Azure non elaborano o producono log correlati a DNS accessibili dagli utenti.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2,10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo IaaS.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [controllo di sicurezza: identità e controllo di accesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: gestire un inventario degli account amministrativi

**Linee guida**: Azure Active Directory (ad) include ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi. 

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="32-change-default-passwords-where-applicable"></a>3,2: modificare le password predefinite se applicabile

**Linee guida**: l'accesso del piano di controllo alle funzioni di Azure viene controllato tramite Azure Active Directory (ad). Azure AD non è il concetto di password predefinite.

L'accesso al piano dati può essere controllato tramite diversi mezzi, incluse le chiavi di autorizzazione, le restrizioni di rete e la convalida di un'identità AAD. Le chiavi di autorizzazione vengono usate dai client che si connettono agli endpoint HTTP di funzioni di Azure e possono essere rigenerate in qualsiasi momento. Per impostazione predefinita, queste chiavi vengono generate per i nuovi endpoint HTTP.

Sono disponibili più metodi di distribuzione per le app per le funzioni, alcune delle quali possono sfruttare un set di credenziali generate. Esaminare i metodi di distribuzione che verranno usati per l'applicazione.

- [Proteggere un endpoint HTTP](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production)

- [Come ottenere e rigenerare le chiavi di autorizzazione](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#obtaining-keys)

- [Tecnologie di distribuzione in funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: usare account amministrativi dedicati

**Linee guida**: creare procedure operative standard per l'uso di account amministrativi dedicati. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio: è necessario che siano stati assegnati più proprietari agli account deprecati della sottoscrizione con le autorizzazioni proprietarie da rimuovere dalla sottoscrizione gli account esterni con autorizzazioni proprietario.

- [Come usare il Centro sicurezza di Azure per monitorare identità e accesso (anteprima)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [Come usare criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida**: laddove possibile, usare Azure Active Directory SSO anziché configurare singole credenziali autonome per l'accesso ai dati all'app per le funzioni. Usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure. Implementare Single Sign-On per le app di funzioni di Azure usando la funzionalità di autenticazione/autorizzazione del servizio app.

- [Informazioni sull'autenticazione e l'autorizzazione in funzioni di Azure](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization#identity-providers)

- [Informazioni su SSO con Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più fattori (multi-factor authentication Azure Active Directory) e seguire le indicazioni di gestione delle identità e degli accessi del Centro sicurezza di Azure.

- [Come abilitare l'autenticazione a più fattori in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Come monitorare identità e accesso nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: usare macchine virtuali dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida**: usare workstation con accesso con privilegi (Paw) con multi-factor authentication (multi-factor authentication) configurato per accedere e configurare le risorse di Azure.

- [Informazioni sulle workstation con accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Come abilitare l'autenticazione a più fattori in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: registrare e segnalare l'attività sospetta da account amministrativi

**Linee guida**: usare Azure Active Directory (AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verifica un'attività sospetta o non sicura nell'ambiente.

Inoltre, utilizzare Azure AD i rilevamenti dei rischi per visualizzare gli avvisi e i report sul comportamento utente rischioso.

- [Come distribuire Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

- [Informazioni sui rilevamenti del rischio Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: gestire le risorse di Azure solo dalle località approvate

**Linee guida**: usare i percorsi denominati di accesso condizionale per consentire l'accesso al portale di Azure solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare le località denominate in Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="39-use-azure-active-directory"></a>3,9: usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (ad) come sistema di autenticazione e autorizzazione centrale per le app di funzioni di Azure. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD anche Salt, hash e archivia in modo sicuro le credenziali utente.

- [Come configurare l'app funzioni di Azure per usare Azure AD account di accesso](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [Come creare e configurare un'istanza di AAD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: rivedere e riconciliare regolarmente l'accesso utente

**Linee guida**: Azure Active Directory (ad) fornisce log che consentono di individuare gli account obsoleti. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso utente può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo. 

- [Informazioni sulla creazione di report Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Come usare le verifiche di accesso alle identità di Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: il monitoraggio tenta di accedere agli account disattivati

**Linee guida**: usare Azure Active Directory (ad) come sistema di autenticazione e autorizzazione centrale per le app per le funzioni di Azure. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD anche Salt, hash e archivia in modo sicuro le credenziali utente.

È possibile accedere alle origini del registro eventi di attività, controllo e rischio Azure AD di accesso, che consentono di eseguire l'integrazione con Azure Sentinel o con SIEM di terze parti.

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi del log desiderati all'interno Log Analytics.

- [Come configurare l'app funzioni di Azure per usare Azure AD account di accesso](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [Come integrare i log attività di Azure in monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Come caricare Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento dell'account di accesso

**Linee guida**: usare Azure Active Directory (ad) come sistema di autenticazione e autorizzazione centrale per le app di funzioni di Azure. Per la deviazione del comportamento di accesso dell'account nel piano di controllo (il portale di Azure), usare le funzionalità di protezione delle identità di Azure Active Directory (AD) e di rilevamento dei rischi per configurare le risposte automatiche per rilevare azioni sospette correlate alle identità utente. È anche possibile inserire dati in Sentinel di Azure per un'analisi più approfondita.

- [Come visualizzare Azure AD accessi a rischio](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: non attualmente disponibili; Customer Lockbox non è attualmente supportata per funzioni di Azure.

- [Elenco di servizi supportati da Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per semplificare il monitoraggio delle risorse di Azure che archiviano o elaborano informazioni riservate.

- [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Le app per le funzioni di Azure devono essere separate da rete virtuale (VNet)/della subnet e contrassegnate in modo appropriato.

È anche possibile usare endpoint privati per eseguire l'isolamento rete. Un endpoint privato di Azure è un'interfaccia di rete che si connette privatamente e in modo sicuro a un servizio (ad esempio, l'endpoint HTTPs dell'app funzioni di Azure) con tecnologia di collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della VNet, in modo da portare il servizio in VNet. Gli endpoint privati si trovano in (anteprima) per le app per le funzioni in esecuzione nel piano Premium. Assicurarsi che gli endpoint privati non siano più in anteprima prima di usarli con i carichi di lavoro di produzione.

- [Come creare sottoscrizioni di Azure aggiuntive](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Come creare Gruppi di gestione](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Opzioni di rete di Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Piano Premium di funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Informazioni sull'endpoint privato](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)

- [Uso di endpoint privati per funzioni di Azure](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: distribuire uno strumento automatizzato sui perimetri di rete che monitora il trasferimento non autorizzato di informazioni riservate e blocca tali trasferimenti mentre invia avvisi ai professionisti della sicurezza delle informazioni. 

Microsoft gestisce l'infrastruttura sottostante per funzioni di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

- [Informazioni sulla protezione dei dati dei clienti in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: non applicabile

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: crittografare tutte le informazioni riservate in transito

**Indicazioni**: nella portale di Azure per le app per le funzioni di Azure, in "funzionalità della piattaforma: rete: SSL", abilitare l'impostazione "solo HTTPS" e impostare la versione minima di TLS su 1,2.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: non attualmente disponibili; le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono attualmente disponibili per funzioni di Azure. Contrassegnare le app per le funzioni che potrebbero elaborare informazioni riservate come tali e implementare una soluzione di terze parti, se necessario ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e passa a grandi lunghezze per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e mantiene una suite di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: usare RBAC di Azure per controllare l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo Azure Active Directory (ad) per controllare l'accesso al piano di controllo delle funzioni di Azure (il portale di Azure). 

- [Come configurare RBAC in Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo IaaS.

Microsoft gestisce l'infrastruttura sottostante per funzioni di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

- [Informazioni sulla protezione dei dati dei clienti in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: crittografare le informazioni riservate inattive

**Linee guida**: quando si crea un'app per le funzioni, è necessario creare o collegare un account di archiviazione di Azure di uso generico che supporti l'archiviazione BLOB, di Accodamento e tabelle. Questo perché le funzioni si basano sull'archiviazione di Azure per operazioni come la gestione dei trigger e la registrazione delle esecuzioni di funzioni. Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, i dati vengono crittografati con le chiavi gestite da Microsoft. Per un maggiore controllo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente per la crittografia dei dati di BLOB e file. Queste chiavi devono essere presenti in Azure Key Vault affinché l'app per le funzioni sia in grado di accedere all'account di archiviazione.

- [Informazioni sulle considerazioni sull'archiviazione per funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/storage-considerations)

- [Comprendere la crittografia di archiviazione di Azure per i dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: condiviso

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: registrare e segnalare le modifiche apportate alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle app per le funzioni di Azure di produzione e ad altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: eseguire strumenti di analisi automatica delle vulnerabilità

**Linee guida**: adottare una procedura DevSecOps per garantire che le applicazioni funzioni di Azure siano sicure e rimangano nel modo più sicuro possibile per tutta la durata del ciclo di vita. DevSecOps incorpora il team di sicurezza dell'organizzazione e le relative funzionalità nelle procedure DevOps che rendono la sicurezza una responsabilità di tutti gli utenti del team.

Inoltre, seguire le raccomandazioni del Centro sicurezza di Azure per proteggere le app per le funzioni di Azure.

- [Come aggiungere la convalida della sicurezza continua alla pipeline CI/CD](https://docs.microsoft.com/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops)

- [Come implementare le raccomandazioni per la valutazione della vulnerabilità del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="52-deploy-an-automated-operating-system-patch-management-solution"></a>5,2: distribuire una soluzione di gestione delle patch per il sistema operativo automatizzata

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo IaaS.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: distribuire una soluzione di gestione delle patch software di terze parti automatizzata

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo IaaS.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: confrontare le analisi delle vulnerabilità back-to-back

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo IaaS.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Linee guida**: Microsoft esegue la gestione delle vulnerabilità sui sistemi sottostanti che supportano funzioni di Azure, ma è possibile usare la gravità delle raccomandazioni nel centro sicurezza di Azure e il Punteggio sicuro per misurare i rischi all'interno dell'ambiente. Il Punteggio sicuro si basa sul numero di raccomandazioni del Centro sicurezza che sono state mitigate. Per definire la priorità delle raccomandazioni da risolvere per prima cosa, considerare la gravità di ciascuna di esse.

- [Guida di riferimento per le raccomandazioni sulla sicurezza](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: condiviso

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [controllo di sicurezza: inventario e gestione delle risorse](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: usare Azure Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) nelle sottoscrizioni.  Verificare le autorizzazioni (lettura) appropriate nel tenant ed enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure classiche possano essere individuate tramite Graph di risorse, è consigliabile creare e usare Azure Resource Manager risorse in futuro.

- [Come creare query con il grafico delle risorse di Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Come visualizzare le sottoscrizioni di Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Informazioni su RBAC di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="62-maintain-asset-metadata"></a>6,2: gestire i metadati dell'asset

**Linee guida**: applicare i tag alle risorse di Azure fornendo metadati per organizzarli in modo logico in una tassonomia.

- [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: eliminare le risorse di Azure non autorizzate

**Linee guida**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle risorse di Azure. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Usare inoltre i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti: i tipi di risorse non consentiti sono consentiti.

- [Come creare sottoscrizioni di Azure aggiuntive](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Come creare Gruppi di gestione](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: gestire un inventario delle risorse di Azure approvate e dei titoli software

**Linee guida**: definire le risorse di Azure approvate e il software approvato per le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorare le risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni. 

Usare il grafico risorse di Azure per eseguire query e individuare le risorse all'interno della sottoscrizione o delle sottoscrizioni.  Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate. 

- [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Come creare query con Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo IaaS.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo IaaS.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="68-use-only-approved-applications"></a>6,8: usare solo le applicazioni approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo IaaS.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="69-use-only-approved-azure-services"></a>6,9: usare solo i servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti: non sono consentiti tipi di risorse consentiti

- [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Come negare un tipo di risorsa specifico con criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="610-implement-approved-application-list"></a>6,10: implementare l'elenco di applicazioni approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo IaaS.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: limitare la capacità degli utenti di interagire con gestione risorse di Azure tramite script

**Linee guida**: configurare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app "gestione Microsoft Azure".

- [Come configurare l'accesso condizionale per bloccare l'accesso ai Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo IaaS.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: per le app per le funzioni di Azure sensibili o ad alto rischio, implementare sottoscrizioni e/o gruppi di gestione distinti per garantire l'isolamento.

Distribuire app per le funzioni di Azure ad alto rischio nella propria rete virtuale (VNet). La sicurezza perimetrale in funzioni di Azure viene eseguita tramite reti virtuali. Le funzioni in esecuzione nel piano Premium o ambiente del servizio app (ASE) possono essere integrate con reti virtuali. Scegliere l'architettura migliore per il caso d'uso.

- [Opzioni di rete di Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Piano Premium di funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Considerazioni sulla rete per un ambiente del servizio app](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [Come creare un ambiente del servizio app esterno](https://docs.microsoft.com/azure/app-service/environment/create-external-ase)

Come creare un ambiente del servizio app interno:

- [https://docs.microsoft.com/azure/app-service/environment/create-ilb-as](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Come creare una NSG con una configurazione di sicurezza](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [controllo di sicurezza: configurazione sicura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per l'app per le funzioni di Azure con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Web" per creare criteri personalizzati per controllare o applicare la configurazione delle app di funzioni di Azure. È anche possibile usare le definizioni di criteri predefinite, ad esempio:
- Nell'app per le funzioni è necessario usare un'identità gestita
- Il debug remoto deve essere disattivato per le app per le funzioni
- L'app per le funzioni deve essere accessibile solo tramite HTTPS

- [Come visualizzare gli alias dei criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: stabilire configurazioni del sistema operativo sicure

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo IaaS.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: mantenere sicure le configurazioni delle risorse di Azure

**Linee guida**: usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure nelle risorse di Azure.

- [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Informazioni sugli effetti dei criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: mantenere sicure le configurazioni del sistema operativo

**Linee guida**: non applicabile; Sebbene sia possibile distribuire le funzioni locali, questa guida è destinata alle risorse di calcolo IaaS. Quando si distribuiscono le funzioni locali, l'utente è responsabile della configurazione sicura dell'ambiente in uso.

- [Informazioni sulle funzioni locali](https://docs.microsoft.com/azure/azure-functions/functions-runtime-install)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: archiviare e gestire i modelli ARM e le definizioni di criteri personalizzati di Azure in modo sicuro nel controllo del codice sorgente.

- [Che cos'è l'infrastruttura come codice](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)

- [Criteri di progettazione come flussi di lavoro di codice](https://docs.microsoft.com/azure/governance/policy/concepts/policy-as-code)

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentazione di Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: archiviare in modo sicuro immagini del sistema operativo personalizzate

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo IaaS.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: distribuire gli strumenti di gestione della configurazione di sistema

**Indicazioni**: usare le definizioni di criteri di Azure predefinite e gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Web" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni dei criteri.

- [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo IaaS.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

**Indicazioni**: usare le definizioni di criteri di Azure predefinite e gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Web" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [Deploy if not exist] per applicare automaticamente le configurazioni per le risorse di Azure.

- [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo IaaS.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7,11: gestire i segreti di Azure in modo sicuro

**Linee guida**: usare identità gestite insieme a Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud. Identità gestite consente all'app per le funzioni di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Key Vault, senza credenziali nel codice.

- [Come creare una Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Come usare le identità gestite nel servizio app e in Funzioni di Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity)

- [Come fornire l'autenticazione Key Vault con un'identità gestita](https://docs.microsoft.com/azure/key-vault/managed-identity)

- [Usare i riferimenti Key Vault per il servizio app e funzioni di Azure](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: gestire le identità in modo sicuro e automatico

**Linee guida**: usare identità gestite per fornire all'app per le funzioni di Azure un'identità gestita automaticamente in Azure ad. Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Key Vault, senza credenziali nel codice.

- [Come usare le identità gestite nel servizio app e in Funzioni di Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminare l'esposizione delle credenziali non intenzionali

**Indicazioni**: implementare Credential scanner per identificare le credenziali all'interno del codice. Credential scanner incoraggerà inoltre lo stato di trasferimento delle credenziali individuate a posizioni più sicure, ad esempio Azure Key Vault. 

- [Come configurare Credential scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [controllo di sicurezza: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: utilizzo del software antimalware gestito centralmente

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo IaaS.

Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio funzioni di Azure, ma non viene eseguito sui contenuti del cliente.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: pre-analizzare i file da caricare in risorse di Azure non di calcolo

**Linee guida**: non applicabile; Questa raccomandazione è concepita per le risorse non di calcolo progettate per archiviare i dati.


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: assicurarsi che il software e le firme anti-malware siano aggiornati

**Linee guida**: non applicabile; Questa raccomandazione è concepita per le risorse non di calcolo progettate per archiviare i dati.

Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio funzioni di Azure, ma non viene eseguito sui contenuti del cliente.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire l'esecuzione di backup automatici regolari

**Indicazioni**: usare la funzionalità di backup e ripristino per pianificare backup regolari dell'app. Le app per le funzioni in esecuzione nel piano Premium hanno le stesse funzionalità di hosting di app Web nel servizio app Azure, che include la funzionalità di backup e ripristino.

Usare anche una soluzione di controllo del codice sorgente, ad esempio Azure Repos e Azure DevOps per archiviare e gestire il codice in modo sicuro. Azure DevOps Services sfrutta molte delle funzionalità di archiviazione di Azure per garantire la disponibilità dei dati in caso di errori hardware, interruzioni del servizio o situazioni di emergenza. Inoltre, il team di Azure DevOps segue le procedure per proteggere i dati da eliminazioni accidentali o dannose.

- [Eseguire il backup dell'app in Azure](https://docs.microsoft.com/azure/app-service/manage-backup)

- [Informazioni sulla disponibilità dei dati in Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentazione di Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni**: usare la funzionalità di backup e ripristino per pianificare backup regolari dell'app. Le app per le funzioni in esecuzione nel piano Premium hanno le stesse funzionalità di hosting di app Web nel servizio app Azure, che include la funzionalità di backup e ripristino. Eseguire il backup delle chiavi gestite dal cliente in Azure Key Vault.

Usare anche una soluzione di controllo del codice sorgente, ad esempio Azure Repos e Azure DevOps per archiviare e gestire il codice in modo sicuro. Azure DevOps Services sfrutta molte delle funzionalità di archiviazione di Azure per garantire la disponibilità dei dati in caso di errori hardware, interruzioni del servizio o situazioni di emergenza. Inoltre, il team di Azure DevOps segue le procedure per proteggere i dati da eliminazioni accidentali o dannose.

- [Eseguire il backup dell'app in Azure](https://docs.microsoft.com/azure/app-service/manage-backup)

- [Come eseguire il backup delle chiavi di Key Vault in Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Informazioni sulla disponibilità dei dati in Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentazione di Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: garantire la possibilità di eseguire periodicamente il ripristino dalla funzionalità di backup e ripristino. Se si usa un altro percorso offline per eseguire il backup del codice, verificare periodicamente la possibilità di eseguire ripristini completi. Testare il ripristino delle chiavi gestite dal cliente sottoposte a backup.

- [Ripristinare un'app in Azure da un backup](https://docs.microsoft.com/azure/app-service/web-sites-restore)

- [Ripristinare un'app in Azure da uno snapshot](https://docs.microsoft.com/azure/app-service/app-service-web-restore-snapshots)

- [Come ripristinare le chiavi di Key Vault in Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: i backup della funzionalità di backup e ripristino usano un account di archiviazione di Azure nella sottoscrizione. Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, i dati vengono crittografati con le chiavi gestite da Microsoft. Per un maggiore controllo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente per la crittografia dei dati di archiviazione.

Se si usano chiavi gestite dal cliente, assicurarsi che l'eliminazione temporanea in Key Vault sia abilitata per proteggere le chiavi da eliminazioni accidentali o dannose.

- [Crittografia dei dati inattivi di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

- [Come abilitare l'eliminazione temporanea in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: condiviso

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: creare una guida alla risposta agli eventi imprevisti

**Linee**guida: creare una guida alla risposta agli eventi imprevisti per la propria organizzazione. Verificare che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione/gestione degli eventi imprevisti dal rilevamento alla verifica post-evento imprevisto.

- [Come configurare le automazione dei flussi di lavoro nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

- [Linee guida per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: creare una procedura per assegnazione di punteggi e assegnazione di priorità

**Indicazioni**: il Centro sicurezza assegna una gravità a ogni avviso per facilitare la priorità degli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, nonché sul livello di confidenza causato da un intento dannoso dietro l'attività che ha portato all'avviso.

Inoltre, contrassegnare chiaramente le sottoscrizioni (per es. produzione, non prod) e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: condiviso

### <a name="103-test-security-response-procedures"></a>10,3: testare le procedure di risposta alla sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare. Identificare i punti deboli e i gap e rivedere il piano in base alle esigenze.

- [Vedere la pubblicazione del NIST: Guida ai programmi di test, formazione e esercizio per piani e funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: specificare i dettagli del contatto per gli eventi imprevisti della sicurezza e configurare le notifiche di avviso per gli eventi

**Linee guida**: le informazioni di contatto per gli eventi imprevisti di sicurezza verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che l'accesso ai dati del cliente è stato effettuato da un'entità illecita o non autorizzata.  Esaminare gli eventi imprevisti dopo il fatto di garantire la risoluzione dei problemi.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo e continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Come trasmettere avvisi in Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte agli avvisi e alle raccomandazioni di sicurezza con le app per la logica.

- [Come configurare l'automazione del flusso di lavoro e le app per la logica](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [controllo di sicurezza: test di penetrazione e esercizi del Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: seguire le regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.

- [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere il [benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Scopri di più sulle [linee di base di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
