---
title: Baseline della sicurezza di Azure per database di Azure per MySQL
description: Baseline della sicurezza di Azure per database di Azure per MySQL
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 1b9a1771ad498fa3fb9b8294adb8a6556a00863a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190419"
---
# <a name="azure-security-baseline-for-azure-database-for-mysql"></a>Baseline della sicurezza di Azure per database di Azure per MySQL

La linea di base di sicurezza di Azure per database di Azure per MySQL contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La linea di base per questo servizio viene creata dalla [versione 1,0 del benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce indicazioni su come proteggere le soluzioni cloud in Azure con le procedure consigliate.

Per altre informazioni, vedere [Panoramica della sicurezza di base di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

**Linee guida**: configurare il collegamento privato per database di Azure per MySQL con endpoint privati. Il servizio Collegamento privato consente di connettersi a diversi servizi PaaS in Azure tramite un endpoint privato. Il collegamento privato di Azure porta essenzialmente i servizi di Azure all'interno della rete virtuale privata (VNet). Il traffico tra la rete virtuale e l'istanza di MySQL raggiunge la rete dorsale Microsoft.

In alternativa, è possibile usare gli endpoint del servizio rete virtuale per proteggere e limitare l'accesso di rete alle implementazioni del database di Azure per MySQL. Le regole di rete virtuale rappresentano una funzionalità di sicurezza del firewall che consente di definire se il server di Database di Azure per MySQL accetta le comunicazioni inviate da subnet specifiche nelle reti virtuali.

È anche possibile proteggere il database di Azure per il server MySQL con le regole del firewall. Il firewall del server impedisce l'accesso al server di database finché non si specificano i computer autorizzati. Per configurare il firewall, creare regole del firewall che specificano gli intervalli di indirizzi IP accettabili. È possibile creare regole firewall a livello di server.

Come configurare un collegamento privato per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

Come creare e gestire endpoint di servizio VNet e regole VNet in database di Azure per MySQL:https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

Come configurare le regole del firewall di database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

**Linee guida**: quando l'istanza di database di Azure per MySQL è protetta a un endpoint privato, è possibile distribuire le macchine virtuali nella stessa rete virtuale. Per ridurre il rischio di exfiltration dei dati, è possibile usare un gruppo di sicurezza di rete (NSG). Abilitare i log di flusso NSG e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi di NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete non configurate.

Come configurare un collegamento privato per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

Come abilitare i log dei flussi di NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare e usare Analisi del traffico:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="13-protect-critical-web-applications"></a>1,3: proteggere le applicazioni Web critiche

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle applicazioni Web in esecuzione in app Azure servizio o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: usare Advanced Threat Protection per database di Azure per MySQL. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database.

Abilitare protezione DDoS standard nelle reti virtuali associate alle istanze di database di Azure per MySQL per proteggersi da attacchi DDoS. Usare il Centro sicurezza di Azure Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP Internet dannosi noti o non usati.

Come configurare Advanced Threat Protection per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Come configurare la protezione DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrare i pacchetti di rete e i log di flusso

**Linee guida**: quando l'istanza di database di Azure per MySQL è protetta a un endpoint privato, è possibile distribuire le macchine virtuali nella stessa rete virtuale. È quindi possibile configurare un gruppo di sicurezza di rete (NSG) per ridurre il rischio di exfiltration dei dati. Abilitare i log di flusso NSG e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi di NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete non configurate.

Come abilitare i log dei flussi di NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare e usare Analisi del traffico:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (IDS/IP)

**Linee guida**: usare Advanced Threat Protection per database di Azure per MySQL. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database.

Come configurare Advanced Threat Protection per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="17-manage-traffic-to-web-applications"></a>1,7: gestire il traffico per le applicazioni Web

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle applicazioni Web in esecuzione in app Azure servizio o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ridurre la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni**: per le risorse che richiedono l'accesso alle istanze di database di Azure per MySQL, usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, SQL. Westus) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag di servizio in base alla modifica degli indirizzi.

Nota: database di Azure per MySQL usa i tag del servizio "Microsoft. SQL".

Per ulteriori informazioni sull'utilizzo dei tag del servizio:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Informazioni sull'utilizzo dei tag di servizio per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet#terminology-and-description

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: mantenere le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le impostazioni di rete e le risorse di rete associate alle istanze del database di Azure per MySQL con criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. DBforMySQL" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle istanze di database di Azure per MySQL. È anche possibile usare le definizioni di criteri predefinite correlate alla rete o alle istanze di database di Azure per MySQL, ad esempio:

- Lo standard di protezione DDoS deve essere abilitato

- Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL

Come configurare e gestire i criteri di Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Esempi di criteri di Azure per la rete:https://docs.microsoft.com/azure/governance/policy/samples/

Come creare un Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="110-document-traffic-configuration-rules"></a>1,10: regole di configurazione del traffico documento

**Linee guida**: usare i tag per le risorse correlate alla sicurezza di rete e al flusso del traffico per le istanze di database di Azure per MySQL per fornire i metadati e l'organizzazione logica.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore", per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

Come creare e usare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usare gli strumenti automatici per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze del database di Azure per MySQL. Creare avvisi in monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse di rete critiche.

Come visualizzare e recuperare gli eventi del log attività di Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Come creare avvisi in monitoraggio di Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: utilizzare origini di sincronizzazione dell'ora approvate

**Linee guida**: Microsoft gestisce l'origine dell'ora usata per le risorse di Azure, ad esempio database di Azure per MySQL per i timestamp nei log.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: configurare la gestione dei log di sicurezza centrale

**Linee guida**: abilitare le impostazioni di diagnostica e i log del server e i log di inserimento per aggregare i dati di sicurezza generati dalle istanze del database di Azure per MySQL. In monitoraggio di Azure usare Log Analytics le aree di lavoro per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archivio. In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti.

Informazioni sui log del server per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Come eseguire l'onboarding di Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare le impostazioni di diagnostica nelle istanze di database di Azure per MySQL per l'accesso ai log di controllo, query lente e metriche MySQL. Assicurarsi di abilitare in modo specifico il registro di controllo di MySQL. I log attività, che sono automaticamente disponibili, includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili. È anche possibile abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log alla stessa area di lavoro Log Analytics o all'account di archiviazione.

Informazioni sui log del server per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Come configurare e accedere ai log di query lente per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

Come configurare e accedere ai log di controllo per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

Come configurare le impostazioni di diagnostica per il log attività di Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: raccogliere i registri di sicurezza dai sistemi operativi

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="25-configure-security-log-storage-retention"></a>2,5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: all'interno di monitoraggio di Azure, per l'area di lavoro log Analytics usata per conservare i log di database di Azure per MySQL, impostare il periodo di conservazione in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archivio.

Come impostare i parametri di conservazione dei log per le aree di lavoro Log Analytics:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

Archiviazione dei log delle risorse in un account di archiviazione di Azure:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: analizzare e monitorare i log delle istanze di database di Azure per MySQL per un comportamento anomalo. Usare Log Analytics di monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log. In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti.

Come eseguire l'onboarding di Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Per ulteriori informazioni sull'Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Come eseguire query personalizzate in monitoraggio di Azure:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: abilitare gli avvisi per attività anomale

**Linee guida**: abilitare Advanced Threat Protection per database di Azure per MySQL. Advanced Threat Protection rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database.

Inoltre, è possibile abilitare i log del server e le impostazioni di diagnostica per MySQL e inviare i log a un'area di lavoro Log Analytics. Caricare l'area di lavoro di Log Analytics in Azure Sentinel perché fornisce una soluzione di sicurezza automatica delle orchestrazioni (SOAR). In questo modo è possibile creare PlayBook (soluzioni automatiche) e usarle per correggere i problemi di sicurezza.

Come abilitare Advanced Threat Protection per database di Azure per MySQL (anteprima):https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Informazioni sui log del server per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Come configurare e accedere ai log di query lente per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

Come configurare e accedere ai log di controllo per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

Come configurare le impostazioni di diagnostica per il log attività di Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Come eseguire l'onboarding di Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizzare la registrazione anti-malware

**Linee guida**: non applicabile; Il database di Azure per MySQL non elabora o produce log correlati all'anti-malware.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="29-enable-dns-query-logging"></a>2,9: abilitare la registrazione delle query DNS

**Linee guida**: non applicabile; Il database di Azure per MySQL non elabora o produce log correlati a DNS.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="210-enable-command-line-audit-logging"></a>2,10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [controllo di sicurezza: identità e controllo di accesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: gestire un inventario degli account amministrativi

**Linee guida**: gestire un inventario degli account utente con accesso amministrativo al piano di gestione (ad esempio portale di Azure) del database di Azure per MySQLinstances. Inoltre, mantenere un inventario degli account amministrativi che hanno accesso al piano dati (all'interno del database stesso) delle istanze di database di Azure per MySQL. Quando si crea il server MySQL, si forniscono le credenziali per un utente amministratore. Questo amministratore può essere usato per creare altri utenti di MySQL.

Il database di Azure per MySQL non supporta il controllo degli accessi in base al ruolo predefinito, ma è possibile creare ruoli personalizzati in base alle opzioni specifiche del provider di risorse.

Informazioni sui ruoli personalizzati per la sottoscrizione di Azure:https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

Informazioni sulle operazioni del provider di risorse per database di Azure per MySQL:https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbformysql

Informazioni sulla gestione dell'accesso per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/concepts-security#access-management

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="32-change-default-passwords-where-applicable"></a>3,2: modificare le password predefinite se applicabile

**Linee guida**: Azure ad non ha il concetto di password predefinite.

Al momento della creazione della risorsa database di Azure per MySQL, Azure forza la creazione di un utente amministratore con una password complessa. Tuttavia, una volta creata l'istanza di MySQL, è possibile usare il primo account amministratore del server creato per creare altri utenti e concedere loro l'accesso amministrativo. Quando si creano questi account, assicurarsi di configurare una password complessa diversa per ogni account.

Come creare account aggiuntivi per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-create-users

Come aggiornare la password amministratore:https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: usare account amministrativi dedicati

**Linee guida**: creare procedure operative standard per l'uso di account amministrativi dedicati che possono accedere alle istanze di database di Azure per MySQL. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Conoscere l'identità e l'accesso del Centro sicurezza di Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access

Informazioni su come creare utenti amministratori in database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-create-users

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida**: l'accesso a database di Azure per MySQL è supportato sia con nome utente/password configurato direttamente nel database, sia con un'identità Azure Active Directory (ad) e utilizzando un token Azure ad per la connessione. Quando si usa un token di Azure AD, sono supportati metodi diversi, ad esempio un utente Azure AD, un gruppo di Azure AD o un'applicazione Azure AD che si connette al database.

Separatamente, l'accesso al piano di controllo per MySQL è disponibile tramite l'API REST e supporta SSO. Per eseguire l'autenticazione, impostare l'intestazione di autorizzazione per le richieste su un token Web JSON ottenuto da Azure Active Directory.

Usare Azure Active Directory per l'autenticazione con database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

Informazioni sull'API REST di database di Azure per MySQL:https://docs.microsoft.com/rest/api/mysql/

Informazioni su SSO con Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare Azure Active Directory multi-factor authentication (multi-factor authentication) e seguire le indicazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure Quando si usano token di Azure AD per accedere al database, è possibile richiedere l'autenticazione a più fattori per gli accessi al database.

Come abilitare l'autenticazione a più fattori in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Usare Azure Active Directory per l'autenticazione con database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

Come monitorare identità e accesso nel centro sicurezza di Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: usare macchine virtuali dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida**: usare workstation con accesso con privilegi (Paw) con multi-factor authentication (multi-factor authentication) configurato per accedere e configurare le risorse di Azure.

Informazioni sulle workstation con accesso con privilegi:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare l'autenticazione a più fattori in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: registrare e segnalare l'attività sospetta da account amministrativi

**Linee guida**: abilitare Advanced Threat Protection per database di Azure per MySQL per generare avvisi per attività sospette.

Inoltre, è possibile usare Azure AD Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verifica un'attività sospetta o non sicura nell'ambiente.

Usare Azure AD i rilevamenti dei rischi per visualizzare gli avvisi e i report sul comportamento utente rischioso.

Come configurare Advanced Threat Protection per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Come distribuire Privileged Identity Management (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Comprendere Azure AD rilevamento del rischio:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: gestire le risorse di Azure solo dalle località approvate

**Linee guida**: usare percorsi denominati di accesso condizionale per consentire l'accesso al portale e al Azure Resource Manager da solo raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree.

Come configurare le località denominate in Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="39-use-azure-active-directory"></a>3,9: usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (ad) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD anche Salt, hash e archivia in modo sicuro le credenziali utente.

Per l'accesso a database di Azure per MySQL è consigliabile usare Azure AD e utilizzare un token Azure AD per la connessione. Quando si usa un token di Azure AD, sono supportati metodi diversi, ad esempio un utente Azure AD, un gruppo di Azure AD o un'applicazione Azure AD che si connette al database. 

Azure AD credenziali possono essere usate anche per l'amministrazione a livello di piano di gestione (ad esempio, il portale di Azure) per controllare gli account amministratore di MySQL.

Usare Azure Active Directory per l'autenticazione con database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: rivedere e riconciliare regolarmente l'accesso utente

**Indicazioni**: esaminare i log di Azure Active Directory per individuare gli account obsoleti che possono includere quelli con i ruoli amministrativi di database di Azure per MySQL. Usare anche le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali che possono essere usate per accedere al database di Azure per MySQL e le assegnazioni di ruolo. L'accesso utente deve essere esaminato a intervalli regolari, ad esempio ogni 90 giorni, per garantire che solo gli utenti giusti abbiano accesso continuo.

Informazioni sulla creazione di report Azure ADhttps://docs.microsoft.com/azure/active-directory/reports-monitoring/

Come usare le verifiche di accesso alle identità di Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: il monitoraggio tenta di accedere agli account disattivati

**Linee guida**: abilitare le impostazioni di diagnostica per database di Azure per MySQL e Azure Active Directory, inviare tutti i log a un'area di lavoro di log Analytics. Configurare gli avvisi desiderati, ad esempio i tentativi di autenticazione non riusciti, all'interno Log Analytics.

Come configurare e accedere ai log di query lente per database di Azure per MySQL:https://docs.microsoft.com/Azure/mysql/howto-configure-server-logs-in-portal

Come configurare e accedere ai log di controllo per database di Azure per MySQL:https://docs.microsoft.com/Azure/mysql/howto-configure-audit-logs-portal

Come integrare i log attività di Azure in monitoraggio di Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento dell'account di accesso

**Linee guida**: abilitare Advanced Threat Protection per database di Azure per MySQL per generare avvisi per attività sospette.

Usare le funzionalità di protezione delle identità e rilevamento dei rischi di Azure Active Directory per configurare risposte automatiche a azioni sospette rilevate. È possibile abilitare le risposte automatiche tramite Sentinel di Azure per implementare le risposte di sicurezza dell'organizzazione.

È anche possibile inserire i log in Sentinel di Azure per un'analisi più approfondita.

Come configurare Advanced Threat Protection per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Panoramica dei Azure AD Identity Protection:https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

Come visualizzare Azure AD accessi a rischio:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Come eseguire l'onboarding di Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: non applicabile; Customer Lockbox non è ancora supportato per database di Azure per MySQL.

Elenco dei servizi Customer Lockbox supportati:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: gestire un inventario delle informazioni riservate

**Linee guida**: usare i tag per facilitare il rilevamento delle istanze di database di Azure per MySQL o risorse correlate che archiviano o elaborano informazioni riservate.

Come creare e usare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Usare una combinazione di collegamento privato, endpoint di servizio e/o regole del firewall per isolare e limitare l'accesso alla rete per le istanze di database di Azure per MySQL.

Come creare sottoscrizioni di Azure aggiuntive:https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare Gruppi di gestione:https://docs.microsoft.com/azure/governance/management-groups/create

Come configurare un collegamento privato per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link

Come creare e gestire endpoint di servizio VNet e regole VNet in database di Azure per MySQL:https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

Come configurare le regole del firewall di database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/concepts-firewall-rules


**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: quando si usano le macchine virtuali di Azure per accedere alle istanze di database di Azure per MySQL, usare il collegamento privato, le configurazioni di rete MySQL, i gruppi di sicurezza di rete e i tag di servizio per attenuare la possibilità di exfiltration di dati.

Microsoft gestisce l'infrastruttura sottostante per database di Azure per MySQL e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

Come attenuare i dati exfiltration per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link#data-exfiltration-prevention

Informazioni sulla protezione dei dati dei clienti in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: crittografare tutte le informazioni riservate in transito

**Linee guida**: database di Azure per MySQL supporta la connessione del server MySQL alle applicazioni client tramite Secure Sockets Layer (SSL). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione. Per impostazione predefinita, nella portale di Azure verificare che "applica connessione SSL" sia abilitato per tutte le istanze di database di Azure per MySQL.

Attualmente la versione TLS supportata per database di Azure per MySQL è TLS 1,0, TLS 1,1, TLS 1,2.

Come configurare la crittografia in transito per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: identificazione dei dati, classificazione e funzionalità di prevenzione della perdita non ancora disponibili per database di Azure per MySQL. Implementare una soluzione di terze parti, se necessario ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e passa a grandi lunghezze per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e mantiene una suite di controlli e funzionalità affidabili per la protezione dei dati.

Informazioni sulla protezione dei dati dei clienti in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: usare RBAC di Azure per controllare l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso al piano di controllo del database di Azure per MySQL, ad esempio portale di Azure. Per l'accesso al piano dati (all'interno del database stesso), usare query SQL per creare gli utenti e configurare le autorizzazioni utente. RBAC non influisce sulle autorizzazioni utente all'interno del database.

Come configurare RBAC in Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Come configurare l'accesso utente con SQL per il database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-create-users

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per database di Azure per MySQL e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

Informazioni sulla protezione dei dati dei clienti in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: crittografare le informazioni riservate inattive

**Linee guida**: il servizio database di Azure per MySQL usa il modulo crittografico convalidato FIPS 140-2 per la crittografia di archiviazione dei dati inattivi. I dati, inclusi i backup, vengono crittografati su disco, ad eccezione dei file temporanei creati durante l'esecuzione delle query. Il servizio usa la crittografia AES a 256 bit inclusa nella crittografia di archiviazione di Azure e le chiavi vengono gestite dal sistema. La crittografia dell'archiviazione è sempre attiva e non può essere disabilitata.

La crittografia dei dati con chiavi gestite dal cliente per database di Azure per MySQL ti permette di usare la tua chiave (BYOK) per la protezione dei dati inattivi. A questo punto, è necessario richiedere l'accesso per usare questa funzionalità. A tale scopo, contattare:

AskAzureDBforMySQL@service.microsoft.com

Informazioni sulla crittografia dei dati inattivi per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/concepts-security

Come configurare le chiavi gestite dal cliente per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/concepts-data-encryption-mysql

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: registrare e segnalare le modifiche apportate alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle istanze di produzione del database di Azure per MySQL e altre risorse critiche o correlate.

Come creare avvisi per gli eventi del log attività di Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: eseguire strumenti di analisi automatica delle vulnerabilità

**Linee guida**: attualmente non disponibile; Il Centro sicurezza di Azure non supporta ancora la valutazione della vulnerabilità per database di Azure per MySQL.

Copertura delle funzionalità per i servizi PaaS di Azure nel centro sicurezza di Azure:https://docs.microsoft.com/azure/security-center/features-paas

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: distribuire la soluzione di gestione delle patch per il sistema operativo automatizzata

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: distribuire una soluzione di gestione delle patch software di terze parti automatizzata

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: confrontare le analisi delle vulnerabilità back-to-back

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Linee guida**: Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano database di Azure per MySQL.


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [controllo di sicurezza: inventario e gestione delle risorse](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: usare Azure Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (incluse le istanze di database di Azure per MySQL) nelle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Come creare query con Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Come visualizzare le sottoscrizioni di Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Informazioni su RBAC di Azure:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="62-maintain-asset-metadata"></a>6,2: gestire i metadati dell'asset

**Linee guida**: applicare tag a istanze di database di Azure per MySQL e ad altre risorse correlate che conferiscono ai metadati di organizzarle logicamente in una tassonomia.

Come creare e usare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle istanze di database di Azure per MySQL e delle risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Come creare sottoscrizioni di Azure aggiuntive:https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare Gruppi di gestione:https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e usare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: gestire un inventario delle risorse di Azure approvate e dei titoli software

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo e ad Azure nel suo complesso.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorare le risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare anche il grafico delle risorse di Azure per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

Come configurare e gestire i criteri di Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare query con Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo e ad Azure nel suo complesso.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="68-use-only-approved-applications"></a>6,8: usare solo le applicazioni approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="69-use-only-approved-azure-services"></a>6,9: usare solo i servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Come configurare e gestire i criteri di Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come negare un tipo di risorsa specifico con criteri di Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="610-implement-approved-application-list"></a>6,10: implementare l'elenco di applicazioni approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: limitare la capacità degli utenti di interagire con gestione risorse di Azure tramite script

**Linee guida**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "blocca l'accesso" per l'app "gestione Microsoft Azure". Ciò può impedire la creazione e le modifiche alle risorse all'interno di un ambiente di sicurezza elevato, ad esempio le istanze di database di Azure per MySQL contenenti informazioni riservate.

Come configurare l'accesso condizionale per bloccare l'accesso ai Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle applicazioni Web in esecuzione in app Azure servizio o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [controllo di sicurezza: configurazione sicura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le istanze di database di Azure per MySQL con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. DBforMySQL" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle istanze di database di Azure per MySQL. È anche possibile usare le definizioni di criteri predefinite correlate alle istanze del database di Azure per MySQL, ad esempio:

Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL

Come visualizzare gli alias dei criteri di Azure disponibili:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Come configurare e gestire i criteri di Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: stabilire configurazioni del sistema operativo sicure

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: mantenere sicure le configurazioni delle risorse di Azure

**Linee guida**: usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure nelle risorse di Azure.

Come configurare e gestire i criteri di Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Comprendere gli effetti dei criteri di Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: mantenere sicure le configurazioni del sistema operativo

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni personalizzate di criteri di Azure per le istanze di database di Azure per MySQL e risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

Come archiviare il codice in Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentazione Azure Repos:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: archiviare in modo sicuro immagini del sistema operativo personalizzate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. DBforMySQL" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni dei criteri.

Come configurare e gestire i criteri di Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. DBforMySQL" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [Deploy if not exist] per applicare automaticamente le configurazioni per le istanze di database di Azure per MySQL e le risorse correlate.

Come configurare e gestire i criteri di Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="711-manage-azure-secrets-securely"></a>7,11: gestire i segreti di Azure in modo sicuro

**Linee guida**: per le macchine virtuali o le applicazioni Web di Azure in esecuzione nel servizio app Azure usato per accedere alle istanze di database di Azure per MySQL, usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere il database di Azure per la gestione dei segreti MySQL. Verificare Key Vault l'eliminazione temporanea sia abilitata.

Come eseguire l'integrazione con le identità gestite di Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Come creare un Key Vault:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Come fornire Key Vault autenticazione con un'identità gestita:https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: gestire le identità in modo sicuro e automatico

**Linee guida**: l'istanza di database di Azure per MySQL supporta l'autenticazione Azure Active Directory (in anteprima) per accedere ai database.  Durante la creazione dell'istanza di database di Azure per MySQL, è possibile fornire le credenziali per un utente amministratore. Questo amministratore può essere utilizzato per creare altri utenti del database.  

Per le macchine virtuali o le applicazioni Web di Azure in esecuzione nel servizio app Azure usato per accedere alle istanze di database di Azure per MySQL, usare identità del servizio gestita insieme a Azure Key Vault per archiviare e recuperare le credenziali per l'istanza di database di Azure per MySQL. Verificare Key Vault l'eliminazione temporanea sia abilitata.

Usare identità gestite per fornire servizi di Azure con un'identità gestita automaticamente in Azure Active Directory (AD). Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Key Vault, senza credenziali nel codice.

Come configurare le identità gestite:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Come eseguire l'integrazione con le identità gestite di Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminare l'esposizione delle credenziali non intenzionali

**Indicazioni**: implementare Credential scanner per identificare le credenziali all'interno del codice. Credential scanner incoraggerà inoltre lo stato di trasferimento delle credenziali individuate a posizioni più sicure, ad esempio Azure Key Vault.

Come configurare Credential scanner:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [controllo di sicurezza: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: utilizzo del software antimalware gestito centralmente

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, database di Azure per SQL), ma non viene eseguito sui contenuti dei clienti.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: pre-analizzare i file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, database di Azure per MySQL), ma non viene eseguito sui contenuti dei clienti.

Pre-analizza i contenuti caricati in risorse di Azure non di calcolo, ad esempio servizio app, Data Lake Storage, archiviazione BLOB, database di Azure per MySQL e così via. Microsoft non è in grado di accedere ai dati in tali istanze.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: condiviso

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: assicurarsi che il software e le firme anti-malware siano aggiornati

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, database di Azure per MySQL), ma non viene eseguito sui contenuti dei clienti.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: N/A

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire l'esecuzione di backup automatici regolari

**Linee guida**: database di Azure per MySQL esegue i backup dei file di dati e del log delle transazioni. A seconda delle dimensioni massime di archiviazione supportate, si accettano backup completi e differenziali (4 TB Max Storage Servers) o backup di snapshot (fino a 16 TB Max Storage Servers). Questi backup consentono di ripristinare un server a qualsiasi momento specifico all'interno del periodo di conservazione dei backup configurato. Il periodo di conservazione dei backup predefinito è di sette giorni. Facoltativamente, è possibile configurare fino a 35 giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit.

Informazioni sui backup per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/concepts-backup

Informazioni sulla configurazione iniziale di database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/tutorial-design-database-using-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: condiviso

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: database di Azure per MySQL crea automaticamente backup del server e li archivia in un archivio con ridondanza locale o con ridondanza geografica, in base alla scelta dell'utente. I backup possono essere usati per ripristinare il server a un momento specifico. Il backup e il ripristino sono una parte essenziale di qualsiasi strategia di continuità aziendale, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. 

Se si usa Azure Key Vault per archiviare le credenziali per le istanze di database di Azure per MySQL, assicurarsi che i backup automatici delle chiavi siano regolari. 

Informazioni sui backup per database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/howto-restore-server-portal 

Come eseguire il backup di chiavi di Key Vault:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: condiviso

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: in database di Azure per MySQL, l'esecuzione di un ripristino crea un nuovo server dai backup del server originale. Sono disponibili due tipi di ripristino: ripristino temporizzato e ripristino geografico. Il ripristino temporizzato è disponibile con entrambe le opzioni di ridondanza per il backup e crea un nuovo server nella stessa area del server originale. Il ripristino geografico è disponibile solo se il server è stato configurato per l'archiviazione con ridondanza geografica e consente di ripristinare il server in un'area diversa.

Il tempo stimato per il ripristino dipende da diversi fattori, tra cui le dimensioni dei database, le dimensioni dei log delle transazioni, la larghezza di banda di rete e il numero totale di database ripristinati contemporaneamente nella stessa area. Il tempo di recupero di solito è inferiore a 12 ore.

Testare periodicamente il ripristino delle istanze di database di Azure per MySQL.

Informazioni su backup e ripristino nel database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/concepts-backup

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: database di Azure per MySQL esegue backup completi, differenziali e del log delle transazioni. Questi backup consentono di ripristinare un server a qualsiasi momento specifico all'interno del periodo di conservazione dei backup configurato. Il periodo di conservazione dei backup predefinito è di sette giorni. Facoltativamente, è possibile configurare fino a 35 giorni. Tutti i backup vengono crittografati con crittografia AES a 256 bit. Verificare Key Vault l'eliminazione temporanea sia abilitata.

Informazioni su backup e ripristino nel database di Azure per MySQL:https://docs.microsoft.com/azure/mysql/concepts-backup

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: creare una guida alla risposta agli eventi imprevisti

**Linee**guida: creare una guida alla risposta agli eventi imprevisti per la propria organizzazione. Verificare che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione/gestione degli eventi imprevisti dal rilevamento alla verifica post-evento imprevisto.

Come configurare le automazioni del flusso di lavoro nel centro sicurezza di Azure:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Linee guida per la creazione di un processo di risposta agli eventi imprevisti di sicurezza:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia di un evento imprevisto di Microsoft Security Response Center:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: creare una procedura per assegnazione di punteggi e assegnazione di priorità

**Indicazioni**: il Centro sicurezza assegna una gravità a ogni avviso per facilitare la priorità degli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, nonché sul livello di confidenza causato da un intento dannoso dietro l'attività che ha portato all'avviso.

Inoltre, contrassegnare chiaramente le sottoscrizioni (per es. produzione, non prod) e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="103-test-security-response-procedures"></a>10,3: testare le procedure di risposta alla sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare. Identificare i punti deboli e i gap e rivedere il piano in base alle esigenze.

Fare riferimento alla pubblicazione del NIST: Guida ai programmi di test, formazione e esercizio per i piani IT e le funzionalità:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: specificare i dettagli del contatto per gli eventi imprevisti della sicurezza e configurare le notifiche di avviso per gli eventi

**Linee guida**: le informazioni di contatto per gli eventi imprevisti di sicurezza verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che l'accesso ai dati del cliente è stato effettuato da un'entità illecita o non autorizzata.  Esaminare gli eventi imprevisti dopo il fatto di garantire la risoluzione dei problemi.

Come impostare il contatto di sicurezza del Centro sicurezza di Azure:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo e continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere la sentinella degli avvisi.

Come configurare l'esportazione continua:https://docs.microsoft.com/azure/security-center/continuous-export

Come trasmettere gli avvisi in Sentinel di Azure:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nelle raccomandazioni.

Come configurare l'automazione del flusso di lavoro e le app per la logica:https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [controllo di sicurezza: test di penetrazione e esercizi del Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza entro 60 giorni

**Linee guida**: seguire le regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

È possibile trovare altre informazioni sulla strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft, qui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere il [benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Scopri di più sulle [linee di base di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
