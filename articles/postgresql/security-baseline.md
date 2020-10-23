---
title: Baseline della sicurezza di Azure per database di Azure per PostgreSQL-server singolo
description: "Il database di Azure per PostgreSQL: la linea di base di sicurezza a server singolo fornisce linee guida e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure."
author: msmbaldwin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 828be8e790803e53b0434a71fb32a29a4a0c17bc
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428072"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql---single-server"></a>Baseline della sicurezza di Azure per database di Azure per PostgreSQL-server singolo

La linea di base sicurezza di Azure per database di Azure per PostgreSQL-server singolo contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](../security/benchmarks/overview.md), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere [Panoramica delle baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Indicazioni**: configurare Collegamento privato per Database di Azure per PostgreSQL con endpoint privati. Il servizio Collegamento privato consente di connettersi a diversi servizi PaaS in Azure tramite un endpoint privato. Collegamento privato di Azure in pratica porta i servizi di Azure all'interno della rete virtuale privata. Il traffico tra la rete virtuale e l'istanza di PostgreSQL attraversa la rete backbone Microsoft.

In alternativa, è possibile usare gli endpoint servizio di rete virtuale per proteggere e limitare l'accesso in rete alle implementazioni di Database di Azure per PostgreSQL. Le regole di rete virtuale rappresentano una funzionalità di sicurezza del firewall che consente di definire se il server Database di Azure per PostgreSQL accetta le comunicazioni inviate da subnet specifiche nelle reti virtuali.

È anche possibile proteggere il server Database di Azure per PostgreSQL con regole del firewall. Il firewall del server impedisce qualsiasi accesso al server di database finché non si specificano i computer autorizzati. Per configurare il firewall, creare regole del firewall che specificano gli intervalli di indirizzi IP accettabili. È possibile creare regole firewall a livello di server.

- [Come configurare un collegamento privato per database di Azure per PostgreSQL](howto-configure-privatelink-portal.md)

- [Come creare e gestire endpoint di servizio VNet e regole VNet in database di Azure per PostgreSQL](howto-manage-vnet-using-portal.md)

- [Come configurare le regole del firewall di database di Azure per PostgreSQL](howto-manage-firewall-using-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Indicazioni**: quando l'istanza di Database di Azure per PostgreSQL è protetta per un endpoint privato, è possibile distribuire le macchine virtuali nella stessa rete virtuale. Per ridurre il rischio di esfiltrazione di dati, è possibile usare un gruppo di sicurezza di rete (NSG). Abilitare i log dei flussi NSG e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Come configurare un collegamento privato per database di Azure per PostgreSQL](howto-configure-privatelink-portal.md)

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni**: usare Advanced Threat Protection per Database di Azure per PostgreSQL. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Abilitare lo standard di Protezione DDoS nelle reti virtuali associate alle istanze di Database di Azure per PostgreSQL per proteggersi da attacchi DDoS. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

- [Come configurare Advanced Threat Protection per database di Azure per PostgreSQL](howto-database-threat-protection-portal.md)

- [Come configurare la protezione DDoS](../virtual-network/manage-ddos-protection.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Indicazioni**: quando l'istanza di Database di Azure per PostgreSQL è protetta per un endpoint privato, è possibile distribuire le macchine virtuali nella stessa rete virtuale. È quindi possibile configurare un gruppo di sicurezza di rete (NSG) per ridurre il rischio di esfiltrazione di dati. Abilitare i log dei flussi NSG e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Indicazioni**: usare Advanced Threat Protection per Database di Azure per PostgreSQL. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

- [Come configurare Advanced Threat Protection per database di Azure per PostgreSQL](howto-database-threat-protection-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni**: per le risorse che devono accedere alle istanze di Database di Azure per PostgreSQL, usare tag di servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o in Firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, SQL.WestUs) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che aggiorna automaticamente il tag in caso di modifica degli indirizzi.

Nota: Database di Azure per PostgreSQL usa il tag di servizio "Microsoft.Sql".

- [Per ulteriori informazioni sull'utilizzo dei tag di servizio](../virtual-network/service-tags-overview.md)

- [Informazioni sull'utilizzo dei tag di servizio per database di Azure per PostgreSQL](concepts-data-access-and-security-vnet.md#terminology-and-description)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: mantenere configurazioni di sicurezza standard per i dispositivi di rete

**Indicazioni**: definire e implementare configurazioni di sicurezza standard per le impostazioni di rete e le risorse di rete associate alle istanze di Database di Azure per PostgreSQL con Criteri di Azure. Usare gli alias di Criteri di Azure negli spazi dei nomi "Microsoft.DBforPostgreSQL" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle istanze di Database di Azure per PostgreSQL. È anche possibile usare le definizioni di criteri predefinite correlate alle funzionalità di rete o alle istanze di Database di Azure per PostgreSQL, ad esempio:

- Lo standard di protezione DDoS deve essere abilitato

- Il criterio Imponi connessione TLS deve essere abilitato per i server di database PostgreSQL

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Esempi di criteri di Azure per la rete](/azure/governance/policy/samples)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni**: usare i tag per le risorse correlate alla sicurezza di rete e al flusso del traffico per le istanze di Database di Azure per PostgreSQL per fornire i metadati e l'organizzazione logica.

Usare una qualsiasi delle definizioni di Criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore", per garantire che tutte le risorse vengano create con tag e per notificare l'esistenza di risorse senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni su risorse basate sui rispettivi tag.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze di Database di Azure per PostgreSQL. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](/azure/azure-monitor/platform/activity-log-view)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Indicazioni**: Microsoft gestisce l'origine ora usata per le risorse di Azure, ad esempio Database di Azure per PostgreSQL per i timestamp nei log.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni**: abilitare le impostazioni di diagnostica e i log del server, quindi inserire i log per aggregare i dati di sicurezza generati dalle istanze di Database di Azure per PostgreSQL. In Monitoraggio di Azure usare una o più aree di lavoro Log Analytics per eseguire query ed effettuare analisi, quindi usare gli account di archiviazione di Azure per l'archiviazione/memorizzazione a lungo termine. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

- [Come configurare e accedere ai log del server per database di Azure per PostgreSQL](howto-configure-server-logs-in-portal.md)

- [Come configurare e accedere ai log di controllo per database di Azure per PostgreSQL](concepts-audit.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni**: abilitare le impostazioni di diagnostica nelle istanze di Database di Azure per PostgreSQL per l'accesso ai log di controllo, sicurezza e risorse. Assicurarsi di abilitare in modo specifico il log di controllo di PostgreSQL. I log attività, che sono automaticamente disponibili, includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili. È anche possibile abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log alla stessa area di lavoro Log Analytics o allo stesso account di archiviazione.

- [Come configurare e accedere ai log del server per database di Azure per PostgreSQL](howto-configure-server-logs-in-portal.md)

- [Come configurare e accedere ai log di controllo per database di Azure per PostgreSQL](concepts-audit.md)

- [Come configurare le impostazioni di diagnostica per il log attività di Azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni**: In Monitoraggio di Azure, per l'area di lavoro Log Analytics usata per conservare i log di Database di Azure per PostgreSQL, impostare il periodo di conservazione in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione/memorizzazione a lungo termine.

- [Come impostare i parametri di conservazione dei log per le aree di lavoro Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Archiviazione dei log delle risorse in un account di archiviazione di Azure](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Indicazioni**: analizzare e monitorare i log delle istanze di Database di Azure per PostgreSQL per individuare un comportamento anomalo. Usare Log Analytics di Monitoraggio di Azure per esaminare i log ed eseguire query sui relativi dati. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Per ulteriori informazioni sull'Log Analytics](../azure-monitor/log-query/get-started-portal.md)

- [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Indicazioni**: abilitare Advanced Threat Protection per Database di Azure per PostgreSQL. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

È inoltre possibile abilitare i log del server e le impostazioni di diagnostica per PostgreSQL e inviare i log a un'area di lavoro Log Analytics. Eseguire l'onboarding dell'area di lavoro Log Analytics in Azure Sentinel perché fornisce una soluzione SOAR (Security Orchestration Automated Response). In questo modo è possibile creare playbook (soluzioni automatizzate) e usarli per risolvere i problemi di sicurezza.

- [Come abilitare Advanced Threat Protection per database di Azure per PostgreSQL](howto-database-threat-protection-portal.md)

- [Come configurare e accedere ai log del server per database di Azure per PostgreSQL](howto-configure-server-logs-in-portal.md)

- [Come configurare e accedere ai log di controllo per database di Azure per PostgreSQL](concepts-audit.md)

- [Come configurare le impostazioni di diagnostica per il log attività di Azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Indicazioni**: non applicabile; Database di Azure per PostgreSQL non elabora o produce log correlati al software antimalware.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Indicazioni**: non applicabile; Database di Azure per PostgreSQL non elabora o produce log correlati a DNS.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: mantenere un inventario degli account amministrativi

**Indicazioni**: mantenere un inventario degli account utente con accesso amministrativo al piano di controllo (ad esempio, portale di Azure) delle istanze di Database di Azure per PostgreSQL. Mantenere inoltre un inventario degli account amministrativi che hanno accesso al piano dati (all'interno del database stesso) delle istanze di Database di Azure per PostgreSQL. Quando si crea il server PostgreSQL, si forniscono le credenziali per un utente amministratore. Questo amministratore può essere usato per creare altri utenti PostgreSQL.

Database di Azure per PostgreSQL non supporta il controllo degli accessi in base al ruolo predefinito, ma è possibile creare ruoli personalizzati basati su operazioni specifiche del provider di risorse.

- [Informazioni sui ruoli personalizzati per la sottoscrizione di Azure](../role-based-access-control/custom-roles.md) 

- [Informazioni sulle operazioni del provider di risorse per database di Azure per PostgreSQL](../role-based-access-control/resource-provider-operations.md#microsoftdbforpostgresql) 

- [Informazioni sulla gestione dell'accesso per database di Azure per PostgreSQL](concepts-security.md#access-management)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Indicazioni**: Azure Active Directory e Database di Azure per PostgreSQL non hanno il concetto di password predefinite.

Al momento della creazione dell'effettiva risorsa di Database di Azure per PostgreSQL, Azure forza la creazione di un utente amministratore con una password complessa. Tuttavia, una volta creata l'istanza di PostgreSQL, è possibile usare il primo account amministratore del server creato per creare altri utenti e concedere loro l'accesso amministrativo. Quando si creano questi account, assicurarsi di configurare una password complessa diversa per ogni account.

- [Come creare account aggiuntivi per database di Azure per PostgreSQL](howto-create-users.md)

- [Come aggiornare la password amministratore](howto-create-manage-server-portal.md#update-admin-password)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'uso di account amministrativi dedicati che abbiano accesso alle istanze di Database di Azure per PostgreSQL. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure per monitorare il numero di account amministrativi. 

- [Informazioni sull'identità e sull'accesso del Centro sicurezza di Azure](../security-center/security-center-identity-access.md) 

- [Informazioni su come creare utenti amministratori in database di Azure per PostgreSQL](howto-create-users.md#the-server-admin-account)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usare Azure Active Directory Single Sign-On (SSO)

**Indicazioni**: l'accesso a Database di Azure per PostgreSQL è supportato sia con l'uso di nome utente e password configurati direttamente nel database, sia con l'uso di un'identità Azure Active Directory (AD) e di un token Azure AD per la connessione. Quando si usa un token Azure AD, sono supportati metodi diversi, ad esempio un utente Azure AD, un gruppo Azure AD o un'applicazione Azure AD che si connette al database.

Separatamente, l'accesso al piano di controllo per PostgreSQL è disponibile tramite l'API REST e supporta SSO. Per l'autenticazione, impostare l'intestazione di autorizzazione per le richieste su un token JSON Web che è possibile ottenere da Azure Active Directory.

- [Usare Azure Active Directory per l'autenticazione con database di Azure per PostgreSQL](howto-configure-sign-in-aad-authentication.md)

- [Informazioni sull'API REST di database di Azure per PostgreSQL](/rest/api/postgresql/)

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Indicazioni**: abilitare Azure Active Directory Multi-Factor Authentication (MFA) e seguire le raccomandazioni sulla gestione delle identità e degli accessi nel Centro sicurezza di Azure. Quando si usano token Azure AD per accedere al database, è possibile richiedere l'autenticazione a più fattori per gli accessi al database.

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Usare Azure Active Directory per l'autenticazione con database di Azure per PostgreSQL](howto-configure-sign-in-aad-authentication.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usare workstation sicure gestite da Azure per le attività amministrative

**Indicazioni**: usare workstation con accesso con privilegi insieme al servizio Multi-Factor Authentication (MFA) configurato per l'accesso e la configurazione delle risorse di Azure.

- [Informazioni sulle workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Indicazioni**: abilitare Advanced Threat Protection per Database di Azure per PostgreSQL per generare avvisi per le attività sospette.

È inoltre possibile usare Azure Active Directory (AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando nell'ambiente si verifica un'attività sospetta o non sicura.

Usare i rilevamenti dei rischi di Azure AD per visualizzare gli avvisi e i report sul comportamento utente rischioso.

- [Come configurare Advanced Threat Protection per database di Azure per PostgreSQL](howto-database-threat-protection-portal.md)

- [Come distribuire Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Informazioni sui rilevamenti di rischi di Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare le località denominate di accesso condizionale per consentire l'accesso al portale e ad Azure Resource Manager solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Indicazioni**: usare Azure Active Directory (AD) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD inoltre effettua il salting, esegue l'hashing e archivia in modo sicuro le credenziali utente.

Per l'accesso a Database di Azure per PostgreSQL, è consigliabile usare Azure AD e usare un token Azure AD per connettersi. Quando si usa un token Azure AD, sono supportati metodi diversi, ad esempio un utente Azure AD, un gruppo Azure AD o un'applicazione Azure AD che si connette al database.

Le credenziali di Azure AD possono essere usate anche per l'amministrazione a livello di piano di gestione (ad esempio, il portale di Azure) per controllare gli account amministratore PostgreSQL.

- [Usare Azure Active Directory per l'autenticazione con database di Azure per PostgreSQL](howto-configure-sign-in-aad-authentication.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare con regolarità l'accesso degli utenti

**Indicazioni**: esaminare i log di Azure Active Directory per individuare gli account obsoleti, che possono includere quelli con ruoli amministrativi di Database di Azure per PostgreSQL. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali che possono essere usate per accedere a Database di Azure per PostgreSQL e le assegnazioni di ruoli. È consigliabile verificare regolarmente l'accesso degli utenti, ad esempio ogni 90 giorni, per assicurarsi che solo gli utenti appropriati dispongano di accesso continuo.

- [Informazioni sulla creazione di report Azure AD](/azure/active-directory/reports-monitoring/)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

- [Esaminare gli utenti di PostgreSQL e i ruoli assegnati](https://www.postgresql.org/docs/current/database-roles.html)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Indicazioni**: abilitare le impostazioni di diagnostica per Database di Azure per PostgreSQL e Azure Active Directory, inviando tutti i log a un'area di lavoro Log Analytics. Configurare gli avvisi desiderati, ad esempio per i tentativi di autenticazione non riusciti, all'interno di Log Analytics.

- [Come configurare e accedere ai log del server per database di Azure per PostgreSQL](howto-configure-server-logs-in-portal.md)

- [Come configurare e accedere ai log di controllo per database di Azure per PostgreSQL](concepts-audit.md)

- [Come integrare i log attività di Azure in Monitoraggio di Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Indicazioni**: abilitare Advanced Threat Protection per Database di Azure per PostgreSQL per generare avvisi per le attività sospette.

Usare le funzionalità di rilevamento di rischi e Identity Protection di Azure Active Directory per configurare risposte automatiche alle azioni sospette rilevate. È possibile abilitare le risposte automatiche tramite Azure Sentinel per implementare le risposte di sicurezza dell'organizzazione.

È anche possibile inserire i log in Azure Sentinel per un'analisi più approfondita.

- [Come configurare Advanced Threat Protection per database di Azure per PostgreSQL](howto-database-threat-protection-portal.md)

- [Panoramica di Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Come visualizzare gli accessi a rischio per Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Indicazioni**: attualmente non disponibile; la funzionalità Customer Lockbox non è ancora supportata per Database di Azure per PostgreSQL.

- [Elenco dei servizi Customer Lockbox supportati](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: mantenere un inventario delle informazioni riservate

**Indicazioni**: usare i tag per semplificare il rilevamento delle istanze di Database di Azure per PostgreSQL o delle risorse correlate che archiviano o elaborano informazioni riservate.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Usare una combinazione di Collegamento privato, endpoint di servizio e/o regole del firewall per isolare e limitare l'accesso in rete alle istanze di Database di Azure per PostgreSQL.

- [Come creare sottoscrizioni di Azure aggiuntive](/azure/billing/billing-create-subscription)

- [Come creare gruppi di gestione](/azure/governance/management-groups/create)

- [Come configurare un collegamento privato per database di Azure per PostgreSQL](howto-configure-privatelink-portal.md)

- [Come creare e gestire endpoint di servizio VNet e regole VNet in database di Azure per PostgreSQL](howto-manage-vnet-using-portal.md)

- [Come configurare le regole del firewall di database di Azure per PostgreSQL](concepts-firewall-rules.md)

**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Indicazioni**: quando si usano macchine virtuali di Azure per accedere alle istanze di Database di Azure per PostgreSQL, usare Collegamento privato, le configurazioni di rete PostgreSQL, i gruppi di sicurezza di rete e i tag di servizio per attenuare la possibilità di esfiltrazione di dati.

Microsoft gestisce l'infrastruttura sottostante per Database di Azure per PostgreSQL e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

- [Come attenuare i dati exfiltration per database di Azure per PostgreSQL](concepts-data-access-and-security-private-link.md)

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsibilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Indicazioni**: Database di Azure per PostgreSQL supporta la connessione del server PostgreSQL alle applicazioni client tramite il protocollo Transport Layer Security (TLS), noto in precedenza come Secure Sockets Layer (SSL). L'imposizione di connessioni TSL tra il server di database e le applicazioni client garantisce la protezione da attacchi "man-in-the-middle" tramite la crittografia del flusso di dati tra il server e l'applicazione. Nel portale di Azure verificare che l'opzione "Imponi connessione SSL" sia abilitata, per impostazione predefinita, per tutte le istanze di Database di Azure per PostgreSQL.

Attualmente le versioni di TLS supportate per Database di Azure per PostgreSQL sono TLS 1.0, TLS 1.1 e TLS 1.2.

- [Come configurare la crittografia in transito per database di Azure per PostgreSQL](concepts-ssl-connection-security.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni**: le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per Database di Azure per PostgreSQL. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per controllare l'accesso al piano di controllo del database di Azure per PostgreSQL, ad esempio portale di Azure. Per l'accesso al piano dati (all'interno del database stesso), usare query SQL per creare gli utenti e configurare le autorizzazioni utente. Il controllo degli accessi in base al ruolo di Azure non influisce sulle autorizzazioni dell'utente

- [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)

- [Come configurare l'accesso utente con SQL per database di Azure per PostgreSQL](howto-create-users.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per Database di Azure per PostgreSQL e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Indicazioni**: Il servizio Database di Azure per PostgreSQL usa il modulo crittografico convalidato FIPS 140-2 per la crittografia dei dati archiviati inattivi. I dati, inclusi i backup, vengono crittografati su disco, ad eccezione dei file temporanei creati durante l'esecuzione di query. Il servizio usa la crittografia AES a 256 bit inclusa nella crittografia di archiviazione di Azure e le chiavi vengono gestite dal sistema. La crittografia dell'archiviazione è sempre attiva e non può essere disabilitata.

La crittografia dei dati con chiavi gestite dal cliente per Database di Azure per PostgreSQL - Server singolo consente di usare Bring Your Own Key (BYOK) per la protezione dei dati inattivi. A questo punto, è necessario richiedere l'accesso per usare questa funzionalità. A tale scopo, contattare:

AskAzureDBforPostgreSQL@service.microsoft.com.

- [Informazioni sulla crittografia dei dati inattivi per database di Azure per PostgreSQL](concepts-security.md)

- [Informazioni sulla crittografia dei dati inattivi per database di Azure per PostgreSQL con chiavi gestite dal cliente](concepts-data-encryption-postgresql.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche apportate alle risorse di Azure critiche

**Indicazioni**: usare Monitoraggio di Azure con il log attività di Azure per creare avvisi per il momento in cui le modifiche vengono apportate alle istanze di produzione di Database di Azure per PostgreSQL e ad altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni**: seguire le raccomandazioni del Centro sicurezza di Azure per proteggere il database di Azure per PostgreSQL e le risorse correlate.

Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano database di Azure per PostgreSQL.

- [Informazioni sulle raccomandazioni del Centro sicurezza di Azure](../security-center/recommendations-reference.md)

- [Copertura delle funzionalità per i servizi PaaS di Azure nel centro sicurezza di Azure](../security-center/features-paas.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: distribuire la soluzione di gestione delle patch automatizzata per i titoli software di terze parti

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano Database di Azure per PostgreSQL.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse, incluse le istanze di database di Azure per PostgreSQL, all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: mantenere i metadati degli asset

**Indicazioni**: applicare i tag alle istanze di Database di Azure per PostgreSQL e ad altre risorse correlate, fornendo i metadati per organizzarle in modo logico in una tassonomia.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle istanze di Database di Azure per PostgreSQL e delle risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](/azure/billing/billing-create-subscription)

- [Come creare gruppi di gestione](/azure/governance/management-groups/create)

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo e Azure in generale.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare le risorse di Azure non approvate

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare anche Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo e Azure in generale.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](/azure/governance/policy/samples/not-allowed-resource-types)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure. Ciò può impedire la creazione e le modifiche alle risorse all'interno di un ambiente ad elevata sicurezza, ad esempio le istanze di Database di Azure per PostgreSQL contenenti informazioni riservate.

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: definire configurazioni sicure per tutte le risorse di Azure

**Indicazioni**: definire e implementare configurazioni di sicurezza standard per le istanze di Database di Azure per PostgreSQL con Criteri di Azure. Usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.DBforPostgreSQL" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle istanze di Database di Azure per PostgreSQL. È anche possibile usare le definizioni di criteri predefinite correlate alle istanze di Database di Azure per PostgreSQL, ad esempio:

- Il criterio Imponi connessione TLS deve essere abilitato per i server di database PostgreSQL

- L'impostazione di registrazione delle connessioni deve essere abilitata per i server di database PostgreSQL

- [Come visualizzare gli alias dei criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni**: se si usano definizioni di Criteri di Azure personalizzate per le istanze di Database di Azure per PostgreSQL e le risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentazione di Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni**: usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft. DBforPostgreSQL" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Indicazioni**: usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft. DBforPostgreSQL" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [deploy if not exist] per applicare automaticamente le configurazioni per le istanze di Database di Azure per PostgreSQL e le risorse correlate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Indicazioni**: per le macchine virtuali di Azure o le applicazioni Web in esecuzione nel Servizio app di Azure in uso per accedere alle istanze di Database di Azure per PostgreSQL, usare l'identità del servizio gestita insieme ad Azure Key Vault per semplificare e proteggere la gestione dei segreti di Database di Azure per PostgreSQL. Assicurarsi che l'eliminazione temporanea di Azure Key Vault sia abilitata.

- [Come eseguire l'integrazione con le identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Come creare una Key Vault](../key-vault/general/quick-create-portal.md)

- [Come fornire l'autenticazione Key Vault con un'identità gestita](/azure/key-vault/general/managed-identity)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Indicazioni**: Il server Database di Azure per PostgreSQL supporta l'autenticazione di Azure Active Directory per accedere ai database.  Mentre si crea il server Database di Azure per PostgreSQL, si forniscono le credenziali per un utente amministratore. Questo amministratore può essere usato per creare altri utenti di database.  

Per le macchine virtuali di Azure o le applicazioni Web in esecuzione nel Servizio app di Azure in uso per accedere al server Database di Azure per PostgreSQL, usare l'identità del servizio gestita insieme ad Azure Key Vault per archiviare e recuperare le credenziali per tale server. Assicurarsi che l'eliminazione temporanea di Azure Key Vault sia abilitata.

Usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (AD). Le identità gestite consentono di eseguire l'autenticazione per qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire credenziali nel codice.

- [Come configurare le identità gestite](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Come eseguire l'integrazione con le identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usare il software antimalware gestito centralmente

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

Il software antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Servizio app di Azure), ma non viene eseguito sui contenuti del cliente.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Indicazioni**: il software antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Database di Azure per PostgreSQL), ma non viene eseguito sui contenuti del cliente.

Eseguire l'analisi preliminare del contenuto da caricare in risorse di Azure non di calcolo, ad esempio Servizio app, Data Lake Storage, Archiviazione BLOB, Database di Azure per PostgreSQL e così via. Microsoft non è in grado di accedere ai dati in questi casi.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: assicurarsi che le firme e il software antimalware siano aggiornati

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

il software antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Database di Azure per PostgreSQL), ma non viene eseguito sui contenuti del cliente.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Indicazioni**: Database di Azure per PostgreSQL esegue backup dei file di dati e del log delle transazioni. A seconda delle massime dimensioni di archiviazione supportate, vengono eseguiti backup completi e differenziali (server di archiviazione da 4 TB al massimo) o backup di snapshot (server di archiviazione fino a 16 TB al massimo). Questi backup consentono di ripristinare un server a qualsiasi momento specifico all'interno del periodo di conservazione dei backup configurato. Il periodo di conservazione dei backup predefinito è di sette giorni. Facoltativamente, è possibile configurare fino a 35 giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit.

- [Come eseguire il backup di un server in database di Azure per PostgreSQL](howto-restore-server-portal.md)

- [Informazioni sulla configurazione iniziale di database di Azure per PostgreSQL](tutorial-design-database-using-azure-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni**: Database di Azure per PostgreSQL crea automaticamente backup del server e li archivia in un archivio con ridondanza locale o geografica, in base alla scelta dell'utente. I backup possono essere usati per ripristinare il server a un momento specifico. Il backup e il ripristino sono una parte essenziale di qualsiasi strategia di continuità aziendale, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale.

Se si usa Azure Key Vault per archiviare le credenziali per le istanze di Database di Azure per PostgreSQL, assicurarsi che i backup automatizzati delle chiavi siano eseguiti con regolarità.

- [Come eseguire il backup di un server in database di Azure per PostgreSQL](howto-restore-server-portal.md)

- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni**: In Database di Azure per PostgreSQL, l'esecuzione di un ripristino crea un nuovo server dai backup del server originale. Sono disponibili due tipi di ripristino: ripristino temporizzato e ripristino geografico. Il ripristino temporizzato è disponibile con entrambe le opzioni di ridondanza per il backup e crea un nuovo server nella stessa area del server originario. Il ripristino geografico è disponibile solo se il server è stato configurato per l'archiviazione con ridondanza geografica e consente di ripristinare il server in un'area diversa.

Il tempo stimato per il ripristino dipende da diversi fattori, tra cui le dimensioni dei database, le dimensioni dei log delle transazioni, la larghezza di banda di rete e il numero totale di database ripristinati contemporaneamente nella stessa area. Il tempo di recupero di solito è inferiore a 12 ore.

Testare periodicamente il ripristino delle istanze di Database di Azure per PostgreSQL.

- [Come eseguire il backup di un server in database di Azure per PostgreSQL](howto-restore-server-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni**: Database di Azure per PostgreSQL crea backup completi, differenziali e del log delle transazioni. Questi backup consentono di ripristinare un server a qualsiasi momento specifico all'interno del periodo di conservazione dei backup configurato. Il periodo di conservazione dei backup predefinito è di sette giorni. Facoltativamente, è possibile configurare fino a 35 giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit.

- [Informazioni su backup e ripristino nel database di Azure per PostgreSQL](concepts-backup.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione) e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente.  Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi in Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Indicazioni**: seguire le regole Microsoft per assicurarsi che i propri test di penetrazione non violino i criteri Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [È possibile trovare altre informazioni sulla strategia Microsoft e sull'esecuzione dei test di penetrazione Red Teaming sull'infrastruttura cloud gestita da Microsoft, sui servizi e sulle applicazioni qui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
