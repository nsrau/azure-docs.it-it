---
title: Baseline della sicurezza di Azure per Azure Resource Manager
description: La linea di base di sicurezza Azure Resource Manager fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6f7ed6381e2f8f203f2fd8e6ec7c073ecf20bafc
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133605"
---
# <a name="azure-security-baseline-for-azure-resource-manager"></a>Baseline della sicurezza di Azure per Azure Resource Manager

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 1,0](../../security/benchmarks/overview-v1.md) per Microsoft Azure Gestione risorse. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili a Azure Resource Manager. I **controlli** non applicabili ai Azure Resource Manager sono stati esclusi.

 Per informazioni su come Azure Resource Manager viene eseguito il mapping completo al benchmark di sicurezza di Azure, vedere il [file di mapping di base Azure Resource Manager sicurezza completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida** : inserire i log attività dei criteri di Azure tramite monitoraggio di Azure. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine o di archiviazione. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

- [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md) 

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../../azure-monitor/platform/diagnostic-settings.md) 

- [Come raccogliere i log degli host interni della macchina virtuale di Azure con monitoraggio di Azure](../../azure-monitor/learn/quick-collect-azurevm.md) 

- [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni** : Azure Resource Manager usa i log attività, abilitati automaticamente, per includere l'origine evento, la data, l'utente, il timestamp, gli indirizzi di origine, gli indirizzi di destinazione e altri elementi utili.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../../azure-monitor/platform/diagnostic-settings.md) 

- [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../../azure-monitor/platform/platform-logs-overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida** : analizzare e monitorare i log per un comportamento anomalo ed esaminare periodicamente i risultati. Usare monitoraggio di Azure e un'area di lavoro Log Analytics per esaminare i log ed eseguire query sui dati di log. 

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti. 

- [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md) 

- [Introduzione alle query di Log Analytics](../../azure-monitor/log-query/get-started-portal.md) 

- [Come eseguire query personalizzate in Monitoraggio di Azure](../../azure-monitor/log-query/get-started-queries.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida** : usare il Centro sicurezza di Azure con log Analytics per il monitoraggio e l'invio di avvisi sulle attività anomale trovate nei log attività. In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure. 

- [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md) 

- [Come gestire gli avvisi nel centro sicurezza di Azure](../../security-center/security-center-managing-and-responding-alerts.md) 

- [Come inviare un avviso sui dati del log Log Analytics](../../azure-monitor/learn/tutorial-response.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida** : il controllo degli accessi in base al ruolo di Azure consente di gestire l'accesso alle risorse di Azure tramite assegnazioni di ruolo. È possibile assegnare questi ruoli a utenti, gruppi di entità servizio e identità gestite. Sono disponibili ruoli predefiniti predefiniti per determinate risorse, che possono essere sottoposti a inventario o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o l'portale di Azure.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni** : creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio:

- Alla sottoscrizione deve essere assegnato più di un proprietario
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

È anche possibile abilitare l'accesso just-in-Time usando Azure AD Privileged Identity Management e Azure Resource Manager. 

- [Altre informazioni su Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

- [Come usare Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida** : laddove possibile, usare Azure Active Directory SSO anziché configurare singole credenziali autonome per servizio. Usare le raccomandazioni per l'identità e l'accesso del Centro sicurezza di Azure. 

- [Informazioni su SSO con Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Linee guida** : abilitare Azure Active Directory multi-factor authentication e seguire le indicazioni relative alla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

- [Come abilitare MFA in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida** : usare una workstation protetta gestita da Azure (nota anche come workstation di accesso con privilegi o Paw) per le attività amministrative che richiedono privilegi elevati.

- [Informazioni sulle workstation sicure gestite da Azure](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Come abilitare l'autenticazione a più fattori Azure AD](../../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Indicazioni** : usare Azure Active Directory report di sicurezza e il monitoraggio per rilevare quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: gestire le risorse di Azure solo dalle località approvate

**Indicazioni** : usare Azure ad località denominate per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o di paesi/aree geografiche.

- [Come configurare Azure AD località denominate](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Indicazioni** : usare Azure ad località denominate per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o di paesi/aree geografiche.

- [Come configurare Azure AD località denominate](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni** : Azure AD mette a disposizione i log necessari per individuare gli account obsoleti. Inoltre, utilizzare Azure AD le verifiche di identità e accesso per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso utente può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo. 

- [Informazioni sulla creazione di report Azure AD](/azure/active-directory/reports-monitoring/)

- [Come usare Azure AD le verifiche di identità e accesso](../../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida** : è possibile accedere alle origini del registro eventi di Azure ad, controllo e attività di accesso, che consentono di integrarsi con qualsiasi strumento Siem/Monitoring.

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro Log Analytics.

- [Come integrare i log attività di Azure con monitoraggio di Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Indicazioni** : usare le funzionalità di Azure ad Identity Protection per configurare risposte automatiche per rilevare azioni sospette correlate alle identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.

- [Come visualizzare gli accessi a rischio per Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: protezione dei dati](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni** : usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: usare RBAC di Azure per gestire l'accesso alle risorse

**Linee guida** : usare Azure ad RBAC per controllare l'accesso ai dati e alle risorse; in caso contrario, usare i metodi di controllo di accesso specifici del servizio.

- [Come configurare RBAC in Azure](../../role-based-access-control/role-assignments-portal.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida** : per la crittografia lato server, Azure Resource Manager supporta le chiavi gestite da Microsoft.

- [Informazioni sulla protezione dei dati in Azure Resource Manager](azure-resource-manager-security-controls.md#data-protection)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida** : usare monitoraggio di Azure con il log attività di Azure per creare avvisi quando le modifiche vengono apportate alle risorse di Azure critiche.

- [Come creare avvisi per gli eventi del log attività di Azure](../../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida** : usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) nelle sottoscrizioni. Verificare le autorizzazioni (lettura) appropriate nel tenant ed enumerare tutte le sottoscrizioni di Azure e le risorse nelle sottoscrizioni.

Sebbene le risorse di Azure classiche possano essere individuate tramite Esplora risorse di Azure, è consigliabile creare e usare Azure Resource Manager risorse.

- [Come creare query con Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee guida** : usare il nome, la descrizione e la categoria dei criteri per organizzare logicamente gli asset in base a una tassonomia.

- [Per altre informazioni sull'assegnazione di tag agli asset, vedere Guida alla decisione relativa alla denominazione delle risorse e all'assegnazione di tag](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni** : usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle risorse di Azure. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Usare inoltre i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Di seguito sono riportati altri dettagli correlati.

- [Come creare sottoscrizioni di Azure aggiuntive](/azure/billing/billing-create-subscription)

- [Come creare gruppi di gestione](/azure/governance/management-groups/create)

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definire e gestire un inventario delle risorse di Azure approvate

**Linee guida** : creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida** : usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.  Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida** : usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Di seguito sono riportati altri dettagli correlati.

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../../governance/policy/samples/built-in-policies.md#general)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee guida** : usare Azure ad l'accesso condizionale per limitare la capacità degli utenti di interagire con gestione risorse di Azure configurando "blocca l'accesso" per l'app "gestione Microsoft Azure".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida** : usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione delle risorse di Azure. È anche possibile usare le definizioni di criteri di Azure predefinite.

Azure Resource Manager è in grado di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per assicurarsi che le configurazioni soddisfino i requisiti di sicurezza per l'organizzazione.

È anche possibile usare le raccomandazioni del Centro sicurezza di Azure come linea di base di configurazione sicura per le risorse di Azure.

- [Come visualizzare gli alias dei criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md)

- [Esportazione di una singola e più risorse in un modello in portale di Azure](../templates/export-template-portal.md)

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../../security-center/recommendations-reference.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni** : usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.  Inoltre, è possibile usare i modelli di Azure Resource Manager per gestire la configurazione di sicurezza delle risorse di Azure richieste dall'organizzazione. 

Inoltre, in qualità di amministratore, potrebbe essere necessario bloccare una sottoscrizione, un gruppo di risorse o una risorsa per impedire ad altri utenti dell'organizzazione di eliminare o modificare accidentalmente le risorse critiche. È possibile impostare il livello di blocco CanNotDelete o ReadOnly.

- [Informazioni sugli effetti di Criteri di Azure](../../governance/policy/concepts/effects.md)

- [Creare e gestire i criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md)

- [Panoramica sui modelli di Azure Resource Manager](../templates/overview.md)

- [Come bloccare le risorse per impedire modifiche impreviste](lock-resources.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida** : usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio definizioni personalizzate di criteri di Azure, modelli di Azure Resource Manager e script di configurazione dello stato desiderato. Per accedere alle risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps oppure Active Directory se integrati con TFS.

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida** : definire e implementare configurazioni di sicurezza standard per le risorse di Azure usando i criteri di Azure. Usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione di rete delle risorse di Azure. È anche possibile usare le definizioni di criteri predefinite correlate alle risorse specifiche.  Inoltre, è possibile usare automazione di Azure per distribuire le modifiche di configurazione.

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Come usare gli alias](../../governance/policy/concepts/definition-structure.md#aliases)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida** : usare le definizioni di criteri di Azure predefinite e i criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [Deploy if not exist] per applicare automaticamente le configurazioni per le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni** : usare le procedure consigliate per la creazione del modello ARM. questi consigli consentono di evitare problemi comuni quando si usa un modello ARM per distribuire una soluzione.

Implementare Credential scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 

- [Implementare le procedure consigliate per la sicurezza del modello](../templates/template-best-practices.md)

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida** : garantire la possibilità di eseguire periodicamente la distribuzione di Azure Resource Manager modelli a intervalli regolari con una sottoscrizione isolata, se necessario.

- [Distribuire le risorse con i modelli ARM e portale di Azure](../templates/deploy-portal.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida** : usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio definizioni personalizzate di criteri di Azure, modelli di Azure Resource Manager e script di configurazione dello stato desiderato. Per accedere alle risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps oppure Active Directory se integrati con TFS.

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: risposta agli eventi imprevisti](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Guida** : sviluppare una guida alla risposta agli eventi imprevisti per la propria organizzazione. Assicurarsi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi della gestione e della gestione degli eventi imprevisti dal rilevamento alla revisione post-evento imprevisto. 

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Usare la guida alla gestione degli eventi imprevisti di sicurezza del computer NIST per semplificare la creazione del piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee guida** : eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare per proteggere le risorse di Azure. Identificare punti deboli e Gap, quindi rivedere il piano di risposta in base alle esigenze.

- [Pubblicazione del NIST: Guida ai programmi di test, formazione e esercizio per piani e funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida** : esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per identificare i rischi per le risorse di Azure. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo e continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida** : usare l'automazione del flusso di lavoro funzionalità del Centro sicurezza di Azure per attivare automaticamente le risposte agli avvisi e alle raccomandazioni di sicurezza per proteggere le risorse di Azure.

- [Come configurare l'automazione del flusso di lavoro nel centro sicurezza](../../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida** : seguire le regole di test di penetrazione Microsoft Cloud di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.

- [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
