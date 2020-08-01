---
title: Baseline della sicurezza di Azure per Azure DevTest Labs
description: Baseline della sicurezza di Azure per Azure DevTest Labs
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: b392af17a24b0a5aabdd245af236caa743762244
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448958"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Baseline della sicurezza di Azure per Azure DevTest Labs

La linea di base di sicurezza di Azure per Azure DevTest Labs contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La linea di base per questo servizio viene creata dalla [versione 1,0 del benchmark di sicurezza di Azure](../security/benchmarks/overview.md), che fornisce indicazioni su come proteggere le soluzioni cloud in Azure.

Per altre informazioni, vedere [Panoramica delle baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate
**Linee guida:** Microsoft gestisce le origini temporali per le risorse di Azure. Tuttavia, è possibile gestire le impostazioni di sincronizzazione dell'ora per le risorse di calcolo.

Vedere l'articolo seguente per informazioni sulla configurazione della sincronizzazione dell'ora per le risorse di calcolo di Azure: [sincronizzazione dell'ora per le macchine virtuali Windows in Azure](../virtual-machines/windows/time-sync.md). 

**Monitoraggio del Centro sicurezza di Azure:** Attualmente non disponibile

**Responsabilità:** Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata
**Linee guida:** Abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro Log Analytics, a hub eventi di Azure o all'account di archiviazione di Azure per l'archiviazione. I log attività forniscono informazioni approfondite sulle operazioni eseguite nelle istanze di Azure DevTest Labs a livello di piano di gestione. Usando i dati del log attività di Azure, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita a livello del piano di gestione per le istanze di DevTest Labs.

Per altre informazioni, vedere [creare le impostazioni di diagnostica per inviare i log e le metriche della piattaforma a destinazioni diverse](../azure-monitor/platform/diagnostic-settings.md).

**Monitoraggio del Centro sicurezza di Azure:** Attualmente non disponibile

**Responsabilità:** Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure
**Linee guida:** Abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro Log Analytics, a hub eventi di Azure o all'account di archiviazione di Azure per l'archiviazione. I log attività forniscono informazioni approfondite sulle operazioni eseguite nelle istanze di Azure DevTest Labs a livello di piano di gestione. Usando i dati del log attività di Azure, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita a livello del piano di gestione per le istanze di DevTest Labs.

Per altre informazioni, vedere [creare le impostazioni di diagnostica per inviare i log e le metriche della piattaforma a destinazioni diverse](../azure-monitor/platform/diagnostic-settings.md).

**Monitoraggio del Centro sicurezza di Azure:** Attualmente non disponibile

**Responsabilità:** Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi
**Linee guida:** Azure DevTest Labs macchine virtuali (VM) vengono create e di proprietà del cliente. Quindi, è responsabilità dell'organizzazione monitorarlo. È possibile usare il Centro sicurezza di Azure per monitorare il sistema operativo di calcolo. I dati raccolti dal centro sicurezza dal sistema operativo includono il tipo di sistema operativo e la versione, il sistema operativo (registri eventi di Windows), i processi in esecuzione, il nome del computer, gli indirizzi IP e l'utente connesso. L'agente di Log Analytics raccoglie anche i file di dump di arresto anomalo del sistema.

Per altre informazioni, vedere gli articoli seguenti: 

- [Come raccogliere i log degli host interni della macchina virtuale di Azure con monitoraggio di Azure](../azure-monitor/learn/quick-collect-azurevm.md)
- [Informazioni sulla raccolta dati del Centro sicurezza di Azure](../security-center/security-center-enable-data-collection.md)

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza
***Linee guida:** In monitoraggio di Azure impostare il periodo di conservazione del log per le aree di lavoro Log Analytics associate alle istanze di Azure DevTest Labs in base alle normative di conformità dell'organizzazione.

Per altre informazioni, vedere l'articolo seguente: [come impostare i parametri di conservazione dei log](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log
**Linee guida:** Abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro Log Analytics. Eseguire query in Log Analytics per cercare termini, identificare tendenze, analizzare modelli e fornire molte altre informazioni basate sui dati del log attività che potrebbero essere stati raccolti per Azure DevTest Labs.

Per altre informazioni, vedere gli articoli seguenti:

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/diagnostic-settings.md)
- [Come raccogliere e analizzare i log attività di Azure nell'area di lavoro Log Analytics in monitoraggio di Azure](../azure-monitor/platform/activity-log.md)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: abilitare gli avvisi per le attività anomale
**Linee guida:** Usare l'area di lavoro di Azure Log Analytics per il monitoraggio e l'invio di avvisi sulle attività anomale nei registri di sicurezza e negli eventi correlati al Azure DevTest Labs.

Per altre informazioni, vedere l'articolo seguente: [come ricevere un avviso sui dati del log di log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitoraggio del Centro sicurezza di Azure:** Attualmente non disponibile

**Responsabilità:** Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware
**Linee guida:** Non applicabile. Azure DevTest Labs non elabora o produce log correlati all'anti-malware.

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS
**Linee guida:** Non applicabile. Azure DevTest Labs non elabora o produce log correlati a DNS.

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando
**Linee guida:** Azure DevTest Labs crea computer di calcolo di Azure che sono di proprietà e gestiti dal cliente. Usare Microsoft Monitoring Agent in tutte le macchine virtuali Windows di Azure supportate per registrare l'evento di creazione del processo e il `CommandLine` campo. Per le macchine virtuali Linux di Azure supportate, è possibile configurare manualmente la registrazione della console in base ai singoli nodi e usare syslog per archiviare i dati. Usare anche l'area di lavoro Log Analytics di monitoraggio di Azure per esaminare i log ed eseguire query sui dati registrati dalle macchine virtuali di Azure.

- [Raccolta dati nel Centro sicurezza di Azure](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [Come eseguire query personalizzate in monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md)
- [Origini dati Syslog in Monitoraggio di Azure](../azure-monitor/platform/data-sources-syslog.md)

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Cliente

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso
*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: mantenere un inventario degli account amministrativi
**Linee guida:** Azure Active Directory (Azure AD) include ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)
- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)
- [Ruoli di Azure DevTest Labs](devtest-lab-add-devtest-user.md)  

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile
**Linee guida:** Azure Active Directory (Azure AD) non ha il concetto di password predefinite. Altre risorse di Azure che richiedono una password forzano la creazione di una password con requisiti di complessità e una lunghezza minima della password, che variano a seconda del servizio. L'utente è responsabile per le applicazioni di terze parti e per i servizi del Marketplace che possono usare password predefinite.

DevTest Labs non ha il concetto di password predefinite. 

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati
**Linee guida:** Creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio:

- Alla sottoscrizione deve essere assegnato più di un proprietario
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

- [Come usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso (Anteprima)](../security-center/security-center-identity-access.md)  
- [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)
- [Ruoli di Azure DevTest Labs](devtest-lab-add-devtest-user.md)  

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory
**Linee guida:** DevTest Labs usa il servizio Azure AD per la gestione delle identità. Considerare questi due aspetti chiave quando si concede agli utenti l'accesso a un ambiente basato su DevTest Labs:

- **Gestione delle risorse:** Consente di accedere al portale di Azure per gestire le risorse (creare macchine virtuali, creare ambienti, avviare, arrestare, riavviare, eliminare e applicare elementi e così via). La gestione delle risorse viene eseguita in Azure usando il controllo degli accessi in base al ruolo (RBAC). Assegnare i ruoli agli utenti e impostare le autorizzazioni a livello di risorsa e di accesso.
- **Macchine virtuali (a livello di rete)**: nella configurazione predefinita, le VM usano un account amministratore locale. Se è disponibile un dominio (Azure AD Domain Services, un dominio locale o un dominio basato su cloud), i computer possono essere aggiunti al dominio. Gli utenti possono quindi usare le proprie identità basate su dominio usando l'elemento di aggiunta al dominio per connettersi ai computer. 

- [Architettura di riferimento per DevTest Labs](devtest-lab-reference-architecture.md#architecture)
- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory
**Linee guida:** Abilitare Azure Active Directory (AD) Multi-Factor Authentication (multi-factor authentication) e seguire le indicazioni sulla gestione delle identità e degli accessi nel centro sicurezza di Azure.

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)  
- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure:*** Sì

**Responsabilità:** Cliente


### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative
**Linee guida:** Usare workstation con accesso con privilegi (Paw) con autenticazione a più fattori configurata per l'accesso e la configurazione delle risorse di Azure.

- [Informazioni sulle workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations)  
- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)  

**Monitoraggio del Centro sicurezza di Azure:** N/A

**Responsabilità:** Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: registrare e inviare avvisi per le attività sospette dagli account amministrativi
**Linee guida:** Usare i report di sicurezza Azure Active Directory (Azure AD) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)  
- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)  

**Monitoraggio del Centro sicurezza di Azure:** Attualmente non disponibile

**Responsabilità:** Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate
**Linee guida:** Usare i percorsi denominati di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare le località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)  

**Monitoraggio del Centro sicurezza di Azure:** Attualmente non disponibile

**Responsabilità:** Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory
**Linee guida:** Usare Azure Active Directory (Azure AD) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)  

**Monitoraggio del Centro sicurezza di Azure:** Attualmente non disponibile

**Responsabilità:** Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente
**Linee guida:** Azure Active Directory (Azure AD) fornisce log che consentono di individuare gli account obsoleti. Usare anche le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

- [Informazioni sulla creazione di report Azure AD](../active-directory/reports-monitoring/overview-reports.md)  
- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)  

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: monitorare i tentativi di accesso agli account disattivati
**Linee guida:** È possibile accedere alle origini del registro eventi di attività, controllo e rischio per l'accesso Azure Active Directory (Azure AD), che consentono di eseguire l'integrazione con qualsiasi strumento/Monitoring di gestione di informazioni ed eventi di sicurezza (SIEM).

È possibile semplificare questo processo creando impostazioni di diagnostica per gli account utente di Azure Active Directory e inviando i log di controllo e di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi nell'area di lavoro Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)  

**Monitoraggio del Centro sicurezza di Azure:** Attualmente non disponibile

**Responsabilità:** Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account
**Linee guida:** Usare le funzionalità di protezione delle identità e di rischio di Azure Active Directory (Azure AD) per configurare risposte automatiche per le azioni sospette rilevate correlate alle identità utente.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)  
- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)  

**Monitoraggio del Centro sicurezza di Azure:** Attualmente non disponibile

**Responsabilità:** Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto
**Linee guida:** Customer Lockbox attualmente non supportato per Azure DevTest Labs.

- [Elenco dei servizi Customer Lockbox supportati](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability) 

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità
*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati
**Linee guida:** Seguire le raccomandazioni del Centro sicurezza di Azure per proteggere le istanze di Azure DevTest Labs e le risorse correlate.

Microsoft esegue la gestione delle vulnerabilità sulle risorse sottostanti che supportano Azure DevTest Labs.

- [Informazioni sulle raccomandazioni del Centro sicurezza di Azure](../security-center/recommendations-reference.md) 

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Condiviso

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo
**Linee guida:** Usare Gestione aggiornamenti di Azure per assicurarsi che gli aggiornamenti della sicurezza più recenti siano installati nelle macchine virtuali Windows e Linux ospitate in DevTest Labs. Per le macchine virtuali Windows, verificare che Windows Update sia stato abilitato e impostato per l'aggiornamento automatico. Questa impostazione non è attualmente disponibile per la configurazione tramite DevTest Labs. Tuttavia, l'amministratore dell'amministratore/sottoscrizione di Lab può configurare questa impostazione nelle macchine virtuali di calcolo sottostanti nella propria sottoscrizione. 

- [Come configurare Gestione aggiornamenti per le macchine virtuali in Azure](../automation/update-management/update-mgmt-overview.md)
- [Informazioni sui criteri di sicurezza di Azure monitorati dal centro sicurezza](../security-center/security-center-policy-definitions.md)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: distribuire una soluzione di gestione delle patch automatizzata per il software di terze parti
***Linee guida:*** In qualità di amministratore del Lab, è possibile usare gli [elementi di DevTest Labs](add-artifact-vm.md) per automatizzare gli aggiornamenti per le immagini personalizzate del Lab, incluse le patch di sicurezza e altri aggiornamenti. 

Scopri di più su [DevTest Labs Image Factory](image-factory-create.md), una soluzione di configurazione come codice che compila e distribuisce automaticamente le immagini a intervalli regolari con tutte le configurazioni desiderate. 

In qualità di amministratore della sottoscrizione, è anche possibile usare la soluzione Gestione aggiornamenti di Azure per gestire gli aggiornamenti e le patch per le macchine virtuali DevTest Labs. Gestione aggiornamenti si basa sul repository di aggiornamenti configurato localmente per applicare patch ai sistemi Windows supportati. Strumenti come System Center Updates Publisher (Updates Publisher) consentono di pubblicare aggiornamenti personalizzati in Windows Server Update Services (WSUS). Questo scenario consente Gestione aggiornamenti di applicare patch ai computer che usano Configuration Manager come repository di aggiornamento con software di terze parti.

- [Soluzione Gestione aggiornamenti in Azure](../automation/update-management/update-mgmt-overview.md)
- [Gestire gli aggiornamenti e le patch per le macchine virtuali](../automation/update-management/update-mgmt-overview.md)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back
**Linee guida:** Esportare i risultati dell'analisi a intervalli coerenti e confrontare i risultati per verificare che le vulnerabilità siano state corrette. Quando si usa la raccomandazione sulla gestione delle vulnerabilità suggerita dal centro sicurezza di Azure, il cliente può passare al portale della soluzione selezionata per visualizzare i dati cronologici dell'analisi.

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate
**Linee guida:** Usare le classificazioni di rischio predefinite (Punteggio sicuro) fornite dal centro sicurezza di Azure.

- [Informazioni sul punteggio sicuro del Centro sicurezza di Azure](../security-center/secure-score-security-controls.md)

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Cliente

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario
*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery
**Linee guida:** Usare Azure Resource Graph per eseguire query e individuare tutte le risorse, incluse le risorse di DevTest Labs, all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (lettura) appropriate nel tenant ed è possibile enumerare tutte le sottoscrizioni e le risorse di Azure all'interno delle sottoscrizioni.

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)
- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)
- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset
**Linee guida:** Applicare i tag alle risorse di Azure fornendo i metadati per organizzarli in modo logico in base a una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)
- [Configurare i tag per DevTest Labs](devtest-lab-add-tag.md)

**Monitoraggio del Centro sicurezza di Azure:** Non disponibile

**Responsabilità:** Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate
**Linee guida:** Usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate e separare i Lab laddove appropriato per organizzare e tenere traccia dei Lab e delle risorse correlate al Lab. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate rapidamente dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)
- [Come creare gruppi di gestione](../governance/management-groups/create.md)
- [Come creare un Lab con DevTest Labs](devtest-lab-create-lab.md)
- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)
- [Come configurare i tag per un Lab](devtest-lab-add-tag.md)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate
**Linee guida:** Creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione. In qualità di amministratore della sottoscrizione, è anche possibile usare i controlli delle applicazioni adattivi, una funzionalità del Centro sicurezza di Azure che consente di definire un set di applicazioni consentite per l'esecuzione su gruppi configurati di computer lab. Questa funzionalità è disponibile sia per le finestre di Azure che per quelle non Azure (tutte le versioni, classiche o Azure Resource Manager) e i computer Linux.

- [Come abilitare il controllo delle applicazioni adattivo](../security-center/security-center-adaptive-application.md)
 
**Monitoraggio del Centro sicurezza di Azure:** Responsabilità non applicabile **:** cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate
**Linee guida:** Usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare anche il grafico delle risorse di Azure per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Questa funzione può essere utile in ambienti con sicurezza elevata, ad esempio quelli con account di archiviazione.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)
- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo
**Linee guida:** Automazione di Azure offre il controllo completo durante la distribuzione, le operazioni e la rimozione delle autorizzazioni dei carichi di lavoro e delle risorse. In qualità di amministratore della sottoscrizione, è possibile sfruttare l'inventario delle macchine virtuali di Azure per automatizzare la raccolta di informazioni su tutto il software nelle VM DevTest Labs nella sottoscrizione. Le proprietà nome software, versione, server di pubblicazione e ora di aggiornamento sono disponibili dal portale di Azure. Per ottenere l'accesso alla data di installazione e ad altre informazioni, il cliente deve abilitare la diagnostica a livello di Guest e portare i registri eventi di Windows in un'area di lavoro Log Analytics.

Oltre a usare Rilevamento modifiche per il monitoraggio di applicazioni software, i controlli delle applicazioni adattivi nel centro sicurezza di Azure usano Machine Learning per analizzare le applicazioni in esecuzione nei computer e creare un elenco di Consenti da questa intelligence. Questa funzionalità semplifica notevolmente il processo di configurazione e gestione dei criteri dell'elenco Consenti applicazioni, consentendo di evitare l'utilizzo di software indesiderato nell'ambiente. È possibile configurare la modalità di controllo o l'imposizione. La modalità di controllo controlla solo l'attività nelle macchine virtuali protette. La modalità di imposizione applica le regole e garantisce che le applicazioni che non sono consentite per l'esecuzione siano bloccate. 

- [Introduzione ad Automazione di Azure](../automation/automation-intro.md)
- [Come abilitare l'inventario delle macchine virtuali di Azure](../automation/automation-tutorial-installed-software.md)
- [Informazioni sui controlli applicazione adattivi](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente


### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate
**Linee guida:** Automazione di Azure offre il controllo completo durante la distribuzione, le operazioni e la rimozione delle autorizzazioni dei carichi di lavoro e delle risorse. Come amministratore della sottoscrizione, è possibile usare Rilevamento modifiche per identificare tutto il software installato nelle macchine virtuali ospitate in DevTest Labs. È possibile implementare un processo personalizzato o usare la configurazione dello stato di automazione di Azure per la rimozione di software non autorizzato.

- [Introduzione ad Automazione di Azure](../automation/automation-intro.md)
- [Rilevare le modifiche nell'ambiente in uso con la soluzione di rilevamento modifiche](../automation/change-tracking.md)
- [Panoramica della configurazione dello stato di automazione di Azure](../automation/automation-dsc-overview.md)

**Monitoraggio del Centro sicurezza di Azure:** Non disponibile

**Responsabilità:** Cliente

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate
**Linee guida:** In qualità di amministratore della sottoscrizione, è possibile usare i controlli delle applicazioni adattivi del Centro sicurezza di Azure per assicurarsi che venga eseguito solo il software autorizzato e che l'esecuzione di tutto il software non autorizzato venga bloccata nelle macchine virtuali di Azure ospitate in DevTest Labs.

- [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati
**Linee guida:** Usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti: 

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Vedere gli articoli seguenti: 
- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)
- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/not-allowed-resource-types.md)

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Cliente


### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati
**Linee guida:** Il controllo delle applicazioni adattivo è una soluzione end-to-end intelligente e automatizzata del Centro sicurezza di Azure, che consente di controllare quali applicazioni possono essere eseguite su computer Azure e non Azure (Windows e Linux), ospitati in DevTest Labs. Si noti che è necessario essere un amministratore della sottoscrizione per poter configurare questa impostazione per le risorse di calcolo sottostanti ospitate in DevTest Labs. Implementare una soluzione di terze parti se questa impostazione non soddisfa i requisiti dell'organizzazione.

- [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager
**Linee guida:** Usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando l' **accesso ai blocchi**"* * per l'app di **gestione Microsoft Azure** .

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo
**Linee guida:** A seconda del tipo di script, è possibile usare configurazioni specifiche del sistema operativo o risorse di terze parti per limitare la capacità degli utenti di eseguire script all'interno delle macchine virtuali ospitate in DevTest Labs. È anche possibile usare i controlli delle applicazioni adattivi del Centro sicurezza di Azure per assicurarsi che venga eseguito solo il software autorizzato e che l'esecuzione di tutto il software non autorizzato venga bloccata nelle VM di Azure sottostanti.

- [Come controllare l'esecuzione di script di PowerShell negli ambienti Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)
- [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure:** Non disponibile

**Responsabilità:** Cliente


### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: separare fisicamente o logicamente le applicazioni ad alto rischio
**Linee guida:** Le applicazioni ad alto rischio distribuite nell'ambiente Azure possono essere isolate mediante rete virtuale, subnet, sottoscrizioni, gruppi di gestione e così via. e sufficientemente protetti con un firewall di Azure, un Web Application Firewall (WAF) o un gruppo di sicurezza di rete (NSG).

- [Configurare la rete virtuale per DevTest Labs](devtest-lab-configure-vnet.md)
- [Panoramica del Firewall di Azure](../firewall/overview.md)
- [Panoramica di Web Application Firewall](../web-application-firewall/overview.md)
- [Panoramica della sicurezza di rete](../virtual-network/security-overview.md)
- [Panoramica di rete virtuale di Azure]()
- [Organizzare le risorse con i gruppi di gestione di Azure ](../governance/management-groups/overview.md)
- [Guida alle decisioni relative alle sottoscrizioni](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitoraggio del Centro sicurezza di Azure:** Non disponibile

**Responsabilità:** Cliente


## <a name="malware-defense"></a>Difesa da malware
*Per altre informazioni, vedere Controllo di sicurezza: difesa da malware.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: usare software antimalware gestito in modo centralizzato
**Linee guida:** USA Microsoft antimalware per servizi cloud e macchine virtuali di Azure per monitorare e difendere continuamente le tue risorse. Per Linux, usare una soluzione antimalware di terze parti. Usare anche il rilevamento delle minacce del Centro sicurezza di Azure per i servizi dati per rilevare il malware caricato negli account di archiviazione.

- Come configurare Microsoft antimalware per servizi cloud e macchine virtuali
- Protezione dalle minacce nel Centro sicurezza di Azure

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo
**Linee guida:** Microsoft antimalware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, app Azure servizio ospitato in un Lab), tuttavia non viene eseguito sul contenuto.
Pre-analizza tutti i file caricati in risorse di Azure non di calcolo, ad esempio servizio app, Data Lake Storage, archiviazione BLOB e così via.

Usare il rilevamento delle minacce del Centro sicurezza di Azure per i servizi dati per rilevare il malware caricato negli account di archiviazione.

- Informazioni su Microsoft antimalware per servizi cloud e macchine virtuali di Azure
- Informazioni sul rilevamento delle minacce del Centro sicurezza di Azure per i servizi dati

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Non applicabile


### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: verificare che le firme e il software antimalware siano aggiornati
**Linee guida:** Quando viene distribuita, Microsoft antimalware per Azure installerà automaticamente gli aggiornamenti più recenti della firma, della piattaforma e del motore per impostazione predefinita. Seguire le raccomandazioni nel centro sicurezza di Azure: "calcolo & app" per assicurarsi che tutti gli endpoint per le risorse di calcolo sottostanti DevTest Labs siano aggiornati con le firme più recenti. Il sistema operativo Windows può essere ulteriormente protetto con sicurezza aggiuntiva per limitare il rischio di attacchi basati su virus o malware con il servizio Microsoft Defender Advanced Threat Protection che si integra con il Centro sicurezza di Azure.

- Come distribuire Microsoft antimalware per servizi cloud e macchine virtuali di Azure
- Microsoft Defender Advanced Threat Protection

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Cliente

## <a name="data-recovery"></a>Recupero dati
*Per altre informazioni, vedere [Controllo di sicurezza: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare
**Linee guida:** Attualmente, Azure DevTest Labs non supporta i backup e gli snapshot delle macchine virtuali. Tuttavia, è possibile abilitare e configurare backup di Azure nelle VM di Azure sottostanti ospitate in DevTest Labs. Inoltre, è possibile configurare la frequenza e il periodo di memorizzazione desiderati per i backup automatici purché si disponga dell'accesso appropriato alle risorse di calcolo sottostanti. 

- [Panoramica del backup delle macchine virtuali di Azure](../backup/backup-azure-vms-introduction.md)
- [Eseguire il backup di una macchina virtuale di Azure dalle impostazioni della macchina virtuale](../backup/backup-azure-vms-first-look-arm.md)

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente
**Linee guida:** Attualmente, Azure DevTest Labs non supporta i backup e gli snapshot delle macchine virtuali. Tuttavia, è possibile creare snapshot delle VM di Azure sottostanti ospitate in DevTest Labs o i dischi gestiti collegati a tali istanze usando PowerShell o le API REST, purché si disponga dell'accesso appropriato alle risorse di calcolo sottostanti. È anche possibile eseguire il backup di tutte le chiavi gestite dal cliente all'interno Azure Key Vault.

Abilitare backup di Azure nelle VM di Azure di destinazione e la frequenza e i periodi di conservazione desiderati. Include il backup completo dello stato del sistema. Se si usa crittografia dischi di Azure, il backup delle macchine virtuali di Azure gestisce automaticamente il backup delle chiavi gestite dal cliente.

- [Eseguire il backup in macchine virtuali di Azure che usano la crittografia](../backup/backup-azure-vms-encryption.md)
- [Panoramica del backup delle macchine virtuali di Azure](../backup/backup-azure-vms-introduction.md)
- [Panoramica del backup delle macchine virtuali di Azure](../backup/backup-azure-vms-introduction.md)
- [Come eseguire il backup di chiavi di Key Vault in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente
**Linee guida:** Verificare la possibilità di eseguire periodicamente il ripristino dei dati del contenuto all'interno di backup di Azure. Se necessario, testare il ripristino del contenuto a una sottoscrizione o a una rete virtuale isolata. Inoltre, testare il ripristino delle chiavi gestite dal cliente sottoposte a backup.

Se si usa crittografia dischi di Azure, è possibile ripristinare la macchina virtuale di Azure con le chiavi di crittografia del disco. Quando si usa la crittografia del disco, è possibile ripristinare la macchina virtuale di Azure con le chiavi di crittografia del disco.

- [Eseguire il backup in macchine virtuali di Azure che usano la crittografia](../backup/backup-azure-vms-encryption.md)
- [Come ripristinare i file dal backup delle macchine virtuali di Azure](../backup/backup-azure-restore-files-from-vm.md)
- [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)
- [Come eseguire il backup e il ripristino di una macchina virtuale crittografata](../backup/backup-azure-vms-encryption.md)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente
**Linee guida:** Quando si esegue il backup di dischi gestiti con backup di Azure, le macchine virtuali vengono crittografate a riposo con crittografia del servizio di archiviazione (SSE). Backup di Azure può anche eseguire il backup di macchine virtuali di Azure crittografate tramite crittografia dischi di Azure. Crittografia dischi di Azure si integra con le chiavi di crittografia di BitLocker (BEKs), che vengono protette in un insieme di credenziali delle chiavi come segreti. Crittografia dischi di Azure si integra inoltre con Azure Key Vault chiavi di crittografia della chiave (KEKs). Abilitare l'eliminazione temporanea in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.

- [Eliminazione temporanea per le macchine virtuali](../backup/soft-delete-virtual-machines.md)
- [Panoramica di Azure Key Vault-soft-delete](../key-vault/general/soft-delete-overview.md)

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Cliente

## <a name="incident-response"></a>Risposta agli eventi imprevisti
*Per altre informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare una guida per rispondere agli eventi imprevisti
**Linee guida:** Creare una guida alla risposta agli eventi imprevisti per la propria organizzazione. Verificare che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale e delle fasi di gestione/gestione degli eventi imprevisti dal rilevamento alla revisione post-evento imprevisto.

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
- [Utilizzare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per semplificare la creazione del piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti
**Linee guida:** Il Centro sicurezza di Azure assegna una gravità a ogni avviso per facilitare la priorità degli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, oltre che sul livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione, tramite i tag e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure, in particolare quelle che elaborano i dati sensibili. È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)
- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure:** Sì

**Responsabilità:** Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza
**Linee guida:** Eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare per aiutare a proteggere le risorse di Azure. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Pubblicazione del NIST: Guida ai programmi di test, formazione e esercizio per piani e funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza
**Linee guida:** Le informazioni di contatto per gli eventi imprevisti di sicurezza verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati da parte di utenti non autorizzati o non autorizzati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti
**Linee guida:** Consente di esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per identificare i rischi per le risorse di Azure. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)
- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Cliente

#### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza
**Linee guida:** Usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nei consigli per proteggere le risorse di Azure.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)
 
Monitoraggio del Centro sicurezza di Azure: * * * * non applicabile

**Responsabilità:** Cliente


## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team
*Per altre informazioni, vedere controllo di sicurezza: [test di penetrazione e esercizi del Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*


### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: eseguire con regolarità test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici in termini di sicurezza entro 60 giorni
**Linee guida:** Segui Microsoft Rules of Engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.

- [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Red Teaming di Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure:** Non applicabile

**Responsabilità:** Condiviso

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente:

- [Avvisi di sicurezza per gli ambienti in Azure DevTest Labs](environment-security-alerts.md)
