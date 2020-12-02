---
title: Baseline della sicurezza di Azure per il Centro sicurezza
description: La linea di base di sicurezza del Centro sicurezza fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3b648168cd80920b8042f1edeacca893c21b98e2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498949"
---
# <a name="azure-security-baseline-for-security-center"></a>Baseline della sicurezza di Azure per il Centro sicurezza

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure](../security/benchmarks/overview.md) al centro sicurezza di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili al centro sicurezza di Azure. I **controlli** non applicabili al centro sicurezza di Azure sono stati esclusi. Per informazioni sul modo in cui il Centro sicurezza di Azure è completamente mappato al benchmark di sicurezza di Azure, vedere il file di mapping di base sicurezza del [Centro sicurezza](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)di Azure completo.

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: il Centro sicurezza di Azure è un'offerta di Azure di base. Non è possibile associare una rete virtuale, una subnet o un gruppo di sicurezza di rete direttamente al centro sicurezza. Se si Abilita la raccolta dati per le risorse di calcolo, il Centro sicurezza archivia i dati raccolti tramite un'area di lavoro di Log Analytics, è possibile configurare l'area di lavoro in modo che usi il collegamento privato per accedere ai dati dell'area di lavoro tramite un endpoint privato nella rete virtuale. Inoltre, se l'uso del Centro sicurezza raccolta dati si basa sull'agente Log Analytics distribuito nei server per raccogliere i dati di sicurezza e garantire la protezione per queste risorse di calcolo. L'agente di Log Analytics richiede l'apertura di porte e protocolli specifici per il corretto funzionamento del Centro sicurezza. Bloccare le reti in modo che consentano solo queste porte e protocolli richiesti e aggiungere solo altre regole che l'applicazione richiede per operare tramite gruppi di sicurezza di rete.

- [Raccolta dati nel Centro sicurezza di Azure](security-center-enable-data-collection.md)

- [Traffico di rete del filer con un gruppo di sicurezza di rete](../virtual-network/tutorial-filter-network-traffic.md)

- [Requisiti del firewall per l'uso dell'agente di Log Analytics](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [Informazioni sul collegamento privato di Azure](../private-link/private-link-overview.md) 

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: l'offerta del Centro sicurezza di Azure non si integra direttamente con una rete virtuale, ma può raccogliere dati dai server configurati con l'agente di log Analytics distribuiti nelle reti. Per i server configurati per l'invio di dati al centro sicurezza è necessario che determinate porte e protocolli siano autorizzati a comunicare correttamente. Definire e applicare le configurazioni di sicurezza standard per queste risorse di rete con criteri di Azure.

È anche possibile usare i progetti di Azure per semplificare le distribuzioni su larga scala di Azure tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio Azure Resource Manager modelli, assegnazioni di ruolo e assegnazioni di criteri di Azure, in una singola definizione di progetto. È possibile applicare il progetto alle nuove sottoscrizioni per distribuire le configurazioni del Centro sicurezza e le risorse di rete correlate in modo coerente e sicuro.

- [Raccolta dati nel Centro sicurezza di Azure](security-center-enable-data-collection.md)

- [Requisiti del firewall per l'uso dell'agente di Log Analytics](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Esempi di criteri di Azure per la rete](../governance/policy/samples/built-in-policies.md#network)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida**: l'offerta del Centro sicurezza di Azure non si integra direttamente con una rete virtuale, ma può raccogliere dati dai server configurati con l'agente di log Analytics distribuiti nelle reti. Per i server configurati per l'invio di dati al centro sicurezza è necessario che determinate porte e protocolli siano autorizzati a comunicare correttamente. Definire e applicare le configurazioni di sicurezza standard per queste risorse di rete con criteri di Azure.

Usare i tag delle risorse per i gruppi di sicurezza di rete e altre risorse, ad esempio i server nelle reti configurate per l'invio dei log di sicurezza al centro sicurezza di Azure. Per le singole regole del gruppo di sicurezza di rete, usare il campo "Description" per documentare le regole che consentono il traffico da e verso una rete. 

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag. 

- [Raccolta dati nel Centro sicurezza di Azure](security-center-enable-data-collection.md)

- [Requisiti del firewall per l'uso dell'agente di Log Analytics](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md) 

- [Come creare una rete virtuale di Azure](../virtual-network/quick-create-portal.md) 

- [Come filtrare il traffico di rete con le regole del gruppo di sicurezza di rete](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse e rilevare le modifiche per le risorse di rete correlate al centro sicurezza di Azure. Creare avvisi in monitoraggio di Azure per ricevere una notifica quando si verificano modifiche alle risorse critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log) 

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: aggregare i dati di sicurezza generati dal centro sicurezza di Azure e dalle relative origini connesse usando un'area di lavoro centrale log Analytics. 

Configurare la raccolta dei dati del Centro sicurezza per inviare dati di sicurezza ed eventi dalle risorse di calcolo di Azure connesse a un'area di lavoro Log Analytics centrale. Oltre alla raccolta dei dati, usare la funzionalità di esportazione continua per trasmettere avvisi di sicurezza e consigli generati dal centro sicurezza nell'area di lavoro di Log Analytics centrale. In monitoraggio di Azure è possibile eseguire query ed eseguire analisi sui dati di sicurezza generati dal centro sicurezza e dalle risorse di Azure connesse. 

In alternativa, è possibile inviare i dati generati dal centro sicurezza ad Azure Sentinel o a un SIEM di terze parti.

- [Esportazione continua dei dati del Centro sicurezza](continuous-export.md)

- [Raccolta dati nel Centro sicurezza di Azure](security-center-enable-data-collection.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md) 

- [Come raccogliere i log degli host interni della macchina virtuale di Azure con monitoraggio di Azure](../azure-monitor/learn/quick-collect-azurevm.md)

- [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: i log attività di monitoraggio di Azure sono automaticamente disponibili. questi log contengono tutte le operazioni di scrittura per la risorsa, ad esempio il Centro sicurezza di Azure, incluse le operazioni effettuate, l'utente che ha avviato l'operazione e quando si sono verificate. Inviare i log attività di Azure a un'area di lavoro Log Analytics per il consolidamento dei log e una maggiore conservazione.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md) 

- [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Inviare i log attività a un'area di lavoro Log Analytics](../azure-monitor/platform/activity-log.md#send-to-log-analytics-workspace)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: in monitoraggio di Azure impostare il periodo di conservazione dell'area di lavoro log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione. 

- [Modificare il periodo di conservazione dei dati in Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) 

- [Come configurare i criteri di conservazione per i log dell'account di archiviazione di Azure](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: analizzare e monitorare i log prodotti dal centro sicurezza di Azure e le relative origini connesse per un comportamento anomalo ed esaminare regolarmente i risultati. Usare monitoraggio di Azure e un'area di lavoro Log Analytics per esaminare i log ed eseguire query sui dati di log.

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti. 

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Introduzione alle query di Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md) 

- [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida**: configurare gli avvisi del log di monitoraggio di Azure per eseguire query per attività indesiderate o anomale registrate dal log attività o dai dati prodotti dal centro sicurezza di Azure. Configurare i gruppi di azione in modo che l'organizzazione riceva una notifica e possa intervenire se viene avviato un avviso del log per attività anomale. Usare la funzionalità di automazione del flusso di lavoro del Centro sicurezza per attivare app per la logica su avvisi di sicurezza e raccomandazioni I flussi di lavoro del Centro sicurezza possono essere usati per notificare agli utenti la risposta agli eventi imprevisti oppure intraprendere azioni per correggere le risorse in base alle informazioni sull'avviso.

In alternativa, è possibile abilitare e caricare i dati correlati e prodotti dal centro sicurezza di Azure ad Azure Sentinel. Azure Sentinel supporta i PlayBook che consentono la risposta automatica alle minacce per i problemi correlati alla sicurezza.

- [Automazione del flusso di lavoro del Centro sicurezza di Azure](workflow-automation.md)

- [Come gestire gli avvisi nel centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) 

- [Come inviare un avviso sui dati del log di log Analytics](../azure-monitor/learn/tutorial-response.md)

- [Configurare le risposte automatiche alle minacce in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

- [Avvisi del log in Monitoraggio di Azure](../azure-monitor/platform/alerts-unified-log.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni**: il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) consente di gestire l'accesso alle risorse di Azure tramite assegnazioni di ruolo. È possibile assegnare questi ruoli a utenti, gruppi di entità servizio e identità gestite. Per alcune risorse sono disponibili ruoli predefiniti, che possono essere inventariati o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o il portale di Azure. Il Centro sicurezza di Azure dispone di ruoli predefiniti per "Security Reader" o "Security admin" che consente agli utenti di leggere o aggiornare i criteri di sicurezza e di ignorare gli avvisi e le raccomandazioni.

- [Autorizzazioni nel Centro sicurezza di Azure](security-center-permissions.md)

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Linee guida**: creare procedure operative standard per l'uso di account amministrativi dedicati per la piattaforma Azure o specifici per l'offerta del Centro sicurezza di Azure. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure per monitorare il numero di account amministrativi in Azure Active Directory. Il Centro sicurezza dispone anche di ruoli predefiniti per la sicurezza admin, che consente agli utenti di aggiornare i criteri di sicurezza e di ignorare gli avvisi e le raccomandazioni, di verificare e risolvere gli eventuali utenti che dispongono di questa assegnazione di ruolo a intervalli regolari.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio:

- Alla sottoscrizione deve essere assegnato più di un proprietario
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

- [Autorizzazioni nel Centro sicurezza di Azure](security-center-permissions.md)

- [Come usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso (Anteprima)](security-center-identity-access.md)

- [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida**: laddove possibile, usare Azure Active Directory SSO anziché configurare singole credenziali autonome per servizio. Usare le raccomandazioni per l'identità e l'accesso del Centro sicurezza di Azure.

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più fattori Azure Active Directory per accedere al centro sicurezza di Azure e alle portale di Azure, seguire le indicazioni relative all'identità e all'accesso al centro sicurezza. 

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida**: usare una workstation protetta gestita da Azure (nota anche come workstation di accesso con privilegi o Paw) per le attività amministrative che richiedono privilegi elevati.

- [Informazioni sulle workstation sicure gestite da Azure](../active-directory/devices/concept-azure-managed-workstation.md)

- [Come abilitare l'autenticazione a più fattori Azure AD](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Indicazioni**: usare Azure Active Directory report di sicurezza e il monitoraggio per rilevare quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md) 

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare Azure ad località denominate per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o di paesi/aree geografiche. 

- [Come configurare Azure AD località denominate](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale quando si usa il Centro sicurezza di Azure. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente. Il Centro sicurezza di Azure dispone di ruoli predefiniti che possono essere assegnati come ' Security admin, che consente agli utenti di aggiornare i criteri di sicurezza e di ignorare gli avvisi e le raccomandazioni.

- [Autorizzazioni nel Centro sicurezza di Azure](security-center-permissions.md)

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure Active Directory mette a disposizione i log necessari per individuare gli account obsoleti. Inoltre, utilizzare Azure AD le verifiche di identità e accesso per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso utente correlato al centro sicurezza di Azure può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti giusti abbiano accesso continuo. 

- [Informazioni sulla creazione di report Azure AD](../active-directory/reports-monitoring/index.yml) 

- [Come usare le verifiche di accesso e delle identità di Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: è possibile accedere alle origini del registro eventi di Azure ad, controllo e attività di accesso, che consentono di integrarsi con qualsiasi strumento Siem/Monitoring. 

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro Log Analytics.  

- [Come integrare i log attività di Azure con monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account 

**Indicazioni**: usare le funzionalità di Azure ad Identity Protection per configurare risposte automatiche per rilevare azioni sospette correlate alle identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita. 

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md) 

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per semplificare il monitoraggio delle risorse di Azure, ad esempio l'area di lavoro log Analytics che archivia le informazioni di sicurezza sensibili dal centro sicurezza di Azure.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare l'isolamento utilizzando sottoscrizioni e gruppi di gestione distinti per singoli domini di sicurezza, ad esempio il tipo di ambiente e il livello di sensibilità dei dati. È possibile limitare il livello di accesso alle risorse di Azure richieste dalle applicazioni e dagli ambienti aziendali. È possibile controllare l'accesso alle risorse di Azure tramite RBAC di Azure.

Per impostazione predefinita, i dati del Centro sicurezza di Azure vengono archiviati nel servizio back-end del Centro sicurezza. Se l'organizzazione ha aggiunto requisiti per archiviare questi dati nelle proprie risorse, è possibile configurare un'area di lavoro di Log Analytics per archiviare i dati del Centro sicurezza, gli avvisi e le raccomandazioni. Quando si usa un'area di lavoro personalizzata, è possibile aggiungere un'ulteriore separazione configurando aree di lavoro diverse in base all'ambiente in cui sono stati originati i dati.

- [Esportazione continua dei dati del Centro sicurezza](continuous-export.md)

- [Raccolta dati nel Centro sicurezza di Azure](security-center-enable-data-collection.md)

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md) 

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md) 

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: crittografare tutte le informazioni riservate in transito. Assicurarsi che tutti i client che si connettono alle risorse di Azure siano in grado di negoziare TLS 1,2 o versione successiva. Tutte le macchine virtuali configurate con l'agente di Log Analytics e per inviare dati al centro sicurezza di Azure devono essere configurate per l'uso di TLS 1,2.

Seguire le raccomandazioni del Centro sicurezza di Azure per la crittografia dei dati inattivi e la crittografia in transito, ove applicabile. 

- [Invio sicuro di dati a Log Analytics](../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)

- [Informazioni sulla crittografia in transito con Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse 

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per gestire l'accesso ai dati e alle risorse correlati al centro sicurezza di Azure. Il Centro sicurezza di Azure dispone di ruoli predefiniti per "Security Reader" o "Security admin" che consente agli utenti di leggere o aggiornare i criteri di sicurezza e di ignorare gli avvisi e le raccomandazioni. L'area di lavoro Log Analytics che archivia i dati raccolti dal centro sicurezza dispone anche di ruoli predefiniti che è possibile assegnare, ad esempio ' Log Analytics Reader ',' Log Analytics Contributor ' e altri. Assegnare il ruolo meno permissivo necessario per consentire agli utenti di completare le attività necessarie. Assegnare ad esempio il ruolo di lettore agli utenti che devono visualizzare solo le informazioni sull'integrità della sicurezza di una risorsa, ma non eseguono alcuna azione, come l'applicazione di consigli e la modifica di criteri.

- [Autorizzazioni per l'area di lavoro di Azure Log Analytics](../role-based-access-control/built-in-roles.md#log-analytics-reader)

- [Autorizzazioni nel Centro sicurezza di Azure](security-center-permissions.md)

- [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: il Centro sicurezza di Azure usa un'area di lavoro configurata log Analytics per archiviare i dati, gli avvisi e le raccomandazioni che genera. Configurare una chiave gestita dal cliente (CMK) per l'area di lavoro configurata per la raccolta dei dati del Centro sicurezza. CMK consente la crittografia di tutti i dati salvati o inviati all'area di lavoro con una chiave di Azure Key Vault creata e di proprietà dell'utente. 

- [Chiave gestita dal cliente di Monitoraggio di Azure](../azure-monitor/platform/customer-managed-keys.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure per creare avvisi quando le modifiche vengono apportate alle risorse di Azure critiche correlate al centro sicurezza di Azure. Queste modifiche possono includere qualsiasi azione che modifichi le configurazioni relative al centro sicurezza, come la disabilitazione di avvisi o consigli oppure l'aggiornamento o l'eliminazione di archivi dati.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Linee guida**: usare un programma comune di assegnazione dei punteggi di rischio, ad esempio un sistema comune di valutazione delle vulnerabilità, o le valutazioni di rischio predefinite fornite dallo strumento di analisi di terze parti.

- [Pubblicazione NIST: sistema di valutazione delle vulnerabilità comune](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse correlate al centro sicurezza di Azure nelle sottoscrizioni. Verificare le autorizzazioni (lettura) appropriate nel tenant ed enumerare tutte le sottoscrizioni di Azure per individuare le risorse del Centro sicurezza. 

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: usare i tag per semplificare il monitoraggio delle risorse di Azure, ad esempio l'area di lavoro log Analytics che archivia le informazioni di sicurezza sensibili dal centro sicurezza di Azure.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle risorse del Centro sicurezza di Azure. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Usare inoltre i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Linee guida**: creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni. 

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.  Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate. 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: rimuovere le risorse di Azure correlate al centro sicurezza di Azure quando non sono più necessarie come parte del processo di inventario e revisione dell'organizzazione.

- [Eliminazione di risorse e gruppi di risorse di Azure](../azure-resource-manager/management/delete-resource-group.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/index.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per il Centro sicurezza di Azure e l'area di lavoro connessa tramite criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. OperationalInsights" e "Microsoft. Security" per creare definizioni di criteri di Azure personalizzate per controllare o applicare la configurazione del Centro sicurezza e della relativa area di lavoro Log Analytics.

- [Come visualizzare gli alias di Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: usare gli effetti dei criteri di Azure per ' Deny ' ed ' deploy if not exist ' per applicare impostazioni sicure tra le risorse di Azure. Inoltre, è possibile usare i modelli di Azure Resource Manager per gestire la configurazione di sicurezza delle risorse di Azure richieste dall'organizzazione. 

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md) 

- [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md) 

- [Panoramica sui modelli di Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: usare Azure DevOps per archiviare e gestire in modo sicuro il codice come definizioni di criteri di Azure personalizzate, Azure Resource Manager modelli e gli script di configurazione dello stato desiderato. Per accedere alle risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps oppure Active Directory se integrati con TFS. 

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di Azure usando i criteri di Azure. Usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione delle risorse correlate al centro sicurezza di Azure. Inoltre, è possibile usare automazione di Azure per distribuire le modifiche di configurazione. 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come usare gli alias](../governance/policy/concepts/definition-structure.md#aliases)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida**: usare le definizioni di criteri di Azure predefinite, nonché gli alias di criteri di Azure in "Microsoft. OperationalInsights" e "Microsoft. Sicurezza "spazi dei nomi per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni delle risorse di Azure. Usare gli effetti dei criteri di Azure "audit", "Deny" e "deploy if not exist" per applicare automaticamente le configurazioni per le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: il Centro sicurezza di Azure usa un'area di lavoro configurata log Analytics per archiviare i dati, gli avvisi e le raccomandazioni che genera. Configurare una chiave gestita dal cliente (CMK) per l'area di lavoro configurata per la raccolta dei dati del Centro sicurezza. CMK consente la crittografia di tutti i dati salvati o inviati all'area di lavoro con una chiave di Azure Key Vault creata e di proprietà dell'utente. 

- [Chiave gestita dal cliente di Monitoraggio di Azure](../azure-monitor/platform/customer-managed-keys.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

- [Come configurare Credential scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: il Centro sicurezza di Azure non è progettato per archiviare o elaborare i file. È responsabilità dell'utente eseguire la pre-analisi di tutti i contenuti caricati nelle risorse di Azure non di calcolo, inclusa Log Analytics area di lavoro.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati 

**Linee guida**: seguire un approccio dell'infrastruttura come codice (IAC) e usare Azure Resource Manager per distribuire le risorse correlate al centro sicurezza di Azure in un modello di JavaScript Object Notation (JSON) che può essere usato come backup per le configurazioni correlate alle risorse.

- [Esportazione di una singola e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Modelli di Azure Resource Manager per la risorsa di sicurezza](/azure/templates/microsoft.security/allversions)

- [Informazioni su Automazione di Azure](../automation/automation-intro.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: il Centro sicurezza di Azure usa un'area di lavoro di log Analytics per archiviare i dati, gli avvisi e le raccomandazioni che genera. È possibile configurare monitoraggio di Azure e l'area di lavoro utilizzata dal centro sicurezza per abilitare una chiave gestita dal cliente. Se si usa un Key Vault per archiviare le chiavi gestite dal cliente, assicurarsi che i backup automatici delle chiavi siano regolari.

- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: garantire la possibilità di eseguire periodicamente il ripristino usando Azure Resource Manager file di modello supportati. Testare il ripristino delle chiavi gestite dal cliente sottoposte a backup.

- [Gestire Log Analytics area di lavoro con modelli di Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md)

- [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio definizioni di criteri di Azure e modelli di Azure Resource Manager personalizzati. Per proteggere le risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps oppure Active Directory se integrato con TFS. Usare il controllo degli accessi in base al ruolo di Azure per proteggere le chiavi gestite dal cliente.

Inoltre, abilitare Soft-Delete ed eliminare la protezione in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.  Se l'archiviazione di Azure viene usata per archiviare i backup del modello di Azure Resource Manager, abilitare l'eliminazione temporanea per salvare e ripristinare i dati quando vengono eliminati BLOB o snapshot BLOB. 

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Come abilitare Soft-Delete ed eliminare la protezione in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal) 

- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Guida**: sviluppare una guida alla risposta agli eventi imprevisti per la propria organizzazione. Assicurarsi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi della gestione e della gestione degli eventi imprevisti dal rilevamento alla revisione post-evento imprevisto. 

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Usare la guida alla gestione degli eventi imprevisti di sicurezza del computer NIST per semplificare la creazione del piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Linee guida**: il Centro sicurezza di Azure assegna una gravità a ogni avviso per facilitare la priorità degli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

Inoltre, contrassegnare le sottoscrizioni usando i tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto. 

- [Avvisi di sicurezza nel Centro sicurezza di Azure](security-center-alerts-overview.md) 

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare per proteggere le risorse di Azure. Identificare punti deboli e Gap, quindi rivedere il piano di risposta in base alle esigenze. 

- [Pubblicazione del NIST: Guida ai programmi di test, formazione e esercizio per piani e funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per identificare i rischi per le risorse di Azure. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo e continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel. 

- [Come configurare l'esportazione continua](continuous-export.md) 

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare l'automazione del flusso di lavoro funzionalità del Centro sicurezza di Azure per attivare automaticamente le risposte agli avvisi e alle raccomandazioni di sicurezza per proteggere le risorse di Azure. 

- [Come configurare l'automazione del flusso di lavoro nel centro sicurezza](workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: seguire le regole di test di penetrazione Microsoft Cloud di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft. 

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)