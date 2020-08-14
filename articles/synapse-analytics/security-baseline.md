---
title: Baseline della sicurezza di Azure per l'analisi delle sinapsi
description: La linea di base di sicurezza di sinapsi Analytics fornisce le informazioni e le procedure per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 7f05e4fb0443107370f9182706bd35b45771e0f2
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210893"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Baseline della sicurezza di Azure per l'analisi delle sinapsi

La linea di base di sicurezza di Azure per sinapsi Analytics contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere [Panoramica delle baseline di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Controllo di sicurezza: sicurezza di rete](/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: proteggere il SQL Server di Azure in una rete virtuale tramite un collegamento privato. Il collegamento privato di Azure consente di accedere ai servizi PaaS di Azure tramite un endpoint privato nella rete virtuale. Il traffico tra la rete virtuale e il servizio attraversa la rete del backbone Microsoft.

In alternativa, quando ci si connette al pool SQL sinapsi, limitare l'ambito della connessione in uscita al database SQL usando un gruppo di sicurezza di rete. Disabilitare tutto il traffico del servizio di Azure nel database SQL tramite l'endpoint pubblico impostando Consenti i servizi di Azure su disattivato. Assicurarsi che non siano consentiti indirizzi IP pubblici nelle regole del firewall.

* [Informazioni sul collegamento privato di Azure](https://docs.microsoft.com/azure/private-link/private-link-overview)

* [Informazioni sul collegamento privato per SQL sinapsi di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)

* [Come creare una rete virtuale](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Come creare una NSG con una configurazione di sicurezza](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee guida**: quando ci si connette al pool SQL sinapsi di Azure e sono stati abilitati i log dei flussi del gruppo di sicurezza di rete (NSG), inviare i log in un account di archiviazione di Azure per il controllo del traffico.

È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

* [Come abilitare i log dei flussi NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Come abilitare e usare Analisi del traffico](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee guida**: non applicabile; Questa raccomandazione è destinata al servizio app di Azure o alle risorse di calcolo che ospitano applicazioni Web.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni**: usare Advanced Threat Protection (ATP) per SQL sinapsi di Azure. ATP rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database e può attivare diversi avvisi, ad esempio "potenziale attacco SQL injection" e "accesso da una posizione insolita". ATP fa parte dell'offerta Advanced Data Security (ADS) ed è possibile accedervi e gestirlo tramite il portale SQL ADS centrale.

Abilitare protezione DDoS standard nelle reti virtuali associate ad Azure sinapsi SQL per la protezione da attacchi Denial of Service distribuiti. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

* [Informazioni su ATP per sinapsi SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [Come abilitare la sicurezza dei dati avanzata per il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Panoramica degli annunci](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Come configurare la protezione DDoS](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: quando ci si connette al pool SQL sinapsi di Azure e sono stati abilitati i log dei flussi del gruppo di sicurezza di rete (NSG), inviare i log in un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log di flusso a un'area di lavoro di Log Analytics o trasmetterli a hub eventi. Se necessario per l'analisi dell'attività anomala, abilitare Network Watcher acquisizione pacchetti.

* [Come abilitare i log dei flussi NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Come abilitare Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Indicazioni**: usare Advanced Threat Protection (ATP) per SQL sinapsi di Azure. ATP rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database e può attivare diversi avvisi, ad esempio "potenziale attacco SQL injection" e "accesso da una posizione insolita". ATP fa parte dell'offerta Advanced Data Security (ADS) ed è possibile accedervi e gestirlo tramite il portale SQL ADS centrale. ATP integra anche gli avvisi con il Centro sicurezza di Azure.

* [Informazioni su ATP per sinapsi SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee guida**: non applicabile; Questa raccomandazione è destinata al servizio app di Azure o alle risorse di calcolo che ospitano applicazioni Web.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

Quando si usa un endpoint di servizio per il pool SQL sinapsi di Azure, è necessario aprire indirizzi IP pubblici in uscita per database SQL di Azure: i gruppi di sicurezza di rete (gruppi) devono essere aperti agli IP del database SQL di Azure per consentire la connettività. A tale scopo, è possibile usare i tag del servizio NSG per il database SQL di Azure.

* [Informazioni sui tag del servizio con gli endpoint di servizio per il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations)

* [Comprendere e usare i tag di servizio](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza di rete per le risorse correlate al pool SQL con criteri di Azure. È possibile usare lo spazio dei nomi "Microsoft. SQL" per definire le definizioni dei criteri personalizzati o usare una qualsiasi delle definizioni di criteri predefinite progettate per la protezione di rete del server o del database SQL di Azure. Un esempio di criteri di sicurezza di rete incorporati applicabili per il server di database SQL di Azure è il seguente: "SQL Server usare un endpoint del servizio di rete virtuale".

USA i progetti di Azure per semplificare le distribuzioni su larga scala di Azure tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di gestione risorse di Azure, controllo degli accessi in base al ruolo e criteri, in una singola definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Come creare un progetto di Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni**: usare i tag per i gruppi di sicurezza di rete (NSG) e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le regole dei singoli gruppi di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni su risorse basate sui rispettivi tag.

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate al pool SQL sinapsi di Azure. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

* [Come visualizzare e recuperare gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Come creare avvisi in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: Registrazione e monitoraggio](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Linee guida**: Microsoft gestisce le origini temporali per le risorse di Azure. È possibile aggiornare la sincronizzazione dell'ora per le distribuzioni di calcolo.

* [Come configurare la sincronizzazione dell'ora per le risorse di calcolo di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: è possibile definire criteri di controllo per un database specifico o come criteri server predefiniti in Azure (che ospita la sinapsi di Azure). I criteri server si applicano a tutti i database nuovi ed esistenti in un server.

Se il controllo del server è abilitato, viene sempre applicato al database. Il database verrà controllato, indipendentemente dalle impostazioni di controllo del database.

Quando si Abilita il controllo, è possibile scriverli in un log di controllo nell'account di archiviazione di Azure, Log Analytics area di lavoro o hub eventi.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

* [Come configurare il controllo per le risorse SQL di Azure](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

* [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare il controllo nel livello server SQL di Azure per il pool SQL sinapsi e scegliere un percorso di archiviazione per i log di controllo (archiviazione di azure, log Analytics o hub eventi).

È possibile abilitare il controllo a livello di database o di server ed è consigliabile abilitarlo solo a livello di server, a meno che non sia necessario configurare un sink di dati separato o una conservazione per un database specifico.

* [Come abilitare il controllo per il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)

* [Come abilitare il controllo per il server](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

* [Differenze tra i criteri di controllo a livello di server e di database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing#server-vs-database-level)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Linee guida**: non applicabile; questo benchmark è destinato alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: quando si archiviano i log relativi al pool SQL sinapsi in un account di archiviazione, log Analytics area di lavoro o hub eventi, impostare il periodo di conservazione dei log in base alle normative di conformità dell'organizzazione.

* [Gestire il ciclo di vita di Archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)

* [Come impostare i parametri di conservazione dei log in un'area di lavoro Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [Acquisire eventi di streaming in hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: analizzare e monitorare i log per i comportamenti anomali ed esaminare periodicamente i risultati. Usare Advanced Threat Protection per il database SQL di Azure insieme al centro sicurezza di Azure per segnalare attività insolite correlate al database SQL. In alternativa, configurare gli avvisi in base ai valori delle metriche o alle voci del log attività di Azure correlate al database SQL.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

* [Informazioni sulla protezione avanzata dalle minacce e sugli avvisi per il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [Come abilitare la sicurezza dei dati avanzata per il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Come configurare gli avvisi personalizzati per il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0)

* [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Indicazioni**: usare Advanced Threat Protection (ATP) per il database SQL di Azure in combinazione con il Centro sicurezza di Azure per monitorare e inviare avvisi sulle attività anomale. ATP fa parte dell'offerta Advanced Data Security (ADS) ed è possibile accedervi e gestirli tramite gli annunci SQL centrali nel portale. Gli annunci includono funzionalità per l'individuazione e la classificazione dei dati sensibili, l'emersione e la mitigazione di potenziali vulnerabilità del database e il rilevamento di attività anomale che potrebbero indicare una minaccia per il database.

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure.

* [Informazioni sulla protezione avanzata dalle minacce e sugli avvisi per il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [Come abilitare la sicurezza dei dati avanzata per il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Come gestire gli avvisi nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

* [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida**: non applicabile; per le risorse correlate al pool SQL sinapsi, la soluzione anti-malware viene gestita da Microsoft sulla piattaforma sottostante.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida**: non applicabile; Nessun log DNS viene prodotto dalle risorse correlate al pool SQL sinapsi.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: non applicabile; il controllo da riga di comando non è applicabile a SQL sinapsi di Azure.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida**: gli utenti vengono autenticati con l'autenticazione di Azure Active Directory o SQL.

Quando si distribuisce per la prima volta SQL di Azure, è necessario specificare un account di accesso amministratore e una password associata per tale account di accesso. Questo account amministrativo è denominato amministratore del server. È possibile identificare gli account amministratore per un database aprendo il portale di Azure e passando alla scheda proprietà del server o dell'istanza gestita. È anche possibile configurare un account amministratore Azure AD con autorizzazioni amministrative complete. questa operazione è necessaria se si desidera abilitare l'autenticazione Azure Active Directory.

Per le operazioni di gestione, usare i ruoli predefiniti di Azure che devono essere assegnati in modo esplicito. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

* [Autenticazione per il database SQL](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

* [Creare account per utenti non amministratori](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

* [Usare un account Azure Active Directory per l'autenticazione](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

* [Come ottenere un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Come gestire gli account di accesso e gli account amministratore esistenti in SQL di Azure](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

* [Ruoli predefiniti di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: Azure Active Directory non ha il concetto di password predefinite. Quando si esegue il provisioning di un pool SQL sinapsi di Azure, è consigliabile scegliere di integrare l'autenticazione con Azure Active Directory. Con questo metodo di autenticazione, l'utente invia un nome di account utente e richiede che il servizio utilizzi le informazioni sulle credenziali archiviate nel Azure Active Directory (Azure AD).

* [Come configurare e gestire l'autenticazione Azure Active Directory con SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#active-directory-password-authentication)

* [Informazioni sull'autenticazione in SQL di Azure](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Linee guida**: creare criteri e procedure per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure per monitorare il numero di account amministrativi che accedono tramite Azure Active Directory.

Per identificare gli account amministratore per un database, aprire il portale di Azure e passare alla scheda proprietà del server o dell'istanza gestita.

* [Informazioni sull'identità e sull'accesso del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Come gestire gli account di accesso e gli account amministratore esistenti in SQL di Azure](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usare Azure Active Directory Single Sign-On (SSO)

**Indicazioni**: usare una registrazione di app di Azure (entità servizio) per recuperare un token che può essere usato per interagire con il data warehouse nel piano di controllo (portale di Azure) tramite chiamate API.

* [Come chiamare le API REST di Azure](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Come registrare l'applicazione client (entità servizio) con Azure AD](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Informazioni sull'API REST SQL di Azure sinapsi](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Indicazioni**: abilitare Azure Active Directory (AD) Multi-Factor Authentication (MFA) e seguire le raccomandazioni sulla gestione delle identità e degli accessi nel Centro sicurezza di Azure.

* [Come abilitare MFA in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Informazioni sull'autenticazione a più fattori in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/authentication-mfa-ssms-overview)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usare workstation sicure gestite da Azure per le attività amministrative

**Indicazioni**: usare una workstation con accesso con privilegi (Paw) con multi-factor authentication (multi-factor authentication) configurato per accedere e configurare le risorse di Azure.

* [Informazioni sulle workstation con accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Come abilitare MFA in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida**: usare Azure Active Directory report di sicurezza per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente.

Usare Advanced Threat Protection per il database SQL di Azure insieme al centro sicurezza di Azure per rilevare e inviare avvisi su attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database.

SQL Server Audit consente di creare controlli del server che possono contenere specifiche di controllo del server per gli eventi a livello di server e specifiche di controllo del database per gli eventi a livello di database. Gli eventi controllati possono essere scritti nei log eventi o in file di controllo.

* [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Come monitorare l'identità degli utenti e l'attività di accesso nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Esaminare Advanced Threat Protection e potenziali avvisi](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts)

* [Informazioni sugli account di accesso e gli account utente in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

* [Informazioni sul controllo SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-ver15)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Linee guida**: usare i percorsi denominati di accesso condizionale per consentire l'accesso al portale e alla gestione delle risorse di Azure solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree.

* [Come configurare località denominate in Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: creare un amministratore di Azure Active Directory (ad) per il server di database SQL di Azure nel pool SQL di sinapsi.

* [Come configurare e gestire l'autenticazione Azure AD con SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

* [Come creare e configurare un'istanza di Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure Active Directory fornisce i log per individuare gli account obsoleti. Inoltre, è possibile utilizzare le verifiche di accesso Azure Active Directory per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso degli utenti può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo.

Quando si usa l'autenticazione SQL, creare utenti di database indipendente nel database. Assicurarsi di inserire uno o più utenti di database in un ruolo del database personalizzato con autorizzazioni specifiche appropriate per il gruppo di utenti.

* [Come usare le verifiche di accesso](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Informazioni sugli account di accesso e gli account utente in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Indicazioni**: configurare l'autenticazione Azure Active Directory (ad) con SQL di Azure e abilitare le impostazioni di diagnostica per Azure Active Directory account utente, inviando i log di controllo e i log di accesso a un'area di lavoro log Analytics. Configurare gli avvisi desiderati in Log Analytics.

Quando si usa l'autenticazione SQL, creare utenti di database indipendente nel database. Assicurarsi di inserire uno o più utenti di database in un ruolo del database personalizzato con autorizzazioni specifiche appropriate per il gruppo di utenti.

* [Come usare le verifiche di accesso](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Come configurare e gestire l'autenticazione di Azure AD con il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

* [Come integrare i log attività di Azure in Monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Informazioni sugli account di accesso e gli account utente in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Indicazioni**: usare le funzionalità di protezione delle identità Azure Active Directory (Azure ad) e di rilevamento del rischio per configurare risposte automatiche per le azioni sospette rilevate correlate alle identità utente. Inoltre, è possibile caricare e inserire i dati in Sentinel di Azure per un'analisi più approfondita.

Quando si usa l'autenticazione SQL, creare utenti di database indipendente nel database. Assicurarsi di inserire uno o più utenti di database in un ruolo del database personalizzato con autorizzazioni specifiche appropriate per il gruppo di utenti.

* [Come visualizzare gli accessi ai rischi Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Come configurare e abilitare i criteri di rischio di Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Come caricare dati in Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-data-sources)

* [Informazioni sugli account di accesso e gli account utente in Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Indicazioni**: in scenari di supporto in cui Microsoft deve accedere ai dati correlati al database SQL di Azure nel pool di SQL sinapsi, Azure Customer Lockbox fornisce un'interfaccia per esaminare e approvare o rifiutare le richieste di accesso ai dati.

* [Informazioni Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

La classificazione di individuazione dei dati &amp; è incorporata in SQL sinapsi di Azure. Fornisce funzionalità avanzate per l'individuazione, la classificazione, l'assegnazione di etichette e la segnalazione dei dati sensibili nei database.

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Informazioni sulla classificazione di individuazione dati &amp;](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Le risorse devono essere separate da rete virtuale/subnet, contrassegnate in modo appropriato e protette in un gruppo di sicurezza di rete o in un firewall di Azure. Le risorse che archiviano o elaborano dati sensibili devono essere isolate. USA collegamento privato; distribuire il SQL Server di Azure all'interno di una rete virtuale e connettersi in modo sicuro usando un collegamento privato.

* [Come creare sottoscrizioni di Azure aggiuntive](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Come creare gruppi di gestione](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Come configurare Collegamento privato per il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: per qualsiasi database SQL di Azure nel pool SQL di sinapsi che archivia o elabora informazioni riservate, contrassegnare il database e le risorse correlate come sensibili usando i tag. Configurare il collegamento privato insieme ai tag del servizio del gruppo di sicurezza di rete (NSG) nelle istanze del database SQL di Azure per impedire il exfiltration di informazioni riservate.

Inoltre, Advanced Threat Protection per il database SQL di Azure, Azure SQL Istanza gestita e la sinapsi di Azure rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere ai database o sfruttarli.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

* [Come configurare il collegamento privato e gruppi per impedire il exfiltration dei dati nelle istanze del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)

* [Informazioni sulla protezione avanzata dalle minacce per il database SQL di Azure](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview)

* [Informazioni sulla protezione dei dati dei clienti in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: il database SQL di Azure protegge i dati mediante la crittografia dei dati in movimento con Transport Layer Security. SQL Server impone sempre la crittografia (SSL/TLS) per tutte le connessioni. Ciò garantisce che tutti i dati siano crittografati in transito tra il client e il server indipendentemente dall'impostazione di Encrypt o TrustServerCertificate nella stringa di connessione.

* [Informazioni sulla crittografia SQL di Azure in transito](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: usare la funzionalità di classificazione individuazione dati SQL di Azure sinapsi &amp; . &amp;La classificazione di individuazione dati fornisce funzionalità avanzate incorporate nel database SQL di Azure per l'individuazione, la classificazione, l'assegnazione di etichette per &amp; la protezione dei dati sensibili nei database.

&amp;La classificazione di individuazione dati fa parte dell'offerta Advanced Data Security, che è un pacchetto unificato per le funzionalità avanzate di sicurezza di SQL. &amp;È possibile accedere alla classificazione di individuazione dati e gestirla tramite il portale SQL ADS centrale.

Inoltre, è possibile configurare un criterio di maschera dati dinamica (DDM) nel portale di Azure. Il motore di raccomandazioni DDM contrassegna determinati campi dal database come campi potenzialmente sensibili che possono essere candidati validi per la maschera.

* [Come usare l'individuazione e la classificazione dei dati per Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

* [Informazioni sulla maschera dati dinamica per SQL sinapsi di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per gestire l'accesso ai database SQL di Azure nel pool di SQL sinapsi.

L'autorizzazione viene controllata dalle appartenenze ai ruoli del database e dalle autorizzazioni a livello di oggetto dell'account utente. È consigliabile concedere agli utenti i privilegi minimi necessari.

* [Come integrare SQL Server di Azure con Azure Active Directory per l'autenticazione](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)

* [Come controllare l'accesso in Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-control-access)

* [Informazioni su autorizzazione e autenticazione in SQL di Azure](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: non applicabile; Microsoft gestisce l'infrastruttura sottostante per SQL sinapsi di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

* [Informazioni sulla protezione dei dati dei clienti in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografare le informazioni riservate inattive

**Linee guida**: Transparent Data Encryption (Transparent Data Encryption) consente di proteggere SQL sinapsi di Azure dalla minaccia di attività offline dannose mediante la crittografia dei dati inattivi. Esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni inattivi, senza richiedere modifiche dell'applicazione. In Azure, l'impostazione predefinita per Transparent Data Encryption è che la chiave di crittografia è protetta da un certificato server incorporato. In alternativa, è possibile utilizzare Transparent Data Encryption gestito dal cliente, noto anche come supporto Bring Your Own Key (BYOK) per Transparent Data Encryption. In questo scenario, la protezione Transparent Data Encryption che crittografa la chiave di crittografia è una chiave asimmetrica gestita dal cliente, archiviata in un Azure Key Vault gestito e di proprietà del cliente (sistema di gestione delle chiavi esterne basato sul cloud di Azure) senza mai lasciare l'insieme di credenziali delle chiavi.

* [Informazioni sulla crittografia Transparent Data Encryption gestita dal servizio](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal)

* [Informazioni sulla crittografia trasparente dei dati gestita dal cliente](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)

* [Come attivare Transparent Data Encryption usando la propria chiave](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-byok-configure)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle istanze di produzione di pool SQL sinapsi e altre risorse critiche o correlate.

Inoltre, è possibile configurare gli avvisi per i database nel pool SQL sinapsi usando il portale di Azure. Gli avvisi possono inviare un messaggio di posta elettronica all'utente o chiamare un webhook quando una o più metriche (ad esempio le dimensioni del database o l'utilizzo della CPU) raggiungono la soglia impostata.

* [Come creare avvisi per gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Come creare avvisi per la sinapsi SQL di Azure](https://docs.microsoft.com/azure/azure-sql/database/alerts-insights-configure-portal)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [Controllo di sicurezza: gestione della vulnerabilità](/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee guida**: abilitare la sicurezza dei dati avanzata e seguire le raccomandazioni del Centro sicurezza di Azure per l'esecuzione di valutazioni delle vulnerabilità nei database SQL di Azure.

* [Come eseguire valutazioni delle vulnerabilità nei database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

* [Come abilitare la sicurezza avanzata dei dati](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Come implementare le raccomandazioni per la valutazione della vulnerabilità del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: distribuire la soluzione di gestione delle patch automatizzata per i titoli software di terze parti

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee guida**: la valutazione della vulnerabilità è un servizio di analisi integrato in SQL sinapsi di Azure. Il servizio usa una Knowledge base di regole che contrassegnano le vulnerabilità della sicurezza. Evidenzia le deviazioni dalle procedure consigliate, ad esempio configurazioni errate, autorizzazioni eccessive e dati sensibili non protetti. È possibile accedere alla valutazione della vulnerabilità e gestirla tramite il portale di SQL Advanced Data Security (ADS) centrale.

* [Gestire ed esportare le analisi della valutazione della vulnerabilità nel portale SQL ADS](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: usare le classificazioni di rischio predefinite (Punteggio sicuro) fornite dal centro sicurezza di Azure.

La classificazione di individuazione dei dati &amp; è incorporata in SQL sinapsi di Azure. Fornisce funzionalità avanzate per l'individuazione, la classificazione, l'assegnazione di etichette e la segnalazione dei dati sensibili nei database.

* [Informazioni sul punteggio sicuro del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

* [Informazioni sulla classificazione di individuazione dati &amp;](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse correlate al pool SQL sinapsi nelle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure classiche possano essere individuate tramite Azure Resource Graph, è consigliabile creare e usare Azure Resource Manager risorse in futuro.

* [Come creare query con Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Come visualizzare le sottoscrizioni di Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Informazioni sul controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia degli asset. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

* [Come creare sottoscrizioni di Azure aggiuntive](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Come creare gruppi di gestione](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Linee guida**: definire un elenco di risorse di Azure approvate correlate al pool SQL sinapsi.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare il grafico risorse di Azure per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Come creare query con Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Come negare un tipo di risorsa specifico con Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle applicazioni in esecuzione sulle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

* [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: tutte le risorse correlate al pool SQL sinapsi richieste per le operazioni aziendali, ma possono comportare un rischio maggiore per l'organizzazione, devono essere isolate all'interno della propria macchina virtuale e/o della rete virtuale e sufficientemente protette con un firewall di Azure o un gruppo di sicurezza di rete.

* [Come creare una rete virtuale](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Come creare una NSG con una configurazione di sicurezza](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Controllo di sicurezza: configurazione sicura](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. SQL" per creare criteri personalizzati per controllare o applicare la configurazione delle risorse correlate al pool SQL sinapsi. È anche possibile usare le definizioni di criteri predefinite per database/server di Azure, ad esempio:
- Distribuisci Rilevamento minacce nelle istanze di SQL Server
- I server SQL devono usare un endpoint servizio di rete virtuale

* [Come visualizzare gli alias di Criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Informazioni sugli effetti di Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni di criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire il codice in modo sicuro.

* [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentazione di Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: non applicabile; SQL sinapsi di Azure non dispone di impostazioni di sicurezza configurabili.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida**: sfruttare il Centro sicurezza di Azure per eseguire analisi di base per tutte le risorse correlate al pool SQL sinapsi.

* [Come correggere le raccomandazioni nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Indicazioni**: Transparent Data Encryption (Transparent Data Encryption) con chiavi gestite dal cliente in Azure Key Vault consente di crittografare la chiave di crittografia del database generata automaticamente con una chiave asimmetrica gestita dal cliente denominata protezione Transparent Data Encryption. Questo è anche comunemente noto come supporto Bring Your Own Key (BYOK) per Transparent Data Encryption. Nello scenario BYOK, la protezione Transparent Data Encryption è archiviata in un Azure Key Vault gestito e di proprietà del cliente. Assicurarsi inoltre che l'eliminazione temporanea sia abilitata in Azure Key Vault.

* [Come abilitare Transparent Data Encryption con la chiave gestita dal cliente da Azure Key Vault](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-byok-configure?tabs=azure-powershell)

* [Come abilitare l'eliminazione temporanea in Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: usare identità gestite per fornire servizi di Azure con un'identità gestita automaticamente in Azure Active Directory (ad). Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Azure Key Vault, senza credenziali nel codice.

* [Esercitazione: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Azure SQL](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql)

* [Come configurare le identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

* [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Controllo di sicurezza: difesa da malware](/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usare il software antimalware gestito centralmente

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo. Microsoft gestisce l'anti-malware per la piattaforma sottostante.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, SQL sinapsi di Azure); Tuttavia, non viene eseguito sui contenuti del cliente.

Pre-analizza i contenuti caricati in risorse di Azure non di calcolo, ad esempio il servizio app, Data Lake Storage, archiviazione BLOB, Azure SQL Server e così via. Microsoft non è in grado di accedere ai dati in tali istanze.

* [Informazioni su Microsoft antimalware per servizi cloud e macchine virtuali di Azure](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: verificare che le firme e il software antimalware siano aggiornati

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo. Microsoft gestisce l'anti-malware per la piattaforma sottostante.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [Controllo di sicurezza: ripristino dei dati](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Linee guida**: gli snapshot del pool SQL sinapsi vengono eseguiti automaticamente durante la giornata creando punti di ripristino disponibili per sette giorni. Questo periodo di conservazione non può essere modificato. Il pool SQL supporta un obiettivo del punto di ripristino (RPO) di otto ore. È possibile ripristinare il data warehouse nell'area primaria da uno qualsiasi degli snapshot acquisiti negli ultimi sette giorni. Si noti che, se necessario, è anche possibile attivare manualmente gli snapshot.

* [Eseguire il backup e il ripristino nel pool SQL sinapsi di Azure](/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: gli snapshot del data warehouse vengono eseguiti automaticamente durante la giornata creando punti di ripristino disponibili per sette giorni. Questo periodo di conservazione non può essere modificato. Il pool SQL supporta un obiettivo del punto di ripristino (RPO) di otto ore. È possibile ripristinare il data warehouse nell'area primaria da uno qualsiasi degli snapshot acquisiti negli ultimi sette giorni. Si noti che, se necessario, è anche possibile attivare manualmente gli snapshot.

Se si usa una chiave gestita dal cliente per crittografare la chiave di crittografia del database, assicurarsi che venga eseguito il backup della chiave.

* [Eseguire il backup e il ripristino nel pool SQL sinapsi di Azure](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

* [Come eseguire il backup di chiavi di Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: testare periodicamente i punti di ripristino per assicurarsi che gli snapshot siano validi. Per ripristinare un pool SQL esistente da un punto di ripristino, è possibile usare il portale di Azure o PowerShell. Testare il ripristino delle chiavi gestite dal cliente sottoposte a backup.

* [Come ripristinare chiavi di Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Eseguire il backup e il ripristino nel pool SQL sinapsi di Azure](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

* [Come ripristinare un pool SQL esistente](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: nel database SQL di Azure è possibile configurare un database singolo o in pool con criteri di conservazione dei backup a lungo termine per mantenere automaticamente i backup del database in contenitori di archiviazione BLOB di Azure separati per un massimo di 10 anni. I dati in archiviazione di Azure vengono crittografati e decrittografati in modo trasparente usando la crittografia AES a 256 bit, una delle crittografie a blocchi più solide disponibili ed è conforme a FIPS 140-2.

Per impostazione predefinita, i dati in un account di archiviazione vengono crittografati con chiavi gestite da Microsoft. È possibile utilizzare chiavi gestite da Microsoft per la crittografia dei dati oppure è possibile gestire la crittografia con chiavi personalizzate. Se si gestiscono chiavi personalizzate con Key Vault, assicurarsi che l'eliminazione temporanea sia abilitata.

* [Gestire la conservazione a lungo termine dei backup del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure)

* [Crittografia del servizio di archiviazione di Azure per dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

* [Come abilitare l'eliminazione temporanea in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: assicurarsi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono i ruoli del personale, nonché le fasi di gestione/gestione degli eventi imprevisti.

* [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Linee guida**: il Centro sicurezza assegna un livello di gravità agli avvisi, in modo da consentire la priorità dell'ordine in cui si partecipa a ogni avviso, in modo che, quando una risorsa viene compromessa, è possibile accedervi immediatamente. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

* [Avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

* [È possibile fare riferimento alla pubblicazione del NIST: Guida ai programmi di test, formazione e esercizio per piani e funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati.

* [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi a Sentinel.

* [Come configurare l'esportazione continua](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Come trasmettere gli avvisi in Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

* [Come configurare l'automazione del flusso di lavoro e App per la logica](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Controllo di sicurezza: test di penetrazione ed esercizi Red Team](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Indicazioni**: * [seguire le regole di engagement Microsoft per assicurarsi che i propri test di penetrazione non violino i criteri Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [È possibile trovare altre informazioni sulla strategia Microsoft e sull'esecuzione dei test di penetrazione Red Teaming sull'infrastruttura cloud gestita da Microsoft, sui servizi e sulle applicazioni qui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
