---
title: Azure Security Baseline for Azure Database for MariaDB
description: Azure Security Baseline for Azure Database for MariaDB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: bca9c0e4c0695b6180775051d8b018930f8b808f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256468"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Azure Security Baseline for Azure Database for MariaDB

La linea di base della sicurezza di Azure per Il database di Azure per MariaDB contiene suggerimenti che consentono di migliorare il livello di sicurezza della distribuzione.

La linea di base per questo servizio è ricavata da [Azure Security Benchmark versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce consigli su come proteggere le soluzioni cloud in Azure con le indicazioni sulle procedure consigliate.

Per altre informazioni, vedere [Panoramica delle linee di base](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)della sicurezza di Azure.For more information, see Azure Security Baselines overview .

## <a name="network-security"></a>Sicurezza di rete

*Per ulteriori informazioni, vedere [Controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale1.1: Protect resources using Network Security Groups or Azure Firewall on your Virtual Network

**Guida:** configurare Private Link per il database di Azure per MariaDB con endpoint privati. Il servizio Collegamento privato consente di connettersi a diversi servizi PaaS in Azure tramite un endpoint privato. Azure Private Link porta essenzialmente i servizi di Azure all'interno della rete virtuale privata (VNet). Il traffico tra la rete virtuale e l'istanza MariaDB viaggia nella rete backbone Microsoft.

In alternativa, è possibile usare gli endpoint del servizio di rete virtuale per proteggere e limitare l'accesso alla rete al database di Azure per le implementazioni MariaDB.Alternatively, you may use Virtual Network Service Endpoints to protect and limit network access to your Azure Database for MariaDB implementations. Le regole di rete virtuale sono una funzionalità di sicurezza del firewall che controlla se il database di Azure per MariaDB accetta le comunicazioni inviate da determinate subnet nelle reti virtuali.

È anche possibile proteggere il database di Azure per MariaDB con le regole del firewall. Il firewall del server impedisce l'accesso al server di database fino a quando non si specificano i computer autorizzati. Per configurare il firewall, creare regole del firewall che specificano gli intervalli di indirizzi IP accettabili. È possibile creare regole firewall a livello di server.

Come configurare Private Link per il database di Azure per MariaDB:How to configure Private Link for Azure Database for MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Come creare e gestire gli endpoint del servizio VNet e le regole della rete virtuale nel database di Azure per il server MariaDB:How to create and manage VNet service endpoints and VNet rules in Azure Database for MariaDB server:https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Come configurare le regole del firewall di Database di Azure per MariaDB:How to configure Azure Database for MariaDB firewall rules:https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Indicazioni:** quando il database di Azure per il server MariaDB è protetto a un endpoint privato, è possibile distribuire le macchine virtuali nella stessa rete virtuale. È possibile utilizzare un gruppo di sicurezza di rete (NSG) per ridurre il rischio di esfiltrazione dei dati. Abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione per il controllo del traffico. È anche possibile inviare log di flusso del gruppo di sicurezza di rete a un'area di lavoro di Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso di traffico nel cloud di Azure.You may also send NSG flow logs to a Log Analytics workspace and use Traffic Analytics to provide insights into traffic flow in your Azure cloud. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare hot spot, identificare le minacce alla sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni errate della rete.

Come configurare Private Link per il database di Azure per MariaDB:How to configure Private Link for Azure Database for MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Come abilitare i registri https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal di flusso del gruppo di sicurezza di rete: come abilitare e usare Analisi del traffico:How to Enable NSG Flow Logs: How to Enable and use Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteggere le applicazioni Web critiche

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle applicazioni Web in esecuzione nel servizio app di Azure o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida:** usare Advanced Threat Protection per Il database di Azure per MariaDB.Guidance : Use Advanced Threat Protection for Azure Database for MariaDB. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Abilitare DDoS Protection Standard nelle reti virtuali associate al database di Azure per le istanze MariaDB per proteggersi dagli attacchi DDoS.Enable DDoS Protection Standard on the virtual networks associated with your Azure Database for MariaDB instances to protection against DDoS attacks. Usare Azure Security Center Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP Internet dannosi o inutilizzati noti.

Come configurare Advanced Threat Protection per Il database di Azure per MariaDB:How to configure Advanced Threat Protection for Azure Database for MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Come configurare la protezione DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Registrare i pacchetti di rete e i registri di flusso

**Indicazioni:** quando il database di Azure per il server MariaDB è protetto a un endpoint privato, è possibile distribuire le macchine virtuali nella stessa rete virtuale. È quindi possibile configurare un gruppo di sicurezza di rete (NSG) per ridurre il rischio di esfiltrazione dei dati. Abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione per il controllo del traffico. È anche possibile inviare log di flusso del gruppo di sicurezza di rete a un'area di lavoro di Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso di traffico nel cloud di Azure.You may also send NSG flow logs to a Log Analytics workspace and use Traffic Analytics to provide insights into traffic flow in your Azure cloud. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare hot spot, identificare le minacce alla sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni errate della rete.

Come abilitare i registri https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal di flusso del gruppo di sicurezza di rete: come abilitare e usare Analisi del traffico:How to Enable NSG Flow Logs: How to Enable and use Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Linee guida:** usare Advanced Threat Protection per Il database di Azure per MariaDB.Guidance : Use Advanced Threat Protection for Azure Database for MariaDB. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.
Come configurare Advanced Threat Protection per Il database di Azure per MariaDB:How to configure Advanced Threat Protection for Azure Database for MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gestire il traffico verso le applicazioni web

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle applicazioni Web in esecuzione nel servizio app di Azure o alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e l'overhead amministrativo delle regole di sicurezza di rete

**Indicazioni:** per le risorse che devono accedere al database di Azure per le istanze MariaDB, usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure.Guidance : For resources that need access to your Azure Database for MariaDB instances, use virtual network service tags to define network access controls on network security groups or Azure Firewall. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag del servizio (ad esempio, SQL. WestU) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi degli indirizzi inclusi nel tag del servizio e aggiorna automaticamente il tag del servizio quando gli indirizzi cambiano.
Nota: il database di Azure per MariaDB usa il tag di servizio "Microsoft.Sql".

Per altre informazioni sull'uso dei tag di servizio: informazioni sull'utilizzo dei tag di servizio per il database di Azure per MariaDB:For more information about using service tags: https://docs.microsoft.com/azure/virtual-network/service-tags-overview Understand service tag usage for Azure Database for MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida:** definire e implementare configurazioni di sicurezza standard per le impostazioni di rete e le risorse di rete associate al database di Azure per le istanze MariaDB con Criteri di Azure.Guidance : Define and implement standard security configurations for network settings and network resources associated with your Azure Database for MariaDB instances with Azure Policy. Usare gli alias dei criteri di Azure negli spazi dei nomi "Microsoft.DBforMariaDB" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete del database di Azure per le istanze MariaDB.Use Azure Policy aliases in the "Microsoft.DBforMariaDB" and "Microsoft.Network" namespaces to create custom policies to audit or enforce the network configuration of your Azure Database for MariaDB instances. È anche possibile usare le definizioni dei criteri predefinite relative alla rete o al database di Azure per le istanze MariaDB, ad esempio:You may also make use of built-in policy definitions related to networking or your Azure Database for MariaDB instances, such as:

- Lo standard di protezione DDoS deve essere abilitato

- L'endpoint privato deve essere abilitato per i server MariaDB

- Il server MariaDB deve usare un endpoint del servizio di rete virtualeMariaDB server should use a virtual network service endpoint

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Esempi di criteri di Azure per la rete:Azure Policy samples for networking:https://docs.microsoft.com/azure/governance/policy/samples/

Come creare un blueprint di Azure:How to create an Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regole di configurazione del traffico dei documenti

**Linee guida**: Utilizzare i tag per le risorse relative alla sicurezza di rete e al flusso di traffico per le istanze MariaDB per fornire metadati e organizzazione logica.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e relativo valore" per assicurarsi che tutte le risorse vengano create con i tag e per notificare le risorse senza tag esistenti.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai relativi tag.

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilizzare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate al database di Azure per le istanze MariaDB.Guidance : Use Azure Activity Log to monitor network resource configurations and detect changes for network resources related to your Azure Database for MariaDB instances. Creare avvisi all'interno di Monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse di rete critiche.
Come visualizzare e recuperare gli https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view eventi del log attività di Azure: come creare avvisi in Monitoraggio di Azure:How to view and retrieve Azure Activity Log events: How to create alerts in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizzare origini di sincronizzazione dell'ora approvate

**Indicazioni:** Microsoft gestisce l'origine ora usata per le risorse di Azure, ad esempio Database di Azure per MariaDB per i timestamp nei log.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurare la gestione dei registri di protezione centralizzati

**Guida:** abilitare le impostazioni di diagnostica e i log del server e inserire i log per aggregare i dati di sicurezza generati dal database di Azure per le istanze MariaDB.Guidance : Enable Diagnostic Settings and Server Logs and ingest logs to aggregate security data generated by your Azure Database for MariaDB instances. In Monitoraggio di Azure usare Log Analytics Workspace(s) per eseguire query ed eseguire analisi e gli account di archiviazione di Azure per l'archiviazione a lungo termine/archiviazione. In alternativa, è possibile abilitare e on-board i dati di Azure Sentinel o un SIEM di terze parti.
Come configurare e accedere ai log del server per il database di Azure per MariaDB:How to configure and access Server Logs for Azure Database for MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Come configurare e accedere ai log https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal di controllo per Il database di Azure per MariaDB: come eseguire l'onboarding di Azure Sentinel:How to configure and access audit logs for Azure Database for MariaDB: How to onboard Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard



**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsabilità**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Abilitare la registrazione di controllo per le risorse di Azure2.3: Abilitare la registrazione di controllo per le risorse di Azure2.

**Guida:** abilitare le impostazioni di diagnostica nel database di Azure per le istanze MariaDB per l'accesso ai log di controllo, sicurezza e diagnostica. Assicurarsi di abilitare in modo specifico il registro di controllo MariaDB. I log attività, che sono automaticamente disponibili, includono l'origine dell'evento, la data, l'utente, il timestamp, gli indirizzi di origine, gli indirizzi di destinazione e altri elementi utili. È anche possibile abilitare impostazioni di diagnostica log attività di Azure e inviare i log alla stessa area di lavoro Log Analytics o account di archiviazione.

Come configurare e accedere ai log https://docs.microsoft.com/azure/mariadb/concepts-server-logs del server per il database di Azure https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal per MariaDB: come configurare e accedere ai log di controllo per Database di Azure per MariaDB: Come configurare le impostazioni di diagnostica per il log attività di Azure:How to configure and access Server Logs for Azure Database for MariaDB: How to configure and access audit logs for Azure Database for MariaDB: How to configure Diagnostic Settings for the Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy



**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsabilità**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Raccogliere i registri di sicurezza dai sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dello spazio di archiviazione del registro di protezione

**Linee guida:** in Monitoraggio di Azure, per l'area di lavoro Log Analytics usata per contenere il database di Azure per i log MariaDB, impostare il periodo di conservazione in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archiviazione.
Come impostare i parametri di conservazione https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period dei log per le aree di lavoro di Log Analytics: Archiviazione dei log delle risorse in un account di archiviazione di Azure:How to set log retention parameters for Log Analytics Workspaces: Storing resource logs in an Azure Storage Account:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i registri

**Guida**: Analizzare e monitorare i registri dalle istanze MariaDB per un comportamento anomalo. Usare l'area di lavoro Log Analytics di Monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log. In alternativa, è possibile abilitare e on-board i dati di Azure Sentinel o di terze parti SIEM.

Come eseguire l'onboarding di Azure Sentinel:How to onboard Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Per altre informazioni sull'area di lavoro di Log Analytics:For more information about the Log Analytics Workspace:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Come eseguire query personalizzate in Monitoraggio di Azure:How to perform custom queries in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Abilitare gli avvisi per attività anomale

**Guida:** Abilitare Advanced Threat Protection per MariaDB. Advanced Threat Protection for Azure Database for MariaDB rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Inoltre, è possibile abilitare i registri del server e le impostazioni di diagnostica per MariaDB e inviare i log a un'area di lavoro di Log Analytics.In addition, you may enable Server Logs and Diagnostic Settings for MariaDB and send logs to a Log Analytics Workspace. Eseguire l'onboarding dell'area di lavoro di Log Analytics in Azure Sentinel in quanto fornisce una soluzione SOAR (Security Orchestration Automated Response). Ciò consente di creare e utilizzare playbook (soluzioni automatizzate) per risolvere i problemi di sicurezza.

Come abilitare Advanced Threat Protection per MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Come configurare e accedere ai registri del server per MariDB:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Come configurare e accedere ai log di controllo per MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Come eseguire l'onboarding di Azure Sentinel:How to onboard Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizzare la registrazione antimalware

**Guida**: N/D; MariaDB non elabora né produce registri anti-malware correlati.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: Abilitare la registrazione delle query DNS

**Guida**: N/D; MariaDB non elabora né produce registri correlati al DNS.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: Abilitare la registrazione di controllo della riga di comando

**Guida**: N/D; benchmark è destinato alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per ulteriori informazioni, vedere [Controllo di sicurezza: controllo delle identità e degli accessi](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Mantenere un inventario degli account amministrativi

**Linee guida:** gestire un inventario degli account utente con accesso amministrativo al piano di gestione (portale di Azure/Azure Resource Manager) delle istanze di MariaDB. Inoltre, gestire un inventario degli account amministrativi che hanno accesso al piano dati delle istanze MariaDB. (Quando si crea il server MariaDB, si forniscono le credenziali per un utente amministratore. Questo amministratore può essere utilizzato per creare altri utenti MariaDB.)

Comprendere la gestione degli accessi per MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-security#access-management

Informazioni sui ruoli RBAC predefiniti per le sottoscrizioni di Azure:Understand built-in RBAC roles for Azure Subscriptions:https://docs.microsoft.com/azure/role-based-access-control/built-in-roles


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Modificare le password predefinite, ove applicabile

**Guida:** Azure Active Directory non ha il concetto di password predefinite.

Al momento della creazione della risorsa MariaDB stessa, Azure impone la creazione di un utente amministratore con una password complessa. Tuttavia, una volta creata l'istanza MariaDB, è possibile utilizzare il primo account di amministratore del server creato account per creare altri utenti e concedere loro l'accesso amministrativo. Quando si creano questi account, assicurarsi di configurare una password complessa diversa per ogni account.

Come creare account aggiuntivi per MariaDB:https://docs.microsoft.com/azure/mariadb/howto-create-users


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizzare account amministrativi dedicati

**Linee guida**: Creare procedure operative standard per l'utilizzo di account amministrativi dedicati che abbiano accesso alle istanze MariaDB. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Informazioni sull'identità e l'accesso del Centro sicurezza di Azure:Understand Azure Security Center Identity and Access:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Usare Single Sign-On (SSO) con Azure Active Directory

**Guida**: L'accesso aereo dati a MariaDB è controllato dalle identità archiviate all'interno del database e non supporta SSO. L'accesso al piano di controllo per MariaDB è disponibile tramite l'API REST e supporta SSO. To authenticate, set the Authorization header for your requests to a JSON Web Token that you obtain from Azure Active Directory.

Informazioni sull'API REST di Azure Database for MariaDB:Understand Azure Database for MariaDB REST API:https://docs.microsoft.com/rest/api/mariadb/

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Indicazioni:** abilitare l'autenticazione a più fattori di Azure AD e seguire i consigli per la gestione delle identità e degli accessi del Centro sicurezza sicurezza di Azure.Guidance: Enable Azure AD MFA and follow Azure Security Center Identity and Access Management recommendations.

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Come monitorare l'identità e l'accesso all'interno del Centro sicurezza di Azure:How to monitor identity and access within Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizzare macchine dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida:** usare PAW (workstation con accesso privilegiato) con l'autenticazione a più fattori configurata per accedere alle risorse di Azure e configurarle.

Ulteriori informazioni sulle workstation con accesso privilegiato:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registrare e avvisare su attività sospette da account amministrativi

**Linee guida:** Abilitare Advanced Threat Protection per MariaDB per generare avvisi per attività sospette.

Inoltre, è possibile usare Azure AD Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verifica un'attività sospetta o non sicura nell'ambiente. Usare Rilevamenti dei rischi di Azure AD per visualizzare avvisi e report sul comportamento rischioso degli utenti.

Come configurare Advanced Threat Protection per MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Come distribuire Privileged Identity Management (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Comprendere i rilevamenti dei rischi di Azure AD:Understand Azure AD risk detections:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo da posizioni approvate

**Linee guida:** usare percorsi denominati con accesso condizionale per consentire l'accesso solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche per limitare l'accesso alle risorse di Azure, ad esempio MariaDB.Guidance : Use Conditional Access Named Locations to allow access from only specific logical groupings of IP address ranges or countries/regions to limit access to Azure resources such as MariaDB.

Come configurare i percorsi denominati in Azure:How to configure Named Locations in Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida:** usare Azure Active Directory (AAD) come sistema di autenticazione e autorizzazione centrale. AAD protegge i dati utilizzando la crittografia avanzata per i dati inattivi e in transito. AAD inoltre consente di, gli ishe e l'archiviazione sicura delle credenziali utente.

L'autenticazione di Azure AD non può essere usata per l'accesso diretto al piano dati MariaDB, tuttavia, le credenziali di Azure AD possono essere usate per l'amministrazione a livello del piano di gestione (ad esempio il portale di Azure) per controllare gli account di amministrazione di MariaDB.

Come aggiornare la password di amministratore per MariaDB:https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rivedere e riconciliare regolarmente l'accesso degli utenti

**Linee guida**: Esaminare i log di Azure Active Directory per individuare gli account non aggiornati che possono includere quelli con ruoli amministrativi MariaDB. Inoltre, usare Le revisioni di Azure Identity Access per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali che possono essere usate per accedere a MariaDB e le assegnazioni di ruolo. L'accesso degli utenti deve essere rivisto regolarmente, ad esempio ogni 90 giorni per assicurarsi che solo gli utenti giusti abbiano accesso continuo.

Informazioni sulla creazione di report di Azure AD:Understand Azure AD Reporting:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Come usare le recensioni di azure Identity Access:How to use Azure Identity Access Reviews:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorare i tentativi di accesso agli account disattivati

**Guida:** abilitare le impostazioni di diagnostica per MariaDB e Azure Active Directory, inviando tutti i log a un'area di lavoro di Log Analytics.Guidance : Enable Diagnostic Settings for MariaDB and Azure Active Directory, sending all logs to a Log Analytics Workspace. Configurare gli avvisi desiderati ( ad esempio i tentativi di autenticazione non riusciti) all'interno dell'area di lavoro di Log Analytics.Configure desired Alerts (such as failed authentication attempts) within Log Analytics Workspace.

Come configurare e accedere ai registri del server per MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Come configurare e accedere ai log di controllo per MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Come integrare i log attività di Azure in Monitoraggio di Azure:How to integrate Azure Activity Logs into Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsabilità**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Scostamenti comportamento di avviso per l'accesso all'account

**Guida:** Abilitare Advanced Threat Protection per MariaDB. Advanced Threat Protection for Azure Database for MariaDB rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Usare le funzionalità di protezione delle identità e di rilevamento dei rischi di Azure Active Directory per configurare le risposte automatiche alle azioni sospette rilevate. È possibile abilitare le risposte automatiche tramite Azure Sentinel per implementare le risposte di sicurezza dell'organizzazione.

Come abilitare Advanced Threat Protection per MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Come configurare e abilitare i criteri di rischio di Identity Protection:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Come visualizzare gli accessi rischiosi di Azure AD:How to view Azure AD risky sign-ins:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Come eseguire l'onboarding di Azure Sentinel:How to onboard Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsabilità**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Orientamento**: Non applicabile; Customer Lockbox non ancora supportato per il database di Azure per MariaDB.

Elenco dei servizi supportati da Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="data-protection"></a>Protezione dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Mantenere un inventario di informazioni sensibili

**Linee guida:** usare i tag per tenere traccia del database di Azure per le istanze MariaDB o le risorse correlate che archiviano o elaborano informazioni riservate.

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolare i sistemi che memorizzano o elaborano informazioni sensibili

**Linee guida:** Implementare sottoscrizioni e/o gruppi di gestione separati per lo sviluppo, il test e la produzione. Utilizzare una combinazione di regole del firewall Private Link, Service Endpoints e/o MariaDB per isolare e limitare l'accesso di rete alle istanze MariaDB.

Come creare sottoscrizioni di Azure aggiuntive:How to create additional Azure subscriptions:https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare i gruppi di gestione:https://docs.microsoft.com/azure/governance/management-groups/create

Come configurare Private Link per il database di Azure per MariaDB:How to configure Private Link for Azure Database for MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Come configurare gli endpoint del servizio per il database di Azure per MariaDB:How to configure Service Endpoints for Azure Database for MariaDB:https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Come configurare le regole del firewall per il database di Azure per MariaDB:How to configure firewall rules for Azure Database for MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsabilità**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorare e bloccare il trasferimento non autorizzato di informazioni sensibili

**Linee guida:** quando si usano macchine virtuali di Azure per accedere alle istanze di MariaDB, usare le configurazioni di rete Private Link, MariaDB, i gruppi di sicurezza di rete e i tag del servizio per ridurre la possibilità di esfiltrazione dei dati.

Microsoft gestisce l'infrastruttura sottostante per MariaDB e ha implementato controlli rigorosi per prevenire la perdita o l'esposizione dei dati dei clienti.

Come ridurre l'esfiltrazione dei dati per Il database di Azure per MariaDB:How to mitigate data exfiltration for Azure Database for MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Crittografare tutte le informazioni sensibili in transito

**Linee guida:** il database di Azure per MariaDB supporta la connessione del database di Azure per il server MariaDB alle applicazioni client tramite SSL (Secure Sockets Layer). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione. Nel portale di Azure verificare che l'opzione "Applica connessione SSL" sia abilitata per tutte le istanze di MariaDB.In the Azure portal, ensure "Enforce SSL connection" is enabled for all of your MariaDB instances.

Come configurare la crittografia in transito per MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-ssl

**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilizzare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida:** le funzionalità di identificazione dei dati, classificazione e prevenzione delle perdite non sono ancora disponibili per il database di Azure per MariaDB.Guidance: Data identification, classification, and loss prevention features are not yet available for Azure Database for MariaDB. Implementare una soluzione di terze parti, se necessario, ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsabilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse4.6: Usare il controllo degli accessi in base al ruolo di

**Linee guida:** usare il controllo degli accessi in base al ruolo di Azure AD per controllare l'accesso al database di Azure per il piano di gestione di MariaDB (portale di Azure/Gestione risorse di Azure). Per l'accesso al piano dati (all'interno del database stesso), utilizzare query SQL per creare utenti e configurare le autorizzazioni utente.

Come configurare il controllo degli accessi in base al ruolo in Azure:How to configure RBAC in Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Come configurare l'accesso utente con SQL per MariaDB:https://docs.microsoft.com/azure/mariadb/howto-create-users

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizzare la prevenzione della perdita di dati basata su host per applicare il controllo degli accessi

**Guida**: N/D; questa linea guida è destinata alle risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per MariaDB e ha implementato controlli rigorosi per prevenire la perdita o l'esposizione dei dati dei clienti.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografare le informazioni sensibili inattivi

**Linee guida:** il servizio Database di Azure per MariaDB usa il modulo di crittografia convalidato FIPS 140-2 per la crittografia di archiviazione dei dati inattivi. I dati, inclusi i backup, vengono crittografati su disco, ad eccezione dei file temporanei creati durante l'esecuzione di query. Il servizio usa la crittografia AES a 256 bit inclusa nella crittografia di archiviazione di Azure e le chiavi vengono gestite dal sistema. La crittografia dell'archiviazione è sempre attiva e non può essere disabilitata.

Comprendere la crittografia inattivi per MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-security

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e avvisare le modifiche alle risorse critiche di Azure4.9: Log and alert on changes to critical Azure resources

**Indicazioni:** usare Monitoraggio di Azure con il log attività di Azure per creare avvisi per quando vengono apportate modifiche alle istanze di produzione del database di Azure per MariaDB e altre risorse critiche o correlate.

Come creare avvisi per gli eventi del log attività di Azure:How to create alerts for Azure Activity Log events:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per ulteriori informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Eseguire strumenti automatici di scansione delle vulnerabilità

**Linee guida**: Attualmente non disponibile; Centro sicurezza di Azure non supporta ancora la valutazione delle vulnerabilità per il database di Azure per il server MariaDB.Azure Security Center does not yet support vulnerability assessment for Azure Database for MariaDB server.


**Monitoraggio del Centro sicurezza di Azure**: Sì

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

**Linee guida:** Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano il database di Azure per il server MariaDB.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per ulteriori informazioni, vedere [Controllo di sicurezza: inventario e gestione delle risorse](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Usare Azure Asset Discovery

**Indicazioni:** usare Azure Resource Graph per eseguire query e individuare tutte le risorse (incluso il database di Azure per il server MariaDB) all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni appropriate (di lettura) nel tenant e di essere in grado di enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Come creare query con Azure Graph:How to create queries with Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Come visualizzare le sottoscrizioni di Azure:How to view your Azure Subscriptions:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Informazioni sul controllo degli accessi in base al ruolo di Azure:Understandhttps://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Gestire i metadati delle risorse

**Linee guida:** applicare tag al database di Azure per il server MariaDB e ad altre risorse correlate che assuncono ai metadati per organizzarli logicamente in una tassonomia.

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminare risorse di Azure non autorizzate

**Linee guida:** usare il tagging, i gruppi di gestione e sottoscrizioni separate, se appropriato, per organizzare e tenere traccia del database di Azure per il server MariaDB e delle risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate dalla sottoscrizione in modo tempestivo.

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

### <a name="611-divlimit-users-ability-to-interact-with-azure-resources-manager-via-scriptsdiv"></a>6.11: <div>Limitare la capacità degli utenti di interagire con Azure Resources Manager tramite script</div>

**Linee guida:** usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app "Gestione di Microsoft Azure". Ciò può impedire la creazione e le modifiche alle risorse all'interno di un ambiente ad alta sicurezza, ad esempio il database di Azure per il server MariaDB contenente informazioni riservate.

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

**Linee guida:** definire e implementare configurazioni di sicurezza standard per il database di Azure per le istanze MariaDB con Criteri di Azure.Guidance : Define and implement standard security configurations for your Azure Database for MariaDB instances with Azure Policy. Usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.DBforMariaDB" per creare criteri personalizzati per controllare o applicare la configurazione di rete del database di Azure per i server MariaDB.Use Azure Policy aliases in the "Microsoft.DBforMariaDB" namespace to create custom policies to audit or enforce the network configuration of your Azure Database for MariaDB servers. È anche possibile usare le definizioni dei criteri predefinite correlate al database di Azure per i server MariaDB, ad esempio:You may also make use of built-in policy definitions related to your Azure Database for MariaDB servers, such as:

- Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MariaDB

Come visualizzare gli alias dei criteri di Azure disponibili:How to view available Azure Policy Aliases:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

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

**Indicazioni:** se si usano definizioni di criteri di Azure personalizzate per il database di Azure per i server MariaDB e le risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

Come archiviare il codice in DevOps di Azure:How to store code in Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos Documentation:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Memorizzare in modo sicuro immagini personalizzate del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida:** usare gli alias dei criteri di Azure nello spazio dei nomi "Microsoft.DBforMariaDB" per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Inoltre, sviluppare un processo e una pipeline per la gestione delle eccezioni dei criteri.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementare il monitoraggio automatico della configurazione per i servizi di Azure7.9: Implement automated configuration monitoring for Azure services

**Linee guida:** usare gli alias dei criteri di Azure nello spazio dei nomi "Microsoft.DBforMariaDB" per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [deploy if not exist] per applicare automaticamente le configurazioni per il database di Azure per le istanze MariaDB e le risorse correlate.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementare il monitoraggio automatico della configurazione per i sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gestire i segreti di Azure in modo sicuro

**Linee guida:** per le macchine virtuali di Azure o le applicazioni Web in esecuzione nel servizio app di Azure usate per accedere al database di Azure per i server MariaDB, usare l'identità del servizio gestito insieme all'insieme di credenziali delle chiavi di Azure per semplificare e proteggere il database di Azure per la gestione dei segreti del server MariaDB.Guidance : For Azure Virtual Machines or web applications running on Azure App Service being used to access your Azure Database for MariaDB servers, use Managed Service Identity in conjunction with Azure Key Vault to simplify and secure Azure Database for MariaDB server secret management. Verificare che l'eliminazione temporanea dell'insieme di credenziali delle chiavi sia abilitata.

Come integrare con le identità gestite di Azure:How to integrate with Azure Managed Identities:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Come creare un insieme di credenziali delle chiavi:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Come fornire l'autenticazione key Vault con un'identità gestita:How to provide Key Vault authentication with a managed identity:https://docs.microsoft.com/azure/key-vault/managed-identity 



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gestire le identità in modo sicuro e automatico

**Linee guida:** il database di Azure per il server MariaDB attualmente non supporta l'autenticazione di Azure Active Directory per accedere ai database.  Durante la creazione del database di Azure per il server MariaDB, si forniscono le credenziali per un utente amministratore. Questo amministratore può essere utilizzato per creare altri utenti MariaDB.  

Per le macchine virtuali di Azure o le applicazioni Web in esecuzione nel servizio app di Azure usate per accedere al database di Azure per il server MariaDB, usare l'identità del servizio gestito insieme all'insieme a Archiviazione delle chiavi di Azure per archiviare e recuperare le credenziali per il database di Azure per il server MariaDB.For Azure Virtual Machines or web applications running on Azure App Service being used to access your Azure Database for MariaDB server, use Managed Service Identity in conjunction with Azure Key Vault to store and retrieve credentials for Azure Database for MariaDB server.  Verificare che l'eliminazione temporanea dell'insieme di credenziali delle chiavi sia abilitata.

Usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (AD). Managed Identities consente di eseguire l'autenticazione a qualsiasi servizio che supporta l'autenticazione di Azure AD, incluso Key Vault, senza credenziali nel codice. Come configurare le identità gestite: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm. Come eseguire l'integrazione con https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identityle identità gestite di Azure: .



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

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

L'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, servizio app di Azure), tuttavia non viene eseguito sul contenuto dei clienti.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: File pre-scansione da caricare in risorse di Azure non di calcolo

**Linee guida:** l'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Database di Azure per il server MariaDB), tuttavia non viene eseguito sul contenuto dei clienti.

Pre-scan qualsiasi contenuto caricato in risorse di Azure non di calcolo, ad esempio servizio app, archiviazione Data Lake, Archiviazione BLOB, Database di Azure per server MariaDB e così via. Microsoft non può accedere ai dati in questi casi.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Verificare che il software antimalware e le firme siano aggiornate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

L'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Database di Azure per il server MariaDB), tuttavia non viene eseguito sul contenuto dei clienti.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="data-recovery"></a>Ripristino dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatici regolari

**Indicazioni:** il database di Azure per MariaDB esegue backup completi, differenziali e del log delle transazioni.  Database di Azure per MariaDB crea automaticamente backup del server e li archivia in un archivio con ridondanza locale o geografica configurato dall'utente. I backup possono essere usati per ripristinare il server a un momento specifico. Il backup e il ripristino sono una parte essenziale di qualsiasi strategia di continuità aziendale, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale.  Il periodo di conservazione dei backup predefinito è di sette giorni. Facoltativamente, è possibile configurare fino a 35 giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit.

Comprendere i backup per MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup

Comprendere la configurazione iniziale di MariaDB:https://docs.microsoft.com/azure/mariadb/tutorial-design-database-using-portal



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** il database di Azure per MariaDB crea automaticamente i backup del server e li archivia in un archivio ridondante o con ridondanza geografica configurato dall'utente. I backup possono essere usati per ripristinare il server a un momento specifico.  Il backup e il ripristino sono una parte essenziale di qualsiasi strategia di continuità aziendale, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale.

Se si utilizza Key Vault per la crittografia dei dati sul lato client per i dati archiviati nel server MariaDB, assicurarsi di eseguire backup automatici regolari delle chiavi.

Comprendere i backup per MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup

Come eseguire il backup delle chiavi dell'insieme di credenziali:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni:** in Database di Azure per MariaDB eseguire un ripristino dai backup del server originale per il test periodico dei backup. Sono disponibili due tipi di ripristino: ripristino temporizzato e ripristino geografico. Il ripristino temporizzato è disponibile con entrambe le opzioni di ridondanza per il backup e crea un nuovo server nella stessa area del server originale. Il ripristino geografico è disponibile solo se il server è stato configurato per l'archiviazione con ridondanza geografica e consente di ripristinare il server in un'area diversa.

Il tempo stimato per il ripristino dipende da diversi fattori, tra cui le dimensioni dei database, le dimensioni dei log delle transazioni, la larghezza di banda di rete e il numero totale di database ripristinati contemporaneamente nella stessa area. Il tempo di recupero di solito è inferiore a 12 ore.

Comprendere il backup e il ripristino nel database di Azure per MariaDB:Understand backup and restore in Azure Database for MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup#restore


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dai clienti

**Indicazioni:** il database di Azure per MariaDB esegue backup completi, differenziali e del log delle transazioni. Questi backup consentono di ripristinare un server a qualsiasi momento specifico all'interno del periodo di conservazione dei backup configurato. Il periodo di conservazione dei backup predefinito è di sette giorni. Facoltativamente, è possibile configurare fino a 35 giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit.

Comprendere il backup e il ripristino nel database di Azure per MariaDB:Understand backup and restore in Azure Database for MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Creare una guida alla risposta agli incidenti

**Linee guida**: Creare una guida alla risposta agli incidenti per l'organizzazione. Assicurarsi che siano presenti piani di risposta agli incidenti scritti che definiscono tutti i ruoli del personale, nonché fasi di gestione/gestione degli incidenti dal rilevamento alla revisione post-incidente.
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final 



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Creare una procedura di assegnazione dei punteggi degli eventi imprevisti e di assegnazione delle priorità

**Linee guida:** il Centro sicurezza assegna una gravità a ogni avviso per consentire di definire la priorità degli avvisi da analizzare per primi. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sull'analita utilizzato per emettere l'avviso, nonché il livello di probabilità che vi sia stato un intento dannoso dietro l'attività che ha portato all'avviso. 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

Usare i tag per organizzare le risorse di Azure:Use tags to organize your Azure resources:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Testare le procedure di risposta alla sicurezza

**Linee guida:** eseguire esercizi per testare le funzionalità di risposta agli incidenti dei sistemi a cadenza regolare per proteggere le risorse di Azure.Guidance : Conduct exercises to test your systems' incident response capabilities on a regular cadeence to help protect your Azure resources. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornire i dettagli di contatto degli eventi imprevisti di sicurezza e configurare le notifiche di avviso per gli incidenti di sicurezza

**Linee guida:** le informazioni di contatto relative agli eventi imprevisti di protezione verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che i dati sono stati utilizzati da una parte illegale o non autorizzata. Esaminare gli incidenti dopo il fatto per assicurarsi che i problemi vengano risolti.
    

    How to set the Azure Security Center Security Contact: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporare avvisi di sicurezza nel sistema di risposta agli incidenti

**Indicazioni:** Esportare gli avvisi e i suggerimenti del Centro sicurezza di Azure usando la funzionalità di esportazione continua per identificare i rischi per le risorse di Azure.Guidance: Export your Azure Security Center alerts and recommendations using the Continuous Export feature to help identify risks to Azure resources. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.You may use the Azure Security Center data connector to stream the alerts to Azure Sentinel.
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizzare la risposta agli avvisi di sicurezza

**Indicazioni:** usare la funzionalità automazione flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite "App per la logica" negli avvisi e suggerimenti per proteggere le risorse di Azure.Guidance: Use the Workflow Automation feature in Azure Security Center to automatically trigger responses via "Logic Apps" on security Apps and recommendations to protect your Azure resources.
    

Come configurare l'automazione del flusso di lavoro e le app per la logica:How to configure Workflow Automation and Logic Apps:https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per ulteriori informazioni, vedere Controllo di sicurezza: test di [penetrazione ed Esercizi del team rosso](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza entro 60 giorni

**Linee guida**: Seguire le regole di coinvolgimento Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Ulteriori informazioni sulla strategia di Microsoft e sull'esecuzione di Red Teaming e sui test di penetrazione dei siti in tempo reale sull'infrastruttura cloud, i servizi e le applicazioni gestiti da Microsoft sono disponibili qui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere il benchmark di sicurezza di [AzureSee](https://docs.microsoft.com/azure/security/benchmarks/overview) the Azure Security Benchmark
- Altre informazioni sulle linee di base per la sicurezza di AzureLearn more about [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
