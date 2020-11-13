---
title: Baseline della sicurezza di Azure per il firewall applicazione Web di Azure
description: La linea di base di sicurezza del firewall applicazione Web di Azure fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: cb5fae022957dd5fdc56058ed08b92734929d13e
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562299"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Baseline della sicurezza di Azure per il firewall applicazione Web di Azure

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 1,0](../security/benchmarks/overview-v1.md) al firewall dell'applicazione Web di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili al firewall dell'applicazione Web di Azure. I **controlli** non applicabili al firewall dell'applicazione Web di Azure sono stati esclusi. 

Per informazioni sul modo in cui il firewall applicazione Web di Azure è completamente mappato al benchmark di sicurezza di Azure, vedere il [file di mapping di base della sicurezza del firewall applicazione Web](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)di Azure completo.

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee guida** : usare Microsoft Azure Web Application Firewall (WAF) per la protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni come SQL injection e gli script tra siti. 

- Modalità di rilevamento: usare questa modalità per apprendere il traffico di rete, comprendere ed esaminare i falsi positivi. Monitora e registra tutti gli avvisi di minaccia. Assicurarsi che i log di diagnostica e WAF siano selezionati e attivati. Si noti che WAF non blocca le richieste in ingresso quando funziona in modalità di rilevamento.
- Modalità di prevenzione: blocca le intrusioni e gli attacchi rilevati dalle regole. Un utente malintenzionato riceve un'eccezione "403 accesso non autorizzato" e la connessione viene chiusa. La modalità di prevenzione registra tali attacchi nei log di WAF.

Basare il traffico di rete con la modalità di rilevamento WAF. Dopo aver determinato le esigenze di traffico, configurare WAF in modalità di prevenzione per il traffico indesiderato di Bock.

Segui le raccomandazioni con gravità elevata del Centro sicurezza per qualsiasi risorsa abilitata per il Web non protetta da WAF.  

- [Regole e gruppi di regole CRS del Web Application Firewall](ag/application-gateway-crs-rulegroups-rules.md) 

- [Modalità WAF sul gateway applicazione](ag/ag-overview.md#waf-modes)

- [Modalità WAF sulla porta anteriore](afds/afds-overview.md#waf-modes)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida** : usare regole personalizzate con il Web Application Firewall (WAF) di Azure per consentire e bloccare il traffico. Ad esempio, tutto il traffico proveniente da un intervallo di indirizzi IP può essere bloccato. Configurare Azure WAF per l'esecuzione in modalità di prevenzione che blocca le intrusioni e gli attacchi rilevati dalle regole. Un utente malintenzionato riceve un'eccezione "403 accesso non autorizzato" e la connessione viene chiusa. La modalità di prevenzione registra tali attacchi nei log di WAF.

- [Modalità WAF sul gateway applicazione](ag/ag-overview.md#waf-modes)

- [Modalità WAF sulla porta anteriore](afds/afds-overview.md#waf-modes)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee guida** : Web Application Firewall (WAF) di Azure è il componente principale delle protezioni per le applicazioni Web di Azure. USA Azure WAF per fornire una protezione centralizzata per le applicazioni Web da exploit e vulnerabilità comuni con regole di confronto gestite preconfigurate in base alle firme di attacco note delle prime 10 categorie di OWASP.

Personalizzare Azure WAF con regole e gruppi di regole in base ai requisiti delle applicazioni Web ed eliminare falsi positivi. Associare un criterio di Azure WAF per ogni sito dietro un WAF per consentire la configurazione specifica del sito. Azure WAF supporta il filtro geografico, la limitazione della frequenza e le regole predefinite gestite da Azure. è possibile creare regole personalizzate per soddisfare le esigenze di un'applicazione. 

Configurare la WAF di Azure per l'esecuzione in modalità di prevenzione dopo la baseline del traffico di rete in modalità di rilevamento per un periodo di tempo determinato. Il WAF di Azure blocca le intrusioni e gli attacchi rilevati dalle regole in modalità di prevenzione. Un utente malintenzionato riceve un'eccezione "403 accesso non autorizzato" e la connessione viene chiusa. La modalità di prevenzione registra tali attacchi nei log di WAF.

- [Modalità WAF sul gateway applicazione](ag/ag-overview.md#waf-modes)

- [Modalità WAF sulla porta anteriore](afds/afds-overview.md#waf-modes)

- [Regole e gruppi di regole CRS del Web Application Firewall](ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida** : creare, associare e organizzare logicamente le risorse in una sottoscrizione di Azure con tag per il rilevamento di configurazioni errate delle applicazioni comuni (ad esempio, Apache e IIS). 

Applicare regole e gruppi di regole ai criteri di Web Application Firewall (WAF) di Azure in base ai metadati dei tag applicati.

- [Criteri di WAF sul gateway applicazione](/cli/azure/network/application-gateway/waf-policy?view=azure-cli-latest) 

- [Criteri di WAF sulla porta anteriore](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni** : usare i tag per i gruppi di sicurezza di rete associati al Web Application Firewall (WAF) di Azure nella subnet del gateway applicazione Azure, nonché altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le singole regole del gruppo di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali, la durata e così via, per tutte le regole che consentono il traffico da o verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

Scegliere Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida** : usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le impostazioni di rete e le risorse correlate alle distribuzioni di Web Application Firewall (WAF) di Azure. Creare avvisi in monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle impostazioni o alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Linee guida** : creare una regola di rete per il Web Application Firewall (WAF) di Azure per consentire l'accesso a un server NTP con la porta e il protocollo appropriati, ad esempio la porta 123 su UDP.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida** : configurare i log di Web Application Firewall (WAF) di Azure da inviare a una soluzione di gestione dei log di sicurezza centralizzata, ad esempio Azure Sentinel o un Siem di terze parti. Questi log includono attività di Azure, diagnostica e log WAF in tempo reale, che possono essere visualizzati in diversi strumenti, ad esempio monitoraggio di Azure, Excel e Power BI. I log del firewall applicazione Web di Azure forniscono informazioni sui dati che la WAF di Azure sta valutando, associando e bloccando.

Azure Sentinel include una cartella di lavoro predefinita di Azure WAF, che offre una panoramica degli eventi di sicurezza nella WAF di Azure. Questa cartella di lavoro include eventi, regole corrispondenti e bloccate e tutto il resto che viene registrato nei log del firewall. Questa telemetria può essere usata per avviare l'automazione PlayBook per notificare o eseguire azioni correttive in base agli eventi WAF raccolti da Sentinel.

- [Visualizzare i log attività](../azure-resource-manager/management/view-activity-logs.md)

- [Log di diagnostica per il gateway applicazione](../application-gateway/application-gateway-diagnostics.md)

- [Connettere i dati da Microsoft web application firewall ad Azure Sentinel](../sentinel/connect-azure-waf.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida** : abilitare la registrazione sulle risorse del Web Application Firewall (WAF) di Azure per l'accesso ai log di controllo, sicurezza e diagnostica. Il Web Application Firewall di Azure fornisce report dettagliati su ogni minaccia rilevata che viene resa disponibile nei log di diagnostica configurati. I log attività, che sono automaticamente disponibili, includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili.

- [Panoramica della registrazione](ag/ag-overview.md#logging)

- [Panoramica delle query di log di monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md)

- [Panoramica dei log della piattaforma Azure](../azure-monitor/platform/platform-logs-overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida** : inviare i log di Azure Web Application Firewall (WAF) a un account di archiviazione personalizzato e definire i criteri di conservazione. Usare monitoraggio di Azure per impostare il periodo di conservazione dell'area di lavoro Log Analytics in base ai requisiti di conformità dell'organizzazione.
- [Configurare il monitoraggio per un account di archiviazione](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida** : Web Application Firewall (WAF) di Azure fornisce report dettagliati su ogni minaccia rilevata. La registrazione è integrata con i log di Diagnostica di Azure che forniscono informazioni dettagliate sulle operazioni e sugli errori importanti per il controllo e la risoluzione dei problemi. 

Le istanze di Azure WAF sono integrate con il Centro sicurezza per inviare avvisi e informazioni di integrità per la creazione di report. Usare le raccomandazioni del Centro sicurezza per rilevare le applicazioni Web non protette e proteggere le risorse vulnerabili. 

Azure Sentinel include una cartella di lavoro di eventi WAF-firewall incorporata, che fornisce una panoramica degli eventi di sicurezza in WAF. Sono inclusi eventi, regole corrispondenti e bloccate e tutto il resto che viene registrato nei log del firewall.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/index.yml) 

- [Come abilitare le impostazioni di diagnostica per applicazione Azure gateway](../application-gateway/application-gateway-diagnostics.md)

- [Monitoraggio di metriche e log in front-end di Azure](../frontdoor/front-door-diagnostics.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida** : abilitare le impostazioni di diagnostica del log attività di Azure, nonché le impostazioni di diagnostica per la WAF di Azure e inviare i log a un'area di lavoro log Analytics. Eseguire query in Log Analytics per cercare termini, identificare le tendenze, analizzare i modelli e ottenere molte altre informazioni dettagliate basate sui dati raccolti. Crea avvisi per attività anomale in base alle metriche di WAF. Se, ad esempio, il numero di richieste bloccato supera "X", fare "Y".

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/activity-log.md)

- [Come creare avvisi in Azure](../azure-monitor/learn/tutorial-response.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida** : distribuire Web Application Firewall (WAF) di Azure davanti alle applicazioni Web critiche per un ulteriore controllo del traffico in ingresso. 

Azure WAF offre una protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni e protegge le app controllando il traffico Web in ingresso per bloccare gli attacchi, ad esempio attacchi SQL injection, script tra siti, caricamenti di malware e attacchi DDoS.

- [Come distribuire Azure WAF](ag/create-waf-policy-ag.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida** : Azure Active Directory (Azure ad) dispone di ruoli predefiniti che sono in grado di eseguire query e devono essere assegnati in modo esplicito. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Linee guida** : non sono disponibili assegnazioni di amministratori locali all'interno della WAF. Tuttavia, potrebbero essere presenti Azure Active Directory (Azure AD) ruoli di amministratore nell'ambiente che possono consentire il controllo della gestione sulle risorse di Azure WAF.
È consigliabile creare procedure operative standard per l'uso di account amministrativi dedicati con accesso alle istanze di Web Application Firewall (WAF) di Azure. Usare le funzionalità di gestione delle identità e degli accessi del Centro sicurezza per monitorare il numero di account amministrativi.

- [Informazioni sull'identità e sull'accesso del Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

- [Informazioni su come creare utenti amministratori in database di Azure per PostgreSQL](../postgresql/howto-create-users.md#the-server-admin-account)

- [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida** : abilitare la Multi-Factor Authentication (Azure ad) di Azure Active Directory (multi-factor authentication) e seguire le indicazioni relative alla gestione delle identità e dell'accesso del Centro sicurezza

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni** : usare workstation con accesso con privilegi (Paw) con multi-factor authentication (multi-factor authentication) configurato per accedere e configurare Web Application Firewall (WAF) di Azure e le risorse correlate. 

- [Informazioni sulle workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations) 

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida** : usare i report di sicurezza Azure Active Directory (Azure ad) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza per monitorare le attività di identità e accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Linee guida** : configurare la condizione del percorso di un criterio di accesso condizionale e gestire le località denominate. 

Creare raggruppamenti logici di intervalli di indirizzi IP o paesi e aree geografiche con località denominate. Limitare l'accesso alle risorse sensibili, ad esempio Azure Key Vault segreti, alle località denominate configurate.

- [Qual è la condizione di posizione nell'accesso condizionale Azure Active Directory](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida** : usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito, oltre a Salt, hash e archivia in modo sicuro le credenziali utente.
- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni** : Azure Active Directory (Azure ad) fornisce i log per individuare gli account obsoleti. Usare le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. Controllare l'accesso degli utenti a intervalli regolari per assicurarsi che solo gli utenti attivi abbiano accesso continuo.

- [Informazioni sulla creazione di report Azure AD](../active-directory/reports-monitoring/index.yml)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida** : integrare Azure Active Directory (Azure ad) l'attività di accesso, le origini del registro eventi di controllo e di rischio, con qualsiasi strumento Siem o di monitoraggio, ad esempio Azure Sentinel.

Semplificare questo processo creando impostazioni di diagnostica per gli account utente Azure Active Directory (Azure AD) e inviando i log di controllo e i log di accesso a un'area di lavoro Log Analytics. Configurare gli avvisi desiderati nell'area di lavoro Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Indicazioni** : usare le funzionalità di protezione delle identità e dei rischi di Azure Active Directory (Azure ad) per configurare risposte automatiche per rilevare azioni sospette correlate alle identità utente. Inserire i dati in Sentinel di Azure per un'analisi più approfondita.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Linee guida** : usare i tag per facilitare il rilevamento del Web Application Firewall (WAF) di Azure e delle risorse correlate che archiviano o elaborano informazioni riservate.
- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida** : implementare l'isolamento utilizzando sottoscrizioni e gruppi di gestione distinti per singoli domini di sicurezza, ad esempio il tipo di ambiente e il livello di sensibilità dei dati, ad esempio gli ambienti di sviluppo, test e produzione. 

Controllare l'accesso alle risorse di Azure con il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida** : crittografare tutte le informazioni riservate in transito. Assicurarsi che tutti i client che si connettono alle istanze di Web Application Firewall (WAF) di Azure e alle risorse correlate siano in grado di negoziare TLS 1,2 o versione successiva.

Seguire le raccomandazioni del Centro sicurezza per la crittografia dei dati inattivi e la crittografia in transito laddove applicabile.

- [Informazioni sulla crittografia in transito con Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Linee guida** : controllare l'accesso alle risorse di Azure con il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
- [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida** : usare la crittografia dei siti inattivi per tutte le risorse di Azure, tra cui Azure Web Application Firewall (WAF) e le risorse correlate. Microsoft consiglia di consentire ad Azure di gestire le chiavi di crittografia. Tuttavia, è possibile gestire le proprie chiavi in alcune istanze.

- [Informazioni sulla crittografia inattiva in Azure](../security/fundamentals/encryption-atrest.md)

- [Come configurare le chiavi di crittografia gestite dal cliente](../storage/common/customer-managed-keys-configure-key-vault.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida** : configurare il Web Application Firewall (WAF) di Azure per l'esecuzione in modalità di prevenzione dopo la baseline del traffico di rete in modalità di rilevamento per un periodo di tempo predeterminato. 

La WAF di Azure, in modalità di prevenzione, blocca le intrusioni e gli attacchi rilevati dalle regole. L'autore dell'attacco riceve un'eccezione di "accesso non autorizzato 403" e la connessione viene chiusa. La modalità di prevenzione registra tali attacchi nei log di WAF.

- [Panoramica dell'integrazione tra il gateway applicazione e il Centro sicurezza di Azure](../application-gateway/application-gateway-integration-security-center.md#overview)

- [Modalità WAF sul gateway applicazione](ag/ag-overview.md#waf-modes)

- [Modalità WAF sulla porta anteriore](afds/afds-overview.md#waf-modes)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida** : usare Azure Resource Graph per eseguire query o individuare tutte le risorse, ad esempio calcolo, archiviazione, rete, porte e protocolli e così via all'interno delle sottoscrizioni. 

Verificare le autorizzazioni (lettura) appropriate nel tenant ed enumerare tutte le sottoscrizioni di Azure, nonché le risorse all'interno delle sottoscrizioni. Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee guida** : usare i tag nei criteri di Web Application Firewall (WAF) di Azure. I tag possono essere associati alle risorse e applicati logicamente per organizzare l'accesso a queste risorse in una sottoscrizione del cliente. 

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Linee guida** : usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia di WAF di Azure e le risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Linee guida** : creare un inventario delle risorse approvate, inclusa la configurazione in base alle esigenze dell'organizzazione.

Usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni. Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida** : usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.
Usare il grafico risorse di Azure per eseguire query o individuare le risorse di Azure Web Application Firewall (WAF) all'interno delle sottoscrizioni. Assicurarsi che tutti i WAF di Azure e le risorse correlate presenti nell'ambiente siano approvati.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida** : monitorare e rimuovere le risorse WAF di Azure non approvate con i criteri di Azure per negare la distribuzione di Azure WAF o un determinato tipo di WAF, ad esempio Azure WAF V1 vs V2.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida** : usare criteri di Azure per limitare i servizi di cui è possibile eseguire il provisioning nell'ambiente.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/index.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee guida** : usare l'accesso condizionale di Azure per limitare la capacità di un utente di interagire con gestione risorse di Azure configurando "blocca l'accesso" per l'app "gestione Microsoft Azure".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida** : il Web Application Firewall (WAF) di Azure può essere associato a diversi ambienti tramite reti, gruppi di risorse o sottoscrizioni per separare le applicazioni a rischio elevato.

- [Panoramica di rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)

- [Organizzare le risorse con i gruppi di gestione di Azure ](../governance/management-groups/overview.md)

- [Guida alle decisioni relative alle sottoscrizioni](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida** : definire e implementare configurazioni di sicurezza standard per le impostazioni di rete correlate alle distribuzioni di Web Application Firewall (WAF) di Azure.
Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete dei gateway applicazione Azure, delle reti virtuali, dei gruppi di sicurezza di rete e usare le definizioni di criteri predefinite.

- [Come visualizzare gli alias di Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida** : usare i criteri di Azure [deny] e gli effetti [Distribuisci se non esistono] per applicare impostazioni sicure in Azure Web Application Firewall (WAF) e le risorse correlate. 

Usare i modelli di Azure Resource Manager per gestire la configurazione di sicurezza della WAF di Azure e le risorse correlate richieste dall'organizzazione.

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

- [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Panoramica sui modelli di Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida** : usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio criteri personalizzati di Azure e modelli di Azure Resource Manager. 

Concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD), se integrati con DevOps di Azure o Active Directory, se integrato con Team Foundation Server (TFS).

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni** : usare le definizioni di criteri di Azure predefinite e gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Network" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare un processo e una pipeline per la gestione delle eccezioni dei criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Documentazione di Criteri di Azure](../governance/policy/index.yml)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Indicazioni** : usare le definizioni di criteri di Azure predefinite e gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Network" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. 

Usare gli effetti criteri di Azure [audit], [deny] e [Distribuisci se non esistono] per applicare automaticamente le configurazioni per le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Documentazione di Criteri di Azure](../governance/policy/index.yml)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida** : usare Azure Key Vault per archiviare in modo sicuro i certificati. Azure Key Vault è un archivio segreto gestito dalla piattaforma che è possibile usare per proteggere i segreti, le chiavi e i certificati SSL. 

Il gateway applicazione di Azure supporta l'integrazione con Key Vault per i certificati server associati a listener abilitati per HTTPS. 

- [Come configurare la terminazione SSL con Key Vault certificati utilizzando Azure PowerShell](../application-gateway/configure-keyvault-ps.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Linee guida** : implementare Credential scanner per identificare le credenziali all'interno del codice, che incoraggerà anche lo trasferimento delle credenziali individuate a posizioni più sicure, ad esempio Azure Key Vault.
- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida** : assicurarsi che l'eliminazione temporanea sia abilitata per Azure Key Vault. L'eliminazione temporanea consente di recuperare gli insiemi di credenziali delle chiavi e gli oggetti dell'insieme di credenziali eliminati, ad esempio chiavi, segreti e certificati.

- [Come usare l'eliminazione temporanea di Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Guida** : sviluppare una guida alla risposta agli eventi imprevisti per la propria organizzazione. Assicurarsi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi della gestione e della gestione degli eventi imprevisti dal rilevamento alla revisione post-evento imprevisto. 

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Usare la guida alla gestione degli eventi imprevisti di sicurezza del computer NIST per semplificare la creazione del piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni** : il Centro sicurezza assegna una gravità a ogni avviso per consentire la priorità degli avvisi da analizzare per primi. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.
Contrassegnare chiaramente le sottoscrizioni, ad esempio produzione e non di produzione, e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni** : con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.
- [Vedere la guida alla pubblicazione del NIST per i programmi di test, formazione e esercizio per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni** : le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.
- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida** : esportare gli avvisi e le raccomandazioni del Centro sicurezza usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. Usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel in base ai requisiti dell'organizzazione.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida** : usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nelle raccomandazioni.
- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida** : seguire le regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft. 

- [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)