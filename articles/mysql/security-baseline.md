---
title: Baseline di sicurezza di Azure per Database di Azure per MySQL
description: Baseline di sicurezza di Azure per Database di Azure per MySQL
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 02050745e7c8a9aa05d3f2de63a4bc5f1ebf8318
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654783"
---
# <a name="azure-security-baseline-for-azure-database-for-mysql"></a>Baseline di sicurezza di Azure per Database di Azure per MySQL

La baseline di sicurezza di Azure per Database di Azure per MySQL contiene raccomandazioni utili per migliorare il comportamento della distribuzione in termini di sicurezza.

La baseline per questo servizio è tratta da [Azure Security Benchmark, versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere [Panoramica delle baseline di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 Proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

**Indicazioni**: configurare Collegamento privato per Database di Azure per MySQL con endpoint privati. Il servizio Collegamento privato consente di connettersi a diversi servizi PaaS in Azure tramite un endpoint privato. Collegamento privato di Azure trasferisce in pratica i servizi di Azure nella rete virtuale privata. Il traffico tra la rete virtuale e l'istanza di MySQL viaggia attraverso la rete backbone Microsoft.

In alternativa, è possibile usare gli endpoint servizio di rete virtuale per proteggere e limitare l'accesso in rete alle implementazioni di Database di Azure per MySQL. Le regole di rete virtuale rappresentano una funzionalità di sicurezza del firewall che consente di definire se il server di Database di Azure per MySQL accetta le comunicazioni inviate da subnet specifiche nelle reti virtuali.

È anche possibile proteggere il server Database di Azure per MySQL con regole del firewall. Il firewall del server impedisce qualsiasi accesso al server di database finché non vengono specificati i computer autorizzati. Per configurare il firewall, creare regole del firewall che specificano gli intervalli di indirizzi IP accettabili. È possibile creare regole firewall a livello di server.

Come configurare Collegamento privato per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

Come creare e gestire gli endpoint di servizio e le regole di rete virtuale in Database di Azure per MySQL: https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

Come configurare le regole del firewall di Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal

**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Indicazioni**: quando l'istanza di Database di Azure per MySQL è protetta nei confronti di un endpoint privato, è possibile distribuire le macchine virtuali nella stessa rete virtuale. Per ridurre il rischio di esfiltrazione di dati, è possibile usare un gruppo di sicurezza di rete (NSG). Abilitare i log dei flussi NSG e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Come configurare Collegamento privato per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

Come abilitare i log dei flussi NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare e usare Analisi del traffico: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3 Proteggere le applicazioni Web critiche

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 Negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni**: usare Advanced Threat Protection per Database di Azure per MySQL. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Abilitare lo standard Protezione DDoS nelle reti virtuali associate alle istanze di Database di Azure per MySQL per proteggersi da attacchi DDoS. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

Come configurare Advanced Threat Protection per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Come configurare Protezione DDoS: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5 Registrare i pacchetti di rete e i log dei flussi

**Indicazioni**: quando l'istanza di Database di Azure per MySQL è protetta nei confronti di un endpoint privato, è possibile distribuire le macchine virtuali nella stessa rete virtuale. È quindi possibile configurare un gruppo di sicurezza di rete (NSG) per ridurre il rischio di esfiltrazione di dati. Abilitare i log dei flussi NSG e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Come abilitare i log dei flussi NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare e usare Analisi del traffico: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6 Distribuire sistemi di rilevamento intrusioni/prevenzione intrusioni (IDS/IPS) basati sulla rete

**Indicazioni**: usare Advanced Threat Protection per Database di Azure per MySQL. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Come configurare Advanced Threat Protection per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7 Gestire il traffico verso le applicazioni Web

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8 Ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni**: per le risorse che devono accedere alle istanze di Database di Azure per MySQL, usare tag di servizio Rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio SQL.WestUs) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che aggiorna automaticamente il tag in caso di modifica degli indirizzi.

Nota: Database di Azure per MySQL usa i tag di servizio "Microsoft.Sql".

Per altre informazioni sull'uso dei tag di servizio: https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Informazioni sull'uso dei tag di servizio per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet#terminology-and-description

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9 Gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Indicazioni**: definire e implementare configurazioni di sicurezza standard per le impostazioni di rete e le risorse di rete associate alle istanze di Database di Azure per MySQL con Criteri di Azure. Usare gli alias di Criteri di Azure negli spazi dei nomi "Microsoft.DBforMySQL" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle istanze di Database di Azure per MySQL. È anche possibile usare le definizioni di criteri predefinite correlate alle funzionalità di rete o alle istanze di Database di Azure per MySQL, ad esempio:

- Lo standard di protezione DDoS deve essere abilitato

- Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Esempi di Criteri di Azure per la rete: https://docs.microsoft.com/azure/governance/policy/samples/

Come creare un progetto di Azure: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10 Documentare le regole di configurazione del traffico

**Indicazioni**: usare i tag per le risorse correlate alla sicurezza di rete e al flusso del traffico per le istanze di Database di Azure per MySQL per fornire i metadati e l'organizzazione logica.

Usare una qualsiasi delle definizioni di Criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore", per garantire che tutte le risorse vengano create con tag e per notificare l'esistenza di risorse senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni su risorse basate sui rispettivi tag.

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 Usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze di Database di Azure per MySQL. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

Come visualizzare e recuperare gli eventi del log attività di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Come creare avvisi in Monitoraggio di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1 Usare origini di sincronizzazione ora approvate

**Indicazioni**: Microsoft gestisce l'origine ora usata per le risorse di Azure, ad esempio Database di Azure per MySQL per i timestamp nei log.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2 Configurare la gestione dei log di sicurezza centralizzata

**Indicazioni**: abilitare le impostazioni di diagnostica e i log del server, quindi inserire i log per aggregare i dati di sicurezza generati dalle istanze di Database di Azure per MySQL. In Monitoraggio di Azure usare una o più aree di lavoro Log Analytics per eseguire query ed effettuare analisi, quindi usare gli account di archiviazione di Azure per l'archiviazione a lungo termine. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o nelle informazioni di sicurezza e gestione degli eventi di terze parti.

Informazioni sui log del server per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Come eseguire l'onboarding di Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 Abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni**: abilitare le impostazioni di diagnostica nelle istanze di Database di Azure per MySQL per l'accesso ai log di controllo, query lente e metriche MySQL. Verificare di abilitare in modo specifico il log di controllo di MySQL. I log attività, che sono automaticamente disponibili, includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili. È anche possibile abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log alla stessa area di lavoro Log Analytics o allo stesso account di archiviazione.

Informazioni sui log del server per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Come configurare e accedere ai log di query lente per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

Come configurare e accedere ai log di controllo per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

Come configurare le impostazioni di diagnostica per il log attività di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsabilità**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4 Raccogliere i log di sicurezza dai sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="25-configure-security-log-storage-retention"></a>2.5 Configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni**: in Monitoraggio di Azure, per l'area di lavoro Log Analytics usata per conservare i log di Database di Azure per MySQL, impostare il periodo di conservazione in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine o l'archivio.

Come impostare i parametri di conservazione dei log per le aree di lavoro Log Analytics: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

Archiviazione dei log delle risorse in un account di archiviazione di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6 Monitorare ed esaminare i log

**Indicazioni**: analizzare e monitorare i log delle istanze di Database di Azure per MySQL per individuare un comportamento anomalo. Usare Log Analytics di Monitoraggio di Azure per esaminare i log ed eseguire query sui relativi dati. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o nelle informazioni di sicurezza e gestione degli eventi di terze parti.

Come eseguire l'onboarding di Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Per altre informazioni su Log Analytics: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Come eseguire query personalizzate in Monitoraggio di Azure: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 Abilitare gli avvisi per le attività anomale

**Indicazioni**: abilitare Advanced Threat Protection per Database di Azure per MySQL Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

È anche possibile abilitare i log del server e le impostazioni di diagnostica per MySQL e inviare i log a un'area di lavoro Log Analytics. Eseguire l'onboarding dell'area di lavoro Log Analytics in Azure Sentinel perché fornisce una soluzione risposta automatica di orchestrazione della sicurezza (SOAR). In questo modo è possibile creare playbook (soluzioni automatizzate) e usarle per risolvere i problemi di sicurezza.

Come abilitare Advanced Threat Protection per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Informazioni sui log del server per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Come configurare e accedere ai log di query lente per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

Come configurare e accedere ai log di controllo per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

Come configurare le impostazioni di diagnostica per il log attività di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Come eseguire l'onboarding di Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8 Centralizzare la registrazione antimalware

**Indicazioni**: non applicabile; Database di Azure per MySQL non elabora né produce log correlati all'antimalware.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="29-enable-dns-query-logging"></a>2.9 Abilitare la registrazione delle query DNS

**Indicazioni**: non applicabile; Database di Azure per MySQL non elabora né produce log correlati a DNS.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="210-enable-command-line-audit-logging"></a>2.10 Abilitare la registrazione di controllo da riga di comando

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 Gestire un inventario degli account amministrativi

**Indicazioni**: mantenere un inventario degli account utente con accesso amministrativo al piano di controllo (ad esempio il portale di Azure) delle istanze di Database di Azure per MySQL. Mantenere inoltre un inventario degli account amministrativi che hanno accesso al piano dati (all'interno del database stesso) delle istanze di Database di Azure per MySQL. Quando si crea il server MySQL, si forniscono le credenziali per un utente amministratore. Questo amministratore può essere usato per creare altri utenti di MySQL.

Database di Azure per MySQL non supporta il controllo degli accessi in base al ruolo predefinito, ma è possibile creare ruoli personalizzati basati su operazioni specifiche del provider di risorse.

Informazioni sui ruoli personalizzati per la sottoscrizione di Azure: https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

Informazioni sulle operazioni del provider di risorse per Database di Azure per MySQL: https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbformysql

Informazioni sulla gestione dell'accesso per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/concepts-security#access-management

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2 Modificare le password predefinite, ove applicabile

**Indicazioni**: in Azure AD non è previsto il concetto di password predefinite.

Al momento della creazione della risorsa di Database di Azure per MySQL, Azure forza la creazione di un utente amministratore con una password complessa. Dopo aver creato l'istanza di MySQL, tuttavia, è possibile usare il primo account amministratore del server creato per creare altri utenti e concedere loro l'accesso amministrativo. Quando si creano questi account, assicurarsi di configurare una password complessa diversa per ogni account.

Come creare altri account per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-create-users

Come aggiornare la password amministratore: https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3 Usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'uso di account amministrativi dedicati che abbiano accesso alle istanze di Database di Azure per MySQL. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Informazioni sul monitoraggio dell'identità e dell'accesso nel Centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-identity-access

Informazioni su come creare utenti amministratore in Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-create-users

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4 Usare Single Sign-On (SSO) con Azure Active Directory

**Indicazioni**: l'accesso a Database di Azure per MySQL è supportato sia con l'uso di nome utente/password configurato direttamente nel database, sia con l'uso di un'identità Azure Active Directory (AD) e di un token Azure AD per la connessione. Quando si usa un token Azure AD, sono supportati metodi diversi, ad esempio un utente Azure AD, un gruppo Azure AD o un'applicazione Azure AD che si connette al database.

Separatamente, l'accesso al piano di controllo per MySQL è disponibile tramite l'API REST e supporta SSO. Per l'autenticazione, impostare l'intestazione di autorizzazione per le richieste su un token JSON Web ottenuto da Azure Active Directory.

Usare Azure Active Directory per l'autenticazione con Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

Informazioni sull'API REST di Database di Azure per MySQL: https://docs.microsoft.com/rest/api/mysql/

Informazioni su SSO con Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 Usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Indicazioni**: abilitare Azure Active Directory Multi-Factor Authentication (MFA) e seguire le raccomandazioni sulla gestione delle identità e degli accessi nel Centro sicurezza di Azure. Quando si usano token Azure AD per accedere al database, è possibile richiedere l'autenticazione a più fattori per gli accessi al database.

Come abilitare MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Usare Azure Active Directory per l'autenticazione con Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6 Usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni**: usare workstation con accesso con privilegi insieme a Multi-Factor Authentication (MFA) configurato per l'accesso e la configurazione delle risorse di Azure.

Informazioni sulle workstation con accesso con privilegi: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7 Registrare e inviare avvisi per le attività sospette dagli account amministrativi

**Indicazioni**: abilitare Advanced Threat Protection per Database di Azure per MySQL per generare avvisi per le attività sospette.

È inoltre possibile usare Azure Active Directory Privileged Identity Management (PIM) per la generazione di log e avvisi quando nell'ambiente si verifica un'attività sospetta o non sicura.

Usare i rilevamenti di rischi di Azure AD per visualizzare gli avvisi e i report sul comportamento utente rischioso.

Come configurare Advanced Threat Protection per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Come distribuire Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Informazioni sui rilevamenti di rischi di Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8 Gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare le località denominate di accesso condizionale per consentire l'accesso al portale e ad Azure Resource Manager solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

Come configurare località denominate in Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9 Usare Azure Active Directory

**Indicazioni**: usare Azure Active Directory (AD) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

Per l'accesso a Database di Azure per MySQL, è consigliabile usare Azure AD e avvalersi di un token Azure AD per connettersi. Quando si usa un token Azure AD, sono supportati metodi diversi, ad esempio un utente Azure AD, un gruppo Azure AD o un'applicazione Azure AD che si connette al database. 

Le credenziali di Azure AD possono essere usate anche per l'amministrazione a livello di piano di gestione (ad esempio, il portale di Azure) per controllare gli account amministratore MySQL.

Usare Azure Active Directory per l'autenticazione con Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 Controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: esaminare i log di Azure Active Directory per individuare gli account obsoleti, che possono includere quelli con ruoli amministrativi di Database di Azure per MySQL. Usare anche le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali che possono essere usate per accedere a Database di Azure per MySQL e le assegnazioni di ruoli. È consigliabile verificare regolarmente l'accesso degli utenti, ad esempio ogni 90 giorni, per assicurarsi che solo gli utenti appropriati dispongano di accesso continuo.

Informazioni sull'architettura per la generazione di report di Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Come usare le verifiche di accesso alle identità di Azure: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11 Monitorare i tentativi di accesso agli account disattivati

**Indicazioni**: abilitare le impostazioni di diagnostica per Database di Azure per MySQL e Azure Active Directory, inviando tutti i log a un'area di lavoro Log Analytics. Configurare gli avvisi desiderati, ad esempio per i tentativi di autenticazione non riusciti, all'interno di Log Analytics.

Come configurare e accedere ai log di query lente per Database di Azure per MySQL: https://docs.microsoft.com/Azure/mysql/howto-configure-server-logs-in-portal

Come configurare e accedere ai log di controllo per Database di Azure per MySQL: https://docs.microsoft.com/Azure/mysql/howto-configure-audit-logs-portal

Come integrare i log attività di Azure in Monitoraggio di Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsabilità**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12 Avvisare in caso di deviazione dal comportamento di accesso dell'account

**Indicazioni**: abilitare Advanced Threat Protection per Database di Azure per MySQL per generare avvisi per le attività sospette.

Usare le funzionalità di rilevamento di rischi e Identity Protection di Azure Active Directory per configurare risposte automatiche alle azioni sospette rilevate. È possibile abilitare le risposte automatiche tramite Azure Sentinel per implementare le risposte di sicurezza dell'organizzazione.

È anche possibile inserire i log in Azure Sentinel per un'analisi più approfondita.

Come configurare Advanced Threat Protection per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Panoramica di Azure AD Identity Protection: https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

Come visualizzare gli accessi a rischio per Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Come eseguire l'onboarding di Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13 Fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Indicazioni**: non applicabile; Customer Lockbox non è ancora supportato per Database di Azure per MySQL.

Elenco dei servizi Customer Lockbox supportati: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 Gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per semplificare il rilevamento delle istanze di Database di Azure per MySQL o delle risorse correlate che archiviano o elaborano informazioni riservate.

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2 Isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Usare una combinazione di Collegamento privato, endpoint di servizio e/o regole del firewall per isolare e limitare l'accesso in rete alle istanze di Database di Azure per MySQL.

Come creare sottoscrizioni di Azure aggiuntive: https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare gruppi di gestione: https://docs.microsoft.com/azure/governance/management-groups/create

Come configurare Collegamento privato per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link

Come creare e gestire gli endpoint di servizio e le regole di rete virtuale in Database di Azure per MySQL: https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

Come configurare le regole del firewall di Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/concepts-firewall-rules


**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3 Monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Indicazioni**: quando si usano macchine virtuali di Azure per accedere alle istanze di Database di Azure per MySQL, usare Collegamento privato, le configurazioni di rete MySQL, i gruppi di sicurezza di rete e i tag di servizio per attenuare la possibilità di esfiltrazione di dati.

Microsoft gestisce l'infrastruttura sottostante per Database di Azure per MySQL e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

Come attenuare l'esfiltrazione di dati per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link#data-exfiltration-prevention

Informazioni sulla protezione dei dati dei clienti in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 Crittografare tutte le informazioni riservate in transito

**Indicazioni**: il Database di Azure per MySQL supporta la connessione del server MySQL alle applicazioni client tramite il protocollo SSL (Secure Sockets Layer). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione. Nel portale di Azure verificare che l'opzione "Imponi connessione SSL" sia abilitata per impostazione predefinita per tutte le istanze di Database di Azure per MySQL.

Attualmente le versioni di TLS supportate per Database di Azure per MySQL sono TLS 1.0, TLS 1.1 e TLS 1.2.

Come configurare la crittografia in transito per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security

**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 Usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni**: le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per Database di Azure per MySQL. Implementare una soluzione di terze parti, se necessario ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

Informazioni sulla protezione dei dati dei clienti in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsabilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Indicazioni**: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso al piano di controllo di Database di Azure per MySQL, ad esempio il portale di Azure. Per l'accesso al piano dati (all'interno del database stesso), usare query SQL per creare gli utenti e configurare le autorizzazioni utente. Il controllo degli accessi in base al ruolo di Azure non influisce sulle autorizzazioni utente all'interno del database.

Come configurare il controllo degli accessi in base al ruolo di Azure: https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Come configurare l'accesso utente con SQL per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-create-users

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7 Usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per Database di Azure per MySQL e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

Informazioni sulla protezione dei dati dei clienti in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 Crittografare le informazioni riservate inattive

**Indicazioni**: il servizio Database di Azure per MySQL usa il modulo crittografico convalidato FIPS 140-2 per la crittografia dei dati archiviati inattivi. I dati, inclusi i backup, vengono crittografati su disco, ad eccezione dei file temporanei creati durante l'esecuzione di query. Il servizio usa la crittografia AES a 256 bit inclusa nella crittografia di archiviazione di Azure e le chiavi vengono gestite dal sistema. La crittografia dell'archiviazione è sempre attiva e non può essere disabilitata.

La crittografia dei dati con chiavi gestite dal cliente per Database di Azure per MySQL consente di usare Bring Your Own Key (BYOK) per la protezione dei dati inattivi. A questo punto, è necessario richiedere l'accesso per usare questa funzionalità. A tale scopo, contattare:

AskAzureDBforMySQL@service.microsoft.com

Informazioni sulla crittografia dei dati inattivi per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/concepts-security

Come configurare le chiavi gestite dal cliente per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/concepts-data-encryption-mysql

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 Registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Indicazioni**: usare Monitoraggio di Azure con il log attività di Azure per creare avvisi per il momento in cui le modifiche vengono apportate alle istanze di produzione di Database di Azure per MySQL e altre risorse critiche o correlate.

Come creare avvisi per gli eventi del log attività di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Controllo di sicurezza: Gestione della vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 Eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni**: attualmente non disponibile; il Centro sicurezza di Azure non supporta ancora la valutazione della vulnerabilità per Database di Azure per MySQL.

Copertura delle funzionalità per i servizi PaaS di Azure nel Centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/features-paas

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 Distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3 Distribuire una soluzione di gestione delle patch automatizzata per il software di terze parti

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Confrontare le analisi di vulnerabilità back-to-back

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 Usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano Database di Azure per MySQL.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1 Usare l'individuazione di asset in Azure

**Indicazioni**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (incluse le istanze di Database di Azure per MySQL) nelle sottoscrizioni. Verificare di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Come creare query con Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Come visualizzare le sottoscrizioni di Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Informazioni sul controllo degli accessi in base al ruolo di Azure: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2 Gestire i metadati degli asset

**Indicazioni**: applicare i tag alle istanze di Database di Azure per MySQL e ad altre risorse correlate, fornendo i metadati per organizzarle in modo logico in una tassonomia.

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3 Eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle istanze di Database di Azure per MySQL e delle risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Come creare sottoscrizioni di Azure aggiuntive: https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare gruppi di gestione: https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4 Mantenere un inventario delle risorse di Azure approvate e dei titoli software

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo e Azure in generale.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 Monitorare la presenza di risorse di Azure non approvate

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare anche Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare query con Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6 Monitorare le applicazioni software non approvate nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7 Rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo e Azure in generale.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="68-use-only-approved-applications"></a>6.8 Usare solo applicazioni approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="69-use-only-approved-azure-services"></a>6.9 Usare solo servizi di Azure approvati

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come negare un tipo di risorsa specifico con Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="610-implement-approved-application-list"></a>6.10 Implementare l'elenco di applicazioni approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11 Limitare la capacità degli utenti di interagire con Azure Resource Manager tramite script

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure. Ciò può impedire la creazione e le modifiche alle risorse all'interno di un ambiente ad elevata sicurezza, ad esempio le istanze di Database di Azure per MySQL contenenti informazioni riservate.

Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12 Limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13 Separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Controllo di sicurezza: configurazione sicura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 Definire configurazioni sicure per tutte le risorse di Azure

**Indicazioni**: definire e implementare configurazioni di sicurezza standard per le istanze di Database di Azure per MySQL con Criteri di Azure. Usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.DBforMySQL" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle istanze di Database di Azure per MySQL. È anche possibile usare le definizioni di criteri predefinite correlate alle istanze di Database di Azure per MySQL, ad esempio:

Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL

Come visualizzare gli alias di Criteri di Azure disponibili: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2 Definire configurazioni sicure del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 Garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: usare criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Informazioni sugli effetti di Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4 Garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 Archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni**: se si usano definizioni di Criteri di Azure personalizzate per le istanze di Database di Azure per MySQL e le risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

Come archiviare il codice in Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentazione di Azure Repos: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6 Archiviare in modo sicuro immagini personalizzate del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="77-deploy-system-configuration-management-tools"></a>7.7 Distribuire strumenti di gestione della configurazione di sistema

**Indicazioni**: usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft. DBforMySQL" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare anche un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8 Distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 Implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

**Indicazioni**: usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft. DBforMySQL" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [deploy if not exist] per applicare automaticamente le configurazioni per le istanze di Database di Azure per MySQL e le risorse correlate.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 Implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="711-manage-azure-secrets-securely"></a>7.11 Gestire i segreti di Azure in modo sicuro

**Indicazioni**: per le macchine virtuali di Azure o le applicazioni Web in esecuzione nel Servizio app di Azure usato per accedere alle istanze di Database di Azure per MySQL, usare l'identità del servizio gestita insieme ad Azure Key Vault per semplificare e proteggere la gestione dei segreti di Database di Azure per MySQL. Verificare che l'eliminazione temporanea di Azure Key Vault sia abilitata.

Come eseguire l'integrazione con identità gestite di Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Come creare un'istanza di Key Vault: https://docs.microsoft.com/azure/key-vault/quick-create-portal

Come fornire l'autenticazione di Key Vault con un'identità gestita: https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12 Gestire le identità in modo sicuro e automatico

**Indicazioni**: l'istanza di Database di Azure per MySQL supporta l'autenticazione Azure Active Directory per accedere ai database.  Durante la creazione dell'istanza di Database di Azure per MySQL, è possibile fornire le credenziali per un utente amministratore. Questo amministratore può essere usato per creare altri utenti di database.  

Per Macchine virtuali di Azure o le applicazioni Web in esecuzione nel Servizio app di Azure usato per accedere alle istanze di Database di Azure per MySQL, usare l'identità del servizio gestita insieme ad Azure Key Vault per archiviare e recuperare le credenziali per tale istanza. Verificare che l'eliminazione temporanea di Azure Key Vault sia abilitata.

Usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (AD). Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice.

Come configurare le identità gestite: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Come eseguire l'integrazione con identità gestite di Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13 Eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

Come impostare Credential Scanner: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Controllo di sicurezza: difesa da malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 Usare software antimalware gestito in modo centralizzato

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

Il software antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio Database di Azure per MySQL, ma non viene eseguito sui contenuti del cliente.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 Eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Indicazioni**: il software antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio Database di Azure per MySQL, ma non viene eseguito sui contenuti del cliente.

Eseguire l'analisi preliminare del contenuto da caricare in risorse di Azure non di calcolo, ad esempio Servizio app, Data Lake Storage, Archiviazione BLOB, Database di Azure per MySQL e così via. Microsoft non è in grado di accedere ai dati in tali istanze.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 Verificare che le firme e il software antimalware siano aggiornati

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

il software antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio Database di Azure per MySQL, ma non viene eseguito sui contenuti del cliente.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: N/D

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1 Garantire l'esecuzione regolare di backup automatizzati

**Indicazioni**: Database di Azure per MySQL esegue il backup dei file di dati e del log delle transazioni. A seconda delle massime dimensioni di archiviazione supportate, vengono eseguiti backup completi e differenziali (server di archiviazione da 4 TB al massimo) o backup di snapshot (server di archiviazione fino a 16 TB al massimo). Questi backup consentono di ripristinare un server a qualsiasi momento specifico all'interno del periodo di conservazione dei backup configurato. Il periodo di conservazione dei backup predefinito è di sette giorni. Facoltativamente, è possibile configurare fino a 35 giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit.

Informazioni sui backup per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/concepts-backup

Informazioni sulla configurazione iniziale di Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/tutorial-design-database-using-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 Eseguire backup completi del sistema e il backup di tutte le chiavi gestite dal cliente

**Indicazioni**: Database di Azure per MySQL crea automaticamente backup del server e li inserisce in un archivio con ridondanza locale o geografica, in base alla scelta dell'utente. I backup possono essere usati per ripristinare il server a un momento specifico. Il backup e il ripristino sono una parte essenziale di qualsiasi strategia di continuità aziendale, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. 

Se si usa Azure Key Vault per archiviare le credenziali per le istanze di Database di Azure per MySQL, assicurarsi che i backup automatizzati delle chiavi siano eseguiti con regolarità. 

Informazioni sui backup per Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/howto-restore-server-portal 

Come eseguire il backup di chiavi di Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3 Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni**: In Database di Azure per MySQL, l'esecuzione di un ripristino crea un nuovo server dai backup del server originale. Sono disponibili due tipi di ripristino: ripristino temporizzato e ripristino geografico. Il ripristino temporizzato è disponibile con entrambe le opzioni di ridondanza per il backup e crea un nuovo server nella stessa area del server originale. Il ripristino geografico è disponibile solo se il server è stato configurato per l'archiviazione con ridondanza geografica e consente di ripristinare il server in un'area diversa.

Il tempo stimato per il ripristino dipende da diversi fattori, tra cui le dimensioni dei database, le dimensioni dei log delle transazioni, la larghezza di banda di rete e il numero totale di database ripristinati contemporaneamente nella stessa area. Il tempo di recupero di solito è inferiore a 12 ore.

Testare periodicamente il ripristino delle istanze di Database di Azure per MySQL.

Informazioni sul backup e sul ripristino in Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/concepts-backup

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 Garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni**: Database di Azure per MySQL crea backup completi, differenziali e del log delle transazioni. Questi backup consentono di ripristinare un server a qualsiasi momento specifico all'interno del periodo di conservazione dei backup configurato. Il periodo di conservazione dei backup predefinito è di sette giorni. Facoltativamente, è possibile configurare fino a 35 giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit. Verificare che l'eliminazione temporanea di Azure Key Vault sia abilitata.

Informazioni sul backup e sul ripristino in Database di Azure per MySQL: https://docs.microsoft.com/azure/mysql/concepts-backup

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1 Creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia di un evento imprevisto di Microsoft Security Response Center: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un proprio piano di risposta agli eventi imprevisti: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione, e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3 Testare le procedure di risposta per la sicurezza

**Indicazioni**: con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 Specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente.  Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

Come impostare il contatto di sicurezza del Centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5 Incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi in Sentinel.

Come configurare l'esportazione continua: https://docs.microsoft.com/azure/security-center/continuous-export

Come trasmettere gli avvisi in Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6 Automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

Come configurare l'automazione del flusso di lavoro e App per la logica: https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Controllo di sicurezza: test di penetrazione ed esercizi Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1 Eseguire con regolarità test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici in termini di sicurezza entro 60 giorni

**Indicazioni**: seguire le regole di engagement Microsoft per assicurarsi che i propri test di penetrazione non violino i criteri Microsoft all'indirizzo https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

È possibile trovare altre informazioni sulla strategia Microsoft e sull'esecuzione dei test di penetrazione Red Teaming sull'infrastruttura cloud gestita da Microsoft, sui servizi e sulle applicazioni qui: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
