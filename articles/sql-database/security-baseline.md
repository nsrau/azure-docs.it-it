---
title: Baseline della sicurezza di Azure per il database SQL di Azure
description: Baseline della sicurezza di Azure per il database SQL di Azure
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ca8d0daf5b6d9bbad0d8fa24b4b150c5e6cd6b73
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300911"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Baseline della sicurezza di Azure per il database SQL di Azure

La linea di base di sicurezza di Azure per il database SQL di Azure contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La linea di base per questi servizi viene disegnata dalla [versione 1,0 del benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce indicazioni su come proteggere le soluzioni cloud in Azure con le procedure consigliate.

Per altre informazioni, vedere [Panoramica della sicurezza di base di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

**Linee guida**: è possibile abilitare il collegamento privato di Azure per consentire l'accesso ai servizi PaaS di Azure, ad esempio al database SQL, e ai servizi cliente/partner ospitati in Azure tramite un endpoint privato nella rete virtuale. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. Per consentire al traffico di raggiungere il database SQL di Azure, usare i tag del servizio SQL per consentire il traffico in uscita attraverso i gruppi di sicurezza di rete.


Le regole della rete virtuale consentono al database SQL di Azure di accettare solo le comunicazioni inviate dalle subnet selezionate all'interno di una rete virtuale.


Come configurare un collegamento privato per il database SQL di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database


Come usare gli endpoint del servizio rete virtuale e le regole per i server di database:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

**Linee guida**: usare il Centro sicurezza di Azure e correggere i consigli sulla protezione della rete per la subnet in cui è distribuito il server di database SQL di Azure. Per le macchine virtuali (VM) di Azure che si connetteranno all'istanza del server di database SQL di Azure, abilitare i log di flusso del gruppo di sicurezza di rete (NSG) per la gruppi che protegge tali macchine virtuali e inviare i log a un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log dei flussi di NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete non configurate.


Come abilitare i log dei flussi di NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


Come abilitare e usare Analisi del traffico:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="13-protect-critical-web-applications"></a>1,3: proteggere le applicazioni Web critiche

**Linee guida**: non applicabile; Questa raccomandazione è destinata al servizio app di Azure o alle risorse di calcolo che ospitano applicazioni Web.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare protezione DDoS standard nelle reti virtuali associate alle istanze di SQL Server per la protezione da attacchi Denial of Service distribuiti. Usare il Centro sicurezza di Azure Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP Internet dannosi noti o non usati.


Come configurare la protezione DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Informazioni sull'Intelligence per le minacce integrata nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrare i pacchetti di rete e i log di flusso

**Linee guida**: per le macchine virtuali (VM) di Azure che si connetteranno all'istanza del database SQL di Azure, abilitare i log di flusso del gruppo di sicurezza di rete (NSG) per il gruppi che protegge tali VM e inviare i log a un account di archiviazione di Azure per il controllo del traffico. Se necessario per l'analisi dell'attività anomala, abilitare Network Watcher acquisizione pacchetti.


Come abilitare i log dei flussi di NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Come abilitare Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (IDS/IP)

**Linee guida**: abilitazione di Advanced Threat Protection (ATP) per il database SQL di Azure.  Gli utenti ricevono un avviso in caso di attività di database sospetta, potenziali vulnerabilità e attacchi SQL injection, nonché in caso di modelli di query e accesso ai database anomali. Advanced Threat Protection integra anche gli avvisi con il Centro sicurezza di Azure.

Comprendere e usare Advanced Threat Protection per il database SQL di Azure: https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="17-manage-traffic-to-web-applications"></a>1,7: gestire il traffico per le applicazioni Web

**Linee guida**: non applicabile; Questa raccomandazione è destinata al servizio app di Azure o alle risorse di calcolo che ospitano applicazioni Web.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ridurre la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag di servizio in base alla modifica degli indirizzi.


Quando si usano gli endpoint di servizio per il database SQL di Azure, è necessario aprire indirizzi IP pubblici in uscita per database SQL di Azure: i gruppi di sicurezza di rete (gruppi) devono essere aperti agli IP del database SQL di Azure per consentire la connettività. A tale scopo, è possibile usare i tag del servizio NSG per il database SQL di Azure.


Informazioni sui tag del servizio con gli endpoint di servizio per il database SQL di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations


Comprendere e usare i tag del servizio:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: mantenere le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza di rete per le istanze del server di database SQL di Azure con criteri di Azure. È possibile usare lo spazio dei nomi "Microsoft. SQL" per definire le definizioni dei criteri personalizzati o usare una qualsiasi delle definizioni dei criteri predefinite progettate per la protezione di rete del server di database SQL di Azure. Un esempio di criteri di sicurezza di rete incorporati applicabili per il server di database SQL di Azure è il seguente: "SQL Server usare un endpoint del servizio di rete virtuale".
 

USA i progetti di Azure per semplificare le distribuzioni di Azure su larga scala mediante la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di gestione risorse di Azure, controllo degli accessi in base al ruolo e criteri, in una singola definizione di progetto. Applica facilmente il progetto a nuove sottoscrizioni e ambienti e ottimizza il controllo e la gestione tramite il controllo delle versioni.


Come configurare e gestire i criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Come creare un Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="110-document-traffic-configuration-rules"></a>1,10: regole di configurazione del traffico documento

**Indicazioni**: usare i tag per i gruppi di sicurezza di rete (NSG) e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le singole regole NSG, usare il campo "Description" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.


Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.


È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.


Come creare e usare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usare gli strumenti automatici per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze del server di database SQL di Azure. Creare avvisi in monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse di rete critiche.


Come visualizzare e recuperare gli eventi del log attività di Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view


Come creare avvisi in monitoraggio di Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: utilizzare origini di sincronizzazione dell'ora approvate

**Linee guida**: Microsoft gestisce le origini temporali per le risorse di Azure. È possibile aggiornare la sincronizzazione dell'ora per le distribuzioni di calcolo.



Come configurare la sincronizzazione dell'ora per le risorse di calcolo di Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: configurare la gestione dei log di sicurezza centrale

**Linee guida**: abilitare il controllo per il database SQL di Azure per tenere traccia degli eventi di database e scriverli in un log di controllo nell'account di archiviazione di azure, log Analytics area di lavoro o hub eventi.


Inoltre, è possibile trasmettere dati di telemetria di diagnostica di Azure SQL in Analisi SQL di Azure, una soluzione cloud che monitora le prestazioni di database SQL di Azure, pool elastici e istanze gestite su larga scala e in più sottoscrizioni. Può aiutare a raccogliere e visualizzare le metriche sulle prestazioni del Database SQL di Azure e ha la funzionalità di intelligence integrata per la risoluzione dei problemi.


Come configurare il controllo per il database SQL di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing


Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging


Come eseguire lo streaming di diagnostica in Analisi SQL di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-diagnostic-telemetry-into-sql-analytics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare il controllo nell'istanza del server di database SQL di Azure e scegliere un percorso di archiviazione per i log di controllo (archiviazione di azure, log Analytics o hub eventi).


Come abilitare il controllo per SQL Server di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: raccogliere i registri di sicurezza dai sistemi operativi

**Linee guida**: non applicabile; questo benchmark è destinato alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2,5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni**: quando si archiviano i log del database SQL di Azure in un'area di lavoro log Analytics, impostare il periodo di conservazione dei log in base alle normative di conformità dell'organizzazione.



Come impostare i parametri di conservazione dei log:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: analizzare e monitorare i log per i comportamenti anomali ed esaminare periodicamente i risultati. Usare la protezione avanzata dalle minacce del Centro sicurezza di Azure per segnalare attività insolite correlate all'istanza del database SQL di Azure. In alternativa, configurare gli avvisi in base ai valori delle metriche o alle voci del log attività di Azure correlate alle istanze del database SQL di Azure.


Informazioni sulla protezione avanzata dalle minacce e sugli avvisi per SQL Server di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview


Come configurare gli avvisi personalizzati per il database SQL di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: abilitare gli avvisi per attività anomale

**Linee guida**: usare il Centro sicurezza di Azure Advanced Threat Protection per i database SQL di Azure per il monitoraggio e l'invio di avvisi sulle attività anomale. Abilitare la sicurezza dei dati avanzata per i database SQL. La sicurezza dei dati avanzata include funzionalità per l'individuazione e la classificazione dei dati sensibili, l'emersione e la mitigazione di potenziali vulnerabilità del database e il rilevamento di attività anomale che potrebbero indicare una minaccia per il database.



Informazioni sulla protezione avanzata dalle minacce e sugli avvisi per il database SQL di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview



Come abilitare la sicurezza dei dati avanzata per il database SQL di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security



Come gestire gli avvisi nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizzare la registrazione anti-malware

**Linee guida**: non applicabile; per SQL Server di Azure, la soluzione anti-malware viene gestita da Microsoft sulla piattaforma sottostante.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="29-enable-dns-query-logging"></a>2,9: abilitare la registrazione delle query DNS

**Linee guida**: non applicabile; La registrazione DNS non è applicabile al SQL Server di Azure.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2,10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: non applicabile; il controllo da riga di comando non è applicabile ad Azure SQL Server.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="identity-and-access-control"></a>Controllo di identità e accesso

*Per altre informazioni, vedere [controllo di sicurezza: identità e controllo di accesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: gestire un inventario degli account amministrativi

**Linee guida**: Azure Active Directory (AAD) include ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere sottoposte a query. Usare il modulo AAD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.


Come ottenere un ruolo della directory in Azure AD con PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="32-change-default-passwords-where-applicable"></a>3,2: modificare le password predefinite se applicabile

**Linee guida**: Azure Active Directory non ha il concetto di password predefinite. Quando si esegue il provisioning di un'istanza del database SQL di Azure, è consigliabile scegliere di integrare l'autenticazione con Azure Active Directory.


Come configurare e gestire l'autenticazione Azure Active Directory con SQL di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: usare account amministrativi dedicati

**Linee guida**: creare criteri e procedure per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure per monitorare il numero di account amministrativi.



Conoscere l'identità e l'accesso del Centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida**: non applicabile; Sebbene sia possibile configurare l'autenticazione Azure Active Directory per l'integrazione con SQL Server di Azure, Single Sign-On non è supportata.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più multi-factor authentication Azure Active Directory fattori (multi-factor authentication) e seguire le indicazioni sulla gestione delle identità e degli accessi nel centro sicurezza di Azure


Come abilitare l'autenticazione a più fattori in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


Come monitorare identità e accesso nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: usare macchine virtuali dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni**: usare una workstation con accesso con privilegi (Paw) con multi-factor authentication multi-factor authentication configurato per accedere e configurare le risorse di Azure.


Informazioni sulle workstation con accesso con privilegi:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


Come abilitare l'autenticazione a più fattori in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: registrare e segnalare l'attività sospetta da account amministrativi

**Linee guida**: usare Azure Active Directory report di sicurezza per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente.



Usare Advanced Threat Protection per il database SQL di Azure per rilevare attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database.



Come identificare Azure AD utenti contrassegnati per le attività rischiose:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk



Come monitorare l'identità degli utenti e l'attività di accesso nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access



Esaminare Advanced Threat Protection e potenziali avvisi:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: gestire le risorse di Azure solo dalle località approvate

**Linee guida**: usare i percorsi denominati di accesso condizionale per consentire l'accesso al portale e alla gestione delle risorse di Azure solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree.


Come configurare le località denominate in Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="39-use-azure-active-directory"></a>3,9: usare Azure Active Directory

**Linee guida**: creare un amministratore di Azure Active Directory (AAD) per le istanze del server di database SQL di Azure.


Come configurare e gestire l'autenticazione Azure Active Directory con SQL di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure


Come creare e configurare un'istanza di AAD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: rivedere e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure Active Directory (AAD) fornisce i log per individuare gli account obsoleti. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso degli utenti può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo.


Come usare le verifiche di accesso alle identità di Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: il monitoraggio tenta di accedere agli account disattivati

**Indicazioni**: configurare l'autenticazione Azure Active Directory (AAD) con SQL di Azure e creare le impostazioni di diagnostica per Azure Active Directory account utente, inviando i log di controllo e i log di accesso a un'area di lavoro log Analytics. Configurare gli avvisi desiderati nell'area di lavoro Log Analytics.


Come configurare e gestire l'autenticazione Azure Active Directory con SQL di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure


Come integrare i log attività di Azure in monitoraggio di Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento dell'account di accesso

**Indicazioni**: usare la protezione delle identità Azure Active Directory (AAD) e i rilevamenti di rischi per configurare risposte automatiche per rilevare azioni sospette correlate alle identità utente. Inoltre, è possibile inserire dati in Sentinel di Azure per un'analisi più approfondita.


Come visualizzare gli accessi ai rischi Azure AD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


Come configurare e abilitare i criteri di rischio di Identity Protection:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: in scenari di supporto in cui Microsoft deve accedere ai dati dei clienti, Azure Customer Lockbox fornisce un'interfaccia che consente ai clienti di rivedere e approvare o rifiutare le richieste di accesso ai dati dei clienti.


Informazioni Customer Lockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per semplificare il monitoraggio delle risorse di Azure che archiviano o elaborano informazioni riservate.


Come creare e usare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Le risorse devono essere separate da VNET/subnet, contrassegnate in modo appropriato e protette in un NSG o in un firewall di Azure. Le risorse che archiviano o elaborano dati sensibili devono essere isolate. USA collegamento privato; Distribuisci SQL Server di Azure all'interno della VNET e connettili privatamente usando endpoint privati.



Come creare sottoscrizioni di Azure aggiuntive:

https://docs.microsoft.com/azure/billing/billing-create-subscription



Come creare Gruppi di gestione:

https://docs.microsoft.com/azure/governance/management-groups/create



Come creare e usare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



Come configurare un collegamento privato per il database SQL di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Indicazioni**: per i database SQL di Azure che archiviano o elaborano informazioni riservate, contrassegnare il database e le risorse correlate come sensibili usando i tag. Configurare il collegamento privato insieme ai tag di servizio del gruppo di sicurezza di rete nelle istanze del database SQL di Azure per impedire la exfiltration di informazioni riservate.



Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e passa a grandi lunghezze per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e mantiene una suite di controlli e funzionalità affidabili per la protezione dei dati.



Come configurare il collegamento privato e gruppi per impedire il exfiltration dei dati nelle istanze del database SQL di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview



Informazioni sulla protezione dei dati dei clienti in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: crittografare tutte le informazioni riservate in transito

**Linee guida**: il database SQL di Azure protegge i dati mediante la crittografia dei dati in movimento con Transport Layer Security. SQL Server impone sempre la crittografia (SSL/TLS) per tutte le connessioni. Ciò garantisce che tutti i dati siano crittografati in transito tra il client e il server indipendentemente dall'impostazione di Encrypt o TrustServerCertificate nella stringa di connessione.



Informazioni sulla crittografia SQL di Azure in transito:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni**: usare la funzionalità di individuazione e classificazione dei dati del database SQL di Azure. Individuazione e classificazione dei dati offre funzionalità avanzate incorporate nel database SQL di Azure per l'individuazione, la classificazione, l'assegnazione di etichette &amp; la protezione dei dati sensibili nei database.



Come usare l'individuazione e la classificazione dei dati per SQL Server di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: usare RBAC di Azure per controllare l'accesso alle risorse

**Linee guida**: usare Azure Active Directory (AAD) per l'autenticazione e il controllo dell'accesso alle istanze del database SQL di Azure.


Come integrare SQL Server di Azure con Azure Active Directory per l'autenticazione:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication


Come controllare l'accesso in SQL Server di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: Microsoft gestisce l'infrastruttura sottostante per il database SQL di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

Informazioni sulla protezione dei dati dei clienti in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: crittografare le informazioni riservate inattive

**Indicazioni**: Transparent Data Encryption (Transparent Data Encryption) consente di proteggere il database SQL di Azure, l'istanza gestita di SQL di Azure e Azure Data Warehouse contro la minaccia di attività offline dannose mediante la crittografia dei dati inattivi. Esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni inattivi, senza richiedere modifiche dell'applicazione. Per impostazione predefinita, la tecnologia TDE è abilitata per tutti i database SQL di Azure appena distribuiti. La chiave di crittografia Transparent Data Encryption può essere gestita da Microsoft o dal cliente.


Come gestire Transparent Data Encryption e usare le proprie chiavi di crittografia:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: registrare e segnalare le modifiche apportate alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle istanze di produzione dei database SQL di Azure e altre risorse critiche o correlate.


Come creare avvisi per gli eventi del log attività di Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: eseguire strumenti di analisi automatica delle vulnerabilità

**Linee guida**: abilitare la sicurezza dei dati avanzata per il database SQL di Azure e seguire le raccomandazioni del Centro sicurezza di Azure per l'esecuzione di valutazioni delle vulnerabilità sui server SQL di Azure.



Come eseguire valutazioni delle vulnerabilità nei database SQL di Azure:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment



Come abilitare la sicurezza avanzata dei dati:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security



Come implementare le raccomandazioni per la valutazione della vulnerabilità del Centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: distribuire la soluzione di gestione delle patch per il sistema operativo automatizzata

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: distribuire una soluzione di gestione delle patch software di terze parti automatizzata

**Linee guida**: non applicabile; questo benchmark è destinato alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: confrontare le analisi delle vulnerabilità back-to-back

**Linee guida**: abilitare analisi periodiche ricorrenti per le istanze del database SQL di Azure. verrà configurata una valutazione della vulnerabilità per eseguire automaticamente un'analisi del database una volta alla settimana. Verrà inviato un riepilogo dei risultati di analisi agli indirizzi di posta elettronica forniti. Confrontare i risultati per verificare che le vulnerabilità siano state corrette.



Come esportare un report di valutazione della vulnerabilità nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: usare le classificazioni di rischio predefinite (Punteggio sicuro) fornite dal centro sicurezza di Azure.

Informazioni sul punteggio sicuro del Centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [controllo di sicurezza: inventario e gestione delle risorse](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: usare Azure Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (incluse le istanze di Azure SQL Server) nelle sottoscrizioni.  Assicurarsi di disporre delle autorizzazioni (lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.


Sebbene le risorse di Azure classiche possano essere individuate tramite Graph di risorse, è consigliabile creare e usare Azure Resource Manager risorse in futuro.


Come creare query con Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


Come visualizzare le sottoscrizioni di Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


Informazioni su RBAC di Azure: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="62-maintain-asset-metadata"></a>6,2: gestire i metadati dell'asset

**Linee guida**: applicare i tag alle risorse di Azure fornendo metadati per organizzarli in modo logico in una tassonomia.



Come creare e usare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia degli asset. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.



Come creare sottoscrizioni di Azure aggiuntive:

https://docs.microsoft.com/azure/billing/billing-create-subscription



Come creare Gruppi di gestione:

https://docs.microsoft.com/azure/governance/management-groups/create



Come creare e usare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: gestire un inventario delle risorse di Azure approvate e dei titoli software

**Linee guida**: definire l'elenco delle risorse di Azure approvate e il software approvato per le risorse di calcolo

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorare le risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

Come configurare e gestire i criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare query con Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="68-use-only-approved-applications"></a>6,8: usare solo le applicazioni approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="69-use-only-approved-azure-services"></a>6,9: usare solo i servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

Come configurare e gestire i criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come negare un tipo di risorsa specifico con criteri di Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="610-implement-approved-application-list"></a>6,10: implementare l'elenco di applicazioni approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle applicazioni in esecuzione sulle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: limitare la capacità degli utenti di interagire con gestione risorse di Azure tramite script

**Guida**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "blocca l'accesso" per l'app "gestione Microsoft Azure".


Come configurare l'accesso condizionale per bloccare l'accesso ai Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo o al servizio app che ospitano applicazioni desktop o Web.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [controllo di sicurezza: configurazione sicura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: definire configurazioni sicure per tutte le risorse di Azure

**Indicazioni**: usare i criteri di Azure o i consigli del Centro sicurezza di Azure per i server/database SQL di Azure per gestire le configurazioni di sicurezza per tutte le risorse di Azure.


Come configurare e gestire i criteri di Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: stabilire configurazioni del sistema operativo sicure

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: mantenere sicure le configurazioni delle risorse di Azure

**Linee guida**: usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure nelle risorse di Azure.



Come configurare e gestire i criteri di Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



Comprendere gli effetti dei criteri di Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: mantenere sicure le configurazioni del sistema operativo

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni di criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire il codice in modo sicuro.



Come archiviare il codice in Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops



Documentazione Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: archiviare in modo sicuro immagini del sistema operativo personalizzate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. SQL" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni dei criteri.



Come configurare e gestire i criteri di Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

**Indicazioni**: sfruttare il Centro sicurezza di Azure per eseguire analisi di base per i database e i server SQL di Azure.



Come correggere le raccomandazioni nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7,11: gestire i segreti di Azure in modo sicuro

**Linee guida**: usare Azure Key Vault per archiviare le chiavi di crittografia per il database SQL di Azure Transparent Data Encryption (Transparent Data Encryption).



Come proteggere i dati sensibili archiviati in Azure SQL Server e archiviare le chiavi di crittografia in Azure Key Vault:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: gestire le identità in modo sicuro e automatico

**Linee guida**: usare identità gestite per fornire servizi di Azure con un'identità gestita automaticamente in Azure Active Directory (AAD). Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione AAD, incluso Azure Key Vault, senza credenziali nel codice.


Esercitazione: usare un'identità gestita assegnata dal sistema VM Windows per accedere a SQL di Azure:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql


Come configurare le identità gestite:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminare l'esposizione delle credenziali non intenzionali

**Indicazioni**: implementare Credential scanner per identificare le credenziali all'interno del codice. Credential scanner incoraggerà inoltre lo stato di trasferimento delle credenziali individuate a posizioni più sicure, ad esempio Azure Key Vault. 

Come configurare Credential scanner: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [controllo di sicurezza: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: utilizzo del software antimalware gestito centralmente

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo. Microsoft gestisce l'anti-malware per la piattaforma sottostante.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: pre-analizzare i file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, app Azure servizio), tuttavia non viene eseguito sui contenuti del cliente.


Pre-analizza i contenuti caricati in risorse di Azure non di calcolo, ad esempio il servizio app, Data Lake Storage, archiviazione BLOB, Azure SQL Server e così via. Microsoft non è in grado di accedere ai dati in tali istanze.


Informazioni su Microsoft antimalware per servizi cloud e macchine virtuali di Azure: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: assicurarsi che il software e le firme anti-malware siano aggiornati

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo. Microsoft gestisce l'anti-malware per la piattaforma sottostante.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="data-recovery"></a>Recupero dei dati

*Per altre informazioni, vedere [controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire l'esecuzione di backup automatici regolari

**Linee guida**: per proteggere l'azienda dalla perdita di dati, il database SQL di Azure crea automaticamente backup completi del database, backup differenziali del database ogni 12 ore e backup del log delle transazioni ogni 5-10 minuti. I backup vengono archiviati nell'archiviazione RA-GRS per almeno 7 giorni per tutti i livelli di servizio. Tutti i livelli di servizio ad eccezione supporto Basic periodo di conservazione dei backup configurabile per il ripristino temporizzato, fino a 35 giorni.


Per soddisfare i diversi requisiti di conformità, è possibile selezionare diversi periodi di conservazione per i backup settimanali, mensili e/o annuali. L'utilizzo delle risorse di archiviazione dipende dalla frequenza selezionata con cui vengono eseguiti backup e dal periodo di conservazione.


Informazioni sui backup e sulla continuità aziendale con SQL Server di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: condiviso

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: il database SQL di Azure crea automaticamente i backup del database mantenuti da 7 a 35 giorni e usa l'archiviazione con ridondanza geografica e accesso in lettura di Azure (RA-GRS) per assicurarsi che vengano conservati anche se il Data Center non è disponibile. Questi backup vengono creati automaticamente. Se necessario, abilitare i backup con ridondanza geografica a lungo termine per i database SQL di Azure.


Se si usano chiavi gestite dal cliente per Transparent Data Encryption, assicurarsi che venga eseguito il backup delle chiavi.


Informazioni sui backup in SQL Server di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database


Come eseguire il backup delle chiavi di Key Vault in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: garantire la possibilità di eseguire periodicamente il ripristino dei dati del contenuto all'interno di backup di Azure. Se necessario, testare il ripristino del contenuto a una VLAN isolata. Testare il ripristino delle chiavi gestite dal cliente sottoposte a backup.


Come ripristinare le chiavi di Key Vault in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


Come ripristinare i backup del database SQL di Azure con il ripristino temporizzato:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: abilitare l'eliminazione temporanea in Azure Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.


Come abilitare l'eliminazione temporanea in Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: creare una guida alla risposta agli eventi imprevisti

**Indicazioni**: assicurarsi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono i ruoli del personale, nonché le fasi di gestione/gestione degli eventi imprevisti.



Come configurare le automazioni del flusso di lavoro nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: creare una procedura per assegnazione di punteggi e assegnazione di priorità

**Linee guida**: il Centro sicurezza assegna un livello di gravità agli avvisi, in modo da consentire la priorità dell'ordine in cui si partecipa a ogni avviso, in modo che, quando una risorsa viene compromessa, è possibile accedervi immediatamente. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, nonché sul livello di confidenza causato da un intento dannoso dietro l'attività che ha portato all'avviso.
Avvisi di sicurezza nel centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="103-test-security-response-procedures"></a>10,3: testare le procedure di risposta alla sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare. Identificare i punti deboli e i gap e rivedere il piano in base alle esigenze.



È possibile fare riferimento alla pubblicazione del NIST: Guida ai programmi di test, formazione e esercizio per i piani IT e le funzionalità:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: specificare i dettagli del contatto per gli eventi imprevisti della sicurezza e configurare le notifiche di avviso per gli eventi

**Linee guida**: le informazioni di contatto per gli eventi imprevisti di sicurezza verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati da parte di utenti non autorizzati o non autorizzati.



Come impostare il contatto di sicurezza del Centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo e continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi a Sentinel.


Come configurare l'esportazione continua:

https://docs.microsoft.com/azure/security-center/continuous-export


Come trasmettere gli avvisi in Sentinel di Azure:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nelle raccomandazioni.



Come configurare l'automazione del flusso di lavoro e le app per la logica:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [controllo di sicurezza: test di penetrazione e esercizi del Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza entro 60 giorni

**Linee guida**: seguire le regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.



È possibile trovare altre informazioni sulla strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestiti da Microsoft, qui: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere il [benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Scopri di più sulle [linee di base di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
