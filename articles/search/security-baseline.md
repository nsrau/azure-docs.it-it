---
title: Baseline della sicurezza di Azure per Azure ricerca cognitiva
description: Azure ricerca cognitiva Baseline Security fornisce istruzioni e risorse per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f4782b923222208bbf759ba8415162621a55e0e1
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631156"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Baseline della sicurezza di Azure per Azure ricerca cognitiva

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 1,0](../security/benchmarks/overview.md) ad Azure ricerca cognitiva. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili ad Azure ricerca cognitiva. I **controlli** non sono applicabili ad Azure ricerca cognitiva o il cliente è stato escluso.

Per informazioni sul modo in cui Azure ricerca cognitiva è completamente mappato al benchmark di sicurezza di Azure, vedere il [file di mapping di base della sicurezza ricerca cognitiva di Azure completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida** : assicurarsi che tutte le distribuzioni di rete virtuale di Microsoft Azure subnet dispongano di un gruppo di sicurezza di rete applicato con regole per implementare uno schema di accesso con privilegi minimi. Consentire l'accesso solo alle porte attendibili dell'applicazione e agli intervalli di indirizzi IP. Distribuire ricerca cognitiva di Azure con un endpoint privato di Azure, ove possibile, per abilitare l'accesso privato ai servizi dalla rete virtuale.

Ricerca cognitiva supporta inoltre funzionalità di sicurezza di rete aggiuntive per la gestione degli elenchi di controllo di accesso di rete. Configurare il servizio di ricerca in modo che consenta solo la comunicazione con origini attendibili limitando l'accesso da intervalli di indirizzi IP pubblici specifici usando la funzionalità del firewall.

- [Come configurare gli endpoint privati per Azure ricerca cognitiva](./service-create-private-endpoint.md)

- [Come configurare il firewall di Azure ricerca cognitiva](./service-configure-firewall.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

**Linee guida** : non è possibile distribuire ricerca cognitiva direttamente in una rete virtuale. Tuttavia, se l'applicazione client o le origini dati si trovano in una rete virtuale, è possibile monitorare e registrare il traffico per i componenti in rete, incluse le richieste inviate a un servizio di ricerca nel cloud. Le raccomandazioni standard includono l'abilitazione di un log di flusso del gruppo di sicurezza di rete e l'invio di log a un'area di lavoro di Azure o Log Analytics Facoltativamente, è possibile usare Analisi del traffico per ottenere informazioni dettagliate sui modelli di traffico.

- [Come abilitare i log dei flussi dei gruppi di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee guida** : non applicabile a ricerca cognitiva. Questa raccomandazione è destinata alle applicazioni Web in esecuzione in app Azure servizio o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida** : ricerca cognitiva non fornisce una funzionalità specifica per combattere un attacco Denial of Service distribuito, ma è possibile abilitare la protezione DDoS standard nelle reti virtuali associate al servizio ricerca cognitiva per la protezione generale.

- [Come configurare la protezione DDoS](../virtual-network/manage-ddos-protection.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida** : abilitare i log di flusso del gruppo di sicurezza di rete per i gruppi di sicurezza di rete che proteggono le macchine virtuali di Azure che si connetteranno al servizio ricerca cognitiva. Inviare i log a un account di archiviazione di Azure per il controllo del traffico. 

Se necessario, abilitare l'acquisizione di pacchetti Network Watcher per analizzare le attività anomale.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida** : ricerca cognitiva non supporta il rilevamento delle intrusioni di rete, ma come mitigazione delle intrusioni, è possibile configurare le regole del firewall per specificare gli indirizzi IP accettati dal servizio di ricerca cognitiva. Configurare un endpoint privato per evitare il traffico di ricerca dalla rete Internet pubblica.

- [Come configurare le chiavi gestite dal cliente per la crittografia dei dati](./search-security-manage-encryption-keys.md)

- [Come ottenere informazioni sulle chiavi gestite dal cliente da indici e mappe sinonimi](./search-security-get-encryption-keys.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee guida** : non applicabile a ricerca cognitiva. Questa raccomandazione è destinata alle applicazioni Web in esecuzione in app Azure servizio o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida** : usare i tag di servizio, se si usano gli indicizzatori e skillsets in ricerca cognitiva, per rappresentare un intervallo di indirizzi IP che dispongono delle autorizzazioni per connettersi a risorse esterne. 

Consentire o negare il traffico alle risorse specificando il nome del tag di servizio (ad esempio, AzureCognitiveSearch) nel campo di origine o destinazione appropriato di una regola. 

- [Tag del servizio di rete virtuale](../virtual-network/service-tags-overview.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida** : ricerca cognitiva non dispone o dipendono dalle risorse di rete per progettazione. Le app client e le origini dati correlate all'applicazione di ricerca potrebbero trovarsi in una rete virtuale, ma il servizio di ricerca non è distribuito nella rete. 

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida** : è possibile configurare ricerca cognitiva con un endpoint privato di Azure per integrare il servizio di ricerca con una rete virtuale.  Usare i tag delle risorse per i gruppi di sicurezza di rete e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le singole regole del gruppo di sicurezza di rete, usare il campo "Description" per documentare le regole che consentono il traffico da e verso una rete. 

Usare una qualsiasi delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio gli effetti "Richiedi tag e il relativo valore", per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag. 

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag. 

- [Come creare un endpoint privato per ricerca cognitiva](./service-create-private-endpoint.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale di Azure](../virtual-network/quick-create-portal.md)

- [Come filtrare il traffico di rete con le regole del gruppo di sicurezza di rete](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida** : ricerca cognitiva non dispone o dipendono da alcun componente di rete, pertanto non è possibile monitorare le configurazioni di queste risorse.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Indicazioni** : ricerca cognitiva non supporta la configurazione di origini di sincronizzazione dell'ora personalizzate. Il servizio di ricerca si basa sulle origini di sincronizzazione ora di Microsoft e non viene esposto ai clienti per la configurazione.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni** : inserire i log correlati all'ricerca cognitiva tramite monitoraggio di Azure per aggregare i dati di sicurezza generati da dispositivi endpoint, risorse di rete e altri sistemi di sicurezza. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione.
In alternativa, è possibile abilitare e caricare questi dati in Sentinel di Azure o in un SIEM di terze parti.

- [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida** : i log di diagnostica e operativi forniscono informazioni approfondite sulle operazioni dettagliate dei ricerca cognitiva e sono utili per il monitoraggio del servizio e per i carichi di lavoro che accedono al servizio.  Per acquisire i dati di diagnostica, abilitare la registrazione specificando dove vengono archiviate le informazioni di registrazione.

- [Come raccogliere e analizzare i dati di log per Azure ricerca cognitiva](./search-monitor-logs.md)

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md) 

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Linee guida** : non applicabile a ricerca cognitiva. Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida** : i dati cronologici che vengono inseriti nelle metriche di diagnostica vengono conservati da ricerca cognitiva per 30 giorni per impostazione predefinita. Per un periodo di conservazione più lungo, assicurarsi di abilitare l'impostazione che specifica un'opzione di archiviazione per rendere permanente le metriche e gli eventi registrati.

In monitoraggio di Azure impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione. 

- [Modificare il periodo di conservazione dei dati in Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Come configurare i criteri di conservazione per i log dell'account di archiviazione di Azure](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida** : analizzare e monitorare i log del servizio ricerca cognitiva per un comportamento anomalo. Usare Log Analytics di Monitoraggio di Azure per esaminare i log ed eseguire query sui relativi dati. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

- [Come raccogliere e analizzare i dati di log per ricerca cognitiva](./search-monitor-logs.md)

- [Come visualizzare i dati del log di ricerca in Power BI](./search-monitor-logs-powerbi.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Informazioni su Log Analytics](../azure-monitor/log-query/get-started-portal.md)

- [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida** : usare il Centro sicurezza con log Analytics area di lavoro per il monitoraggio e l'invio di avvisi su attività anomale riscontrate in registri di sicurezza ed eventi. In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come gestire gli avvisi nel centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Come inviare un avviso sui dati del log di log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida** : non applicabile a ricerca cognitiva. Microsoft gestisce la soluzione anti-malware per la piattaforma sottostante.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida** : non applicabile a ricerca cognitiva. Non produce né utilizza i log DNS.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Linee guida** : non applicabile a ricerca cognitiva. Il controllo da riga di comando non è disponibile per ricerca cognitiva.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni** : il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) consente di gestire l'accesso alle risorse di Azure tramite assegnazioni di ruolo. È possibile assegnare questi ruoli a utenti, gruppi di entità servizio e identità gestite. Sono disponibili ruoli predefiniti predefiniti per determinate risorse, che possono essere sottoposti a inventario o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o l'portale di Azure.

Ricerca cognitiva ruoli sono associati alle autorizzazioni che supportano le attività di gestione del livello di servizio.  Questi ruoli non concedono l'accesso all'endpoint del servizio. Per l'accesso alle operazioni eseguite sull'endpoint, ad esempio la gestione degli indici, il popolamento dell'indice e le query sui dati di ricerca, usare le chiavi API per autenticare la richiesta.

- [Impostare i ruoli per l'accesso amministrativo ad Azure ricerca cognitiva](./search-security-rbac.md)

- [Creare e gestire le chiavi API per un servizio ricerca cognitiva di Azure](./search-security-api-keys.md)

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)
- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida** : non applicabile a ricerca cognitiva. Non dispone di un concetto di password predefinite.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Linee guida** : ricerca cognitiva non ha il concetto di account di amministratore di Azure Active Directory o di livello locale (Azure ad) che può essere usato per gestire gli indici e le operazioni. 

Usare i ruoli predefiniti Azure AD che devono essere assegnati in modo esplicito per le operazioni di gestione. Richiamare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

- [Come usare i ruoli per l'accesso amministrativo in ricerca cognitiva](./search-security-rbac.md)

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Indicazioni** : usare l'autenticazione SSO con Azure Active Directory (Azure ad) per accedere alle informazioni sul servizio di ricerca per le operazioni di gestione supportate tramite Azure Resource Manager. 

Definire un processo per ridurre il numero di identità e credenziali abilitando SSO per il servizio con le identità preesistenti dell'organizzazione.

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Linee guida** : abilitare la funzionalità di multi-factor authentication (multi Azure ad) di Azure Active Directory e seguire le indicazioni relative all'identità e all'accesso del Centro sicurezza.

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md) 

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni** : usare una workstation con accesso con privilegi (Paw) con multi-factor authentication (multi-factor authentication) configurato per accedere alle risorse di Azure e accedervi.

- [Informazioni sulle workstation sicure gestite da Azure](../active-directory/devices/concept-azure-managed-workstation.md)
 

- [Come abilitare l'autenticazione a più fattori Azure AD](../active-directory/authentication/howto-mfa-getstarted.md)
 

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida** : usare i report di sicurezza di Azure Active Directory (Azure ad) e il monitoraggio per rilevare quando nell'ambiente si verificano attività sospette o non sicure. Usare il Centro sicurezza per monitorare le attività di identità e accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: gestire le risorse di Azure solo dalle località approvate

**Linee guida** : non applicabile a ricerca cognitiva. Non supporta l'uso della località approvata come condizione per l'accesso.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida** : usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale per le attività di gestione del livello di servizio in Azure ricerca cognitiva. Azure AD identità non consentono l'accesso all'endpoint del servizio di ricerca.  L'accesso a operazioni come la gestione degli indici, il popolamento degli indici e le query sui dati di ricerca sono disponibili tramite le chiavi API.

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Creare e gestire le chiavi API per un servizio ricerca cognitiva di Azure](./search-security-api-keys.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni** : Azure Active Directory (Azure ad) fornisce i log per individuare gli account obsoleti. Usare le verifiche di accesso e identità di Azure AD per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso utente può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo. 

Esaminare i log di diagnostica da ricerca cognitiva per attività nell'endpoint del servizio di ricerca, ad esempio gestione degli indici, popolamento degli indici e query.

- [Informazioni sulla creazione di report Azure AD](../active-directory/reports-monitoring/index.yml)

- [Come usare Azure AD le verifiche di identità e accesso](../active-directory/governance/access-reviews-overview.md)

- [Monitorare le operazioni e le attività di Azure ricerca cognitiva](./search-monitor-usage.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida** : Accesso ad Azure Active Directory (Azure ad) l'attività di accesso, il controllo e le origini del registro eventi di rischio consentono di integrarsi con qualsiasi strumento Siem o Monitoring.

Semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. Configurare gli avvisi desiderati nell'area di lavoro Log Analytics.

- [Come integrare i log attività di Azure con monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Indicazioni** : usare le funzionalità di protezione delle identità Azure Active Directory (Azure ad) per configurare risposte automatiche per rilevare azioni sospette correlate alle identità utente. Inserire i dati in Sentinel di Azure per un'analisi più approfondita, come richiesto.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md) 

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida** : non applicabile a ricerca cognitiva. Customer Lockbox non supporta ricerca cognitiva.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

## <a name="data-protection"></a>Protezione dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni** : usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni** : implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Le risorse devono essere separate da rete virtuale/subnet, contrassegnate in modo appropriato e protette in un gruppo di sicurezza di rete o in un firewall di Azure. Le risorse che archiviano o elaborano dati sensibili devono essere isolate. Usare il collegamento privato per configurare un endpoint privato per ricerca cognitiva.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md) 

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare un endpoint privato per ricerca cognitiva](./service-create-private-endpoint.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Indicazioni** : usare una soluzione di terze parti da Azure Marketplace in perimetri di rete per monitorare il trasferimento non autorizzato di informazioni riservate e bloccare tali trasferimenti mentre si inviano avvisi ai professionisti della sicurezza delle informazioni.

Microsoft gestisce la piattaforma sottostante e considera tutti i contenuti dei clienti come sensibili e protegge dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md) 

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Indicazioni** : ricerca cognitiva crittografa i dati in transito con Transport Layer Security 1,2 e impone la crittografia (SSL/TLS) in qualsiasi momento per tutte le connessioni. Ciò garantisce che tutti i dati siano crittografati in transito tra il client e il servizio.

- [Informazioni sulla crittografia in transito con Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni** : le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per ricerca cognitiva. Implementare una soluzione di terze parti, se necessario ai fini della conformità. 

Microsoft gestisce la piattaforma sottostante e considera tutti i contenuti dei clienti come sensibili e protegge dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: usare RBAC di Azure per gestire l'accesso alle risorse

**Linee guida** : per l'amministrazione del servizio, usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per gestire l'accesso alle chiavi e alla configurazione. Per le operazioni sul contenuto, ad esempio indicizzazione e query, ricerca cognitiva usa le chiavi anziché un modello di controllo degli accessi in base all'identità. Usare il controllo degli accessi in base al ruolo di Azure.
- [Come configurare RBAC in Azure](../role-based-access-control/role-assignments-portal.md) 

 
- [Come usare i ruoli per l'accesso amministrativo ai ricerca cognitiva](./search-security-rbac.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida** : non applicabile a ricerca cognitiva. Questa guida è destinata alle risorse di calcolo. 

Microsoft gestisce l'infrastruttura sottostante per ricerca cognitiva e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida** : ricerca cognitiva crittografa automaticamente il contenuto indicizzato inattivo con le chiavi gestite da Microsoft. Se è necessaria una maggiore protezione, è possibile integrare la crittografia predefinita con un secondo livello di crittografia usando le chiavi create e gestite in Azure Key Vault.

- [Configurare chiavi gestite dal cliente per la crittografia dei dati in Azure ricerca cognitiva](./search-security-manage-encryption-keys.md)

- [Informazioni sulla crittografia inattiva in Azure](../security/fundamentals/encryption-atrest.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Condiviso

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida** : usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle istanze di produzione di ricerca cognitiva e altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/platform/alerts-activity-log.md)

- [Come creare avvisi per le attività ricerca cognitiva](./search-monitor-logs.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee guida** : attualmente non disponibili per ricerca cognitiva.  Per i cluster che archiviano il contenuto del servizio di ricerca, Microsoft è responsabile della gestione delle vulnerabilità di tali cluster.

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Linee guida** : non applicabile a ricerca cognitiva. Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: distribuire una soluzione di gestione delle patch automatizzata per i titoli software di terze parti

**Linee guida** : non applicabile a ricerca cognitiva. Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee guida** : non applicabile a ricerca cognitiva. Microsoft esegue la gestione delle vulnerabilità sui sistemi sottostanti che supportano ricerca cognitiva Services.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Linee guida** : non applicabile a ricerca cognitiva. Per i risultati dell'analisi delle vulnerabilità non è disponibile alcun sistema di valutazione o valutazione dei rischi standard.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida** : usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte, protocolli e così via) nelle sottoscrizioni.  

Verificare le autorizzazioni (lettura) appropriate nel tenant ed enumerare tutte le sottoscrizioni di Azure e le risorse nelle sottoscrizioni.  

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription) 

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee guida** : applicare i tag alle risorse di Azure con i metadati per organizzarli in modo logico in una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni** : usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate laddove appropriato per organizzare e tenere traccia degli asset. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.
- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md) 

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md) 

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md) 

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definire e gestire un inventario delle risorse di Azure approvate

**Linee guida** : definire un elenco di risorse di Azure approvate correlate all'elaborazione dell'indicizzazione e delle competenze in ricerca cognitiva.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida** : è consigliabile definire un inventario delle risorse di Azure approvate per l'utilizzo in base ai criteri e agli standard dell'organizzazione precedenti, quindi monitorare le risorse di Azure non approvate con criteri di Azure o Azure Resource Graph.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md) 

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida** : non applicabile a ricerca cognitiva. Questa guida è destinata alle risorse di calcolo.

Si consiglia di disporre di un inventario delle applicazioni software che sono state ritenute approvate in base ai criteri dell'organizzazione e agli standard di sicurezza e di monitorare eventuali titoli software non approvati installati nelle risorse di calcolo di Azure.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida** : non applicabile a ricerca cognitiva. Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Linee guida** : non applicabile a ricerca cognitiva. Non espone alcuna risorsa di calcolo né consente l'installazione di applicazioni software su una delle relative risorse.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida** : usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query o individuare risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/index.md) 

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

**Linee guida** : non applicabile a ricerca cognitiva. Questa raccomandazione è destinata alle applicazioni in esecuzione sulle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee guida** : per la gestione dei servizi, usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "blocca l'accesso" per l'App "Microsoft Azure Management". 

Controllare l'accesso alle chiavi usate per autenticare le richieste per tutte le altre operazioni, in particolare quelle correlate al contenuto con ricerca cognitiva.

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Linee guida** : non applicabile a ricerca cognitiva. Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida** : non applicabile a ricerca cognitiva. Questa raccomandazione è destinata alle applicazioni Web in esecuzione in app Azure servizio o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida** : usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. search" per creare criteri personalizzati per controllare o applicare la configurazione delle risorse di Azure ricerca cognitiva. È anche possibile usare le definizioni di criteri di Azure predefinite per ricerca cognitiva servizi, ad esempio:

- Abilitare la registrazione di controllo per le risorse di Azure

Azure Resource Manager è in grado di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per assicurarsi che le configurazioni soddisfino i requisiti di sicurezza per l'organizzazione. 

È anche possibile usare le raccomandazioni del Centro sicurezza di Azure come linea di base di configurazione sicura per le risorse di Azure. 

- [Controlli di conformità alle normative di Criteri di Azure per Ricerca cognitiva di Azure](./security-controls-policy.md)

- [Come visualizzare gli alias dei criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Linee guida** : non applicabile a ricerca cognitiva. Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida** : usare i criteri di Azure [deny] e gli effetti [Distribuisci se non esistono] per applicare impostazioni sicure tra le risorse del servizio ricerca cognitiva. 

Azure Resource Manager modelli possono essere usati per gestire la configurazione di sicurezza delle risorse di Azure richieste dall'organizzazione. 

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

- [Controlli di conformità alle normative di Criteri di Azure per Ricerca cognitiva di Azure](./security-controls-policy.md)

- [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Panoramica sui modelli di Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Linee guida** : non applicabile a ricerca cognitiva. Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida** : se si usano definizioni di criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire il codice in modo sicuro.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentazione di Azure Repos](/azure/devops/repos/index)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Linee guida** : non applicabile a ricerca cognitiva. Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida** : definire e implementare configurazioni di sicurezza standard per le risorse del servizio ricerca cognitiva usando criteri di Azure. 

Usare gli alias per creare criteri personalizzati per controllare o applicare le configurazioni di rete. È anche possibile usare le definizioni di criteri predefinite correlate alle risorse specifiche. 

Inoltre, è possibile usare automazione di Azure per distribuire le modifiche di configurazione e gestire le eccezioni dei criteri. 

- [Controlli di conformità alle normative di Criteri di Azure per Ricerca cognitiva di Azure](./security-controls-policy.md)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Linee guida** : non applicabile a ricerca cognitiva. Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida** : usare il Centro sicurezza per eseguire analisi di base delle risorse del servizio ricerca cognitiva.  Usare inoltre i criteri di Azure per inviare avvisi e controllare le configurazioni delle risorse. 

- [Come correggere le raccomandazioni nel centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

- [Controlli di conformità alle normative di Criteri di Azure per Ricerca cognitiva di Azure](./security-controls-policy.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida** : non applicabile a ricerca cognitiva. Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida** : usare identità gestite di Azure insieme a Azure Key Vault per semplificare la gestione dei segreti per le applicazioni cloud.
- [Come usare le identità gestite per le risorse di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 
- [Come creare una Key Vault](../key-vault/secrets/quick-create-portal.md) 

- [Come fornire l'autenticazione Key Vault con un'identità gestita](../key-vault/general/assign-access-policy-portal.md) 

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida** : usare un'identità gestita di Azure per concedere a ricerca cognitiva l'accesso ad altri servizi di Azure, come Key Vault e le origini dati dell'indicizzatore, usando un'identità gestita automaticamente in Azure Active Directory (Azure ad). Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Azure Key Vault, senza credenziali nel codice. 

- [Configurare una connessione dell'indicizzatore a un'origine dati usando un'identità gestita](./search-howto-managed-identities-data-sources.md)

- [Configurare chiavi gestite dal cliente per la crittografia dei dati usando un'identità gestita](./search-security-manage-encryption-keys.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Linee guida** : non applicabile a ricerca cognitiva. Non ospita il codice e non dispone di credenziali da identificare.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: usare il software antimalware gestito centralmente

**Linee guida** : non applicabile a ricerca cognitiva. Questa raccomandazione è destinata alle risorse di calcolo.

Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio ricerca cognitiva di Azure, ma non viene eseguito sui contenuti del cliente.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida** : pre-analizzare i contenuti caricati in risorse di Azure non di calcolo, ad esempio ricerca cognitiva, archiviazione BLOB, database SQL di Azure e così via. 

È responsabilità dell'utente eseguire la pre-analisi di tutti i contenuti caricati in risorse di Azure non di calcolo. Microsoft non può accedere ai dati dei clienti e pertanto non può eseguire analisi antimalware dei contenuti del cliente per conto dell'utente.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: assicurarsi che il software e le firme antimalware siano aggiornati

**Linee guida** : non applicabile a ricerca cognitiva. Non consente l'installazione di soluzioni anti-malware sulle risorse. Per la piattaforma sottostante Microsoft gestisce l'aggiornamento di qualsiasi software e firma anti-malware. 

Per tutte le risorse di calcolo di proprietà dell'organizzazione e usate nella soluzione di ricerca, seguire le raccomandazioni nel centro sicurezza, &amp; le app di calcolo per assicurarsi che tutti gli endpoint siano aggiornati con le firme più recenti. Per Linux, usare una soluzione antimalware di terze parti.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Indicazioni** : non è possibile eseguire il backup del contenuto archiviato in un servizio di ricerca tramite backup di Azure o qualsiasi altro meccanismo incorporato, ma è possibile ricompilare un indice dal codice sorgente dell'applicazione e dalle origini dati primarie oppure creare uno strumento personalizzato per recuperare e archiviare il contenuto indicizzato.

- [Esempio di indice GitHub-backup-restore](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni** : ricerca cognitiva attualmente non supporta il backup automatico dei dati in un servizio di ricerca ed è necessario eseguirne il backup tramite un processo manuale.  È anche possibile eseguire il backup delle chiavi gestite dal cliente in Azure Key Vault. 

- [Eseguire il backup e il ripristino di un indice di ricerca cognitiva di Azure](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Come eseguire il backup di chiavi di Key Vault in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni** : ricerca cognitiva attualmente non supporta il backup automatico dei dati in un servizio di ricerca ed è necessario eseguirne il backup e il ripristino tramite un processo manuale.  Eseguire periodicamente il ripristino dei dati del contenuto di cui è stato eseguito il backup manualmente per garantire l'integrità end-to-end del processo di backup.

- [Eseguire il backup e il ripristino di un indice di ricerca cognitiva di Azure](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Come ripristinare chiavi di Key Vault in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni** : ricerca cognitiva attualmente non supporta il backup automatico dei dati in un servizio di ricerca ed è necessario eseguirne il backup tramite un processo manuale.  È anche possibile eseguire il backup delle chiavi gestite dal cliente in Azure Key Vault. 

Abilitare l'eliminazione temporanea e ripulire la protezione in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose. Se si usa archiviazione di Azure per archiviare i backup manuali, abilitare l'eliminazione temporanea per salvare e ripristinare i dati quando vengono eliminati BLOB o snapshot BLOB. 

- [Eseguire il backup e il ripristino di un indice di ricerca cognitiva di Azure](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Come abilitare l'eliminazione temporanea e ripulire la protezione in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Eliminazione temporanea per archiviazione BLOB di Azure](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Guida** : sviluppare una guida alla risposta agli eventi imprevisti per la propria organizzazione. Assicurarsi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi della gestione e della gestione degli eventi imprevisti dal rilevamento alla revisione post-evento imprevisto.

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni** : il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. La gravità è basata sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'utilizzo analitico per emettere l'avviso, oltre al livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso.

Inoltre, contrassegnare le sottoscrizioni usando i tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili. È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni** : con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Vedere la pubblicazione del NIST, la guida ai programmi di test, formazione e esercizio per i piani IT e le funzionalità.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni** : le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida** : esportare gli avvisi e le raccomandazioni del Centro sicurezza usando la funzionalità di esportazione continua. L'esportazione continua consente di esportare avvisi e consigli manualmente o su base continua. È possibile usare il connettore dati del Centro sicurezza per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni** : usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida** : seguire le regole di test di penetrazione Microsoft Cloud di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.
- [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)