---
title: Baseline della sicurezza di Azure per il bus di servizio
description: La linea di base di sicurezza del bus di servizio fornisce linee guida procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: service-bus-messaging
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a6d4e7f4ca7288b36f6801b9ddb362b7e936862d
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843622"
---
# <a name="azure-security-baseline-for-service-bus"></a>Baseline della sicurezza di Azure per il bus di servizio

La linea di base di sicurezza di Azure per il bus di servizio contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione. La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](../security/benchmarks/overview-v1.md), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft. Per altre informazioni, vedere [Panoramica delle baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md).

Per informazioni sul mapping completo del bus di servizio al benchmark di sicurezza di Azure, vedere il [file di mapping della linea di base di sicurezza del bus di servizio completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali 

**Linee guida**: l'integrazione del bus di servizio con il servizio di collegamento privato di Azure consente l'accesso privato sicuro alle funzionalità di messaggistica da carichi di lavoro, ad esempio macchine virtuali, associate a reti virtuali. Creare una connessione di endpoint privato allo spazio dei nomi del bus di servizio. L'endpoint privato usa un indirizzo IP privato della rete virtuale, portando il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite tale endpoint privato, in modo che non siano necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN o indirizzi IP pubblici.

È anche possibile proteggere lo spazio dei nomi del bus di servizio di Azure usando i firewall. Il bus di servizio di Azure supporta i controlli di accesso basato su IP per il supporto del firewall in ingresso. È possibile impostare le regole del firewall usando il portale di Azure, Azure Resource Manager modelli o tramite l'interfaccia della riga di comando di Azure o Azure PowerShell.

- [Consentire l'accesso agli spazi dei nomi del bus di servizio di Azure tramite endpoint privati](private-link-service.md)

- [Consentire l'accesso allo spazio dei nomi del bus di servizio di Azure da intervalli o indirizzi IP specifici](service-bus-ip-filtering.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee guida**: se si usano macchine virtuali di Azure per accedere alle entità del bus di servizio, abilitare i log di flusso del gruppo di sicurezza di rete (NSG) e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate. 

Usare il Centro sicurezza di Azure e seguire le raccomandazioni per la protezione della rete per proteggere le risorse del bus di servizio in Azure.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare protezione DDoS standard nelle reti virtuali associate agli spazi dei nomi del bus di servizio per la protezione da attacchi di tipo Denial of Service (DDoS) distribuiti. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

- [Come configurare la protezione DDoS](../virtual-network/manage-ddos-protection.md)

- [Intelligence per le minacce integrata nel centro sicurezza di Azure](../security-center/azure-defender.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: se si usano macchine virtuali di Azure per accedere alle entità del bus di servizio, è possibile usare Network Watcher acquisizione pacchetti per analizzare le attività anomale.

- [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida**: se si usano macchine virtuali di Azure per accedere alle entità del bus di servizio, selezionare un'offerta da Azure Marketplace che supporta la funzionalità di ID/IP con funzionalità di ispezione del payload. Se il rilevamento delle intrusioni e/o la prevenzione basata sull'ispezione del payload non è necessario per l'organizzazione, è possibile usare la funzionalità firewall incorporata del bus di servizio di Azure. È possibile limitare l'accesso allo spazio dei nomi del bus di servizio per un intervallo limitato di indirizzi IP o un indirizzo IP specifico usando le regole del firewall.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Come aggiungere una regola del firewall negli spazi dei nomi del bus di servizio per un indirizzo IP specifico](service-bus-ip-filtering.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure che filtrano il traffico da e verso le risorse del bus È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, ServiceBus) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi. 

- [Comprendere e usare i tag di servizio](../virtual-network/service-tags-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete associate agli spazi dei nomi del bus di servizio di Azure con criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. ServiceBus" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete degli spazi dei nomi del bus di servizio. È anche possibile usare le definizioni di criteri predefinite correlate al bus di servizio di Azure, ad esempio:

- Il bus di servizio deve usare un endpoint servizio di rete virtuale
- È consigliabile abilitare i log di diagnostica nel bus di servizio

È anche possibile creare definizioni di criteri personalizzate se le definizioni predefinite non soddisfano le esigenze dell'organizzazione.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Criteri predefiniti di Azure per lo spazio dei nomi del bus di servizio](./policy-reference.md#azure-service-bus-messaging)

- [Esempi di criteri di Azure per la rete](../governance/policy/samples/built-in-policies.md#network)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni**: usare i tag per le reti virtuali e altre risorse correlate alla sicurezza di rete e al flusso del traffico associati agli spazi dei nomi del bus di servizio. Per le singole regole del gruppo di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali, la durata e altre informazioni descrittive per le regole che consentono il traffico da o verso una rete associata agli spazi dei nomi del bus di servizio. 

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag. 

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag. 

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md) 

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md) 

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate al bus di servizio di Azure. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati dalle risorse del bus di servizio. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi, configurare gli account di archiviazione di Azure per l'archiviazione a lungo termine o di archiviazione. È anche possibile configurare i log relativi al bus di servizio da inviare ad Azure Sentinel o a SIEM di terze parti.

- [Come configurare le impostazioni di diagnostica per il bus di servizio di Azure](service-bus-diagnostic-logs.md)

- [Informazioni sul log attività di Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni**: abilitare le impostazioni di diagnostica per lo spazio dei nomi del bus di servizio di Azure. Il bus di servizio di Azure supporta attualmente log di attività e operativi o di diagnostica. I log attività contengono informazioni sulle operazioni eseguite in un processo. I log di diagnostica forniscono informazioni più complete sulle operazioni e sulle azioni eseguite sullo spazio dei nomi tramite l'API o i client di gestione che usano il linguaggio SDK. In particolare, questi log acquisiscono il tipo di operazione, tra cui la creazione delle code, le risorse usate e lo stato dell'operazione.

- [Come abilitare le impostazioni di diagnostica per il bus di servizio di Azure](service-bus-diagnostic-logs.md)

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: in monitoraggio di Azure impostare il periodo di conservazione dell'area di lavoro log Analytics in base alle normative di conformità dell'organizzazione per acquisire ed esaminare gli eventi imprevisti relativi al bus di servizio.

- [Come impostare i parametri di conservazione dei log per aree di lavoro Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: analizzare e monitorare i log per un comportamento anomalo ed esaminare periodicamente i risultati relativi alle entità del bus di servizio. Usare monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log relativi al bus di servizio.

- [Per ulteriori informazioni sull'area di lavoro Log Analytics](../azure-monitor/log-query/get-started-portal.md)

- [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Indicazioni**: usare il Centro sicurezza di Azure con log Analytics area di lavoro per il monitoraggio e l'invio di avvisi su attività anomale rilevate in registri di sicurezza ed eventi. In alternativa, è anche possibile abilitare e caricare i dati in Sentinel di Azure.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come gestire gli avvisi nel centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Come inviare un avviso sui dati del log di log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni**: il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) consente di gestire l'accesso alle risorse di Azure tramite assegnazioni di ruolo. È possibile assegnare questi ruoli a utenti, gruppi di entità servizio e identità gestite. Sono disponibili ruoli predefiniti predefiniti per il bus di servizio, che possono essere sottoposti a inventario o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o l'portale di Azure.

- [Ruoli predefiniti per il bus di servizio di Azure](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: l'accesso del piano di controllo al bus di servizio viene controllato tramite Azure Active Directory (Azure ad). Azure AD non è il concetto di password predefinite.

L'accesso al bus di servizio del piano dati viene controllato tramite Azure AD usando identità gestite, Registrazioni app o firme di accesso condiviso. Le firme di accesso condiviso vengono usate dai client che si connettono allo spazio dei nomi del bus di servizio e possono essere rigenerate in qualsiasi momento.

- [Informazioni sulle firme di accesso condiviso per il bus di servizio](service-bus-sas.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio:

- Alla sottoscrizione deve essere assegnato più di un proprietario
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

È anche possibile creare definizioni di criteri personalizzate se le definizioni predefinite non soddisfano le esigenze dell'organizzazione.

- [Come usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso](../security-center/security-center-identity-access.md)

- [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usare Azure Active Directory Single Sign-On (SSO)

**Linee guida**: Microsoft Azure offre la gestione integrata del controllo degli accessi per le risorse e le applicazioni basate su Azure Active Directory (Azure ad). Un vantaggio fondamentale dell'uso di Azure AD con il bus di servizio di Azure consiste nel fatto che non è più necessario archiviare le credenziali nel codice. È invece possibile richiedere un token di accesso OAuth 2,0 dalla piattaforma di identità Microsoft. Il nome della risorsa per richiedere un token è https://azure.microsoft.com/services/service-bus/ . Azure AD autentica l'entità di sicurezza (un utente, un gruppo o un'entità servizio) che esegue l'applicazione. Se l'autenticazione ha esito positivo, Azure AD restituisce un token di accesso all'applicazione e l'applicazione può quindi usare il token di accesso per autorizzare la richiesta alle risorse del bus di servizio di Azure.

- [Come autenticare un'applicazione con Azure AD per accedere alle risorse del bus di servizio](authenticate-application.md)

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare Azure Active Directory multi-factor authentication (multi-factor authentication) e seguire le indicazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure per proteggere le risorse abilitate per il bus di servizio.

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usare workstation sicure gestite da Azure per le attività amministrative

**Linee guida**: usare workstation con accesso con privilegi (Paw) con multi-factor authentication (multi-factor authentication) configurato per accedere e configurare le risorse abilitate per il bus di servizio.

- [Informazioni sulle workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Indicazioni**: usare Azure Active Directory report di sicurezza e il monitoraggio per rilevare quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare Azure ad località denominate per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o di paesi/aree geografiche. 

- [Come configurare Azure AD località denominate](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (ad) come sistema di autenticazione e autorizzazione centrale per le risorse di Azure, ad esempio il bus di servizio. Questo consente il controllo degli accessi in base al ruolo di Azure (RBAC) per le risorse amministrative riservate.

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autorizzare l'accesso alle risorse del bus di servizio usando Azure Active Directory](authenticate-application.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

Materiale sussidiario **: Azure Active Directory**(Azure ad) fornisce log che consentono di individuare gli account obsoleti. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

In aggiunta, ruotare regolarmente la firma di accesso condiviso dello spazio dei nomi del bus di servizio.

- [Informazioni sulla creazione di report Azure AD](../active-directory/reports-monitoring/index.yml)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

- [Informazioni sulle firme di accesso condiviso per lo spazio dei nomi del bus di servizio](service-bus-sas.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: è possibile accedere alle origini dei log eventi di attività di accesso, controllo e rischio di Azure Active Directory (Azure ad), che consentono di eseguire l'integrazione con Azure Sentinel o con uno strumento Siem di terze parti.

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. In monitoraggio di Azure è quindi possibile configurare gli avvisi del log desiderati per determinate azioni che si verificano nei log.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Autorizzare l'accesso alle risorse del bus di servizio usando Azure Active Directory](authenticate-application.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Linee guida**: usare le funzionalità di protezione delle identità e rilevamento dei rischi di Azure Active Directory per configurare risposte automatiche per rilevare azioni sospette correlate alle risorse abilitate per il bus di servizio. È necessario abilitare le risposte automatiche tramite Sentinel di Azure per implementare le risposte di sicurezza dell'organizzazione.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: attualmente non disponibile; Customer Lockbox non è ancora supportato per il bus di servizio.

- [Elenco dei servizi Customer Lockbox supportati](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag sulle risorse correlate al bus di servizio per semplificare il monitoraggio delle risorse di Azure che archiviano o elaborano informazioni riservate.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida**: implementare sottoscrizioni e gruppi di gestione distinti per lo sviluppo, il test e la produzione. Gli spazi dei nomi del bus di servizio devono essere separati da reti virtuali con endpoint privati configurati e contrassegnati in modo appropriato.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: quando si usano le macchine virtuali per accedere alle entità del bus di servizio, usare le reti virtuali, gli endpoint privati, il firewall del bus di servizio, i gruppi di sicurezza di rete e i tag di servizio per attenuare la possibilità di exfiltration di dati.

Microsoft gestisce l'infrastruttura sottostante per il bus di servizio di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

- [Configurare le regole del firewall IP per gli spazi dei nomi del bus di servizio di Azure](service-bus-ip-filtering.md)

- [Consentire l'accesso allo spazio dei nomi del bus di servizio di Azure da reti virtuali specifiche](private-link-service.md)

- [Consentire l'accesso agli spazi dei nomi del bus di servizio di Azure tramite endpoint privati](private-link-service.md)

- [Informazioni sui gruppi di sicurezza di rete e sui tag di servizio](../virtual-network/network-security-groups-overview.md)

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: il bus di servizio di Azure impone le comunicazioni crittografate TLS per impostazione predefinita. Attualmente sono supportate le versioni di TLS 1,0, 1,1 e 1,2. Tuttavia, TLS 1,0 e 1,1 si trovano in un percorso di deprecazione a livello di settore, quindi usare TLS 1,2 o versione successiva laddove possibile.

- [Per informazioni sulle funzionalità di sicurezza del bus di servizio, vedere sicurezza di rete](network-security.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: identificazione dei dati, classificazione e funzionalità di prevenzione della perdita non ancora disponibili per il bus di servizio di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Indicazioni**: il bus di servizio di Azure supporta l'uso di Azure Active Directory (Azure ad) per autorizzare le richieste alle entità del bus di servizio. Con Azure AD, è possibile usare il controllo degli accessi in base al ruolo di Azure per concedere le autorizzazioni a un'entità di sicurezza, che può essere un utente o un'entità servizio dell'applicazione.

- [Informazioni sui ruoli disponibili e RBAC di Azure per il bus di servizio di Azure](authenticate-application.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: il bus di servizio di Azure supporta l'opzione di crittografia dei dati inattivi con chiavi gestite da Microsoft o chiavi gestite dal cliente. Questa funzionalità consente di creare, ruotare, disabilitare e revocare l'accesso alle chiavi gestite dal cliente usate per la crittografia dei dati inattivi del bus di servizio di Azure.

- [Come configurare le chiavi gestite dal cliente per la crittografia del bus di servizio di Azure](configure-customer-managed-key.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle istanze di produzione del bus di servizio di Azure e altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (inclusi gli spazi dei nomi del bus di servizio di Azure) all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Linee guida**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e monitorare gli spazi dei nomi del bus di servizio di Azure e le risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

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

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Inoltre, usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

È anche possibile creare definizioni di criteri personalizzate se le definizioni predefinite non soddisfano le esigenze dell'organizzazione.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/index.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le distribuzioni del bus di servizio di Azure. È anche possibile usare le definizioni di criteri predefinite per il bus di servizio di Azure, ad esempio:

- È consigliabile abilitare i log di diagnostica nel bus di servizio
- Il bus di servizio deve usare un endpoint di servizio di rete virtuale per limitare il traffico di rete alle reti private.

Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. ServiceBus" per creare criteri personalizzati per il controllo o l'applicazione delle configurazioni.

- [Criteri predefiniti di Azure per il bus di servizio ](./policy-reference.md)

- [Come visualizzare gli alias dei criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare i criteri di Azure [deny] e [Deploy if not exist] per applicare impostazioni sicure nelle risorse o nelle applicazioni abilitate per il bus di servizio.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Per altre informazioni sugli effetti dei criteri di Azure](../governance/policy/concepts/effects.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. ServiceBus" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. ServiceBus" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [Deploy if not exist] per applicare automaticamente le configurazioni per le distribuzioni del bus di servizio di Azure e le risorse correlate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: per le macchine virtuali o le applicazioni Web di Azure in esecuzione nel servizio app Azure usato per accedere alle entità del bus di servizio, usare una identità del servizio gestita insieme ai Azure Key Vault per semplificare e proteggere la gestione delle firme di accesso condiviso per le distribuzioni del bus di servizio di Azure. Assicurarsi Key Vault l'eliminazione temporanea sia abilitata.

- [Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse del bus di servizio](service-bus-managed-service-identity.md)

- [Configurare le chiavi gestite dal cliente per il bus di servizio](configure-customer-managed-key.md)

- [Come creare una Key Vault](../key-vault/general/quick-create-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: per le macchine virtuali o le applicazioni Web di Azure in esecuzione nel servizio app Azure usato per accedere alle entità del bus di servizio, usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere il bus di servizio di Azure. Assicurarsi Key Vault l'eliminazione temporanea sia abilitata.

Usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Azure Key Vault, senza credenziali nel codice.

- [Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse del bus di servizio](service-bus-managed-service-identity.md)

- [Configurare le chiavi gestite dal cliente per il bus di servizio](configure-customer-managed-key.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: pre-analizzare i contenuti caricati in risorse di Azure non di calcolo, ad esempio il bus di servizio di Azure, il servizio App, data Lake storage, archiviazione BLOB, database di Azure per PostgreSQL e così via. Microsoft non è in grado di accedere ai dati in tali istanze.

Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ma non viene eseguito sui contenuti del cliente.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Linee guida**: configurare il ripristino di emergenza geografico per il bus di servizio di Azure. In caso di tempo di inattività di interi data center o aree di Azure (se non vengono usate zone di disponibilità), è essenziale che l'elaborazione dei dati continui in un'area o in un data center diverso. Il ripristino di emergenza geografico e la replica geografica sono quindi funzionalità importanti per qualsiasi azienda. Il bus di servizio di Azure supporta il ripristino di emergenza geografico e la replica geografica a livello di spazio dei nomi.

- [Informazioni sul ripristino di emergenza geografico per il bus di servizio di Azure](service-bus-geo-dr.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: il bus di servizio di Azure fornisce la crittografia dei dati inattivi con crittografia del servizio di archiviazione di Azure (SSE di Azure). Il bus di servizio si basa su archiviazione di Azure per archiviare i dati e, per impostazione predefinita, tutti i dati archiviati con archiviazione di Azure vengono crittografati usando le chiavi gestite da Microsoft. Se si usano Azure Key Vault per archiviare le chiavi gestite dal cliente, assicurarsi che i backup automatici delle chiavi siano regolari.

Verificare la regolarità dei backup automatici dei segreti di Key Vault con il comando di PowerShell seguente: Backup-AzKeyVaultSecret

- [Come configurare le chiavi gestite dal cliente per la crittografia dei dati del bus di servizio di Azure inattivi](configure-customer-managed-key.md)

- [Come eseguire il backup di Key Vault segreti](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni**: ripristino del test di backup delle chiavi gestite dal cliente per crittografare i dati del bus di servizio.

- [Come configurare le chiavi gestite dal cliente per la crittografia dei dati del bus di servizio di Azure inattivi](configure-customer-managed-key.md)

- [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: abilitare l'eliminazione temporanea in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose. Il bus di servizio di Azure richiede che le chiavi gestite dal cliente dispongano di eliminazione temporanea e non ripuliscono la configurazione.

- [Come abilitare l'eliminazione temporanea in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Configurare un insieme di credenziali delle chiavi con chiavi](../event-hubs/configure-customer-managed-key.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

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

Inoltre, contrassegnare le sottoscrizioni usando i tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili. È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto. 

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md) 

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Pubblicazione del NIST: Guida ai programmi di test, formazione e esercizio per piani e funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per identificare i rischi per le risorse di Azure. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo e continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel. 

- [Come configurare l'esportazione continua](../security-center/continuous-export.md) 

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: 

Usare il Centro sicurezza di Azure per l'automazione del flusso di lavoro per attivare automaticamente le risposte agli avvisi e alle raccomandazioni di sicurezza per proteggere le risorse di Azure. 

- [Come configurare l'automazione del flusso di lavoro nel centro sicurezza](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: seguire le regole di test di penetrazione Microsoft Cloud di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft. 

- [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)