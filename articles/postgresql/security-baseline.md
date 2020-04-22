---
title: Azure Security Baseline for Azure Database for PostgreSQL Single Server
description: Azure Security Baseline for Azure Database for PostgreSQL Single Server
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: dfe1bd1cbc99838f427aaba5bc29b23f8bfcbaff
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758669"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql-single-server"></a>Azure Security Baseline for Azure Database for PostgreSQL Single Server

La linea di base della sicurezza di Azure per il database di Azure per PostgreSQL Single Server contiene suggerimenti che consentono di migliorare il livello di sicurezza della distribuzione.

La linea di base per questo servizio è ricavata da [Azure Security Benchmark versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce consigli su come proteggere le soluzioni cloud in Azure con le indicazioni sulle procedure consigliate.

Per altre informazioni, vedere [Panoramica delle linee di base](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)della sicurezza di Azure.For more information, see Azure Security Baselines overview .

## <a name="network-security"></a>Sicurezza di rete

*Per ulteriori informazioni, vedere [Controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale1.1: Protect resources using Network Security Groups or Azure Firewall on your Virtual Network

**Guida:** configurare Private Link per il database di Azure per PostgreSQL con endpoint privati. Il servizio Collegamento privato consente di connettersi a diversi servizi PaaS in Azure tramite un endpoint privato. Azure Private Link porta essenzialmente i servizi di Azure all'interno della rete virtuale privata (VNet). Il traffico tra la rete virtuale e l'istanza PostgreSQL viaggia nella rete backbone Microsoft.Traffic between your virtual network and PostgreSQL instance travels the Microsoft backbone network.

In alternativa, è possibile usare gli endpoint del servizio di rete virtuale per proteggere e limitare l'accesso alla rete al database di Azure per le implementazioni PostgreSQL.Alternatively, you may use Virtual Network Service Endpoints to protect and limit network access to your Azure Database for PostgreSQL implementations. Le regole di rete virtuale rappresentano una funzionalità di sicurezza del firewall che consente di definire se il server di Database di Azure per PostgreSQL accetta le comunicazioni inviate da subnet specifiche nelle reti virtuali.

È anche possibile proteggere il database di Azure per il server PostgreSQL con le regole del firewall. Il firewall del server impedisce l'accesso al server di database fino a quando non si specificano i computer autorizzati. Per configurare il firewall, creare regole del firewall che specificano gli intervalli di indirizzi IP accettabili. È possibile creare regole firewall a livello di server.

Come configurare Private Link per il database di Azure per PostgreSQL:How to configure Private Link for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Come creare e gestire gli endpoint del servizio VNet e le regole della rete virtuale nel database di Azure per PostgreSQL:How to create and manage VNet service endpoints and VNet rules in Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-manage-vnet-using-portal

Come configurare il database di Azure per le regole del firewall PostgreSQL:How to configure Azure Database for PostgreSQL firewall rules:https://docs.microsoft.com/azure/postgresql/howto-manage-firewall-using-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Indicazioni:** quando l'istanza del database di Azure per PostgreSQL è protetta in un endpoint privato, è possibile distribuire macchine virtuali nella stessa rete virtuale. È possibile utilizzare un gruppo di sicurezza di rete (NSG) per ridurre il rischio di esfiltrazione dei dati. Abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione per il controllo del traffico. È anche possibile inviare log di flusso del gruppo di sicurezza di rete a un'area di lavoro di Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso di traffico nel cloud di Azure.You may also send NSG flow logs to a Log Analytics workspace and use Traffic Analytics to provide insights into traffic flow in your Azure cloud. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare hot spot, identificare le minacce alla sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni errate della rete.

Come configurare Private Link per il database di Azure per PostgreSQL:How to configure Private Link for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Abilitazione dei registri di flusso del gruppo di sicurezza di rete:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare e usare Analisi del traffico:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteggere le applicazioni Web critiche

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle applicazioni Web in esecuzione nel servizio app di Azure o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida:** usare Advanced Threat Protection for Azure Database for PostgreSQL.Guidance : Use Advanced Threat Protection for Azure Database for PostgreSQL. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Abilitare DDoS Protection Standard nelle reti virtuali associate al database di Azure per le istanze PostgreSQL per proteggersi dagli attacchi DDoS.Enable DDoS Protection Standard on the virtual networks associated with your Azure Database for PostgreSQL instances to protection against DDoS attacks. Usare Azure Security Center Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP Internet dannosi o inutilizzati noti.

Come configurare Advanced Threat Protection per il database di Azure per PostgreSQL:How to configure Advanced Threat Protection for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Come configurare la protezione DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Registrare i pacchetti di rete e i registri di flusso

**Indicazioni:** quando l'istanza del database di Azure per PostgreSQL è protetta in un endpoint privato, è possibile distribuire macchine virtuali nella stessa rete virtuale. È quindi possibile configurare un gruppo di sicurezza di rete (NSG) per ridurre il rischio di esfiltrazione dei dati. Abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione per il controllo del traffico. È anche possibile inviare log di flusso del gruppo di sicurezza di rete a un'area di lavoro di Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso di traffico nel cloud di Azure.You may also send NSG flow logs to a Log Analytics workspace and use Traffic Analytics to provide insights into traffic flow in your Azure cloud. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare hot spot, identificare le minacce alla sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni errate della rete.

Abilitazione dei registri di flusso del gruppo di sicurezza di rete:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare e usare Analisi del traffico:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Linee guida:** usare Advanced Threat Protection for Azure Database for PostgreSQL.Guidance : Use Advanced Threat Protection for Azure Database for PostgreSQL. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Come configurare Advanced Threat Protection per il database di Azure per PostgreSQL:How to configure Advanced Threat Protection for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gestire il traffico verso le applicazioni web

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle applicazioni Web in esecuzione nel servizio app di Azure o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e l'overhead amministrativo delle regole di sicurezza di rete

**Indicazioni:** per le risorse che devono accedere al database di Azure per le istanze PostgreSQL, usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure.Guidance : For resources that need access to your Azure Database for PostgreSQL instances, use virtual network service tags to define network access controls on network security groups or Azure Firewall. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag del servizio (ad esempio, SQL. WestU) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi degli indirizzi inclusi nel tag del servizio e aggiorna automaticamente il tag del servizio quando gli indirizzi cambiano.

Nota: il database di Azure per PostgreSQL usa il tag di servizio "Microsoft.Sql".

Per altre informazioni sull'uso dei tag di servizio:For more information about using service tags:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Comprendere l'utilizzo dei tag di servizio per il database di Azure per PostgreSQL:Understand service tag usage for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet#terminology-and-description

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida:** definire e implementare configurazioni di sicurezza standard per le impostazioni di rete e le risorse di rete associate al database di Azure per le istanze PostgreSQL con Criteri di Azure.Guidance: Define and implement standard security configurations for network settings and network resources associated with your Azure Database for PostgreSQL instances with Azure Policy. Usare gli alias dei criteri di Azure negli spazi dei nomi "Microsoft.DBforPostgreSQL" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete del database di Azure per le istanze PostgreSQL.Use Azure Policy aliases in the "Microsoft.DBforPostgreSQL" and "Microsoft.Network" namespaces to create custom policies to audit or enforce the network configuration of your Azure Database for PostgreSQL instances. È anche possibile usare le definizioni dei criteri predefinite relative alla rete o al database di Azure per le istanze PostgreSQL, ad esempio:You may also make use of built-in policy definitions related to networking or your Azure Database for PostgreSQL instances, such as:

- Lo standard di protezione DDoS deve essere abilitato

- Il criterio Imponi connessione SSL deve essere abilitato per i server di database PostgreSQL

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Esempi di criteri di Azure per la rete:Azure Policy samples for networking:https://docs.microsoft.com/azure/governance/policy/samples/

Come creare un blueprint di Azure:How to create an Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regole di configurazione del traffico dei documenti

**Linee guida:** usare i tag per le risorse correlate alla sicurezza di rete e al flusso di traffico per il database di Azure per le istanze PostgreSQL per fornire metadati e organizzazione logica.

Usare una delle definizioni dei criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e relativo valore", per assicurarsi che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai relativi tag.

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilizzare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate al database di Azure per le istanze PostgreSQL.Guidance : Use Azure Activity Log to monitor network resource configurations and detect changes for network resources related to your Azure Database for PostgreSQL instances. Creare avvisi all'interno di Monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse di rete critiche.

Come visualizzare e recuperare gli eventi del log attività di Azure:How to view and retrieve Azure Activity Log events:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Come creare avvisi in Monitoraggio di Azure:How to create alerts in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizzare origini di sincronizzazione dell'ora approvate

**Indicazioni:** Microsoft gestisce l'origine ora usata per le risorse di Azure, ad esempio Database di Azure per PostgreSQL per i timestamp nei log.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurare la gestione dei registri di protezione centralizzati

**Indicazioni:** abilitare le impostazioni di diagnostica e i log del server e inserire i log per aggregare i dati di sicurezza generati dal database di Azure per le istanze PostgreSQL.Guidance: Enable Diagnostic Settings and Server Logs and ingest logs to aggregate security data generated by your Azure Database for PostgreSQL instances. In Monitoraggio di Azure usare Log Analytics Workspace(s) per eseguire query ed eseguire analisi e gli account di archiviazione di Azure per l'archiviazione a lungo termine/archiviazione. In alternativa, è possibile abilitare e on-board i dati di Azure Sentinel o un SIEM di terze parti.

Come configurare e accedere ai log del server per il database di Azure per PostgreSQL:How to configure and access Server Logs for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Come configurare e accedere ai log di controllo per il database di Azure per PostgreSQL:How to configure and access audit logs for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-audit

Come eseguire l'onboarding di Azure Sentinel:How to onboard Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Abilitare la registrazione di controllo per le risorse di Azure2.3: Abilitare la registrazione di controllo per le risorse di Azure2.

**Guida:** abilitare le impostazioni di diagnostica nel database di Azure per le istanze PostgreSQL per l'accesso ai log di controllo, sicurezza e diagnostica. Assicurarsi di abilitare in modo specifico il registro di controllo PostgreSQL. I log attività, che sono automaticamente disponibili, includono l'origine dell'evento, la data, l'utente, il timestamp, gli indirizzi di origine, gli indirizzi di destinazione e altri elementi utili. È anche possibile abilitare impostazioni di diagnostica log attività di Azure e inviare i log alla stessa area di lavoro Log Analytics o account di archiviazione.

Come configurare e accedere ai log del server per il database di Azure per PostgreSQL:How to configure and access Server Logs for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Come configurare e accedere ai log di controllo per il database di Azure per PostgreSQL:How to configure and access audit logs for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-audit

Come configurare le impostazioni di diagnostica per il log attività di Azure:How to configure Diagnostic Settings for the Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Raccogliere i registri di sicurezza dai sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dello spazio di archiviazione del registro di protezione

**Linee guida:** in Monitoraggio di Azure, per l'area di lavoro di Log Analytics usata per contenere il database di Azure per i log PostgreSQL, impostare il periodo di conservazione in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archiviazione.

Come impostare i parametri di conservazione dei log per le aree di lavoro di Log Analytics:How to set log retention parameters for Log Analytics Workspaces:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

Archiviare i log delle risorse in un account di archiviazione di Azure:Storage resource logs in an Azure Storage Account:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i registri

**Linee guida:** analizzare e monitorare i log dal database di Azure per le istanze PostgreSQL per un comportamento anomalo. Usare Analisi dei log di Monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log. In alternativa, è possibile abilitare e on-board i dati di Azure Sentinel o di terze parti SIEM.

Come eseguire l'onboarding di Azure Sentinel:How to onboard Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Per altre informazioni su Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Come eseguire query personalizzate in Monitoraggio di Azure:How to perform custom queries in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Abilitare gli avvisi per attività anomale

**Guida:** abilitare Advanced Threat Protection per il database di Azure per PostgreSQL.Guidance: Enable Advanced Threat Protection for Azure Database for PostgreSQL. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Inoltre, è possibile abilitare i registri del server e le impostazioni di diagnostica per PostgreSQL e inviare i log a un'area di lavoro di Log Analytics.In addition, you may enable Server Logs and Diagnostic Settings for PostgreSQL and send logs to a Log Analytics Workspace. Eseguire l'onboarding dell'area di lavoro di Log Analytics in Azure Sentinel in quanto fornisce una soluzione SOAR (Security Orchestration Automated Response). Ciò consente di creare e utilizzare playbook (soluzioni automatizzate) per risolvere i problemi di sicurezza.

Come abilitare Advanced Threat Protection per il database di Azure per PostgreSQL:How to enable Advanced Threat Protection for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Come configurare e accedere ai log del server per il database di Azure per PostgreSQL:How to configure and access Server Logs for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Come configurare e accedere ai log di controllo per il database di Azure per PostgreSQL:How to configure and access audit logs for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-audit

Come configurare le impostazioni di diagnostica per il log attività di Azure:How to configure Diagnostic Settings for the Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Come eseguire l'onboarding di Azure Sentinel:How to onboard Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizzare la registrazione antimalware

**Orientamento**: Non applicabile; Il database di Azure per PostgreSQL non elabora né produce log correlati all'antimalware.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: Abilitare la registrazione delle query DNS

**Orientamento**: Non applicabile; Il database di Azure per PostgreSQL non elabora né produce log correlati al DNS.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: Abilitare la registrazione di controllo della riga di comando

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per ulteriori informazioni, vedere [Controllo di sicurezza: controllo delle identità e degli accessi](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Mantenere un inventario degli account amministrativi

**Linee guida**: Gestire un inventario degli account utente che dispongono dell'accesso amministrativo al piano di controllo (ad esempio il portale di Azure) del database di Azure per le istanze PostgreSQL.Guidance : Maintain an inventory of the user accounts that have administrative access to the control plane (e.g. Azure portal) of your Azure Database for PostgreSQL instances. Inoltre, gestire un inventario degli account amministrativi che hanno accesso al piano dati (all'interno del database stesso) del database di Azure per le istanze PostgreSQL. (Quando si crea il server PostgreSQL, si forniscono le credenziali per un utente amministratore. Questo amministratore può essere utilizzato per creare altri utenti PostgreSQL.)

Il database di Azure per PostgreSQL non supporta il controllo degli accessi in base al ruolo incorporato, ma è possibile creare ruoli personalizzati in base a operazioni specifiche del provider di risorse.

Comprendere i ruoli personalizzati per la sottoscrizione di Azure:Understand custom roles for Azure subscription:https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

Informazioni sul database di Azure per le operazioni del provider di risorse PostgreSQL:Understand Azure Database for PostgreSQL resource provider operations:https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbforpostgresql 

Informazioni sulla gestione degli accessi per Il database di Azure per PostgreSQL:Understand access management for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-security#access-management

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Modificare le password predefinite, ove applicabile

**Indicazioni:** Azure Active Directory e Azure Database per PostgreSQL non hanno il concetto di password predefinite.

Al momento della creazione del database di Azure per la risorsa PostgreSQL stessa, Azure forza la creazione di un utente amministrativo con una password complessa. Tuttavia, una volta creata l'istanza PostgreSQL, è possibile utilizzare il primo account di amministratore del server creato account per creare altri utenti e concedere loro l'accesso amministrativo. Quando si creano questi account, assicurarsi di configurare una password complessa diversa per ogni account.

Come creare account aggiuntivi per il database di Azure per PostgreSQL:How to create additional accounts for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-create-users

Come aggiornare la password di amministratore:https://docs.microsoft.com/azure/postgresql/howto-create-manage-server-portal#update-admin-password

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizzare account amministrativi dedicati

**Linee guida:** creare procedure operative standard per l'uso di account amministrativi dedicati che hanno accesso al database di Azure per le istanze PostgreSQL.Guidance : Create standard operating procedures around the use of dedicated administrative accounts that have access to your Azure Database for PostgreSQL instances. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi. 

Informazioni sull'identità e l'accesso del Centro sicurezza di Azure:Understand Azure Security Center Identity and Access:https://docs.microsoft.com/azure/security-center/security-center-identity-access 

Informazioni su come creare utenti amministratori nel database di Azure per PostgreSQL:Understand how to create admin users in Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-create-users#the-server-admin-account


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida:** l'accesso al database di Azure per PostgreSQL è supportato sia l'utilizzo di nome utente/password configurato direttamente nel database, sia l'utilizzo di un'identità di Azure Active Directory (AD) e l'utilizzo di un token di Azure AD per la connessione. Quando si usa un token di Azure AD, sono supportati metodi diversi, ad esempio un utente di Azure AD, un gruppo di Azure AD o un'applicazione Azure AD che si connette al database.

Separatamente, l'accesso al piano di controllo per PostgreSQL è disponibile tramite l'API REST e supporta SSO. To authenticate, set the Authorization header for your requests to a JSON Web Token that you obtain from Azure Active Directory.

Usare Azure Active Directory per l'autenticazione con il database di Azure per PostgreSQL:Use Azure Active Directory for authenticating with Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

Informazioni sul database di Azure per l'API REST PostgreSQL:Understand Azure Database for PostgreSQL REST API:https://docs.microsoft.com/rest/api/postgresql/

Comprendere SSO con Azure AD:Understand SSO with Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Indicazioni:** abilitare Azure Active Directory Multi-Factor Authentication (MFA) e seguire i consigli per la gestione delle identità e degli accessi del Centro sicurezza di Azure.Guidance: Enable Azure Active Directory Multi-Factor Authentication (MFA) and follow Azure Security Center Identity and Access Management recommendations. Quando si usano i token di Azure AD per l'accesso al database, è possibile richiedere l'autenticazione a più fattori per gli accessi al database.

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Usare Azure Active Directory per l'autenticazione con il database di Azure per PostgreSQL:Use Azure Active Directory for authenticating with Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

Come monitorare l'identità e l'accesso all'interno del Centro sicurezza di Azure:How to monitor identity and access within Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizzare macchine dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

**Guida:** usare le workstation con accesso privilegiato (PAW) con Multi-Factor Authentication (MFA) configurato per accedere alle risorse di Azure e configurarle.

Ulteriori informazioni sulle workstation con accesso privilegiato:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registrare e avvisare su attività sospette da account amministrativi

**Linee guida:** abilitare Advanced Threat Protection for Azure Database for PostgreSQL per generare avvisi per attività sospette.

Inoltre, è possibile usare Azure Active Directory (AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verifica un'attività sospetta o non sicura nell'ambiente.

Usare Rilevamenti dei rischi di Azure AD per visualizzare avvisi e report sul comportamento rischioso degli utenti.

Come configurare Advanced Threat Protection per il database di Azure per PostgreSQL:How to setup Advanced Threat Protection for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Come distribuire Privileged Identity Management (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Comprendere i rilevamenti dei rischi di Azure AD:Understand Azure AD risk detections:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo da posizioni approvate

**Linee guida:** usare percorsi denominati con accesso condizionale per consentire l'accesso al portale e Azure Resource Manager solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree.

Come configurare i percorsi denominati in Azure:How to configure Named Locations in Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida:** usare Azure Active Directory (AD) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD inoltre consente di archiviare le credenziali utente in Azure AD.

Per accedere al database di Azure per PostgreSQL, è consigliabile usare Azure AD e usare un token di Azure AD per la connessione. Quando si usa un token di Azure AD, sono supportati metodi diversi, ad esempio un utente di Azure AD, un gruppo di Azure AD o un'applicazione Azure AD che si connette al database.

Le credenziali di Azure AD possono essere usate anche per l'amministrazione a livello del piano di gestione (ad esempio il portale di Azure) per controllare gli account amministratore PostgreSQL.Azure AD credentials may also be used for administration at the management plane level (es.

Usare Azure Active Directory per l'autenticazione con il database di Azure per PostgreSQL:Use Azure Active Directory for authenticating with Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rivedere e riconciliare regolarmente l'accesso degli utenti

**Linee guida:** esaminare i log di Azure Active Directory per individuare gli account non aggiornati che possono includere quelli con ruoli amministrativi di Database di Azure per PostgreSQL.Guidance : Review the Azure Active Directory logs to help discover stale accounts which can include those with Azure Database for PostgreSQL administrative roles. Inoltre, usare le revisioni di Azure Identity Access per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali che possono essere usate per accedere al database di Azure per PostgreSQL e le assegnazioni di ruolo. L'accesso degli utenti deve essere rivisto regolarmente, ad esempio ogni 90 giorni per assicurarsi che solo gli utenti giusti abbiano accesso continuo.

Informazioni sulla creazione di report di Azure AD:Understand Azure AD Reporting:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Come usare le recensioni di azure Identity Access:How to use Azure Identity Access Reviews:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Esaminare gli utenti PostgreSQL e i ruoli assegnati:Review PostgreSQL users and assigned roles:https://www.postgresql.org/docs/current/database-roles.html

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorare i tentativi di accesso agli account disattivati

**Guida:** abilitare le impostazioni di diagnostica per il database di Azure per PostgreSQL e Azure Active Directory, inviando tutti i log a un'area di lavoro di Log Analytics.Guidance : Enable Diagnostic Settings for Azure Database for PostgreSQL and Azure Active Directory, sending all logs to a Log Analytics workspace. Configurare gli avvisi desiderati, ad esempio i tentativi di autenticazione non riusciti, in Log Analytics.Configure desired alerts (such as failed authentication attempts) within Log Analytics.

Come configurare e accedere ai log del server per il database di Azure per PostgreSQL:How to configure and access Server Logs for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Come configurare e accedere ai log di controllo per il database di Azure per PostgreSQL:How to configure and access audit logs for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-audit

Come integrare i log attività di Azure in Monitoraggio di Azure:How to integrate Azure Activity Logs into Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Scostamenti comportamento di avviso per l'accesso all'account

**Linee guida:** abilitare Advanced Threat Protection for Azure Database for PostgreSQL per generare avvisi per attività sospette.

Usare le funzionalità di protezione delle identità e di rilevamento dei rischi di Azure Active Directory per configurare le risposte automatiche alle azioni sospette rilevate. È possibile abilitare le risposte automatiche tramite Azure Sentinel per implementare le risposte di sicurezza dell'organizzazione.

È anche possibile inserire i log in Azure Sentinel per ulteriori indagini.

Come configurare Advanced Threat Protection per il database di Azure per PostgreSQL:How to setup Advanced Threat Protection for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Panoramica di Azure AD Identity Protection:Overview of Azure AD Identity Protection:https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

Come visualizzare gli accessi rischiosi di Azure AD:How to view Azure AD risky sign-ins:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Come eseguire l'onboarding di Azure Sentinel:How to onboard Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: Attualmente non disponibile; Customer Lockbox non è ancora supportato per Il database di Azure per PostgreSQL.

Elenco dei servizi supportati da Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

## <a name="data-protection"></a>Protezione dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Mantenere un inventario di informazioni sensibili

**Linee guida:** usare i tag per facilitare il rilevamento del database di Azure per le istanze PostgreSQL o le risorse correlate che archiviano o elaborano informazioni riservate.

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolare i sistemi che memorizzano o elaborano informazioni sensibili

**Linee guida:** Implementare sottoscrizioni e/o gruppi di gestione separati per lo sviluppo, il test e la produzione. Usare una combinazione di regole di collegamento privato, endpoint del servizio e/o regole del firewall per isolare e limitare l'accesso di rete al database di Azure per le istanze PostgreSQL.Use a combination of Private Link, Service Endpoints, and/or firewall rules to isolate and limit network access to your Azure Database for PostgreSQL instances.

Come creare sottoscrizioni di Azure aggiuntive:How to create additional Azure subscriptions:https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare i gruppi di gestione:https://docs.microsoft.com/azure/governance/management-groups/create

Come configurare Private Link per il database di Azure per PostgreSQL:How to configure Private Link for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Come creare e gestire gli endpoint del servizio VNet e le regole della rete virtuale nel database di Azure per PostgreSQL:How to create and manage VNet service endpoints and VNet rules in Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-manage-vnet-using-portal

Come configurare il database di Azure per le regole del firewall PostgreSQL:How to configure Azure Database for PostgreSQL firewall rules:https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules

**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsabilità**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorare e bloccare il trasferimento non autorizzato di informazioni sensibili

**Indicazioni:** quando si usano Macchine virtuali di Azure per accedere alle istanze PostgreSQL, usare le configurazioni di rete PostgreSQL, i gruppi di sicurezza di rete e i tag del servizio per ridurre la possibilità di esfiltrazione dei dati.

Microsoft gestisce l'infrastruttura sottostante per il database di Azure per PostgreSQL e ha implementato controlli rigorosi per prevenire la perdita o l'esposizione dei dati dei clienti.

Come ridurre l'esfiltrazione dei dati per il database di Azure per PostgreSQL:How to mitigate data exfiltration for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Crittografare tutte le informazioni sensibili in transito

**Linee guida:** il database di Azure per PostgreSQL supporta la connessione del server PostgreSQL alle applicazioni client tramite SSL (Secure Sockets Layer). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione. Nel portale di Azure verificare che l'opzione "Applica connessione SSL" sia abilitata per tutti i database di Azure per le istanze PostgreSQL per impostazione predefinita.

Attualmente la versione TLS supportata per il database di Azure per PostgreSQL sono TLS 1.0, TLS 1.1, TLS 1.2.

Come configurare la crittografia in transito per il database di Azure per PostgreSQL:How to configure encryption in transit for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilizzare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida:** le funzionalità di identificazione dei dati, classificazione e prevenzione delle perdite non sono ancora disponibili per il database di Azure per PostgreSQL.Guidance: Data identification, classification, and loss prevention features are not yet available for Azure Database for PostgreSQL. Implementare una soluzione di terze parti, se necessario, ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse4.6: Usare il controllo degli accessi in base al ruolo di

**Linee guida:** usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso al piano di controllo del database di Azure per PostgreSQL (ad esempio il portale di Azure). Per l'accesso al piano dati (all'interno del database stesso), utilizzare query SQL per creare utenti e configurare le autorizzazioni utente. Il controllo degli accessi in base al ruolo non influisce sulle autorizzazioni utente all'interno del database.

Come configurare il controllo degli accessi in base al ruolo in Azure:How to configure RBAC in Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Come configurare l'accesso utente con SQL per il database di Azure per PostgreSQL:How to configure user access with SQL for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-create-users

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizzare la prevenzione della perdita di dati basata su host per applicare il controllo degli accessi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per il database di Azure per PostgreSQL e ha implementato controlli rigorosi per prevenire la perdita o l'esposizione dei dati dei clienti.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografare le informazioni sensibili inattivi

**Linee guida:** il servizio Database di Azure per PostgreSQL usa il modulo di crittografia convalidato FIPS 140-2 per la crittografia di archiviazione dei dati inattivi. I dati, inclusi i backup, vengono crittografati su disco, ad eccezione dei file temporanei creati durante l'esecuzione di query. Il servizio usa la crittografia AES a 256 bit inclusa nella crittografia di archiviazione di Azure e le chiavi vengono gestite dal sistema. La crittografia dell'archiviazione è sempre attiva e non può essere disabilitata.

La crittografia dei dati con chiavi gestite dal cliente (CMK) per il database di Azure per PostgreSQL Singolo server consente di portare la propria chiave (BYOK) per la protezione dei dati inattivi. A questo punto, è necessario richiedere l'accesso per utilizzare questa funzionalità. A tale scopo, contattare:

AskAzureDBforPostgreSQL@service.microsoft.com.

Comprendere la crittografia inattivi per il database di Azure per PostgreSQL:Understand encryption at-rest for Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-security

Comprendere la crittografia inattivi per il database di Azure per PostgreSQL usando le chiavi gestite dal cliente:Understand encryption at-rest for Azure Database for PostgreSQL using customer-managed keys:https://docs.microsoft.com/azure/postgresql/concepts-data-encryption-postgresql


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e avvisare le modifiche alle risorse critiche di Azure4.9: Log and alert on changes to critical Azure resources

**Indicazioni:** usare Monitoraggio di Azure con il log attività di Azure per creare avvisi per quando vengono apportate modifiche alle istanze di produzione del database di Azure per PostgreSQL e altre risorse critiche o correlate.

Come creare avvisi per gli eventi del log attività di Azure:How to create alerts for Azure Activity Log events:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per ulteriori informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Eseguire strumenti automatici di scansione delle vulnerabilità

**Linee guida**: Attualmente non disponibile; Azure Security Center does not yet support vulnerability assessment for Azure Database for PostgreSQL.

Copertura delle funzionalità per i servizi di Azure PaaS nel Centro sicurezza di Azure:Feature coverage for Azure PaaS services in Azure Security Center:https://docs.microsoft.com/azure/security-center/features-paas

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuire una soluzione di gestione automatica delle patch del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuire una soluzione di gestione automatica delle patch software di terze parti

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Confrontare le scansioni delle vulnerabilità back-to-back

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilizzare un processo di valutazione del rischio per assegnare priorità alla correzione delle vulnerabilità individuate

**Linee guida:** Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano il database di Azure per PostgreSQL.Guidance : Microsoft performs vulnerability management on the underlying systems that support Azure Database for PostgreSQL.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per ulteriori informazioni, vedere [Controllo di sicurezza: inventario e gestione delle risorse](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Usare Azure Asset Discovery

**Indicazioni:** usare Azure Resource Graph per eseguire query e individuare tutte le risorse (incluso il database di Azure per le istanze PostgreSQL) all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni appropriate (di lettura) nel tenant e di essere in grado di enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Come creare query con Azure Resource Graph:How to create queries with Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Come visualizzare le sottoscrizioni di Azure:How to view your Azure Subscriptions:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Informazioni sul controllo degli accessi in base al ruolo di Azure:Understandhttps://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Gestire i metadati delle risorse

**Linee guida:** applicare tag al database di Azure per le istanze PostgreSQL e altre risorse correlate che assegnino metadati per organizzarli logicamente in una tassonomia.

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminare risorse di Azure non autorizzate

**Linee guida:** usare il tagging, i gruppi di gestione e sottoscrizioni separate, se appropriato, per organizzare e tenere traccia del database di Azure per le istanze PostgreSQL e le risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate dalla sottoscrizione in modo tempestivo.

Come creare sottoscrizioni di Azure aggiuntive:How to create additional Azure subscriptions:https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare i gruppi di gestione:https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Gestire un inventario delle risorse di Azure approvate e dei titoli software

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo e ad Azure nel suo complesso.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorare le risorse di Azure non approvate

**Linee guida:** usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:Guidance : Use Azure policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Inoltre, usare Azure Resource Graph per eseguire query/individuare risorse all'interno delle sottoscrizioni.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare query con Azure Graph:How to create queries with Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorare le applicazioni software non approvate all'interno delle risorse di calcolo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Rimuovere le risorse e le applicazioni software di Azure non approvate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo e ad Azure nel suo complesso.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="68-use-only-approved-applications"></a>6.8: Utilizzare solo applicazioni approvate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="69-use-only-approved-azure-services"></a>6.9: Usare solo i servizi di Azure approvati

**Linee guida:** usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:Guidance : Use Azure policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come negare un tipo di risorsa specifico con Criteri di Azure:How to deny a specific resource type with Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementare l'elenco delle applicazioni approvate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resources Manager tramite script

**Linee guida:** usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app "Gestione di Microsoft Azure". Ciò può impedire la creazione e le modifiche alle risorse all'interno di un ambiente ad alta sicurezza, ad esempio istanze del database di Azure per PostgreSQL contenenti informazioni riservate.

Come configurare l'accesso condizionale per bloccare l'accesso a Azure Resource Manager:How to configure Conditional Access to block access to Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitare la capacità degli utenti di eseguire script all'interno di risorse di calcolo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separare fisicamente o logicamente le applicazioni ad alto rischio

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle applicazioni Web in esecuzione nel servizio app di Azure o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per ulteriori informazioni, vedere [Controllo di sicurezza: configurazione protetta](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Stabilire configurazioni sicure per tutte le risorse di Azure7.1: Establish secure configurations for all Azure resources

**Linee guida:** definire e implementare configurazioni di sicurezza standard per il database di Azure per le istanze PostgreSQL con Criteri di Azure.Guidance : Define and implement standard security configurations for your Azure Database for PostgreSQL instances with Azure Policy. Usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.DBforPostgreSQL" per creare criteri personalizzati per controllare o applicare la configurazione di rete del database di Azure per le istanze PostgreSQL.Use Azure Policy aliases in the "Microsoft.DBforPostgreSQL" namespace to create custom policies to audit or enforce the network configuration of your Azure Database for PostgreSQL instances. È anche possibile usare le definizioni dei criteri predefinite correlate al database di Azure per le istanze PostgreSQL, ad esempio:You may also make use of built-in policy definitions related to your Azure Database for PostgreSQL instances, such as:

- Il criterio Imponi connessione SSL deve essere abilitato per i server di database PostgreSQL

- L'impostazione di registrazione delle connessioni deve essere abilitata per i server di database PostgreSQL

Come visualizzare gli alias di Criteri di Azure disponibili:How to view available Azure Policy aliases:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Stabilire configurazioni sicure del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Gestire le configurazioni delle risorse di Azure protette

**Linee guida:** usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure tra le risorse di Azure.Guidance: Use Azure policy [deny] and [deploy if not exist] to enforce secure settings across your Azure resources.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Informazioni sugli effetti dei criteri di Azure:Understand Azure Policy Effects:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Mantenere le configurazioni sicure del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Archiviare in modo sicuro la configurazione delle risorse di Azure7.5: Securely store configuration of Azure resources

**Indicazioni:** se si usano definizioni di criteri di Azure personalizzate per il database di Azure per le istanze PostgreSQL e le risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

Come archiviare il codice in DevOps di Azure:How to store code in Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos Documentation:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Memorizzare in modo sicuro immagini personalizzate del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida:** usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.DBforPostgreSQL" per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Inoltre, sviluppare un processo e una pipeline per la gestione delle eccezioni dei criteri.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementare il monitoraggio automatico della configurazione per i servizi di Azure7.9: Implement automated configuration monitoring for Azure services

**Linee guida:** usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.DBforPostgreSQL" per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [deploy if not exist] per applicare automaticamente le configurazioni per il database di Azure per le istanze PostgreSQL e le risorse correlate.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementare il monitoraggio automatico della configurazione per i sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gestire i segreti di Azure in modo sicuro

**Linee guida:** per le macchine virtuali di Azure o le applicazioni Web in esecuzione nel servizio app di Azure usate per accedere al database di Azure per le istanze PostgreSQL, usare l'identità del servizio gestito insieme a Archiviazione delle chiavi di Azure per semplificare e proteggere il database di Azure per la gestione dei segreti PostgreSQL.Guidance : For Azure Virtual Machines or web applications running on Azure App Service being used to access your Azure Database for PostgreSQL instances, use Managed Service Identity in conjunction with Azure Key Vault to simplify and secure Azure Database for PostgreSQL secret management. Verificare che l'eliminazione temporanea dell'insieme di credenziali delle chiavi sia abilitata.

Come integrare con le identità gestite di Azure:How to integrate with Azure Managed Identities:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Come creare un insieme di credenziali delle chiavi:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Come fornire l'autenticazione key Vault con un'identità gestita:How to provide Key Vault authentication with a managed identity:https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gestire le identità in modo sicuro e automatico

**Linee guida:** il database di Azure per il server PostgreSQL supporta l'autenticazione di Azure Active Directory (in anteprima) per accedere ai database.  Durante la creazione del database di Azure per il server PostgreSQL, si forniscono le credenziali per un utente amministratore. Questo amministratore può essere utilizzato per creare altri utenti del database.  

Per le macchine virtuali di Azure o le applicazioni Web in esecuzione nel servizio app di Azure usate per accedere al database di Azure per il server PostgreSQL, usare l'identità del servizio gestito insieme all'insieme a Archiviazione delle chiavi di Azure per archiviare e recuperare le credenziali per il database di Azure per il server PostgreSQL.For Azure Virtual Machines or web applications running on Azure App Service being used to access your Azure Database for PostgreSQL server, use Managed Service Identity in conjunction with Azure Key Vault to store and retrieve credentials for Azure Database for PostgreSQL server. Verificare che l'eliminazione temporanea dell'insieme di credenziali delle chiavi sia abilitata.

Usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (AD). Managed Identities consente di eseguire l'autenticazione a qualsiasi servizio che supporta l'autenticazione di Azure AD, incluso Key Vault, senza credenziali nel codice.

Come configurare le identità gestite:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Come integrare con le identità gestite di Azure:How to integrate with Azure Managed Identities:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminare l'esposizione di credenziali indesiderate

**Linee guida**: Implementare Credential Scanner per identificare le credenziali all'interno del codice. Credential Scanner will also encourage moving discovered credentials to more secure locations such as Azure Key Vault.

Come configurare Credential Scanner:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="malware-defense"></a>Difesa da malware

*Per ulteriori informazioni, vedere [Controllo di sicurezza: Difesa da malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilizzare software antimalware gestito centralmente

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

L'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, servizio app di Azure), tuttavia non viene eseguito sul contenuto dei clienti.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: File pre-scansione da caricare in risorse di Azure non di calcolo

**Linee guida:** l'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Database di Azure per PostgreSQL), tuttavia non viene eseguito sul contenuto dei clienti.

Pre-analisi di qualsiasi contenuto caricato in risorse di Azure non di calcolo, ad esempio servizio app, archiviazione Data Lake, archiviazione BLOB, Database di Azure per PostgreSQL e così via. Microsoft non può accedere ai dati in questi casi.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Verificare che il software antimalware e le firme siano aggiornate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

L'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Database di Azure per PostgreSQL), tuttavia non viene eseguito sul contenuto dei clienti.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

## <a name="data-recovery"></a>Ripristino dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatici regolari

**Indicazioni:** il database di Azure per PostgreSQL esegue il backup dei file di dati e del log delle transazioni. A seconda delle dimensioni massime di archiviazione supportate, vengono eseguiti backup completi e differenziali (server di archiviazione max 4 TB) o backup snapshot (fino a 16 SERVER di archiviazione max di 16 TB). Questi backup consentono di ripristinare un server a qualsiasi momento specifico all'interno del periodo di conservazione dei backup configurato. Il periodo di conservazione dei backup predefinito è di sette giorni. Facoltativamente, è possibile configurare fino a 35 giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit.

Come eseguire il backup di un server nel database di Azure per PostgreSQL:How to backup an server in Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

Comprendere il database di Azure per la configurazione iniziale di PostgreSQL:Understand Azure Database for PostgreSQL initial configuration:https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida:** il database di Azure per PostgreSQL crea automaticamente i backup del server e li archivia in un archivio con ridondanza locale o con ridondanza geografica, in base alla scelta dell'utente. I backup possono essere usati per ripristinare il server a un momento specifico. Il backup e il ripristino sono una parte essenziale di qualsiasi strategia di continuità aziendale, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale.

Se si usa L'insieme di credenziali delle chiavi di Azure per archiviare le credenziali per il database di Azure per le istanze PostgreSQL, assicurarsi che i backup automatici delle chiavi siano regolari.

Come eseguire il backup di un server nel database di Azure per PostgreSQL:How to backup an server in Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

Come eseguire il backup delle chiavi dell'insieme di credenziali:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Condiviso

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni:** nel database di Azure per PostgreSQL, l'esecuzione di un ripristino crea un nuovo server dai backup del server originale. Sono disponibili due tipi di ripristino: ripristino temporizzato e ripristino geografico. Il ripristino temporizzato è disponibile con entrambe le opzioni di ridondanza per il backup e crea un nuovo server nella stessa area del server originale. Il ripristino geografico è disponibile solo se il server è stato configurato per l'archiviazione con ridondanza geografica e consente di ripristinare il server in un'area diversa.

Il tempo stimato per il ripristino dipende da diversi fattori, tra cui le dimensioni dei database, le dimensioni dei log delle transazioni, la larghezza di banda di rete e il numero totale di database ripristinati contemporaneamente nella stessa area. Il tempo di recupero di solito è inferiore a 12 ore.

Testperiodico del ripristino del database di Azure per le istanze PostgreSQL.

Come eseguire il backup di un server nel database di Azure per PostgreSQL:How to backup an server in Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dai clienti

**Indicazioni:** il database di Azure per PostgreSQL esegue backup completi, differenziali e del log delle transazioni. Questi backup consentono di ripristinare un server a qualsiasi momento specifico all'interno del periodo di conservazione dei backup configurato. Il periodo di conservazione dei backup predefinito è di sette giorni. Facoltativamente, è possibile configurare fino a 35 giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit.

Comprendere il backup e il ripristino nel database di Azure per PostgreSQL:Understand backup and restore in Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-backup

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Creare una guida alla risposta agli incidenti

**Linee guida**: Creare una guida alla risposta agli incidenti per l'organizzazione. Assicurarsi che siano presenti piani di risposta agli incidenti scritti che definiscono tutti i ruoli del personale, nonché fasi di gestione/gestione degli incidenti dal rilevamento alla revisione post-incidente.

Come configurare le automazioni del flusso di lavoro all'interno del Centro sicurezza di Azure:How to configure Workflow Automations within Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Linee guida per creare un processo di risposta agli incidenti di sicurezza:Guidance on building your own security incident response process:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia di Microsoft Security Response Center di un incidente:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Il cliente può anche sfruttare la Guida alla gestione degli incidenti di sicurezza del COMPUTER del NIST per facilitare la creazione del proprio piano di risposta agli incidenti:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Creare una procedura di assegnazione dei punteggi degli eventi imprevisti e di assegnazione delle priorità

**Linee guida:** il Centro sicurezza assegna una gravità a ogni avviso per consentire di definire la priorità degli avvisi da analizzare per primi. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sull'analita utilizzato per emettere l'avviso, nonché il livello di probabilità che vi sia stato un intento dannoso dietro l'attività che ha portato all'avviso.

Inoltre, contrassegnare chiaramente gli abbonamenti (per ex. produzione, non-prod) e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Testare le procedure di risposta alla sicurezza

**Linee guida**: Eseguire esercizi per testare le capacità di risposta agli incidenti dei sistemi a una cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

Fare riferimento alla pubblicazione del NIST: Guida ai programmi di test, formazione ed eserciziper piani e funzionalità IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornire i dettagli di contatto degli eventi imprevisti di sicurezza e configurare le notifiche di avviso per gli incidenti di sicurezza

**Linee guida:** le informazioni di contatto relative agli eventi imprevisti di protezione verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che i dati del cliente sono stati utilizzati da una parte non legale o non autorizzata.  Esaminare gli incidenti dopo il fatto per assicurarsi che i problemi vengano risolti.

Come impostare il contatto di sicurezza del centro di sicurezza di Azure:How to set the Azure Security Center Security Contact:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporare avvisi di sicurezza nel sistema di risposta agli incidenti

**Indicazioni:** esportare gli avvisi e i suggerimenti del Centro sicurezza di Azure usando la funzionalità di esportazione continua. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi Sentinel.You may use the Azure Security Center data connector to stream the alerts Sentinel.

Come configurare l'esportazione continua:https://docs.microsoft.com/azure/security-center/continuous-export

Come trasmettere gli avvisi in Azure Sentinel:How to stream alerts into Azure Sentinel:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizzare la risposta agli avvisi di sicurezza

**Indicazioni:** usare la funzionalità automazione flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite "App per la logica" in avvisi e suggerimenti per la sicurezza.

Come configurare l'automazione del flusso di lavoro e le app per la logica:How to configure Workflow Automation and Logic Apps:https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per ulteriori informazioni, vedere Controllo di sicurezza: test di [penetrazione ed Esercizi del team rosso](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza entro 60 giorni

**Linee guida**: Seguire le regole di coinvolgimento Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Ulteriori informazioni sulla strategia di Microsoft e sull'esecuzione di Red Teaming e sui test di penetrazione dei siti in tempo reale sull'infrastruttura cloud, i servizi e le applicazioni gestiti da Microsoft sono disponibili qui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere il benchmark di sicurezza di [AzureSee](https://docs.microsoft.com/azure/security/benchmarks/overview) the Azure Security Benchmark
- Altre informazioni sulle linee di base per la sicurezza di AzureLearn more about [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
