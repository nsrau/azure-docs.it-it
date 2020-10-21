---
title: Baseline della sicurezza di Azure per hub eventi
description: Baseline della sicurezza di Azure per hub eventi
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 007409d755da2d879297b4b898663dcbd9fd6d7f
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329628"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Baseline della sicurezza di Azure per hub eventi

La linea di base di sicurezza di Azure per hub eventi contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](../security/benchmarks/overview.md), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere [Panoramica delle baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Controllo di sicurezza: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

**Linee guida**: l'integrazione di hub eventi con gli endpoint del servizio rete virtuale consente l'accesso sicuro alle funzionalità di messaggistica da carichi di lavoro, ad esempio macchine virtuali associate a reti virtuali, con il percorso del traffico di rete protetto su entrambe le estremità.

Una volta associato ad almeno un endpoint di servizio della subnet di rete virtuale, il rispettivo spazio dei nomi di hub eventi non accetta più il traffico da qualsiasi posizione, ma le subnet autorizzate nelle reti virtuali. Dal punto di vista della rete virtuale, il binding dello spazio dei nomi di hub eventi a un endpoint di servizio configura un tunnel di rete isolato dalla subnet della rete virtuale al servizio di messaggistica. 

È anche possibile creare un endpoint privato, che è un'interfaccia di rete che si connette privatamente e in modo sicuro al servizio Hub eventi di Azure usando il servizio di collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. 

È anche possibile proteggere lo spazio dei nomi di hub eventi di Azure usando i firewall. Hub eventi di Azure supporta i controlli di accesso basato su IP per il supporto del firewall in ingresso. È possibile impostare le regole del firewall usando il portale di Azure, Azure Resource Manager modelli o tramite l'interfaccia della riga di comando di Azure o Azure PowerShell.

Come usare gli endpoint del servizio rete virtuale con hub eventi di Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Per altre informazioni, vedere integrare Hub eventi di Azure con collegamento privato di Azure: https://docs.microsoft.com/azure/event-hubs/private-link-service .

Abilitare l'integrazione delle reti virtuali e i firewall nello spazio dei nomi di hub eventi: https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Come configurare le regole del firewall IP per gli spazi dei nomi di hub eventi di Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Linee guida**: usare il Centro sicurezza di Azure e seguire le raccomandazioni per la protezione della rete per proteggere le risorse di hub eventi in Azure. Se si usano macchine virtuali di Azure per accedere a hub eventi, abilitare i log di flusso del gruppo di sicurezza di rete (NSG) e inviare i log a un account di archiviazione per il controllo del traffico.

Come abilitare i log dei flussi NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare protezione DDoS standard nelle reti virtuali associate agli hub eventi per prevenire gli attacchi di tipo Denial of Service (DDoS) distribuiti. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

Come configurare la protezione DDoS: [https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](../virtual-network/manage-ddos-protection.md)

Per ulteriori informazioni sull'Intelligence per le minacce integrata nel centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: registrare i pacchetti di rete e i log dei flussi

**Linee guida**: se si usano macchine virtuali di Azure per accedere a hub eventi, abilitare i log di flusso del gruppo di sicurezza di rete (NSG) e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Se necessario per l'analisi dell'attività anomala, abilitare Network Watcher acquisizione pacchetti.

Come abilitare i log dei flussi NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare e usare Analisi del traffico: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Come abilitare Network Watcher: https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: distribuire sistemi di rilevamento intrusioni/prevenzione intrusioni (IDS/IPS) basati sulla rete

**Linee guida**: se si usano macchine virtuali di Azure per accedere a hub eventi, selezionare un'offerta da Azure Marketplace che supporta la funzionalità di ID/IP con le funzionalità di ispezione del payload. Se il rilevamento delle intrusioni e/o la prevenzione basata sull'ispezione del payload non è necessario per l'organizzazione, è possibile usare la funzionalità firewall incorporata di hub eventi di Azure. È possibile limitare l'accesso allo spazio dei nomi di hub eventi per un intervallo limitato di indirizzi IP o un indirizzo IP specifico usando le regole del firewall.

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

Come aggiungere una regola del firewall in hub eventi per un indirizzo IP specifico:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitoraggio del Centro sicurezza di Azure**: non ancora disponibile

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: non applicabile. questa raccomandazione è destinata alle applicazioni Web in esecuzione in app Azure servizio o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete associate agli spazi dei nomi di hub eventi di Azure con criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. EventHub" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete degli spazi dei nomi di hub eventi. È anche possibile usare le definizioni di criteri predefinite correlate a hub eventi di Azure, ad esempio:

- Hub eventi deve usare un endpoint di servizio di rete virtuale.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Criteri predefiniti di Azure per lo spazio dei nomi di hub eventi:  https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



Esempi di Criteri di Azure per la rete: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Come creare un progetto di Azure: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni**: usare i tag per le reti virtuali e altre risorse correlate alla sicurezza di rete e al flusso del traffico associati all'hub eventi.

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate a hub eventi di Azure. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

Come visualizzare e recuperare gli eventi del log attività di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Come creare avvisi in Monitoraggio di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Linee guida**: non applicabile; Microsoft gestisce l'origine dell'ora usata per le risorse di Azure, ad esempio hub eventi di Azure, per i timestamp nei log.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: all'interno di monitoraggio di Azure, configurare i log correlati a hub eventi nelle impostazioni di diagnostica del log attività e dell'hub eventi per inviare i log in un'area di lavoro di log Analytics per eseguire query o in un account di archiviazione per l'archiviazione a lungo termine dell'archivio.

Come configurare le impostazioni di diagnostica per hub eventi di Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Informazioni sul log attività di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare le impostazioni di diagnostica per lo spazio dei nomi di hub eventi di Azure. Sono disponibili tre categorie di impostazioni di diagnostica per hub eventi di Azure: log di archivio, log operativi e log di ridimensionamento automatico. Consentire ai log operativi di acquisire informazioni su ciò che accade durante le operazioni di hub eventi, in particolare il tipo di operazione, tra cui la creazione dell'hub eventi, le risorse usate e lo stato dell'operazione.

Inoltre, è possibile abilitare le impostazioni di diagnostica del log attività di Azure e inviarle a un account di archiviazione di Azure, a un hub eventi o a un'area di lavoro Log Analytics. I log attività forniscono informazioni approfondite sulle operazioni eseguite sull'hub eventi di Azure e altre risorse. Usando i log attività, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sugli spazi dei nomi di hub eventi di Azure.

Come abilitare le impostazioni di diagnostica per hub eventi di Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Come abilitare le impostazioni di diagnostica per log attività di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: all'interno di monitoraggio di Azure impostare il periodo di conservazione dell'area di lavoro log Analytics in base alle normative di conformità dell'organizzazione per acquisire e verificare gli eventi imprevisti correlati all'hub eventi.

Come impostare i parametri di conservazione dei log per le aree di lavoro Log Analytics: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida**: analizzare e monitorare i log per un comportamento anomalo ed esaminare periodicamente i risultati correlati all'hub eventi. Usare Log Analytics di Monitoraggio di Azure per esaminare i log ed eseguire query sui relativi dati. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.
 

Per ulteriori informazioni sull'area di lavoro Log Analytics: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Come eseguire query personalizzate in Monitoraggio di Azure: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Come eseguire l'onboarding di Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: abilitare gli avvisi per le attività anomale

**Indicazioni**: all'interno di monitoraggio di Azure, configurare i log correlati all'hub eventi di Azure nel log attività e le impostazioni di diagnostica di hub eventi per inviare i log in un'area di lavoro di log Analytics per eseguire query o in un account di archiviazione per l'archiviazione a lungo termine dell'archivio. Utilizzare Log Analytics area di lavoro per creare avvisi per attività anomale rilevate negli eventi e nei registri di sicurezza.

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure. 

Informazioni sul log attività di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Come configurare le impostazioni di diagnostica per hub eventi di Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Come inviare un avviso sui dati di log dell'area di lavoro Log Analytics: https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Come eseguire l'onboarding di Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: non ancora disponibile

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida**: non applicabile; Hub eventi non elabora la registrazione anti-malware.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida**: non applicabile; Hub eventi non elabora o produce log correlati a DNS.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: Abilitare la registrazione di controllo da riga di comando

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: mantenere un inventario degli account amministrativi

**Linee guida**: Azure Active Directory (ad) include ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi. 

Come ottenere un ruolo della directory in Azure AD con PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: l'accesso del piano di controllo a hub eventi è controllato tramite Azure Active Directory (ad). Azure AD non è il concetto di password predefinite.

L'accesso al piano dati a hub eventi è controllato tramite Azure AD con identità gestite o Registrazioni app, nonché con firme di accesso condiviso. Le firme di accesso condiviso vengono usate dai client che si connettono a hub eventi e possono essere rigenerate in qualsiasi momento.

Informazioni sulle firme di accesso condiviso per hub eventi: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio:

- Alla sottoscrizione deve essere assegnato più di un proprietario

- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

Come usare il Centro sicurezza di Azure per monitorare identità e accesso (anteprima): https://docs.microsoft.com/azure/security-center/security-center-identity-access

Come usare criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Indicazioni**: Microsoft Azure offre la gestione integrata del controllo di accesso per le risorse e le applicazioni basate su Azure Active Directory (ad). Un vantaggio fondamentale dell'uso di Azure AD con hub eventi di Azure consiste nel fatto che non è più necessario archiviare le credenziali nel codice. È invece possibile richiedere un token di accesso OAuth 2,0 dalla piattaforma di identità Microsoft. Il nome della risorsa per richiedere un token è https: \/ /Eventhubs.Azure.NET/. Azure AD autentica l'entità di sicurezza (un utente, un gruppo o un'entità servizio) che esegue l'applicazione. Se l'autenticazione ha esito positivo, Azure AD restituisce un token di accesso all'applicazione e l'applicazione può quindi usare il token di accesso per autorizzare la richiesta alle risorse di hub eventi di Azure.

Come autenticare un'applicazione con Azure AD per accedere alle risorse di hub eventi: https://docs.microsoft.com/azure/event-hubs/authenticate-application

Informazioni su SSO con Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Linee guida**: abilitare Azure Active Directory multi-factor authentication (multi-factor authentication) e seguire le indicazioni sulla gestione delle identità e degli accessi del Centro sicurezza di Azure per proteggere le risorse abilitate per hub eventi.

Come abilitare MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida**: usare workstation con accesso con privilegi (Paw) con multi-factor authentication (multi-factor authentication) configurato per accedere e configurare le risorse abilitate per hub eventi.

Informazioni sulle workstation con accesso con privilegi: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: registrare e inviare avvisi per le attività sospette dagli account amministrativi

**Indicazioni**: usare Azure Active Directory (AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando nell'ambiente si verifica un'attività sospetta o non sicura. Usare Azure AD i rilevamenti dei rischi per visualizzare gli avvisi e i report sul comportamento utente rischioso. Per la registrazione aggiuntiva, inviare avvisi di rilevamento dei rischi del Centro sicurezza di Azure in monitoraggio di Azure e configurare avvisi/notifiche personalizzati usando i gruppi di azioni.

Come distribuire Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Informazioni sui rilevamenti dei rischi di Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Come configurare gruppi di azioni per avvisi e notifiche personalizzati: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare le località denominate di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.



Come configurare località denominate in Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (ad) come sistema di autenticazione e autorizzazione centrale per le risorse di Azure, ad esempio hub eventi. Questo consente il controllo degli accessi in base al ruolo di Azure (RBAC) per le risorse amministrative riservate.

 Come creare e configurare un'istanza di Azure AD: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Per informazioni sull'integrazione di hub eventi di Azure con Azure Active Directory (AAD), vedere autorizzare l'accesso alle risorse di hub eventi usando Azure Active Directory: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Linee guida**: Azure Active Directory (ad) fornisce log che consentono di individuare gli account obsoleti. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

In altre, ruotare regolarmente le firme di accesso condiviso di hub eventi.

Informazioni Azure AD la creazione di report: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Come usare le verifiche di accesso alle identità di Azure: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Informazioni sulle firme di accesso condiviso per hub eventi: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: monitorare i tentativi di accesso agli account disattivati

**Linee guida**: è possibile accedere alle origini del registro eventi di attività di accesso, controllo e rischio di Azure Active Directory (ad), che consentono di eseguire l'integrazione con qualsiasi strumento Siem/Monitoring.

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi del log desiderati all'interno Log Analytics.

Come integrare i log attività di Azure in Monitoraggio di Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Autorizzare l'accesso alle risorse di hub eventi usando Azure Active Directory: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Linee guida**: usare le funzionalità di protezione delle identità e rilevamento dei rischi di Azure Active Directory per configurare risposte automatiche per rilevare azioni sospette correlate alle risorse abilitate per hub eventi. È necessario abilitare le risposte automatiche tramite Sentinel di Azure per implementare le risposte di sicurezza dell'organizzazione.

Come visualizzare gli accessi rischiosi per Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Come configurare e abilitare i criteri di rischio di Identity Protection: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Come eseguire l'onboarding di Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: attualmente non disponibile; Customer Lockbox non è ancora supportato per gli hub eventi.

Elenco di servizi supportati da Customer Lockbox: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: attualmente non disponibile

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: mantenere un inventario delle informazioni riservate

**Indicazioni**: usare i tag sulle risorse correlate all'hub eventi per semplificare il monitoraggio delle risorse di Azure che archiviano o elaborano informazioni riservate.

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Gli spazi dei nomi di hub eventi devono essere separati da una rete virtuale con gli endpoint di servizio abilitati e contrassegnati in modo appropriato.

È anche possibile proteggere lo spazio dei nomi di hub eventi di Azure usando i firewall. Hub eventi di Azure supporta i controlli di accesso basato su IP per il supporto del firewall in ingresso. È possibile impostare le regole del firewall usando il portale di Azure, Azure Resource Manager modelli o tramite l'interfaccia della riga di comando di Azure o Azure PowerShell.

Come creare sottoscrizioni di Azure aggiuntive: https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare gruppi di gestione: https://docs.microsoft.com/azure/governance/management-groups/create

Configurare le regole del firewall IP per gli spazi dei nomi di hub eventi di Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Come creare una rete virtuale: https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: quando si usano le macchine virtuali per accedere a hub eventi, usare le reti virtuali, gli endpoint di servizio, il firewall di hub eventi, i gruppi di sicurezza di rete e i tag di servizio per attenuare la possibilità di exfiltration di dati.

Microsoft gestisce l'infrastruttura sottostante per hub eventi di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

Configurare le regole del firewall IP per gli spazi dei nomi di hub eventi di Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Informazioni sugli endpoint del servizio rete virtuale con hub eventi di Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Integrare Hub eventi di Azure con collegamento privato di Azure: https://docs.microsoft.com/azure/event-hubs/private-link-service

Informazioni sui gruppi di sicurezza di rete e sui tag del servizio: https://docs.microsoft.com/azure/virtual-network/security-overview

Informazioni sulla protezione dei dati dei clienti in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: Hub eventi di Azure applica le comunicazioni crittografate TLS per impostazione predefinita. Attualmente sono supportate le versioni di TLS 1,0, 1,1 e 1,2. Tuttavia, TLS 1,0 e 1,1 si trovano in un percorso di deprecazione a livello di settore, quindi, se possibile, usare TLS 1,2.

Per informazioni sulle funzionalità di sicurezza di hub eventi, vedere sicurezza di rete:  https://docs.microsoft.com/azure/event-hubs/network-security

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: identificazione dei dati, classificazione e funzionalità di prevenzione della perdita non ancora disponibili per hub eventi di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

Informazioni sulla protezione dei dati dei clienti in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Linee guida**: Hub eventi di Azure supporta l'uso di Azure Active Directory (ad) per autorizzare le richieste alle risorse di hub eventi. Con Azure AD, è possibile usare il controllo degli accessi in base al ruolo di Azure per concedere le autorizzazioni a un'entità di sicurezza, che può essere un utente o un'entità servizio dell'applicazione.

Informazioni sui ruoli disponibili e RBAC di Azure per hub eventi di Azure: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per hub eventi e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

Informazioni sulla protezione dei dati dei clienti in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografare le informazioni riservate inattive

**Linee guida**: Hub eventi di Azure supporta l'opzione di crittografia dei dati inattivi con chiavi gestite da Microsoft o chiavi gestite dal cliente. Questa funzionalità consente di creare, ruotare, disabilitare e revocare l'accesso alle chiavi gestite dal cliente usate per la crittografia dei dati inattivi di hub eventi di Azure.

Come configurare le chiavi gestite dal cliente per la crittografia di hub eventi di Azure: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle istanze di produzione di hub eventi di Azure e altre risorse critiche o correlate.

Come creare avvisi per gli eventi del log attività di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee guida**: non applicabile; Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano Hub eventi.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Linee guida**: non applicabile; Microsoft esegue la gestione delle patch nei sistemi sottostanti che supportano Hub eventi.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: distribuire una soluzione di gestione delle patch automatizzata per il software di terze parti

**Linee guida**: non applicabile; il benchmark è progettato per le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee guida**: non applicabile; Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano Hub eventi.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Linee guida**: non applicabile; Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano Hub eventi.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: usare l'individuazione di asset in Azure

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (inclusi gli spazi dei nomi di hub eventi di Azure) all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Come creare query con Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Come visualizzare le sottoscrizioni di Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Informazioni sul controllo degli accessi in base al ruolo di Azure: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia degli spazi dei nomi e delle risorse correlate di hub eventi di Azure. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Come creare sottoscrizioni di Azure aggiuntive: https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare gruppi di gestione: https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: mantenere un inventario delle risorse di Azure approvate e dei titoli software

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo e Azure in generale.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare le risorse di Azure non approvate

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare anche Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare query con Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo e Azure in generale.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come negare un tipo di risorsa specifico con Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="610-implement-approved-application-list"></a>6.10: implementare l'elenco di applicazioni approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Limitare la capacità degli utenti di interagire con Azure Resource Manager tramite script</div>

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Controllo di sicurezza: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le distribuzioni di hub eventi di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. EventHub" per creare criteri personalizzati per il controllo o l'applicazione delle configurazioni. È anche possibile usare le definizioni di criteri predefinite per hub eventi di Azure, ad esempio:

- È consigliabile abilitare i log di diagnostica in Hub eventi

- Gli hub eventi devono usare un endpoint servizio di rete virtuale

Criteri predefiniti di Azure per lo spazio dei nomi di hub eventi: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

Come visualizzare gli alias di Criteri di Azure disponibili: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare i criteri di Azure [deny] e [Deploy if not exist] per applicare le impostazioni sicure nelle risorse abilitate per gli hub eventi. 

Come configurare e gestire i criteri di Azure:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Per ulteriori informazioni sugli effetti dei criteri di Azure:  https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni di criteri di Azure personalizzate per hub eventi o risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

Come archiviare il codice in Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentazione di Azure Repos: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: distribuire strumenti di gestione della configurazione di sistema

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. EventHub" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. EventHub" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [Deploy if not exist] per applicare automaticamente le configurazioni per le distribuzioni di hub eventi di Azure e le risorse correlate.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: per le macchine virtuali o le applicazioni Web di Azure in esecuzione in app Azure servizio usato per accedere a hub eventi, usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione delle firme di accesso condiviso per le distribuzioni di hub eventi di Azure. Assicurarsi Key Vault l'eliminazione temporanea sia abilitata.

Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse di hub eventi: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Configurare chiavi gestite dal cliente per hub eventi: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Come eseguire l'integrazione con identità gestite di Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Come creare un insieme di credenziali delle chiavi: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Come eseguire l'autenticazione a Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Come assegnare un criterio di accesso Key Vault: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: per le macchine virtuali o le applicazioni Web di Azure in esecuzione in app Azure servizio usato per accedere a hub eventi, usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere Hub eventi di Azure. Assicurarsi Key Vault l'eliminazione temporanea sia abilitata.

Usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (AD). Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Azure Key Vault, senza credenziali nel codice.

Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse di hub eventi: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Configurare chiavi gestite dal cliente per hub eventi: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Come configurare le identità gestite: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Come eseguire l'integrazione con identità gestite di Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

Come impostare Credential Scanner: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Controllo di sicurezza: difesa da malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: usare software antimalware gestito in modo centralizzato

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

Il software antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Servizio app di Azure), ma non viene eseguito sui contenuti del cliente.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: pre-analizzare i contenuti caricati in risorse di Azure non di calcolo, ad esempio hub eventi di Azure, servizio App, data Lake storage, archiviazione BLOB, database di Azure per PostgreSQL e così via. Microsoft non è in grado di accedere ai dati in tali istanze.

Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, cache di Azure per Redis), ma non viene eseguito sui contenuti dei clienti.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: verificare che le firme e il software antimalware siano aggiornati

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Linee guida**: configurare il ripristino di emergenza geografico per hub eventi di Azure. In caso di tempo di inattività di interi data center o aree di Azure (se non vengono usate zone di disponibilità), è essenziale che l'elaborazione dei dati continui in un'area o in un data center diverso. Il ripristino di emergenza geografico e la replica geografica sono quindi funzionalità importanti per qualsiasi azienda. Il servizio Hub eventi di Azure supporta il ripristino di emergenza geografico e la replica geografica a livello di spazio dei nomi. 

Informazioni sul ripristino di emergenza geografico per hub eventi di Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: eseguire backup completi del sistema e il backup di tutte le chiavi gestite dal cliente

**Linee guida**: Hub eventi di Azure fornisce la crittografia dei dati inattivi con Azure crittografia del servizio di archiviazione (Azure SSE). Hub eventi si basa su archiviazione di Azure per archiviare i dati e, per impostazione predefinita, tutti i dati archiviati con archiviazione di Azure vengono crittografati usando le chiavi gestite da Microsoft. Se si usano Azure Key Vault per archiviare le chiavi gestite dal cliente, assicurarsi che i backup automatici delle chiavi siano regolari.

Verificare la regolarità dei backup automatici dei segreti di Key Vault con il comando di PowerShell seguente: Backup-AzKeyVaultSecret

Come configurare chiavi gestite dal cliente per la crittografia dei dati inattivi di hub eventi di Azure: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Come eseguire il backup di Key Vault segreti: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni**: ripristino di test delle chiavi gestite dal cliente sottoposte a backup.

 

Come ripristinare le chiavi di Key Vault in Azure: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: abilitare l'eliminazione temporanea in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose. Hub eventi di Azure richiede chiavi gestite dal cliente per l'eliminazione temporanea e non ripulisce la configurazione.

Configurare l'eliminazione temporanea per l'account di archiviazione di Azure usato per l'acquisizione dei dati di hub eventi. Si noti che questa funzionalità non è ancora supportata per Azure Data Lake Storage generazione 2.

Come abilitare l'eliminazione temporanea nei Key Vault: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

Configurare un insieme di credenziali delle chiavi con chiavi: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Eliminazione temporanea per i BLOB di archiviazione di Azure: https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare una guida per rispondere agli eventi imprevisti

**Indicazioni**: assicurarsi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono i ruoli del personale, nonché le fasi di gestione/gestione degli eventi imprevisti.

Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Linee guida**: il Centro sicurezza assegna un livello di gravità agli avvisi, in modo da consentire la priorità dell'ordine in cui si partecipa a ogni avviso, in modo che, quando una risorsa viene compromessa, è possibile accedervi immediatamente. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10,3: testare le procedure di risposta alla sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

Fare riferimento alla pubblicazione NIST: Guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente.  Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti. 

Come impostare il contatto di sicurezza del Centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi in Sentinel.

Come configurare l'esportazione continua: https://docs.microsoft.com/azure/security-center/continuous-export

Come trasmettere gli avvisi in Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" per gli avvisi e le raccomandazioni di sicurezza.

Come configurare l'automazione del flusso di lavoro e le app per la logica: https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Controllo di sicurezza: test di penetrazione ed esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: eseguire con regolarità test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici in termini di sicurezza entro 60 giorni

**Linee guida**: seguire le regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .
È possibile trovare altre informazioni sulla strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestiti da Microsoft, qui: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Benchmark di sicurezza di Azure](../security/benchmarks/overview.md)
- Vedere altre informazioni sulle [baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)
