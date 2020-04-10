---
title: Azure Security Baseline for Azure SQL Database
description: Azure Security Baseline for Azure SQL Database
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d51cf22d7be167501927e54ce159e0b732209b0d
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011379"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Azure Security Baseline for Azure SQL Database

La linea di base della sicurezza di Azure per il database SQL di Azure contiene suggerimenti che consentono di migliorare il livello di sicurezza della distribuzione.

La linea di base per questo servizio è ricavata da [Azure Security Benchmark versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce consigli su come proteggere le soluzioni cloud in Azure con le indicazioni sulle procedure consigliate.

Per altre informazioni, vedere [Panoramica delle linee di base](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)della sicurezza di Azure.For more information, see Azure Security Baselines overview .

## <a name="network-security"></a>Sicurezza di rete

*Per ulteriori informazioni, vedere [Controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale1.1: Protect resources using Network Security Groups or Azure Firewall on your Virtual Network

**Linee guida:** è possibile abilitare Collegamento privato di Azure per consentire l'accesso ai servizi di Azure PaaS (ad esempio, Database SQL) e ai servizi cliente/partner ospitati di Azure tramite un endpoint privato nella rete virtuale. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. 

Per consentire al traffico di raggiungere il database SQL di Azure, usare i tag di servizio SQL per consentire il traffico in uscita tramite gruppi di sicurezza di rete.

Le regole di rete virtuale consentono al database SQL di Azure di accettare solo le comunicazioni inviate da subnet specifiche all'interno di una rete virtuale.

Come configurare Private Link per il database SQL di Azure:How to set up Private Link for Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

Come usare gli endpoint e le regole del servizio di rete virtuale per i server di database:How to use virtual network service endpoints and rules for database servers:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Linee guida:** usare il Centro sicurezza di Azure e correggere i consigli di protezione della rete per la subnet in cui viene distribuito il server di database SQL di Azure.Guidance: Use Azure Security Center and remediate network protection recommendations for the subnet your Azure SQL Database Server is deployed to. 

Per le macchine virtuali di Azure che si connetteranno all'istanza del server di database SQL di Azure, abilitare i log di flusso del gruppo di sicurezza di rete per i gruppi di sicurezza di rete che proteggono tali macchine virtuali e invierà i log in un account di archiviazione di Azure per il controllo del traffico. 

È anche possibile inviare log di flusso del gruppo di sicurezza di rete a un'area di lavoro di Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso di traffico nel cloud di Azure.You may also send NSG flow logs to a Log Analytics workspace and use Traffic Analytics to provide insights into traffic flow in your Azure cloud. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare hot spot, identificare le minacce alla sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni errate della rete.

Abilitazione dei registri di flusso del gruppo di sicurezza di rete:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Informazioni sulla sicurezza di rete fornita dal Centro sicurezza di Azure:Understand Network Security provided by Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Come abilitare e usare Analisi del traffico:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Informazioni sulla sicurezza di rete fornita dal Centro sicurezza di Azure:Understand Network Security provided by Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteggere le applicazioni Web critiche

**Orientamento**: Non applicabile; questa raccomandazione è destinata al servizio App di Azure o alle risorse di calcolo che ospitano le applicazioni Web.This recommendation is intended for Azure Apps Service or compute resources hosting web applications.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Guida:** abilitare DDoS Protection Standard nelle reti virtuali associate alle istanze di SQL ServerSQL Server per le protezioni da attacchi d'account d'account distribuiti. Usare Azure Security Center Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP Internet dannosi o inutilizzati noti.

Come configurare la protezione DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Informazioni su Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Registrare i pacchetti di rete e i registri di flusso

**Linee guida:** per le macchine virtuali di Azure che si connetteranno all'istanza del database SQL di Azure, abilitare i log di flusso del gruppo di sicurezza di rete per i gruppi di sicurezza di rete che proteggono tali macchine virtuali e invia i log in un account di archiviazione di Azure per il controllo del traffico. Se necessario per analizzare l'attività anomala, abilitare l'acquisizione di pacchetti di Network Watcher.If required for investigating anomalous activity, enable Network Watcher packet capture.

Abilitazione dei registri di flusso del gruppo di sicurezza di rete:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Linee guida:** abilitare Advanced Threat Protection (ATP) per il database SQL di Azure.Guidance: Enable Advanced Threat Protection (ATP) for Azure SQL Database.  Gli utenti ricevono un avviso in caso di attività di database sospetta, potenziali vulnerabilità e attacchi SQL injection, nonché in caso di modelli di query e accesso ai database anomali. Advanced Threat Protection also integrates alerts with Azure Security Center. 

Comprendere e usare Advanced Threat Protection per il database SQL di Azure:Understand and using Advanced Threat Protection for Azure SQL Database:https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gestire il traffico verso le applicazioni web

**Orientamento**: Non applicabile; questa raccomandazione è destinata al servizio App di Azure o alle risorse di calcolo che ospitano le applicazioni Web.This recommendation is intended for Azure Apps Service or compute resources hosting web applications.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e l'overhead amministrativo delle regole di sicurezza di rete

**Linee guida:** usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure.Guidance: Use virtual network service tags to define network access controls on network security groups or Azure Firewall. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag del servizio (ad esempio, ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi degli indirizzi inclusi nel tag del servizio e aggiorna automaticamente il tag del servizio quando gli indirizzi cambiano.

Quando si usano gli endpoint del servizio per il database SQL di Azure, è necessario l'uscita verso gli indirizzi IP pubblici del database SQL di Azure: i gruppi di sicurezza di rete (NSG) devono essere aperti agli indirizzi IP del database SQL di Azure per consentire la connettività. A tale scopo, usare i tag del servizio NSG per il database SQL di Azure.You can do this by using NSG service tags for Azure SQL Database.

Informazioni sui tag di servizio con gli endpoint del servizio per il database SQL di Azure:Understand Service Tags with Service Endpoints for Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations

Comprendere e utilizzare i tag di servizio:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida:** definire e implementare le configurazioni di sicurezza di rete per le istanze del server di database SQL di Azure con Criteri di Azure.Guidance: Define and implement network security configurations for your Azure SQL Database server instances with Azure Policy. È possibile usare lo spazio dei nomi "Microsoft.Sql" per definire definizioni di criteri personalizzate oppure usare una delle definizioni dei criteri predefinite progettate per la protezione di rete del server di database SQL di Azure.You may use the "Microsoft.Sql" namespace to define custom policy definitions, or use any of the built-in policy definitions designed for Azure SQL Database server network protection. Un esempio di criteri di sicurezza di rete predefiniti applicabili per il server di database SQL di Azure potrebbe essere: "SQL Server deve usare un endpoint del servizio di rete virtuale".

 

Usare Blueprint di Azure per semplificare le distribuzioni di Azure su larga scala creando una creazione di pacchetti di elementi dell'ambiente chiave, ad esempio modelli di Gestione risorse di Azure, Controllo degli accessi in base al ruolo e criteri, in una singola definizione del blueprint. Applica facilmente il blueprint a nuove sottoscrizioni e ambienti e ottimizza il controllo e la gestione tramite il controllo delle versioni.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare un blueprint di Azure:How to create an Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regole di configurazione del traffico dei documenti

**Linee guida:** usare i tag per i gruppi di sicurezza di rete (NSG) e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le singole regole del gruppo di sicurezza di rete, utilizzare il campo "Descrizione" per specificare le esigenze e/o la durata dell'azienda (ecc.) per tutte le regole che consentono il traffico da/verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e relativo valore" per assicurarsi che tutte le risorse vengano create con i tag e per notificare le risorse senza tag esistenti.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai relativi tag.

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilizzare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze del server di database SQL di Azure.Guidance : Use Azure Activity Log to monitor network resource configurations and detect changes for network resources related to your Azure SQL Database server instances. Creare avvisi all'interno di Monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse di rete critiche.

Come visualizzare e recuperare gli eventi del log attività di Azure:How to view and retrieve Azure Activity Log events:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Come creare avvisi in Monitoraggio di Azure:How to create alerts in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizzare origini di sincronizzazione dell'ora approvate

**Linee guida:** Microsoft gestisce le origini ora per le risorse di Azure.Guidance: Microsoft maintains time sources for Azure resources. È possibile aggiornare la sincronizzazione dell'ora per le distribuzioni di calcolo.

Come configurare la sincronizzazione dell'ora per le risorse di calcolo di Azure:How to configure time synchronization for Azure compute resources:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurare la gestione dei registri di protezione centralizzati

**Linee guida:** abilitare il controllo per il database SQL di Azure per tenere traccia degli eventi del database e scriverli in un log di controllo nell'account di archiviazione di Azure, nell'area di lavoro log Analytics o negli hub eventi.

Inoltre, è possibile trasmettere i dati di telemetria di diagnostica SQL di Azure in Azure SQL Analytics, una soluzione cloud che monitora le prestazioni dei database SQL di Azure, dei pool elastici e delle istanze gestite su larga scala e tra più sottoscrizioni. Può aiutare a raccogliere e visualizzare le metriche sulle prestazioni del Database SQL di Azure e ha la funzionalità di intelligence integrata per la risoluzione dei problemi.

Come configurare il controllo per il database SQL di Azure:How to setup auditing for your Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

Come raccogliere i log e le metriche della piattaforma con Monitoraggio di Azure:How to collect platform logs and metrics with Azure Monitor:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging

Come trasmettere la diagnostica in Analisi SQL di Azure:How to stream diagnostics into Azure SQL Analytics:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Abilitare la registrazione di controllo per le risorse di Azure2.3: Abilitare la registrazione di controllo per le risorse di Azure2.

**Linee guida:** abilitare il controllo nell'istanza del server di database SQL di Azure e scegliere un percorso di archiviazione per i log di controllo (Archiviazione di Azure, Log Analytics o Hub eventi).

Come abilitare il controllo per SQL Server di Azure:How to enable auditing for Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Raccogliere i registri di sicurezza dai sistemi operativi

**Orientamento**: Non applicabile; questo benchmark è destinato alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dello spazio di archiviazione del registro di protezione

**Indicazioni:** quando si archiviano i log del database SQL di Azure in un'area di lavoro di Log Analytics, impostare il periodo di conservazione dei log in base alle normative di conformità dell'organizzazione.

Come impostare i parametri di conservazione dei log:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i registri

**Guida**: Analizzare e monitorare i log per verificare la ricerca di comportamenti anomali ed esaminare regolarmente i risultati. Usare Advanced Threat Protection del Centro sicurezza di Azure per avvisare le attività insolite correlate all'istanza del database SQL di Azure.Use Azure Security Center's Advanced Threat Protection to alert on unusual activity related to your Azure SQL Database instance. In alternativa, configurare gli avvisi in base ai valori di metrica o alle voci del log attività di Azure correlate alle istanze del database SQL di Azure.Alternatively, configure alerts based on Metric Values or Azure Activity Log entries related to your Azure SQL Database instances.

Informazioni su Advanced Threat Protection e avvisi per Azure SQL Server:Understand Advanced Threat Protection and alerting for Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Come configurare avvisi personalizzati per il database SQL di Azure:How to configure custom alerts for Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Abilitare gli avvisi per attività anomale

**Linee guida:** usare La protezione avanzata dalle minacce di Azure per i database SQL di Azure per il monitoraggio e gli avvisi sulle attività anomale. Abilitare la sicurezza avanzata dei dati per i database SQL. Advanced Data Security include funzionalità per l'individuazione e la classificazione di dati sensibili, la visualizzazione e la riduzione delle potenziali vulnerabilità del database e il rilevamento di attività anomale che potrebbero indicare una minaccia per il database.

Informazioni su Advanced Threat Protection e avvisi per il database SQL di Azure:Understand Advanced Threat Protection and alerting for Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Come abilitare la sicurezza avanzata dei dati per il database SQL di Azure:How to enable Advanced Data Security for Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

Come gestire gli avvisi nel Centro sicurezza di Azure:How to manage alerts in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizzare la registrazione antimalware

**Orientamento**: Non applicabile; per SQL Server di Azure, la soluzione antimalware è gestita da Microsoft sulla piattaforma sottostante.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: Abilitare la registrazione delle query DNS

**Orientamento**: Non applicabile; DNS logging is not applicable to Azure SQL Server.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: Abilitare la registrazione di controllo della riga di comando

**Orientamento**: Non applicabile; il controllo da riga di comando non è applicabile a Sql Server di Azure.command-line auditing is not applicable to Azure SQL Server.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per ulteriori informazioni, vedere [Controllo di sicurezza: controllo delle identità e degli accessi](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Mantenere un inventario degli account amministrativi

**Linee guida:** Azure Active Directory (AAD) dispone di ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere query. Utilizzare il modulo PowerShell di AAD per eseguire query ad hoc per individuare gli account membri dei gruppi amministrativi.

Come ottenere un ruolo di directory in Azure AD con PowerShell:How to get a directory role in Azure AD with PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Come ottenere i membri di un ruolo di directory in Azure AD con PowerShell:How to get members of a directory role in Azure AD with PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Modificare le password predefinite, ove applicabile

**Guida:** Azure Active Directory non ha il concetto di password predefinite. Quando si esegue il provisioning di un'istanza del database SQL di Azure, è consigliabile scegliere di integrare l'autenticazione con Azure Active Directory.When provisioning an Azure SQL Database instance, it is recommended that you choose to integrate authentication with Azure Active Directory.

Come configurare e gestire l'autenticazione di Azure Active Directory con SQL di Azure:How to configure and manage Azure Active Directory authentication with Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizzare account amministrativi dedicati

**Linee guida**: Creare criteri e procedure sull'utilizzo di account amministrativi dedicati. Usare Gestione identità e accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Informazioni sull'identità e l'accesso del Centro sicurezza di Azure:Understand Azure Security Center Identity and Access:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Usare Single Sign-On (SSO) con Azure Active Directory

**Orientamento**: Non applicabile; mentre è possibile configurare l'autenticazione di Azure Active Directory per l'integrazione con Azure SQL Server, Single Sign-On non è supportato.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Indicazioni:** abilitare Azure Active Directory (AAD) Multi-Factor Authentication (MFA) e seguire i suggerimenti per la gestione delle identità e degli accessi del Centro sicurezza di Azure.Guidance: Enable Azure Active Directory (AAD) Multi-Factor Authentication (MFA) and follow Azure Security Center Identity and Access Management recommendations.

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Come monitorare l'identità e l'accesso all'interno del Centro sicurezza di Azure:How to monitor identity and access within Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizzare macchine dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

**Guida:** usare una workstation con accesso privilegiato (PAW) con l'autenticazione a più fattori configurata per accedere alle risorse di Azure.Guidance : Use a Privileged Access Workstation (PAW) with Multi-Factor Authentication MFA configured to log into and configure Azure resources.

Ulteriori informazioni sulle workstation con accesso privilegiato:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registrare e avvisare su attività sospette da account amministrativi

**Indicazioni:** usare i report di sicurezza di Azure Active Directory per la generazione di log e avvisi quando si verificauna attività sospette o non sicure nell'ambiente.

Usare Advanced Threat Protection per il database SQL di Azure per rilevare attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Come identificare gli utenti di Azure AD contrassegnati per l'attività rischiosa:How to identify Azure AD users flagged for risky activity:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Come monitorare l'identità degli utenti e l'attività di accesso nel Centro sicurezza di Azure:How to monitor users identity and access activity in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

Esaminare Advanced Threat Protection e potenziali avvisi:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo da posizioni approvate

**Linee guida:** usare percorsi denominati con accesso condizionale per consentire l'accesso a Portal and Azure Resource Management solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche.

Come configurare i percorsi denominati in Azure:How to configure Named Locations in Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida:** creare un amministratore di Azure Active Directory (AAD) per le istanze del server di database SQL di Azure.Guidance : Create an Azure Active Directory (AAD) administrator for your Azure SQL Database server instances.

Come configurare e gestire l'autenticazione di Azure Active Directory con SQL di Azure:How to configure and manage Azure Active Directory authentication with Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

Come creare e configurare un'istanza di AAD:How to create and configure an AAD instance:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rivedere e riconciliare regolarmente l'accesso degli utenti

**Indicazioni:** Azure Active Directory (AAD) fornisce log che consentono di individuare gli account non aggiornati. Usare inoltre le verifiche di accesso di Azure Identity per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso degli utenti può essere esaminato regolarmente per assicurarsi che solo gli utenti giusti abbiano l'accesso continuo.

Come usare le recensioni di azure Identity Access:How to use Azure Identity Access Reviews:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorare i tentativi di accesso agli account disattivati

**Linee guida:** Configurare l'autenticazione di Azure Active Directory (AAD) con SQL di Azure e creare impostazioni di diagnostica per gli account utente di Azure Active Directory, inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics.Guidance: Configure Azure Active Directory (AAD) authentication with Azure SQL and create Diagnostic Settings for Azure Active Directory user accounts, sending the audit logs and sign-in logs to a Log Analytics workspace. Configurare gli avvisi desiderati nell'area di lavoro di Log Analytics.Configure desired Alerts within Log Analytics workspace.

Come configurare e gestire l'autenticazione di Azure Active Directory con SQL di Azure:How to configure and manage Azure Active Directory authentication with Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

Come integrare i log attività di Azure in Monitoraggio di Azure:How to integrate Azure Activity Logs into Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Scostamenti comportamento di avviso per l'accesso all'account

**Linee guida:** usare Azure Active Directory (AAD) Identity Protection e rilevamenti dei rischi per configurare le risposte automatiche alle azioni sospette rilevate relative alle identità utente. Inoltre, è possibile inserire dati in Azure Sentinel per ulteriori indagini.

Come visualizzare gli accessi ai rischi di Azure AD:How to view Azure AD risk sign-ins:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Come configurare e abilitare i criteri di rischio di Identity Protection:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida:** negli scenari di supporto in cui Microsoft deve accedere ai dati dei clienti, Azure Customer Lockbox offre ai clienti un'interfaccia per esaminare e approvare o rifiutare le richieste di accesso ai dati dei clienti.

Comprendere Il Customer Lockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="data-protection"></a>Protezione dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Mantenere un inventario di informazioni sensibili

**Linee guida:** usare i tag per tenere traccia delle risorse di Azure che archiviano o elaborano informazioni riservate.

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolare i sistemi che memorizzano o elaborano informazioni sensibili

**Linee guida:** Implementare sottoscrizioni e/o gruppi di gestione separati per lo sviluppo, il test e la produzione. Le risorse devono essere separate da Vnet/Subnet, contrassegnate in modo appropriato e protette all'interno di un server di rete o di un firewall di Azure.Resources should be separated by Vnet/Subnet, tagged appropriately, and secured within an NSG or Azure Firewall. Le risorse che archiviano o elaborano dati sensibili devono essere isolate. Utilizzare Private Link; distribuire Azure SQL Server all'interno della rete virtuale e connettersi privatamente usando endpoint privati.

Come creare sottoscrizioni di Azure aggiuntive:How to create additional Azure subscriptions:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare i gruppi di gestione:

https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Come configurare Private Link per il database SQL di Azure:How to set up Private Link for Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorare e bloccare il trasferimento non autorizzato di informazioni sensibili

**Linee guida:** per i database SQL di Azure che archiviano o elaborano informazioni riservate, contrassegnare il database e le risorse correlate come riservate tramite tag. Configurare Private Link insieme ai tag del servizio del gruppo di sicurezza di rete nelle istanze del database SQL di Azure per impedire l'esfiltrazione di informazioni riservate.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.

Come configurare Private Link e NSG per impedire l'esfiltrazione dei dati nelle istanze del database SQL di Azure:How to configure Private Link and NSGs to prevent data exfiltration on your Azure SQL Database instances:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Crittografare tutte le informazioni sensibili in transito

**Linee guida:** il database SQL di Azure protegge i dati crittografando i dati in movimento con Transport Layer Security.Guidance: Azure SQL Database secures your data by encrypting data in motion with Transport Layer Security. SQL Server applica la crittografia (SSL/TLS) in ogni momento per tutte le connessioni. In questo modo tutti i dati vengono crittografati "in transito" tra il client e il server indipendentemente dall'impostazione di Encrypt o TrustServerCertificate nella stringa di connessione.

Informazioni sulla crittografia SQL di Azure in transito:Understand Azure SQL Encryption in Transit:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilizzare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni:** usare la funzionalità di individuazione e classificazione dei dati del database SQL di Azure.Guidance : Use the Azure SQL Database data discovery and classification feature. L'individuazione e la classificazione dei dati offrono funzionalità avanzate &amp; integrate nel database SQL di Azure per l'individuazione, la classificazione e l'etichettatura della protezione dei dati sensibili nei database.

Come usare l'individuazione e la classificazione dei dati per SQL Server di Azure:How to use data discovery and classification for Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse4.6: Usare il controllo degli accessi in base al ruolo di

**Linee guida:** usare Azure Active Directory (AAD) per autenticare e controllare l'accesso alle istanze del database SQL di Azure.Guidance : Use Azure Active Directory (AAD) for authenticating and controlling access to Azure SQL Database instances.

Come integrare Azure SQL Server con Azure Active Directory per l'autenticazione:How to integrate Azure SQL Server with Azure Active Directory for authentication:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication

Come controllare l'accesso in SQL Server di Azure:How to control access in Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizzare la prevenzione della perdita di dati basata su host per applicare il controllo degli accessi

**Linee guida:** Microsoft gestisce l'infrastruttura sottostante per il database SQL di Azure e ha implementato controlli rigorosi per prevenire la perdita o l'esposizione dei dati dei clienti.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografare le informazioni sensibili inattivi

**Linee guida:** la crittografia dei dati trasparente (TDE) consente di proteggere il database SQL di Azure, l'istanza gestita di SQL di Azure e Il data warehouse di Azure dalla minaccia di attività offline dannose crittografando i dati inattivi. Esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni inattivi, senza richiedere modifiche dell'applicazione. Per impostazione predefinita, la tecnologia TDE è abilitata per tutti i database SQL di Azure appena distribuiti. La chiave di crittografia TDE può essere gestita da Microsoft o dal cliente.

Come gestire la crittografia dei dati trasparente e utilizzare le proprie chiavi di crittografia:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e avvisare le modifiche alle risorse critiche di Azure4.9: Log and alert on changes to critical Azure resources

**Linee guida:** usare Monitoraggio di Azure con il log attività di Azure per creare avvisi per quando vengono apportate modifiche alle istanze di produzione dei database SQL di Azure e ad altre risorse critiche o correlate.

Come creare avvisi per gli eventi del log attività di Azure:How to create alerts for Azure Activity Log events:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per ulteriori informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Eseguire strumenti automatici di scansione delle vulnerabilità

**Linee guida:** abilitare la sicurezza avanzata dei dati per il database SQL di Azure e seguire i consigli del Centro sicurezza di Azure per l'esecuzione di valutazioni delle vulnerabilità nei server SQL di Azure.Guidance: Enable Advanced Data Security for Azure SQL Database and follow recommendations from Azure Security Center on performing vulnerability assessments on your Azure SQL Servers.

Come eseguire valutazioni delle vulnerabilità nei database SQL di Azure:How to run vulnerability assessments on your Azure SQL Databases:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment

Come abilitare Advanced Data Security:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

Come implementare i consigli per la valutazione della vulnerabilità del Centro sicurezza di Azure:How to implement Azure Security Center vulnerability assessment recommendations:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuire una soluzione di gestione automatica delle patch del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuire una soluzione di gestione automatica delle patch software di terze parti

**Orientamento**: Non applicabile; questo benchmark è destinato alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Confrontare le scansioni delle vulnerabilità back-to-back

**Linee guida**: Abilitare analisi periodiche periodiche per le istanze del database SQL di Azure.Guidance : Enable periodic recurring scans for your Azure SQL Database instances; questo configurerà una valutazione della vulnerabilità per eseguire automaticamente un'analisi sul database una volta alla settimana. Verrà inviato un riepilogo dei risultati di analisi agli indirizzi di posta elettronica forniti. Confrontare i risultati per verificare che le vulnerabilità siano state corretti.

Come esportare un report di valutazione della vulnerabilità nel Centro sicurezza di Azure:How to export a vulnerability assessment report in Azure Security Center:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilizzare un processo di valutazione del rischio per assegnare priorità alla correzione delle vulnerabilità individuate

**Indicazioni:** usare le classificazioni dei rischi predefinite (Secure Score) fornite dal Centro sicurezza di Azure.Guidance: Use the default risk ratings (Secure Score) provided by Azure Security Center.

Informazioni sul punteggio sicuro del Centro sicurezza di Azure:Understand Azure Security Center Secure Score:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per ulteriori informazioni, vedere [Controllo di sicurezza: inventario e gestione delle risorse](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Usare Azure Asset Discovery

**Indicazioni:** usare Azure Resource Graph per eseguire query e individuare tutte le risorse (incluse le istanze di SQL Server di Azure) all'interno delle sottoscrizioni.  Assicurarsi di disporre delle autorizzazioni appropriate (di lettura) nel tenant e di essere in grado di enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Anche se le risorse di Azure classiche possono essere individuate tramite Resource Graph, è consigliabile creare e usare le risorse di Azure Resource Manager in futuro.

Come creare query con Azure Graph:How to create queries with Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Come visualizzare le sottoscrizioni di Azure:How to view your Azure Subscriptions:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Informazioni sul controllo degli accessi in base al ruolo di Azure:Understandhttps://docs.microsoft.com/azure/role-based-access-control/overview


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Gestire i metadati delle risorse

**Linee guida:** applicare tag alle risorse di Azure che assiecano metadati per organizzarli logicamente in una tassonomia.

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminare risorse di Azure non autorizzate

**Linee guida**: utilizzare l'assegnazione di tag, gruppi di gestione e sottoscrizioni separate, se appropriato, per organizzare e tenere traccia delle risorse. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate dalla sottoscrizione in modo tempestivo.

Come creare sottoscrizioni di Azure aggiuntive:How to create additional Azure subscriptions:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare i gruppi di gestione:

https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Gestire un inventario delle risorse di Azure approvate e dei titoli software

**Linee guida:** definire un elenco delle risorse di Azure approvate e del software approvato per le risorse di calcolo

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorare le risorse di Azure non approvate

**Linee guida:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:Guidance : Use Azure Policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query/individuare risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare query con Azure Graph:How to create queries with Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorare le applicazioni software non approvate all'interno delle risorse di calcolo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Rimuovere le risorse e le applicazioni software di Azure non approvate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="68-use-only-approved-applications"></a>6.8: Utilizzare solo applicazioni approvate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="69-use-only-approved-azure-services"></a>6.9: Usare solo i servizi di Azure approvati

**Indicazioni:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:Guidance: Use Azure Policy to place restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query/individuare risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come negare un tipo di risorsa specifico con Criteri di Azure:How to deny a specific resource type with Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementare l'elenco delle applicazioni approvate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle applicazioni in esecuzione su risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resources Manager tramite script

**Linee guida:** usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app "Gestione di Microsoft Azure".

Come configurare l'accesso condizionale per bloccare l'accesso a Azure Resource Manager:How to configure Conditional Access to block access to Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitare la capacità degli utenti di eseguire script all'interno di risorse di calcolo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separare fisicamente o logicamente le applicazioni ad alto rischio

**Orientamento**: Non applicabile; questo consiglio è destinato al servizio app o alle risorse di calcolo che ospitano applicazioni desktop o Web.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per ulteriori informazioni, vedere [Controllo di sicurezza: configurazione protetta](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Stabilire configurazioni sicure per tutte le risorse di Azure7.1: Establish secure configurations for all Azure resources

**Linee guida:** usare Criteri di Azure o consigli del Centro sicurezza di Azure per Azure SQL Server/Database per gestire le configurazioni di sicurezza per tutte le risorse di Azure.Guidance: Use Azure Policy or Azure Security Center recommendations for Azure SQL Servers/Databases to maintain security configurations for all Azure Resources.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Stabilire configurazioni sicure del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Gestire le configurazioni delle risorse di Azure protette

**Linee guida:** usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure tra le risorse di Azure.Guidance: Use Azure policy [deny] and [deploy if not exist] to enforce secure settings across your Azure resources.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Informazioni sugli effetti dei criteri di Azure:Understand Azure Policy Effects:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Mantenere le configurazioni sicure del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Archiviare in modo sicuro la configurazione delle risorse di Azure7.5: Securely store configuration of Azure resources

**Indicazioni:** se si usano definizioni di criteri di Azure personalizzate, usare DevOps di Azure o Azure Repos per archiviare e gestire il codice in modo sicuro.

Come archiviare il codice in DevOps di Azure:How to store code in Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos Documentation:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Memorizzare in modo sicuro immagini personalizzate del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida:** usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.SQL" per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Inoltre, sviluppare un processo e una pipeline per la gestione delle eccezioni dei criteri.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementare il monitoraggio automatico della configurazione per i servizi di Azure7.9: Implement automated configuration monitoring for Azure services

**Linee guida:** Sfruttare il Centro sicurezza di Azure per eseguire analisi di base per i server e i database SQL di Azure.Guidance: Leverage Azure Security Center to perform baseline scans for your Azure SQL Servers and Databases.

Come correggere i consigli nel Centro sicurezza di Azure:How to remediate recommendations in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementare il monitoraggio automatico della configurazione per i sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gestire i segreti di Azure in modo sicuro

**Indicazioni:** usare L'insieme di credenziali delle chiavi di Azure per archiviare le chiavi di crittografia per La crittografia dati trasparente del database SQL (TDE, Data Security) del database SQL di Azure.Guidance : Use Azure Key Vault to store encryption keys for Azure SQL Database Transparent Data Encryption (TDE).

Come proteggere i dati sensibili archiviati in Azure SQL Server e archiviare le chiavi di crittografia in Archiviazione delle chiavi di Azure:How to protect sensitive data being stored in Azure SQL Server and store the encryption keys in Azure Key Vault:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gestire le identità in modo sicuro e automatico

**Linee guida:** usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (AAD). Managed Identities consente di eseguire l'autenticazione a qualsiasi servizio che supporta l'autenticazione AAD, incluso l'insieme di credenziali delle chiavi di Azure, senza credenziali nel codice.

Esercitazione: Usare un'identità gestita assegnata dal sistema di macchine virtuali Windows per accedere a SQL di Azure:Tutorial: Use a Windows VM system-assigned managed identity to access Azure SQL:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql

Come configurare le identità gestite:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminare l'esposizione di credenziali indesiderate

**Linee guida**: Implementare Credential Scanner per identificare le credenziali all'interno del codice. Credential Scanner will also encourage moving discovered credentials to more secure locations such as Azure Key Vault. 

Come configurare Credential Scanner:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="malware-defense"></a>Difesa da malware

*Per ulteriori informazioni, vedere [Controllo di sicurezza: Difesa da malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilizzare software antimalware gestito centralmente

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo. Microsoft gestisce l'antimalware per la piattaforma sottostante.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: File pre-scansione da caricare in risorse di Azure non di calcolo

**Linee guida:** l'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, servizio app di Azure), tuttavia non viene eseguito sul contenuto del cliente.

Pre-scan qualsiasi contenuto caricato in risorse di Azure non di calcolo, ad esempio servizio app, archiviazione Data Lake, archiviazione BLOB, SQL Server di Azure e così via. Microsoft non può accedere ai dati in questi casi.

Informazioni su Microsoft Antimalware per i servizi cloud di Azure e le macchine virtuali:Understand Microsoft Antimalware for Azure Cloud Services and Virtual Machines:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Verificare che il software antimalware e le firme siano aggiornate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo. Microsoft gestisce l'antimalware per la piattaforma sottostante.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="data-recovery"></a>Ripristino dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatici regolari

**Linee guida:** per proteggere l'azienda dalla perdita di dati, il database SQL di Azure crea automaticamente backup completi del database settimanali, backup differenziali del database ogni 12 ore e backup del log delle transazioni ogni 5 - 10 minuti. I backup vengono archiviati nell'archivio RA-GRS per almeno 7 giorni per tutti i livelli di servizio. Tutti i livelli di servizio, ad eccezione del supporto Basic, supportano il periodo di conservazione dei backup configurabili per il ripristino temporizzato, fino a 35 giorni.

Per soddisfare diversi requisiti di conformità, è possibile selezionare periodi di conservazione diversi per i backup settimanali, mensili e/o annuali. L'utilizzo delle risorse di archiviazione dipende dalla frequenza selezionata con cui vengono eseguiti backup e dal periodo di conservazione.

Comprendere i backup e la continuità aziendale con SQL Server di Azure:Understand backups and business continuity with Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** il database SQL di Azure crea automaticamente i backup del database che vengono mantenuti tra 7 e 35 giorni e usa l'archiviazione con ridondanza geografica (RA-GRS) di accesso in lettura di Azure per garantire che vengano mantenuti anche se il data center non è disponibile. Questi backup vengono creati automaticamente. Se necessario, abilitare i backup con ridondanza geografica a lungo termine per i database SQL di Azure.If required, enable long-term geo-redundant backups for your Azure SQL Databases.

Se si utilizzano chiavi gestite dal cliente per Transparent Data Encryption, assicurarsi che venga eseguito il backup delle chiavi.

Comprendere i backup in Azure SQL Server:Understand backups in Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database

Come eseguire il backup delle chiavi dell'insieme di credenziali in Azure:How to backup key vault keys in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida:** verificare la possibilità di eseguire periodicamente il ripristino dei dati all'interno di Backup di Azure.Guidance : Ensure ability to periodically perform data restoration of content within Azure Backup. Se necessario, testare il ripristino del contenuto in una VLAN isolata. Testare il ripristino delle chiavi gestite dal cliente di cui è stato eseguito il backup.

Come ripristinare le chiavi dell'insieme di credenziali delle chiavi in Azure:How to restore key vault keys in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

Come ripristinare i backup del database SQL di Azure usando il ripristino temporizzato:How to recover Azure SQL Database backups using point-in-time restore:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dai clienti

**Guida:** abilitare l'eliminazione temporanea nell'insieme di credenziali delle chiavi di Azure per proteggere le chiavi da eliminazioni accidentali o dannose.

Come abilitare l'eliminazione temporanea nell'insieme di credenziali delle chiavi:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Creare una guida alla risposta agli incidenti

**Linee guida**: Assicurarsi che siano presenti piani di risposta agli incidenti scritti che definiscono i ruoli del personale e le fasi della gestione/gestione degli incidenti.

Come configurare le automazioni del flusso di lavoro all'interno del Centro sicurezza di Azure:How to configure Workflow Automations within Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Creare una procedura di assegnazione dei punteggi degli eventi imprevisti e di assegnazione delle priorità

**Guida:** il Centro sicurezza assegna un livello di gravità agli avvisi, per consentire di assegnare una priorità all'ordine di assistenza a ogni avviso, in modo che quando una risorsa viene compromessa, è possibile raggiungerlo immediatamente. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sull'analita utilizzato per emettere l'avviso, nonché il livello di probabilità che vi sia stato un intento dannoso dietro l'attività che ha portato all'avviso.

Security alerts in Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-alerts-overview



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Testare le procedure di risposta alla sicurezza

**Linee guida**: Eseguire esercizi per testare le capacità di risposta agli incidenti dei sistemi a una cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

È possibile fare riferimento alla pubblicazione del NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities(A noto)

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornire i dettagli di contatto degli eventi imprevisti di sicurezza e configurare le notifiche di avviso per gli incidenti di sicurezza

**Linee guida:** le informazioni di contatto relative agli eventi imprevisti di protezione verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che i dati sono stati utilizzati da una parte illegale o non autorizzata.

Come impostare il contatto di sicurezza del centro di sicurezza di Azure:How to set the Azure Security Center Security Contact:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporare avvisi di sicurezza nel sistema di risposta agli incidenti

**Indicazioni:** esportare gli avvisi e i suggerimenti del Centro sicurezza di Azure usando la funzionalità di esportazione continua. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi a Sentinel.You may use the Azure Security Center data connector to stream the alerts to Sentinel.

Come configurare l'esportazione continua:

https://docs.microsoft.com/azure/security-center/continuous-export

Come trasmettere gli avvisi in Azure Sentinel:How to stream alerts into Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizzare la risposta agli avvisi di sicurezza

**Indicazioni:** usare la funzionalità automazione flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite "App per la logica" in avvisi e suggerimenti per la sicurezza.

Come configurare l'automazione del flusso di lavoro e le app per la logica:How to configure Workflow Automation and Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per ulteriori informazioni, vedere Controllo di sicurezza: test di [penetrazione ed Esercizi del team rosso](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza entro 60 giorni

**Linee guida**: Seguire le regole di coinvolgimento Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Ulteriori informazioni sulla strategia di Microsoft e sull'esecuzione di Red Teaming e sui test di penetrazione dei siti in tempo reale su infrastruttura cloud, servizi e applicazioni gestiti Microsoft sono disponibili qui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere il benchmark di sicurezza di [AzureSee](https://docs.microsoft.com/azure/security/benchmarks/overview) the Azure Security Benchmark
- Altre informazioni sulle linee di base per la sicurezza di AzureLearn more about [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
