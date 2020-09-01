---
title: Baseline della sicurezza di Azure per le istanze di contenitore
description: Baseline della sicurezza di Azure per le istanze di contenitore
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 87421509326ef8837a9787ed01a70915c7c5aafa
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230739"
---
# <a name="azure-security-baseline-for-container-instances"></a>Baseline della sicurezza di Azure per le istanze di contenitore

La linea di base di sicurezza di Azure per le istanze di contenitore contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](../security/benchmarks/overview.md), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere la [Panoramica delle baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Controllo di sicurezza: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

**Linee guida**: rete virtuale di Azure offre una rete sicura e privata per le risorse di Azure e locali. Integrare i gruppi di contenitori in istanze di contenitore di Azure con una rete virtuale di Azure. 

* [Scenari e risorse della rete virtuale-istanze di contenitore di Azure](./container-instances-virtual-network-concepts.md)

* [Distribuire le istanze di contenitore in una rete virtuale di Azure](./container-instances-vnet.md)

* [Come creare una NSG con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

* [Come distribuire e configurare il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Linee guida**: usare il Centro sicurezza di Azure e correggere i consigli sulla protezione della rete per proteggere le risorse di rete in Azure. Abilitare i log dei flussi NSG e inviare i log a un account di archiviazione per il controllo del traffico.

* [Come abilitare i log di flusso NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Proteggere le risorse di rete](../security-center/security-center-network-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee guida**: distribuire il Web Application Firewall (WAF) di Azure davanti alle applicazioni Web critiche ospitate in istanze di contenitore di Azure per un'ulteriore ispezione del traffico in ingresso. Abilitare l'impostazione di diagnostica per WAF e inserire i log in un account di archiviazione, un hub eventi o un'area di lavoro Log Analytics.

* [Come distribuire Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare la protezione standard DDoS nelle reti virtuali di Azure per proteggersi da attacchi DDoS. Usare il Centro sicurezza di Azure Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP dannosi noti. Distribuire il firewall di Azure in ogni limite di rete dell'organizzazione con l'Intelligence per le minacce abilitata e configurata per "avviso e negazione" per il traffico di rete dannoso. Usare il Centro sicurezza di Azure accesso alla rete just-in-time per configurare gruppi per limitare l'esposizione degli endpoint agli indirizzi IP approvati per un periodo di tempo limitato. Usare il Centro sicurezza di Azure Adaptive Network hardening per consigliare configurazioni NSG che limitano le porte e gli indirizzi IP di origine in base al traffico effettivo e all'Intelligence per le minacce. 

* [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](../security-center/threat-protection.md)

* [Informazioni sul centro sicurezza di Azure Adaptive Network hardening](../security-center/security-center-adaptive-network-hardening.md)

* [Controllo di accesso di rete just-in-Time del Centro sicurezza di Azure](../security-center/security-center-just-in-time.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: registrare i pacchetti di rete e i log dei flussi

**Linee guida**: se si usano i gruppi di sicurezza di rete (gruppi) con l'implementazione della rete virtuale, abilitare i log dei flussi NSG per il NSG collegato alla subnet delegata alle istanze di contenitore di Azure. Registrare i log dei flussi di NSG in un account di archiviazione di Azure per generare record di flusso. Se necessario per l'analisi dell'attività anomala, abilitare l'acquisizione di pacchetti di Azure Network Watcher.

* [Come abilitare i log di flusso NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: distribuire sistemi di rilevamento intrusioni/prevenzione intrusioni (IDS/IPS) basati sulla rete

**Linee guida**: selezionare un'offerta da Azure Marketplace che supporta la funzionalità di ID/IP con funzionalità di ispezione del payload. Se il rilevamento e/o la prevenzione di intrusioni in base all'ispezione del payload non costituisce un requisito, è possibile usare Firewall di Azure con intelligence sulle minacce. I filtri basati sull'intelligence sulle minacce del Firewall di Azure possono creare avvisi e rifiutare il traffico da o verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

Distribuire la soluzione firewall scelta a ogni limite di rete dell'organizzazione per rilevare e/o negare il traffico dannoso.

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Come configurare gli avvisi con il firewall di Azure](../firewall/threat-intel.md)

* [Distribuire in una rete virtuale-istanze di contenitore di Azure](./container-instances-vnet.md) 



**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee guida**: distribuire applicazione Azure gateway per le applicazioni Web con HTTPS/SSL abilitato per i certificati attendibili.

* [Come distribuire il gateway applicazione](../application-gateway/quick-create-portal.md)

* [Come configurare il gateway applicazione per l'uso di HTTPS](../application-gateway/create-ssl-portal.md) 

* [Informazioni sul bilanciamento del carico di livello 7 con i gateway applicazione Web di Azure](../application-gateway/overview.md)

* [Esporre un indirizzo IP statico per un gruppo di contenitori](./container-instances-application-gateway.md)

* [Distribuire in una rete virtuale-istanze di contenitore di Azure](./container-instances-vnet.md)



**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi. 

È anche possibile usare i gruppi di sicurezza delle applicazioni per semplificare la configurazione della sicurezza complessa. I gruppi di sicurezza delle applicazioni consentono di configurare la sicurezza di rete come un'estensione naturale della struttura di un'applicazione, raggruppando le macchine virtuali e definendo i criteri di sicurezza di rete in base a tali gruppi. 

* [Comprendere e usare i tag di servizio](../virtual-network/service-tags-overview.md) 

* [Comprendere e usare i gruppi di sicurezza delle applicazioni](../virtual-network/security-overview.md#application-security-groups)

* [Distribuire in una rete virtuale-istanze di contenitore di Azure](./container-instances-vnet.md)



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: è anche possibile usare i progetti di Azure per semplificare le distribuzioni di Azure su larga scala creando pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controlli RBAC di Azure e criteri, in una singola definizione di progetto. È possibile applicare il progetto a nuove sottoscrizioni e ottimizzare il controllo e la gestione tramite il controllo delle versioni. 

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Esempi di criteri di Azure per la rete](/azure/governance/policy/samples/#network)

* [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni**: usare i tag per gruppi e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le regole dei singoli gruppi di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

* [Distribuire in una rete virtuale-istanze di contenitore di Azure](./container-instances-vnet.md)

* [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze di contenitore. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

* [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: Registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Usare origini di sincronizzazione ora approvate

**Indicazioni**: Microsoft gestisce le origini temporali per le risorse di Azure, ma è possibile gestire autonomamente le impostazioni di sincronizzazione dell'ora per le risorse di calcolo. Ad esempio, eseguire un comando di sincronizzazione dell'ora in un contenitore in esecuzione.

* [Come configurare la sincronizzazione dell'ora per le risorse di calcolo di Azure](../virtual-machines/windows/time-sync.md)

* [Eseguire un comando in un'istanza di contenitore di Azure in esecuzione](./container-instances-exec.md)



**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati da un gruppo di contenitori di Azure. In Monitoraggio di Azure usare una o più aree di lavoro Log Analytics per eseguire query ed effettuare analisi, quindi usare gli account di archiviazione di Azure per l'archiviazione/memorizzazione a lungo termine.

* [Registrazione di istanze e gruppi di contenitori con i log di monitoraggio di Azure](./container-instances-log-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: monitoraggio di Azure raccoglie i log delle risorse (in precedenza denominati log di diagnostica) per gli eventi guidati dall'utente nel registro. Istanze di contenitore di Azure include il supporto incorporato per l'invio di log del gruppo di contenitori, dati degli eventi e log del contenitore ai log di monitoraggio di Azure.

* [Registrazione di istanze e gruppi di contenitori con i log di monitoraggio di Azure](../container-registry/container-registry-diagnostics-audit-logs.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Indicazioni**: Non applicabile. Questa guida è destinata alle risorse di calcolo IaaS.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni**: In monitoraggio di Azure, impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione/memorizzazione a lungo termine.

* [Come impostare i parametri di conservazione dei log per le aree di lavoro Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida**: analizzare e monitorare i log per un comportamento anomalo ed esaminare periodicamente i risultati. Usare l'area di lavoro Log Analytics di monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log. 

* [Registrazione di istanze e gruppi di contenitori con i log di monitoraggio di Azure](./container-instances-log-analytics.md)

* [Informazioni sull'area di lavoro Log Analytics](../azure-monitor/log-query/get-started-portal.md)

* [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: abilitare gli avvisi per le attività anomale

**Indicazioni**: usare log Analytics area di lavoro per il monitoraggio e l'invio di avvisi su attività anomale rilevate in registri di sicurezza ed eventi. 

* [Registrazione di istanze e gruppi di contenitori con i log di monitoraggio di Azure](./container-instances-log-analytics.md)

* [Come inviare un avviso sui dati del log di log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida**: fornire la propria soluzione antimalware e la raccolta di eventi se necessario per l'esecuzione in un contenitore. 


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida**: fornire una soluzione personalizzata, se necessario, per eseguire query sui log DNS in un contenitore.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: se necessario, configurare la registrazione della console in un'istanza del contenitore in esecuzione.

* [Eseguire un comando in un'istanza di contenitore di Azure in esecuzione](./container-instances-exec.md)



**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida**: Azure Active Directory (Azure ad) include ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

Se si usa un registro contenitori di Azure con istanze di contenitore di Azure, per ogni registro contenitori di Azure verificare se l'account amministratore predefinito è abilitato o disabilitato. Disabilitare l'account quando non è in uso.

* [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Account amministratore di Azure Container Registry](../container-registry/container-registry-authentication.md#admin-account)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: Azure Active Directory (Azure ad) non ha il concetto di password predefinite. Altre risorse di Azure che richiedono una password forzano la creazione di una password con requisiti di complessità e una lunghezza minima della password, che variano a seconda del servizio. L'utente è responsabile per le applicazioni di terze parti e per i servizi del Marketplace che possono usare password predefinite.

Se si usa un registro contenitori di Azure con istanze di contenitore di Azure, se l'account amministratore predefinito di un registro contenitori di Azure è abilitato, le password complesse vengono create automaticamente e devono essere ruotate. Disabilitare l'account quando non è in uso.

* [Account amministratore di Azure Container Registry](../container-registry/container-registry-authentication.md#admin-account)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Se si usa un registro contenitori di Azure con istanze di contenitore di Azure, creare procedure per abilitare l'account amministratore predefinito di un registro contenitori. Disabilitare l'account quando non è in uso.

* [Informazioni sull'identità e sull'accesso del Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

* [Account amministratore di Azure Container Registry](../container-registry/container-registry-authentication.md#admin-account)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida**: laddove possibile, usare Azure Active Directory SSO anziché configurare singole credenziali autonome per servizio. Usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

* [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Linee guida**: Azure Active Directory abilitare l'autenticazione a più fattori (Azure ad) e seguire le indicazioni sulla gestione delle identità e degli accessi nel centro sicurezza di Azure.

* [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni**: usare workstation con accesso con privilegi insieme a Multi-Factor Authentication (MFA) configurato per l'accesso e la configurazione delle risorse di Azure.

* [Informazioni sulle workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: registrare e inviare avvisi per le attività sospette dagli account amministrativi

**Linee guida**: usare i report di sicurezza Azure Active Directory (Azure ad) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

* [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

* [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare le località denominate di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

* [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

* [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure Active Directory (Azure ad) fornisce i log per individuare gli account obsoleti. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

* [Informazioni sulla creazione di report Azure AD](../active-directory/reports-monitoring/index.yml)

* [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: monitorare i tentativi di accesso agli account disattivati

**Linee guida**: è possibile accedere alle origini dei log eventi di attività di accesso, controllo e rischio di Azure Active Directory (Azure ad), che consentono di eseguire l'integrazione con qualsiasi strumento/Monitoring di gestione delle informazioni e degli eventi di sicurezza.

È possibile semplificare questo processo creando impostazioni di diagnostica per gli account utente di Azure Active Directory e inviando i log di controllo e di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro di Log Analytics.

* [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Linee guida**: usare Azure Active Directory (Azure ad) funzionalità di protezione delle identità e di rischio per configurare risposte automatiche per le azioni sospette rilevate correlate alle identità utente.

* [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: non attualmente disponibili; Customer Lockbox non è attualmente supportata per le istanze di contenitore di Azure.

* [Elenco dei servizi Customer Lockbox supportati](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag delle risorse per semplificare il rilevamento di istanze di contenitore di Azure che archiviano o elaborano informazioni riservate. 

Immagini del contenitore di tag e versioni, per semplificare il rilevamento di immagini che archiviano o elaborano informazioni riservate.

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

* [Suggerimenti per l'assegnazione di tag e il controllo delle versioni delle immagini del contenitore](../container-registry/container-registry-image-tag-version.md)


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida**: implementare sottoscrizioni separate e/o gruppi di gestione per lo sviluppo, il test e la produzione. Le risorse devono essere separate da VNet/subnet, contrassegnate in modo appropriato e protette da un NSG o da un firewall di Azure. Le risorse che archiviano o elaborano dati sensibili devono essere sufficientemente isolate.

* [Eseguire un comando in un'istanza di contenitore di Azure in esecuzione](./container-instances-exec.md)

* [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

* [Come creare gruppi di gestione](../governance/management-groups/create.md)

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

* [Distribuire in una rete virtuale-istanze di contenitore di Azure](./container-instances-vnet.md) 
* [Come creare una NSG con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

* [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Come configurare avvisi o avvisi e negare con il firewall di Azure](../firewall/threat-intel.md)


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: distribuire uno strumento automatizzato sui perimetri di rete che monitora il trasferimento non autorizzato di informazioni riservate e blocca tali trasferimenti mentre invia avvisi ai professionisti della sicurezza delle informazioni. Monitorare e bloccare il trasferimento di informazioni non autorizzate da condivisioni file di Azure e altri volumi montati in istanze di contenitore.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

* [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

* [Distribuire in una rete virtuale-istanze di contenitore di Azure](./container-instances-vnet.md) 

* [Mount an Azure file share in Azure Container Instances](./container-instances-volume-azure-files.md) (Montare una condivisione file di Azure in Istanze di Azure Container)


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: assicurarsi che tutti i client che si connettono ai gruppi di contenitori di Azure siano in grado di negoziare TLS 1,2 o versione successiva. Microsoft Azure risorse negoziano TLS 1,2 per impostazione predefinita.

Seguire le raccomandazioni del Centro sicurezza di Azure per la crittografia dei dati inattivi e la crittografia in transito, ove applicabile.

* [Informazioni sulla crittografia in transito con Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni**: le funzionalità di identificazione dei dati, classificazione e prevenzione della perdita non sono attualmente disponibili per le istanze di contenitore di Azure. Contrassegnare i gruppi di contenitori che potrebbero elaborare informazioni riservate come tali e implementare una soluzione di terze parti, se necessario ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

* [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso ai dati e alle risorse delle istanze di contenitore di Azure. 

* [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo IaaS.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

* [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: per impostazione predefinita, tutti i dati di distribuzione nelle istanze di contenitore di Azure vengono crittografati a riposo usando chiavi gestite da Microsoft. Facoltativamente, gestire la crittografia con una chiave personalizzata (chiave gestita dal cliente).

* [Informazioni sulla crittografia inattiva in Azure](../security/fundamentals/encryption-atrest.md)

* [Crittografare i dati di distribuzione con istanze di contenitore di Azure](./container-instances-encrypt-data.md)



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate ai gruppi di contenitori e alle istanze di contenitore. 

* [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/platform/alerts-activity-log.md)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [Controllo di sicurezza: gestione della vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee guida**: implementare soluzioni per analizzare le immagini del contenitore in un registro privato e identificare le potenziali vulnerabilità. Seguire le raccomandazioni del Centro sicurezza di Azure per l'esecuzione di valutazioni delle vulnerabilità sulle immagini del contenitore archiviate in Azure Container Registry. Facoltativamente, è possibile distribuire soluzioni di terze parti da Azure Marketplace per eseguire valutazioni delle vulnerabilità delle immagini.

* [Considerazioni sulla sicurezza per le istanze di contenitore di Azure](./container-instances-image-security.md)

* [Integrazione del Container Registry di Azure con il Centro sicurezza](../security-center/azure-container-registry-integration.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Linee guida**: Microsoft esegue la gestione delle patch sui sistemi sottostanti che supportano i contenitori in esecuzione.

Usare una soluzione personalizzata o di terze parti per applicare patch alle immagini del contenitore. Se si archiviano le immagini del contenitore in Azure Container Registry, eseguire le attività di Azure Container Registry per automatizzare gli aggiornamenti alle immagini dell'applicazione in un registro contenitori in base alle patch di sicurezza o ad altri aggiornamenti nelle immagini del sistema operativo di base.

* [Considerazioni sulla sicurezza per le istanze di contenitore di Azure](./container-instances-image-security.md)

* [Informazioni sugli aggiornamenti delle immagini di base per le attività Container Registry di Azure](../container-registry/container-registry-tasks-base-images.md)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: distribuire una soluzione di gestione delle patch automatizzata per il software di terze parti

**Linee guida**: usare una soluzione personalizzata o di terze parti per applicare patch alle immagini del contenitore. Se si archiviano le immagini del contenitore in Azure Container Registry, eseguire le attività di Azure Container Registry per automatizzare gli aggiornamenti alle immagini dell'applicazione in un registro contenitori in base alle patch di sicurezza o ad altri aggiornamenti nelle immagini del sistema operativo di base.

* [Considerazioni sulla sicurezza per le istanze di contenitore di Azure](./container-instances-image-security.md)

* [Informazioni sugli aggiornamenti delle immagini di base per le attività ACR](../container-registry/container-registry-tasks-base-images.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee guida**: esportare i risultati dell'analisi delle immagini a intervalli coerenti e confrontare i risultati per verificare che le vulnerabilità siano state corrette. Se si archiviano le immagini del contenitore in Azure Container Registry, integrare il registro di sistema con il Centro sicurezza di Azure per abilitare l'analisi periodica delle immagini del contenitore per le vulnerabilità. Facoltativamente, è possibile distribuire soluzioni di terze parti da Azure Marketplace per eseguire analisi periodiche delle vulnerabilità delle immagini.

* [Considerazioni sulla sicurezza per le istanze di contenitore di Azure](./container-instances-image-security.md)

* [Integrazione del Container Registry di Azure con il Centro sicurezza](../security-center/azure-container-registry-integration.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Linee guida**: se si archiviano le immagini del contenitore in Azure container Registry, integrare il registro di sistema con il Centro sicurezza di Azure per abilitare l'analisi periodica delle immagini del contenitore per le vulnerabilità e per classificare i rischi. Facoltativamente, Distribuisci soluzioni di terze parti da Azure Marketplace per eseguire analisi periodiche delle vulnerabilità delle immagini e classificazione dei rischi.

* [Considerazioni sulla sicurezza per le istanze di contenitore di Azure](./container-instances-image-security.md)

* [Integrazione del Container Registry di Azure con il Centro sicurezza](../security-center/azure-container-registry-integration.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: usare l'individuazione di asset in Azure

**Indicazioni**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) disponibili nelle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

* [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee guida**: applicare tag alle istanze di contenitore di Azure e alle risorse correlate fornendo metadati per organizzarle in modo logico in una tassonomia.

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia degli asset. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

* [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

* [Come creare gruppi di gestione](../governance/management-groups/create.md)

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: mantenere un inventario delle risorse di Azure approvate e dei titoli software

**Linee guida**: è necessario creare un inventario delle risorse di Azure approvate in base alle esigenze dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

* [Controllare la conformità dei registri contenitori di Azure con criteri di Azure](../container-registry/container-registry-azure-policy.md)

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: implementare una soluzione personalizzata o una soluzione di terze parti per eseguire l'inventario del software per le applicazioni incluse in contenitori approvate. 

Implementare soluzioni per analizzare le immagini del contenitore in un registro privato e identificare le potenziali vulnerabilità. Seguire le raccomandazioni del Centro sicurezza di Azure per eseguire valutazioni delle vulnerabilità sulle immagini del contenitore archiviate in Azure Container Registry. Facoltativamente, è possibile distribuire soluzioni di terze parti da Azure Marketplace per eseguire valutazioni delle vulnerabilità delle immagini.

Monitorare i log di istanze di contenitore di Azure per un comportamento anomalo ed esaminare periodicamente i risultati. Usare l'area di lavoro Log Analytics di monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log.

* [Registrazione di istanze e gruppi di contenitori con i log di monitoraggio di Azure](./container-instances-log-analytics.md)

* [Informazioni sull'area di lavoro Log Analytics](../azure-monitor/log-query/get-started-portal.md)

* [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md)

* [Considerazioni sulla sicurezza per le istanze di contenitore di Azure](./container-instances-image-security.md)
* [Integrazione del Container Registry di Azure con il Centro sicurezza](../security-center/azure-container-registry-integration.md)



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: automazione di Azure offre il controllo completo durante la distribuzione, le operazioni e la rimozione delle autorizzazioni di carichi di lavoro e risorse. È possibile implementare una soluzione personalizzata per rimuovere le risorse di Azure e le applicazioni software non autorizzate.

* [Introduzione ad Automazione di Azure](../automation/automation-intro.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Linee guida**: immagini del contenitore di tag e versioni, per semplificare il rilevamento di immagini che eseguono applicazioni approvate.
* [Suggerimenti per l'assegnazione di tag e il controllo delle versioni delle immagini del contenitore](../container-registry/container-registry-image-tag-version.md)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: usare criteri di Azure per limitare i servizi di cui è possibile eseguire il provisioning nell'ambiente.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Come negare un tipo di risorsa specifico con Criteri di Azure](/azure/governance/policy/samples/not-allowed-resource-types)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="610-implement-approved-application-list"></a>6.10: implementare l'elenco di applicazioni approvate

**Linee guida**: immagini del contenitore di tag e versioni, per semplificare il rilevamento di immagini che eseguono applicazioni approvate.
* [Suggerimenti per l'assegnazione di tag e il controllo delle versioni delle immagini del contenitore](../container-registry/container-registry-image-tag-version.md)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsbrdiv"></a>6.11: <div>Limitare la capacità degli utenti di interagire con Azure Resource Manager tramite script<br></div>

**Linee guida**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con gestione risorse di Azure configurando "blocca l'accesso" per l'app "gestione Microsoft Azure". 

* [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Linee guida**: tutti gli utenti con accesso alle istanze di contenitore di Azure possono eseguire script all'interno di contenitori.

Gestire ed esaminare l'accesso alle risorse di istanze di contenitore di Azure con diverse sottoscrizioni o gruppi di gestione di Azure oppure isolare le risorse usando reti virtuali e gruppi o il firewall di Azure.

* [Eseguire un comando in un'istanza di contenitore di Azure in esecuzione](./container-instances-exec.md)

* [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

* [Come creare gruppi di gestione](../governance/management-groups/create.md)

* [Distribuire in una rete virtuale-istanze di contenitore di Azure](./container-instances-vnet.md)

* [Come creare una NSG con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

* [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: il software necessario per le operazioni aziendali, ma può comportare un rischio maggiore per l'organizzazione, deve essere isolato all'interno della propria rete virtuale e sufficientemente protetto con un firewall di Azure o un gruppo di sicurezza di rete.

* [Distribuire in una rete virtuale-istanze di contenitore di Azure](./container-instances-vnet.md) 

* [Come creare una NSG con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Controllo di sicurezza: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: mantenere una configurazione del gruppo di contenitori approvata usando un modello di Azure Resource Manager o esportando in un file YAML. Usare i criteri di Azure per gestire le configurazioni di sicurezza per le risorse di Azure correlate.

* [Gruppi di contenitori in Istanze di Azure Container](container-instances-container-groups.md#deployment)

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Linee guida**: usare una soluzione personalizzata o di terze parti per applicare patch alle immagini del contenitore. Se si archiviano le immagini del contenitore in Azure Container Registry, eseguire le attività di Azure Container Registry per automatizzare gli aggiornamenti alle immagini dell'applicazione in un registro contenitori in base alle patch di sicurezza o ad altri aggiornamenti nelle immagini del sistema operativo di base. 

* [Informazioni sugli aggiornamenti delle immagini di base per le attività Container Registry di Azure](../container-registry/container-registry-tasks-base-images.md)



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure nelle risorse di Azure.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Informazioni sugli effetti dei criteri di Azure](../governance/policy/concepts/effects.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Linee guida**: implementare soluzioni per analizzare le immagini del contenitore in un registro privato e identificare le potenziali vulnerabilità nelle configurazioni del sistema operativo. Seguire le raccomandazioni del Centro sicurezza di Azure per eseguire valutazioni delle vulnerabilità sulle immagini del contenitore archiviate in Azure Container Registry. Facoltativamente, è possibile distribuire soluzioni di terze parti da Azure Marketplace per eseguire valutazioni delle vulnerabilità delle immagini.

Usare una soluzione personalizzata o di terze parti per applicare patch alle immagini del contenitore. Se si archiviano le immagini del contenitore in Azure Container Registry, eseguire le attività di Azure Container Registry per automatizzare gli aggiornamenti alle immagini dell'applicazione in un registro contenitori in base alle patch di sicurezza o ad altri aggiornamenti nelle immagini del sistema operativo di base. 

* [Suggerimenti sulla sicurezza per il monitoraggio e l'analisi dei contenitori per istanze di contenitore di Azure](./container-instances-image-security.md)

* [Integrazione del Container Registry di Azure con il Centro sicurezza](../security-center/azure-container-registry-integration.md)
* [Informazioni sugli aggiornamenti delle immagini di base per le attività Container Registry di Azure](../container-registry/container-registry-tasks-base-images.md)



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: archiviare e gestire i modelli ARM, i file YAML e le definizioni di criteri personalizzati di Azure in modo sicuro nel controllo del codice sorgente.

* [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentazione di Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Linee guida**: archiviare le immagini del contenitore in Azure container Registry e sfruttare il controllo degli accessi in base al ruolo di Azure per garantire che solo gli utenti autorizzati possano accedere

* [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Informazioni su RBAC di Azure per Container Registry](../container-registry/container-registry-roles.md)

* [Come configurare RBAC di Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: distribuire strumenti di gestione della configurazione di sistema

**Linee guida**: usare criteri di Azure per inviare avvisi, controllare e applicare configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo IaaS.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

**Linee guida**: usare il Centro sicurezza di Azure per eseguire analisi di base per le risorse di Azure.

Usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

* [Come correggere le raccomandazioni nel centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

* [Controllare la conformità dei registri contenitori di Azure con criteri di Azure](../container-registry/container-registry-azure-policy.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: se si usa container Registry di Azure per archiviare le immagini del contenitore, usare il Centro sicurezza di Azure per eseguire analisi di base per le impostazioni del sistema operativo e Docker per i contenitori.

* [Informazioni sulle raccomandazioni per i contenitori nel Centro sicurezza di Azure](../security-center/container-security.md)


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud.

* [Come eseguire l'integrazione con le identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Come creare una Key Vault](../key-vault/secrets/quick-create-portal.md)

* [Come fornire l'autenticazione Key Vault con un'identità gestita](../key-vault/general/managed-identity.md)

* [Come usare identità gestite con Istanze di Azure Container](./container-instances-managed-identity.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure ad. Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Key Vault, senza credenziali nel codice.

* [Come configurare le identità gestite](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Come usare identità gestite con Istanze di Azure Container](./container-instances-managed-identity.md)



**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

* [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Controllo di sicurezza: difesa da malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: usare software antimalware gestito in modo centralizzato

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo IaaS.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft antimalware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio istanze di contenitore di Azure, ma non viene eseguito sui contenuti del cliente.

Pre-analizza tutti i file caricati in risorse di Azure non di calcolo, ad esempio servizio app, Data Lake Storage, archiviazione BLOB e così via. 


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: verificare che le firme e il software antimalware siano aggiornati

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo IaaS.

Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio istanze di contenitore di Azure, ma non viene eseguito sui contenuti del cliente.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [Controllo di sicurezza: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Linee guida**: abilitare backup di Azure e configurare l'origine di backup, ad esempio una condivisione file montata nei gruppi di contenitori, nonché la frequenza e il periodo di memorizzazione desiderati. 

* [Come abilitare backup di Azure](../backup/index.yml)

* [Mount an Azure file share in Azure Container Instances](./container-instances-volume-azure-files.md) (Montare una condivisione file di Azure in Istanze di Azure Container)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: eseguire backup completi del sistema e il backup di tutte le chiavi gestite dal cliente

**Linee guida**: eseguire il backup delle chiavi gestite dal cliente in Azure Key Vault usando gli SDK o gli strumenti da riga di comando di Azure.

Facoltativamente, eseguire il backup delle immagini del contenitore importando da un registro a un altro.
* [Come eseguire il backup di chiavi di Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Importare immagini del contenitore in un registro contenitori](../container-registry/container-registry-import-images.md)



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni**: ripristino di test delle chiavi gestite dal cliente sottoposte a backup in Azure Key Vault tramite gli SDK o gli strumenti da riga di comando di Azure.

* [Come ripristinare chiavi di Azure Key Vault in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Crittografare i dati di distribuzione-istanze di contenitore di Azure](./container-instances-encrypt-data.md)



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: è possibile abilitare l'eliminazione temporanea in Azure Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.

* [Come abilitare l'eliminazione temporanea in Key Vault](../storage/blobs/soft-delete-overview.md?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti.

* [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

* [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Guida alla gestione degli eventi imprevisti di sicurezza dei computer di NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Linee guida**: il Centro sicurezza di Azure assegna una gravità a ogni avviso per facilitare la priorità degli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

Inoltre, contrassegnare chiaramente le sottoscrizioni, ad esempio. produzione, non produzione) e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

* [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

* [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi in Sentinel.

* [Come configurare l'esportazione continua](../security-center/continuous-export.md)

* [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

* [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Controllo di sicurezza: test di penetrazione ed esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: eseguire con regolarità test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici in termini di sicurezza entro 60 giorni

**Linee guida**: seguire le regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.

* [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)
