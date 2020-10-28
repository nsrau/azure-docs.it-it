---
title: Baseline della sicurezza di Azure per Site Recovery
description: La linea di base di sicurezza Site Recovery fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9ded8e989572d83b4761dfaaaa681505952b375d
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754466"
---
# <a name="azure-security-baseline-for-site-recovery"></a>Baseline della sicurezza di Azure per Site Recovery

Questa linea di base di sicurezza applica le indicazioni della [versione 1,0 del benchmark di sicurezza di Azure](../security/benchmarks/overview-v1.md) a Site Recovery. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili a Site Recovery. I **controlli** non applicabili ai Site Recovery sono stati esclusi. 

Per informazioni su come Site Recovery viene eseguito il mapping completo al benchmark di sicurezza di Azure, vedere il [file di mapping di base Site Recovery sicurezza completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida** : Microsoft Azure Site Recovery non supporta la distribuzione in una rete virtuale di Azure. Configurare Site Recovery servizio con un endpoint privato di Azure per applicare comunicazioni sicure sulla rete.

- [Supporto del collegamento privato Azure Site Recovery](azure-to-azure-how-to-enable-replication-private-endpoints.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni** : il servizio Site Recovery supporta i tag di servizio che consentono ai clienti di aprire il traffico solo per servizi e porte specifici. I clienti devono consentire il tag di servizio "AzureSiteRecovery" nel firewall o nel gruppo di sicurezza di rete per consentire l'accesso in uscita al servizio Site Recovery.

- [Connettività in uscita tramite tag di servizio](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida** : usare i tag delle risorse per i gruppi di sicurezza di rete e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le singole regole del gruppo di sicurezza di rete, usare il campo "Description" per documentare le regole che consentono il traffico da e verso una rete. 

Incorporare le definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag. 

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag. 

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags) 

- [Come creare una rete virtuale di Azure](../virtual-network/quick-create-portal.md) 

- [Come filtrare il traffico di rete con le regole del gruppo di sicurezza di rete](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida** : monitorare le modifiche apportate alle configurazioni di risorse di rete correlate al servizio Site Recovery usando i log attività di Azure. Creare avvisi in monitoraggio di Azure per ricevere una notifica quando vengono modificate le risorse di rete Site Recovery critiche.

- [Visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Creare, visualizzare e gestire gli avvisi del log attività usando Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni** : abilitare le impostazioni di diagnostica del log attività di Azure per la registrazione di controllo e inviare i log a un'area di lavoro log Analytics, un account di archiviazione di Azure o un hub eventi di Azure per l'archiviazione.

Usare i dati dei log attività di Azure per determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse di Azure.

Inserire i log Site Recovery in monitoraggio di Azure per aggregare i dati di sicurezza generati. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione per l'archiviazione a lungo termine o di archiviazione. Inoltre, è possibile abilitare e caricare i dati in Sentinel di Azure o in una soluzione di gestione degli eventi e degli eventi imprevisti di sicurezza di terze parti.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/activity-log.md)

- [Monitorare Site Recovery con i log di Monitoraggio di Azure](monitor-log-analytics.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida** : abilitare le impostazioni di diagnostica del log attività di Azure per la registrazione di controllo e inviare i log a un'area di lavoro log Analytics, un account di archiviazione di Azure o un hub eventi di Azure per l'archiviazione. 

Usare i dati dei log attività di Azure per determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse di Azure.

Inserire i log di Site Recovery con monitoraggio di Azure per aggregare i dati di sicurezza generati. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione per l'archiviazione a lungo termine/archivio. Abilitare e caricare i dati in Sentinel di Azure o in una soluzione di gestione degli eventi e degli eventi imprevisti di sicurezza di terze parti.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/activity-log.md)

- [Monitorare Site Recovery con i log di Monitoraggio di Azure](monitor-log-analytics.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida** : impostare il periodo di conservazione del log per le aree di lavoro log Analytics associate agli insiemi di credenziali dei servizi di ripristino di Azure usando monitoraggio di Azure in base alle normative di conformità dell'organizzazione. 

- [Come impostare i parametri di conservazione dei log](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida** : abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro log Analytics. 

Eseguire query in Log Analytics per cercare termini, identificare tendenze, analizzare modelli e informazioni dettagliate sui dati del log attività raccolti dagli insiemi di credenziali dei servizi di ripristino.

- [Monitorare Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/activity-log.md)

- [Come raccogliere e analizzare i log attività di Azure nell'area di lavoro Log Analytics in monitoraggio di Azure](../azure-monitor/platform/activity-log.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida** : monitorare i computer replicati da Azure Site Recovery usando i log di monitoraggio di Azure e log Analytics. Usare Log Analytics all'interno di monitoraggio di Azure per scrivere e testare le query di log e analizzare i dati di log in modo interattivo. Monitoraggio di Azure raccoglie le attività e i log delle risorse, insieme ad altri dati di monitoraggio. 

Visualizzare ed eseguire query sui risultati dei log e configurare gli avvisi per eseguire azioni in base ai dati monitorati. Configurare gli avvisi in un'area di lavoro di Log Analytics in Sentinel di Azure perché fornisce una soluzione di risposta automatica per l'orchestrazione della sicurezza (SOAR). In questo modo è possibile creare soluzioni automatiche, come i PlayBook, da creare e utilizzare per correggere i problemi di sicurezza. Creare avvisi del log personalizzati nell'area di lavoro di Log Analytics usando monitoraggio di Azure. 

- [Monitorare Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Creare, visualizzare e gestire gli avvisi del log tramite Monitoraggio di Azure](../azure-monitor/platform/alerts-log.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida** : nessun ruolo è assegnato per impostazione predefinita. Devono essere assegnati in modo esplicito in base alle esigenze aziendali. È possibile controllare le assegnazioni di ruolo con l'interfaccia della riga di comando di PowerShell o Azure Active Directory (Azure AD) per individuare gli account che sono membri di gruppi amministrativi.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni** : creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare le funzionalità di gestione delle identità e degli accessi del Centro sicurezza per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, usare le raccomandazioni del Centro sicurezza o i criteri predefiniti di Azure, ad esempio: 

- Alla sottoscrizione deve essere assegnato più di un proprietario 
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione 

- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

Creare un processo per tenere traccia delle identità e del controllo degli accessi per gli account amministrativi ed esaminarli periodicamente.

- [Come usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso](../security-center/security-center-identity-access.md)

- [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida** : usare la registrazione di app di Azure con un'entità servizio per recuperare un token da usare per interagire con gli insiemi di credenziali dei servizi di ripristino tramite chiamate API.

- [Come chiamare le API REST di Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Come registrare l'applicazione client (entità servizio) con Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informazioni sull'API dei servizi di ripristino di Azure](/rest/api/recoveryservices)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Linee guida** : abilitare Azure ad, l'autenticazione a più fattori e seguire le indicazioni relative all'identità e all'accesso del Centro sicurezza. 
- [Pianificare una distribuzione di Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

- [Monitorare identità e accesso](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida** : usare una workstation protetta gestita da Azure, nota anche come workstation di accesso con privilegi (Paw), con l'autenticazione a più fattori di Azure per le attività amministrative e per eseguire azioni con privilegi sulle risorse Site Recovery.

- [Workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Pianificazione di una distribuzione di Azure Multi-Factor Authentication basata sul cloud](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida** : usare la funzionalità di Privileged Identity Management (PIM) di Azure ad per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente.
Visualizzare avvisi e report sul comportamento utente rischioso con Azure AD funzionalità di rilevamento dei rischi.

- [Come distribuire Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Informazioni sui rilevamenti di rischi di Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: gestire le risorse di Azure solo dalle località approvate

**Linee guida** : usare i percorsi denominati di accesso condizionale per consentire l'accesso al portale di Azure solo da specifici raggruppamenti logici di intervalli di indirizzi IP, aree o paesi.
- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida** : usare Azure ad come sistema di autenticazione e autorizzazione centrale per Site Recovery. Azure AD protegge i dati usando crittografia avanzata per i dati inattivi, in transito e anche Salt, hash e archivia in modo sicuro le credenziali utente. 

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni** : usare i log di Azure ad per individuare gli account obsoleti. 

Gestisci in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo con le verifiche di accesso e identità di Azure AD. 

Creare un processo per controllare l'accesso degli utenti a intervalli regolari per garantire che solo gli utenti con verifiche di accesso completate abbiano accesso continuo. 

- [Informazioni sulla creazione di report Azure AD](/azure/active-directory/reports-monitoring/)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida** : usare Azure ad come sistema di autenticazione e autorizzazione centrale per le risorse Site Recovery. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito, oltre a Salt, hash e archivia in modo sicuro le credenziali utente.

È possibile accedere alle origini del registro eventi di rischio, controllo e attività di accesso Azure AD, che consentono di integrarle con Sentinel di Azure o qualsiasi strumento SIEM o di monitoraggio disponibile in Azure Marketplace.

Semplificare ulteriormente questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi desiderati all'interno di un'area di lavoro Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Come caricare dati in Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Linee guida** : usare Azure ad come sistema di autenticazione e autorizzazione centrale per gli insiemi di credenziali dei servizi di ripristino. 

Usare le funzionalità di protezione delle identità di Azure AD per il rilevamento del comportamento dell'account di accesso e per configurare risposte automatiche a azioni sospette rilevate in relazione alle identità utente. Inserire anche i dati in Sentinel di Azure per un'analisi più approfondita.

- [Come visualizzare Azure AD l'accesso rischioso](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni** : usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida** : implementare sottoscrizioni o gruppi di gestione distinti per gli insiemi di credenziali di servizi di ripristino di sviluppo, test e produzione. Separare le risorse con una rete virtuale o una subnet, contrassegnate in modo appropriato e protette da un gruppo di sicurezza di rete o da un firewall di Azure. 

Disattivare le macchine virtuali, che archiviano o elaborano dati sensibili, quando non sono in uso. Implementare criteri e procedure per rendere questo processo ricorrente. 

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Panoramica di Site Recovery](site-recovery-overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Indicazioni** : usare un collegamento privato o un endpoint privato, gruppi di sicurezza di rete e tag di servizio per attenuare le opportunità di exfiltration dati dalle macchine virtuali abilitate per la Site Recovery.

Microsoft gestisce la piattaforma sottostante usata da Site Recovery e considera tutti i contenuti dei clienti come sensibili e assicurano la perdita di dati e l'esposizione dei clienti. Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati per garantire che i dati dei clienti in Azure rimangano protetti. 

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

- [Eseguire la replica di macchine virtuali con endpoint privati di Azure](azure-to-azure-how-to-enable-replication-private-endpoints.md)

- [Replicare le macchine virtuali con tag del servizio Azure Site Recovery](azure-to-azure-about-networking.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Indicazioni** : Site Recovery usa un canale HTTPS sicuro, crittografato con Advanced Encryption Standard (AES 256), dai server del carico di lavoro di Azure ai servizi Site Recovery ospitati dietro un insieme di credenziali di servizi di ripristino.

Le versioni correnti di TLS supportate per Site Recovery sono TLS 1,0, TLS 1,1, TLS 1,2 in Regions, che si trovavano entro la fine del 2019. TLS 1.2 è l'unica versione di TLS supportata per le nuove aree geografiche.

- [Informazioni sulla crittografia in transito per Azure Site Recovery](physical-azure-set-up-source.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni** : le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per Site Recovery. 

Implementare una soluzione di terze parti, se necessario, per finalità di conformità.

Microsoft gestisce la piattaforma sottostante usata da Site Recovery e considera tutti i contenuti dei clienti come sensibili e protegge dalla perdita di dati e dall'esposizione dei clienti. Ha implementato e mantiene una suite di potenti controlli e funzionalità di protezione dei dati per garantire che i dati dei clienti in Azure rimangano protetti. 

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: usare RBAC di Azure per gestire l'accesso alle risorse

**Indicazioni** : usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per gestire l'accesso ai dati e alle risorse correlate alle risorse Site Recovery. 

Separare i compiti di lavoro con il controllo degli accessi in base al ruolo di Azure e concedere l'accesso appropriato Usare i ruoli predefiniti Site Recovery per controllare Site Recovery operazioni di gestione.

- [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)

- [Usare il controllo di accesso Role-Based per gestire Azure Site Recovery](site-recovery-role-based-linked-access-control.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida** : abilitare la crittografia doppia con chiavi di piattaforma e gestite dal cliente. Questa funzionalità è disponibile in Site Recovery. 

Site Recovery supporta la crittografia dei dati inattivi per i dati. Per i carichi di lavoro IaaS di Azure, i dati vengono crittografati a riposo usando crittografia del servizio di archiviazione (SSE). 

Solo il cliente ha accesso alla chiave di crittografia quando usa un insieme di credenziali di servizi di ripristino crittografato con una chiave gestita dal cliente. Microsoft non gestisce mai una copia, non ha accesso alla chiave e non decrittografa i dati trasferiti dalla posizione primaria a quella di ripristino di emergenza in qualsiasi momento. 

- [Supporto delle chiavi gestite dal cliente per Azure Site Recovery](azure-to-azure-how-to-enable-replication-cmk-disks.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida** : usare monitoraggio di Azure con i log attività di Azure per creare avvisi quando le modifiche vengono apportate alle risorse critiche. Queste risorse possono includere istanze di produzione di insiemi di credenziali dei servizi di ripristino, risorse di Site Recovery servizio e risorse correlate.
- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida** : usare Azure Resource Graph per eseguire query o individuare tutte le risorse, inclusi gli insiemi di credenziali dei servizi di ripristino, all'interno delle sottoscrizioni. Verificare le autorizzazioni di lettura appropriate nel tenant ed enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee guida** : applicare tag agli insiemi di credenziali dei servizi di ripristino e ad altre risorse correlate, utilizzate da Site Recovery con i metadati, per organizzarle in modo logico in una tassonomia.
- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni** : usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia Site Recovery (insiemi di credenziali dei servizi di ripristino) e altre risorse correlate. 

Usare inoltre i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti: 

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definire e gestire un inventario delle risorse di Azure approvate

**Linee guida** : creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base ai requisiti aziendali del cliente.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida** : usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti: 

- Tipi di risorse non consentiti 
- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida** : usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti 
- Tipi di risorse consentiti

Comprendere come creare e gestire i criteri in Azure è importante per assicurare la conformità agli standard e ai contratti di servizio aziendali.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](/azure/governance/policy/samples)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni** : usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure. Ciò può impedire la creazione e le modifiche alle risorse all'interno di un ambiente con sicurezza elevata.

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida** : definire e implementare configurazioni di sicurezza standard per l'insieme di credenziali di servizi di ripristino con criteri di Azure. 

Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. RecoveryServices" per creare criteri personalizzati da controllare o applicare la configurazione delle risorse dell'insieme di credenziali dei servizi di ripristino del servizio Site Recovery.
- [Come visualizzare gli alias di Criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida** : usare i criteri di Azure [deny] e gli effetti [Distribuisci se non esistono] per applicare impostazioni sicure tra le risorse di Azure.
- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida** : scegliere Azure Repos per archiviare e gestire in modo sicuro il codice se si usano definizioni di criteri di Azure personalizzate per gli insiemi di credenziali dei servizi di ripristino e le risorse correlate.

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Documentazione di Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni** : usare le definizioni di criteri di Azure predefinite e gli alias di criteri di Azure nello spazio dei nomi "Microsoft. RecoveryServices" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. 

Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Indicazioni** : usare le definizioni di criteri di Azure predefinite e gli alias di criteri di Azure nello spazio dei nomi "Microsoft. RecoveryServices" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. 

Usare gli effetti criteri di Azure [audit], [deny] e [Distribuisci se non esistono] per applicare automaticamente le configurazioni per le risorse di Azure.
- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida** : il cliente deve gestire i segreti Site Recovery integrati con Azure Key Vault, abilitando il ripristino di emergenza per le macchine virtuali abilitate per crittografia dischi di Azure. 

- [Come creare un insieme di credenziali delle chiavi](../key-vault/secrets/quick-create-portal.md)

- [Come eseguire l'autenticazione a Key Vault](../key-vault/general/authentication.md)

- [Come assegnare un criterio di accesso di Key Vault](../key-vault/general/assign-access-policy-portal.md)

- [Come abilitare il ripristino di emergenza per le macchine virtuali abilitate per crittografia dischi di Azure usando Site Recovery](azure-to-azure-how-to-enable-replication-ade-vms.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Indicazioni** : Site Recovery supporta l'identità gestita dal sistema solo se un cliente può abilitare l'identità gestita dal sistema nell'insieme di credenziali di servizi di ripristino. La stessa metodologia si applica alle risorse usate nell'offerta di ripristino di emergenza per definire il limite di accesso. 

Usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure AD. 

Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Key Vault, senza credenziali nel codice.

- [Come eseguire l'integrazione con le identità gestite di Azure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity?tabs=core2x)

- [Come abilitare l'identità gestita di sistema nell'insieme di credenziali di servizi di ripristino](azure-to-azure-how-to-enable-replication-private-endpoints.md#enable-the-managed-identity-for-the-vault)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni** : implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida** : Microsoft antimalware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Site Recovery), tuttavia non viene eseguito sul contenuto. Eseguire la pre-analisi di tutti i file caricati in risorse di Azure non di calcolo, ad esempio servizio app, Data Lake Storage e archiviazione BLOB.

Usare il rilevamento delle minacce del Centro sicurezza per i servizi dati per rilevare il malware caricato negli account di archiviazione.

- [Informazioni su Microsoft antimalware per servizi cloud e macchine virtuali di Azure](../security/fundamentals/antimalware.md)

- [Informazioni sul rilevamento delle minacce del Centro sicurezza di Azure per i servizi dati](/azure/security-center/threat-protection)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni** : Site Recovery usa internamente un account di archiviazione di Azure per mantenere lo stato della soluzione di ripristino di emergenza, come configurato dai clienti sui rispettivi carichi di lavoro.

Tutte le risorse di archiviazione usate dai metadati di Site Recovery Services con configurazione di tipo: archiviazione con ridondanza geografica e accesso in lettura (RA-GRS). Gli account di archiviazione di tipo superiore a GRS (ad esempio RAGRS, RAG-ZRS) replicano i dati in un'area secondaria (centinaia di chilometri di distanza dalla posizione primaria dei dati di origine) per continuare a gestire il ripristino di emergenza per i clienti durante le interruzioni.

Questa operazione non rientra nell'ambito dei clienti e Site Recovery Team si occupa internamente. Il cliente può eseguire il backup di chiavi di Key Vault in Azure.

- [Come eseguire il backup di chiavi di Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida** : testare periodicamente i ripristini delle chiavi gestite dal cliente sottoposte a backup.

- [Come ripristinare le chiavi di Key Vault in Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida** : i dati vengono crittografati a riposo usando crittografia del servizio di archiviazione (SSE) con macchine virtuali basate su IaaS (Infrastructure as a Service) di Azure. Abilitare l'eliminazione temporanea in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.

- [Come abilitare l'eliminazione temporanea in Key Vault](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni** : creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. 

Verificare che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti o la gestione dal rilevamento alla verifica post-evento imprevisto.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Linee guida** : definire le priorità degli avvisi che devono essere analizzati prima in base al livello di gravità degli avvisi assegnato al centro sicurezza. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare chiaramente le sottoscrizioni, ad esempio produzione e non di produzione, e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md) 

- [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni** : con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e i gap e rivedere il piano in base alle esigenze

- [Vedere la guida alla pubblicazione del NIST per i programmi di test, formazione e esercizio per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni** : le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente. 

Creare un processo per esaminare gli eventi imprevisti, post-occorrenza, per garantire la risoluzione dei problemi.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida** : esportare gli avvisi e le raccomandazioni del Centro sicurezza usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. 

Usare il connettore dati del Centro sicurezza per trasmettere gli avvisi ad Azure Sentinel, in base alle esigenze.
- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida** : usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nelle raccomandazioni.
- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Indicazioni** : seguire le regole Microsoft per assicurarsi che i propri test di penetrazione non violino i criteri Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [È possibile trovare altre informazioni sulla strategia Microsoft e sull'esecuzione dei test di penetrazione Red Teaming sull'infrastruttura cloud gestita da Microsoft, sui servizi e sulle applicazioni qui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
