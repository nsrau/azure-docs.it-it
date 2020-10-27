---
title: Baseline della sicurezza di Azure per database di Azure per MariaDB
description: Baseline della sicurezza di Azure per database di Azure per MariaDB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f6071a3675b1ad6aa5d49395fb9050c941af9be0
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92532748"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Baseline della sicurezza di Azure per database di Azure per MariaDB

La linea di base di sicurezza di Azure per il database di Azure per MariaDB contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](../security/benchmarks/overview.md), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere [Panoramica delle baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Controllo di sicurezza: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

**Linee guida** : configurare il collegamento privato per il database di Azure per MariaDB con endpoint privati. Il servizio Collegamento privato consente di connettersi a diversi servizi PaaS in Azure tramite un endpoint privato. Collegamento privato di Azure in pratica porta i servizi di Azure all'interno della rete virtuale privata. Il traffico tra la rete virtuale e l'istanza di MariaDB viaggia alla rete dorsale Microsoft.

In alternativa, è possibile usare gli endpoint del servizio rete virtuale per proteggere e limitare l'accesso di rete al database di Azure per le implementazioni di MariaDB. Le regole della rete virtuale sono una funzionalità di sicurezza del firewall che controlla se il database di Azure per MariaDB accetta le comunicazioni inviate da determinate subnet nelle reti virtuali.

È anche possibile proteggere il database di Azure per MariaDB con le regole del firewall. Il firewall del server impedisce qualsiasi accesso al server di database finché non si specificano i computer autorizzati. Per configurare il firewall, creare regole del firewall che specificano gli intervalli di indirizzi IP accettabili. È possibile creare regole firewall a livello di server.

Come configurare il collegamento privato per il database di Azure per MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Come creare e gestire gli endpoint di servizio VNet e le regole VNet nel database di Azure per il server MariaDB: https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Come configurare le regole del firewall di database di Azure per MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Linee guida** : quando il database di Azure per il server MariaDB è protetto a un endpoint privato, è possibile distribuire le macchine virtuali nella stessa rete virtuale. Per ridurre il rischio di esfiltrazione di dati, è possibile usare un gruppo di sicurezza di rete (NSG). Abilitare i log dei flussi NSG e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Come configurare il collegamento privato per il database di Azure per MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Come abilitare i log dei flussi di NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal come abilitare e usare analisi del traffico: https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Indicazioni** : non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida** : usare Advanced Threat Protection per database di Azure per MariaDB. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Abilitare protezione DDoS standard nelle reti virtuali associate al database di Azure per le istanze di MariaDB per la protezione da attacchi DDoS. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

Come configurare Advanced Threat Protection per il database di Azure per MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Come configurare Protezione DDoS: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection



**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: registrare i pacchetti di rete e i log dei flussi

**Linee guida** : quando il database di Azure per il server MariaDB è protetto a un endpoint privato, è possibile distribuire le macchine virtuali nella stessa rete virtuale. È quindi possibile configurare un gruppo di sicurezza di rete (NSG) per ridurre il rischio di esfiltrazione di dati. Abilitare i log dei flussi NSG e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Come abilitare i log dei flussi di NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal come abilitare e usare analisi del traffico: https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: distribuire sistemi di rilevamento intrusioni/prevenzione intrusioni (IDS/IPS) basati sulla rete

**Linee guida** : usare Advanced Threat Protection per database di Azure per MariaDB. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.
Come configurare Advanced Threat Protection per il database di Azure per MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal


**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Indicazioni** : non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni** : per le risorse che richiedono l'accesso alle istanze del database di Azure per MariaDB, usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, SQL.WestUs) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che aggiorna automaticamente il tag in caso di modifica degli indirizzi.
Nota: per database di Azure per MariaDB viene usato il tag di servizio "Microsoft. SQL".

Per altre informazioni sull'uso dei tag di servizio, https://docs.microsoft.com/azure/virtual-network/service-tags-overview comprendere l'uso dei tag di servizio per il database di Azure per MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description



**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida** : definire e implementare configurazioni di sicurezza standard per le impostazioni di rete e le risorse di rete associate al database di Azure per le istanze di MariaDB con criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. DBforMariaDB" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete del database di Azure per le istanze di MariaDB. È anche possibile usare le definizioni di criteri predefinite correlate alla rete o al database di Azure per le istanze di MariaDB, ad esempio:

- Lo standard di protezione DDoS deve essere abilitato

- L'endpoint privato deve essere abilitato per i server MariaDB

- I server MariaDB devono usare un endpoint servizio di rete virtuale

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Esempi di Criteri di Azure per la rete: https://docs.microsoft.com/azure/governance/policy/samples/

Come creare un progetto di Azure: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida** : usare i tag per le risorse correlate alla sicurezza di rete e al flusso del traffico per le istanze di MariaDB per fornire i metadati e l'organizzazione logica.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida** : usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate al database di Azure per le istanze MariaDB. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.
Come visualizzare e recuperare gli eventi del log attività di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view come creare avvisi in monitoraggio di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Linee guida** : Microsoft gestisce l'origine dell'ora usata per le risorse di Azure, ad esempio database di Azure per MariaDB per i timestamp nei log.


**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida** : abilitare le impostazioni di diagnostica e i log del server e i log di inserimento per aggregare i dati di sicurezza generati dal database di Azure per le istanze MariaDB. In Monitoraggio di Azure usare una o più aree di lavoro Log Analytics per eseguire query ed effettuare analisi, quindi usare gli account di archiviazione di Azure per l'archiviazione/memorizzazione a lungo termine. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.
Come configurare e accedere ai log del server per il database di Azure per MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Come configurare e accedere ai log di controllo per il database di Azure per MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal How to Board Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard



**Monitoraggio del Centro sicurezza di Azure** : Non disponibile

**Responsabilità** : Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida** : abilitare le impostazioni di diagnostica nel database di Azure per le istanze di MariaDB per l'accesso ai log di controllo, sicurezza e diagnostica. Assicurarsi di abilitare in modo specifico il registro di controllo di MariaDB. I log attività, che sono automaticamente disponibili, includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili. È anche possibile abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log alla stessa area di lavoro Log Analytics o allo stesso account di archiviazione.

Come configurare e accedere ai log del server per il database di Azure per MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-server-logs come configurare e accedere ai log di controllo per il database di Azure per MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal come configurare le impostazioni di diagnostica per il log attività di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy



**Monitoraggio del Centro sicurezza di Azure** : Non disponibile

**Responsabilità** : Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida** : all'interno di monitoraggio di Azure, per l'area di lavoro log Analytics usata per conservare i log del database di Azure per MariaDB, impostare il periodo di conservazione in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione/memorizzazione a lungo termine.
Come impostare i parametri di conservazione del log per le aree di lavoro Log Analytics: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period archiviazione dei log delle risorse in un account di archiviazione di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage



**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida** : analizzare e monitorare i log delle istanze di MariaDB per un comportamento anomalo. Usare l'area di lavoro Log Analytics di monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

Come eseguire l'onboarding di Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Per ulteriori informazioni sull'area di lavoro Log Analytics: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Come eseguire query personalizzate in Monitoraggio di Azure: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: abilitare gli avvisi per le attività anomale

**Linee guida** : abilitare Advanced Threat Protection per MariaDB. Advanced Threat Protection per database di Azure per MariaDB rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database.

Inoltre, è possibile abilitare i log del server e le impostazioni di diagnostica per MariaDB e inviare i log a un'area di lavoro Log Analytics. Eseguire l'onboarding dell'area di lavoro Log Analytics in Azure Sentinel perché fornisce una soluzione SOAR (Security Orchestration Automated Response). In questo modo è possibile creare playbook (soluzioni automatizzate) e usarli per risolvere i problemi di sicurezza.

Come abilitare Advanced Threat Protection per MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Come configurare e accedere ai log del server per MariDB: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Come configurare e accedere ai log di controllo per MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Come eseguire l'onboarding di Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida** : N/A; MariaDB non elabora o produce log correlati all'anti-malware.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida** : N/A; MariaDB non elabora o produce log correlati a DNS.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Linee guida** : N/A; il benchmark è progettato per le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: mantenere un inventario degli account amministrativi

**Linee guida** : gestire un inventario degli account utente con accesso amministrativo al piano di gestione (portale di Azure/Azure Resource Manager) delle istanze di MariaDB. Inoltre, mantenere un inventario degli account amministrativi che hanno accesso al piano dati delle istanze di MariaDB. Quando si crea il server MariaDB, si forniscono le credenziali per un utente amministratore. Questo amministratore può essere usato per creare altri utenti MariaDB.

Informazioni sulla gestione degli accessi per MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-security#access-management

Informazioni sui ruoli predefiniti di Azure per le sottoscrizioni di Azure: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles


**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida** : Azure Active Directory non ha il concetto di password predefinite.

Al momento della creazione della risorsa MariaDB, Azure forza la creazione di un utente amministratore con una password complessa. Tuttavia, una volta creata l'istanza di MariaDB, è possibile usare il primo account amministratore del server creato per creare altri utenti e concedere loro l'accesso amministrativo. Quando si creano questi account, assicurarsi di configurare una password complessa diversa per ogni account.

Come creare account aggiuntivi per MariaDB: https://docs.microsoft.com/azure/mariadb/howto-create-users


**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Linee guida** : creare procedure operative standard per l'uso di account amministrativi dedicati che possono accedere alle istanze di MariaDB. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Informazioni sul monitoraggio dell'identità e dell'accesso nel Centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida** : l'accesso del piano dati a MariaDB è controllato dalle identità archiviate nel database e non supporta SSO. L'accesso al piano di controllo per MariaDB è disponibile tramite l'API REST e supporta SSO. Per l'autenticazione, impostare l'intestazione di autorizzazione per le richieste su un token JSON Web che è possibile ottenere da Azure Active Directory.

Informazioni sull'API REST di database di Azure per MariaDB: https://docs.microsoft.com/rest/api/mariadb/

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Indicazioni** : abilitare Azure AD MFA e seguire le raccomandazioni sulla gestione delle identità e degli accessi nel Centro sicurezza di Azure.

Come abilitare MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni** : usare workstation con accesso con privilegi insieme a Multi-Factor Authentication (MFA) configurato per l'accesso e la configurazione delle risorse di Azure.

Informazioni sulle workstation con accesso con privilegi: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: registrare e inviare avvisi per le attività sospette dagli account amministrativi

**Linee guida** : abilitare Advanced Threat Protection per MariaDB per generare avvisi per attività sospette.

È inoltre possibile usare Azure Active Directory Privileged Identity Management (PIM) per la generazione di log e avvisi quando nell'ambiente si verifica un'attività sospetta o non sicura. Usare i rilevamenti di rischi di Azure AD per visualizzare gli avvisi e i report sul comportamento utente rischioso.

Come configurare Advanced Threat Protection per MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Come distribuire Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Informazioni sui rilevamenti dei rischi di Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Linee guida** : usare i percorsi denominati di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche per limitare l'accesso alle risorse di Azure, ad esempio MariaDB.

Come configurare località denominate in Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida** : usare Azure Active Directory (AAD) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. AAD inoltre sale, hash e archivia in modo sicuro le credenziali utente.

Non è possibile usare l'autenticazione Azure AD per l'accesso diretto al piano dati MariaDB, tuttavia, Azure AD credenziali possono essere usate per l'amministrazione a livello del piano di gestione (ad esempio, il portale di Azure) per controllare gli account amministratore di MariaDB.

Come aggiornare la password amministratore per MariaDB: https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

Materiale sussidiario **: esaminare** i registri Azure Active Directory per individuare gli account obsoleti che possono includere quelli con ruoli amministrativi MariaDB. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali che possono essere usate per accedere a MariaDB e le assegnazioni di ruolo. È consigliabile verificare regolarmente l'accesso degli utenti, ad esempio ogni 90 giorni, per assicurarsi che solo gli utenti appropriati dispongano di accesso continuo.

Informazioni sull'architettura per la generazione di report di Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Come usare le verifiche di accesso alle identità di Azure: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: monitorare i tentativi di accesso agli account disattivati

**Linee guida** : abilitare le impostazioni di diagnostica per MariaDB e Azure Active Directory, inviando tutti i log a un'area di lavoro di log Analytics. Configurare gli avvisi desiderati, ad esempio i tentativi di autenticazione non riusciti, all'interno Log Analytics area di lavoro.

Come configurare e accedere ai log del server per MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Come configurare e accedere ai log di controllo per MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Come integrare i log attività di Azure in Monitoraggio di Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoraggio del Centro sicurezza di Azure** : Non disponibile

**Responsabilità** : Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Linee guida** : abilitare Advanced Threat Protection per MariaDB. Advanced Threat Protection per database di Azure per MariaDB rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database.

Usare le funzionalità di rilevamento di rischi e Identity Protection di Azure Active Directory per configurare risposte automatiche alle azioni sospette rilevate. È possibile abilitare le risposte automatiche tramite Azure Sentinel per implementare le risposte di sicurezza dell'organizzazione.

Come abilitare Advanced Threat Protection per MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Come configurare e abilitare i criteri di rischio di Identity Protection: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Come visualizzare gli accessi rischiosi per Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Come eseguire l'onboarding di Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure** : Non disponibile

**Responsabilità** : Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida** : non applicabile; Customer Lockbox non ancora supportata per il database di Azure per MariaDB.

Elenco dei servizi Customer Lockbox supportati: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: mantenere un inventario delle informazioni riservate

**Indicazioni** : usare i tag per facilitare il rilevamento delle istanze di database di Azure per MariaDB o risorse correlate che archiviano o elaborano informazioni riservate.

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni** : implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Usare una combinazione di regole del firewall di collegamento privato, endpoint di servizio e/o MariaDB per isolare e limitare l'accesso di rete alle istanze di MariaDB.

Come creare sottoscrizioni di Azure aggiuntive: https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare gruppi di gestione: https://docs.microsoft.com/azure/governance/management-groups/create

Come configurare il collegamento privato per il database di Azure per MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Come configurare gli endpoint di servizio per il database di Azure per MariaDB: https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Come configurare le regole del firewall per il database di Azure per MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Monitoraggio del Centro sicurezza di Azure** : Non disponibile

**Responsabilità** : Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida** : quando si usano macchine virtuali di Azure per accedere alle istanze di MariaDB, usare il collegamento privato, le configurazioni di rete MariaDB, i gruppi di sicurezza di rete e i tag di servizio per attenuare la possibilità di dati exfiltration.

Microsoft gestisce l'infrastruttura sottostante per MariaDB e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

Come attenuare i dati exfiltration per il database di Azure per MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Informazioni sulla protezione dei dati dei clienti in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida** : database di Azure per MariaDB supporta la connessione del database di Azure per il server MariaDB alle applicazioni client tramite Transport Layer Security (TLS), precedentemente noto come Secure Sockets Layer (SSL). L'imposizione di connessioni TSL tra il server di database e le applicazioni client garantisce la protezione da attacchi "man-in-the-middle" tramite la crittografia del flusso di dati tra il server e l'applicazione. Nel portale di Azure assicurarsi che "applica connessione SSL" sia abilitato per tutte le istanze di MariaDB.

Come configurare la crittografia in transito per MariaDB: https://docs.microsoft.com/azure/mariadb/howto-configure-ssl

**Monitoraggio del Centro sicurezza di Azure** : Non disponibile

**Responsabilità** : Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida** : identificazione dei dati, classificazione e funzionalità di prevenzione della perdita non ancora disponibili per il database di Azure per MariaDB. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

Informazioni sulla protezione dei dati dei clienti in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure** : Non disponibile

**Responsabilità** : Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Linee guida** : usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per controllare l'accesso al database di Azure per il piano di gestione di MariaDB (portale di Azure/Azure Resource Manager). Per l'accesso al piano dati (all'interno del database stesso), usare query SQL per creare gli utenti e configurare le autorizzazioni utente.

Come configurare RBAC di Azure: https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Come configurare l'accesso utente con SQL per MariaDB: https://docs.microsoft.com/azure/mariadb/howto-create-users

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida** : N/A; Questa guida è destinata alle risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per MariaDB e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

Informazioni sulla protezione dei dati dei clienti in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida** : il servizio database di Azure per MariaDB usa il modulo di crittografia FIPS 140-2 convalidato per la crittografia di archiviazione dei dati inattivi. I dati, inclusi i backup, vengono crittografati su disco, ad eccezione dei file temporanei creati durante l'esecuzione di query. Il servizio usa la crittografia AES a 256 bit inclusa nella crittografia di archiviazione di Azure e le chiavi vengono gestite dal sistema. La crittografia dell'archiviazione è sempre attiva e non può essere disabilitata.

Informazioni sul resto della crittografia per MariaDB: https://docs.microsoft.com/azure/mariadb/concepts-security

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida** : usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle istanze di produzione del database di Azure per MariaDB e altre risorse critiche o correlate.

Come creare avvisi per gli eventi del log attività di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee guida** : attualmente non disponibile; Il Centro sicurezza di Azure non supporta ancora la valutazione della vulnerabilità per il server di database di Azure per MariaDB.


**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: distribuire una soluzione di gestione delle patch automatizzata per il software di terze parti

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Linee guida** : Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano il database di Azure per il server MariaDB.


**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Microsoft

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: usare l'individuazione di asset in Azure

**Linee guida** : usare Azure Resource Graph per eseguire query e individuare tutte le risorse (incluso il database di Azure per il server MariaDB) nelle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Come creare query con Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Come visualizzare le sottoscrizioni di Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription

Informazioni sul controllo degli accessi in base al ruolo di Azure: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee guida** : applicare tag al database di Azure per il server MariaDB e ad altre risorse correlate, fornendo metadati per organizzarle in modo logico in una tassonomia.

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni** : usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e monitorare il database di Azure per il server MariaDB e le risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Come creare sottoscrizioni di Azure aggiuntive: https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare gruppi di gestione: https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: mantenere un inventario delle risorse di Azure approvate e dei titoli software

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo e Azure in generale.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare le risorse di Azure non approvate

**Indicazioni** : usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare anche Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare query con Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo e Azure in generale.



**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni** : usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come negare un tipo di risorsa specifico con Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="610-implement-approved-application-list"></a>6.10: implementare l'elenco di applicazioni approvate

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="611-divlimit-users-ability-to-interact-with-azure-resources-manager-via-scriptsdiv"></a>6.11: <div>Limitare la capacità degli utenti di interagire con gestione risorse di Azure tramite script</div>

**Indicazioni** : usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure. Questo può impedire la creazione e le modifiche alle risorse all'interno di un ambiente con sicurezza elevata, ad esempio il database di Azure per il server MariaDB che contiene informazioni riservate.

Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni** : non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Controllo di sicurezza: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: definire configurazioni sicure per tutte le risorse di Azure

**Linee guida** : definire e implementare configurazioni di sicurezza standard per le istanze di database di Azure per MariaDB con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. DBforMariaDB" per creare criteri personalizzati per controllare o applicare la configurazione di rete del database di Azure per i server MariaDB. È anche possibile usare le definizioni di criteri predefinite correlate al database di Azure per i server MariaDB, ad esempio:

- Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MariaDB

Come visualizzare gli alias di Criteri di Azure disponibili: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni** : usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Informazioni sugli effetti di Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida** : se si usano definizioni di criteri di Azure personalizzate per il database di Azure per i server MariaDB e le risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

Come archiviare il codice in Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true

Documentazione di Azure Repos: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops&preserve-view=true

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: distribuire strumenti di gestione della configurazione di sistema

**Linee guida** : usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. DBforMariaDB" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

**Linee guida** : usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. DBforMariaDB" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [Deploy if not exist] per applicare automaticamente le configurazioni per il database di Azure per le istanze di MariaDB e le risorse correlate.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida** : per le macchine virtuali o le applicazioni Web di Azure in esecuzione nel servizio app Azure usato per accedere al database di Azure per i server MariaDB, usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere il database di Azure per la gestione dei segreti del server MariaDB. Assicurarsi che l'eliminazione temporanea di Azure Key Vault sia abilitata.

Come eseguire l'integrazione con identità gestite di Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Come creare un insieme di credenziali delle chiavi: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Come eseguire l'autenticazione a Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Come assegnare un criterio di accesso Key Vault: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida** : il server di database di Azure per MariaDB attualmente non supporta l'autenticazione Azure Active Directory per accedere ai database.  Quando si crea il database di Azure per il server MariaDB, si forniscono le credenziali per un utente amministratore. Questo amministratore può essere usato per creare altri utenti MariaDB.  

Per le macchine virtuali o le applicazioni Web di Azure in esecuzione nel servizio app Azure usato per accedere al database di Azure per il server MariaDB, usare identità del servizio gestita insieme ai Azure Key Vault per archiviare e recuperare le credenziali per il server di database di Azure per MariaDB.  Assicurarsi che l'eliminazione temporanea di Azure Key Vault sia abilitata.

Usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (AD). Le identità gestite consentono di eseguire l'autenticazione per qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire credenziali nel codice. Come configurare le identità gestite: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm . Come eseguire l'integrazione con le identità gestite di Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity .



**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni** : implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 

Come impostare Credential Scanner: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Controllo di sicurezza: difesa da malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: usare software antimalware gestito in modo centralizzato

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

Il software antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Servizio app di Azure), ma non viene eseguito sui contenuti del cliente.


**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida** : Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio il database di Azure per il server MariaDB, ma non viene eseguito sui contenuti dei clienti.

Pre-analizza i contenuti caricati in risorse di Azure non di calcolo, ad esempio servizio app, Data Lake Storage, archiviazione BLOB, database di Azure per il server MariaDB e così via. Microsoft non è in grado di accedere ai dati in tali istanze.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: assicurarsi che le firme e il software antimalware siano aggiornati

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio il database di Azure per il server MariaDB, ma non viene eseguito sui contenuti dei clienti.


**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Linee guida** : database di Azure per MariaDB accetta backup completi, differenziali e del log delle transazioni.  Database di Azure per MariaDB crea automaticamente backup del server e li archivia in un archivio con ridondanza locale o geografica configurato dall'utente. I backup possono essere usati per ripristinare il server a un momento specifico. Il backup e il ripristino sono una parte essenziale di qualsiasi strategia di continuità aziendale, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale.  Il periodo di conservazione dei backup predefinito è di sette giorni. Facoltativamente, è possibile configurare fino a 35 giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit.

Informazioni sui backup per MariaDB:  https://docs.microsoft.com/azure/mariadb/concepts-backup

Comprendere la configurazione iniziale di MariaDB: https://docs.microsoft.com/azure/mariadb/tutorial-design-database-using-portal



**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Condiviso

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: eseguire backup completi del sistema e il backup di tutte le chiavi gestite dal cliente

**Linee guida** : database di Azure per MariaDB crea automaticamente backup del server e li archivia in un archivio con ridondanza locale o con ridondanza geografica configurato dall'utente. I backup possono essere usati per ripristinare il server a un momento specifico.  Il backup e il ripristino sono una parte essenziale di qualsiasi strategia di continuità aziendale, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale.

Se si usa Key Vault per la crittografia dei dati sul lato client per i dati archiviati nel server MariaDB, assicurarsi che i backup automatici delle chiavi siano regolari.

Informazioni sui backup per MariaDB:  https://docs.microsoft.com/azure/mariadb/concepts-backup

Come eseguire il backup di chiavi di Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Condiviso

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida** : nel database di Azure per MariaDB eseguire un ripristino dai backup del server originale per i test periodici dei backup. Sono disponibili due tipi di ripristino: ripristino temporizzato e ripristino geografico. Il ripristino temporizzato è disponibile con entrambe le opzioni di ridondanza per il backup e crea un nuovo server nella stessa area del server originario. Il ripristino geografico è disponibile solo se il server è stato configurato per l'archiviazione con ridondanza geografica e consente di ripristinare il server in un'area diversa.

Il tempo stimato per il ripristino dipende da diversi fattori, tra cui le dimensioni dei database, le dimensioni dei log delle transazioni, la larghezza di banda di rete e il numero totale di database ripristinati contemporaneamente nella stessa area. Il tempo di recupero di solito è inferiore a 12 ore.

Informazioni su backup e ripristino nel database di Azure per MariaDB:  https://docs.microsoft.com/azure/mariadb/concepts-backup#restore


**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida** : database di Azure per MariaDB accetta backup completi, differenziali e del log delle transazioni. Questi backup consentono di ripristinare un server a qualsiasi momento specifico all'interno del periodo di conservazione dei backup configurato. Il periodo di conservazione dei backup predefinito è di sette giorni. Facoltativamente, è possibile configurare fino a 35 giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit.

Informazioni su backup e ripristino nel database di Azure per MariaDB:  https://docs.microsoft.com/azure/mariadb/concepts-backup


**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare una guida per rispondere agli eventi imprevisti

**Indicazioni** : creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/
- Anatomia di un evento imprevisto di Microsoft Security Response Center: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/
- Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un proprio piano di risposta agli eventi imprevisti: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final 

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni** : il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso. 

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione, tramite i tag e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- Avvisi di sicurezza nel centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

- Usare i tag per organizzare le risorse di Azure: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee guida** : eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare per proteggere le risorse di Azure. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- Fare riferimento alla pubblicazione NIST: Guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni** : le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- Come impostare il contatto di sicurezza del Centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni** : esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per contribuire a individuare i rischi per le risorse di Azure. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- Come configurare l'esportazione continua: https://docs.microsoft.com/azure/security-center/continuous-export
- Come trasmettere gli avvisi in Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida** : usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nei consigli per proteggere le risorse di Azure.
    

Come configurare l'automazione del flusso di lavoro e le app per la logica: https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Controllo di sicurezza: test di penetrazione ed esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: eseguire con regolarità test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici in termini di sicurezza entro 60 giorni

**Indicazioni** : seguire le regole Microsoft per assicurarsi che i propri test di penetrazione non violino i criteri Microsoft: 

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

È possibile trovare altre informazioni sulla strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft, qui:  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e


**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Benchmark di sicurezza di Azure](../security/benchmarks/overview.md)
- Vedere altre informazioni sulle [baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)