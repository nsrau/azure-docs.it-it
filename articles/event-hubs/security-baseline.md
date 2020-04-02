---
title: Azure Security Baseline for Event Hubs
description: Azure Security Baseline for Event Hubs
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 10fc822f9f36512405dd4e3b5aba6270b53e163f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549036"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Azure Security Baseline for Event Hubs

La linea di base della sicurezza di Azure per gli hub eventi contiene suggerimenti che consentono di migliorare il livello di sicurezza della distribuzione.

La linea di base per questo servizio è ricavata da [Azure Security Benchmark versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce consigli su come proteggere le soluzioni cloud in Azure con le indicazioni sulle procedure consigliate.

Per altre informazioni, vedere [Panoramica delle linee di base](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)della sicurezza di Azure.For more information, see Azure Security Baselines overview .

## <a name="network-security"></a>Sicurezza di rete

*Per ulteriori informazioni, vedere [Controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale1.1: Protect resources using Network Security Groups or Azure Firewall on your Virtual Network

**Linee guida:** l'integrazione degli hub eventi con gli endpoint del servizio di rete virtuale consente l'accesso sicuro alle funzionalità di messaggistica da carichi di lavoro, ad esempio macchine virtuali associate a reti virtuali, con il percorso del traffico di rete protetto su entrambe le estremità.

Un'associazione ad almeno un endpoint del servizio subnet di rete virtuale, il rispettivo spazio dei nomi Hub eventi non accetta più traffico da qualsiasi luogo, ma subnet autorizzate nelle reti virtuali. Dal punto di vista della rete virtuale, l'associazione dello spazio dei nomi Hub eventi a un endpoint del servizio configura un tunnel di rete isolato dalla subnet della rete virtuale al servizio di messaggistica. 

È anche possibile creare un endpoint privato, ovvero un'interfaccia di rete che si connette privatamente e in modo sicuro al servizio Hub eventi di Azure usando il servizio Azure Private Link.You can also create a private endpoint, which is a network interface that connects you privately and securely to Azure Event Hubs service by using the Azure Private Link service. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. 

È anche possibile proteggere lo spazio dei nomi degli hub eventi di Azure usando i firewall. Hub eventi di Azure supporta i controlli di accesso basati su IP per il supporto del firewall in ingresso. È possibile impostare le regole del firewall usando il portale di Azure, i modelli di Azure Resource Manager o tramite l'interfaccia della riga di comando di Azure o Azure PowerShell.You can set firewall rules by using the Azure portal, Azure Resource Manager templates, or through the Azure CLI or Azure PowerShell.

Come usare gli endpoint del servizio di rete virtuale con gli hub eventi di Azure:How to use virtual network service endpoints with Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Per altre informazioni, vedere Integrare hub eventi https://docs.microsoft.com/azure/event-hubs/private-link-servicedi Azure con Azure Private Link: .

Abilitare l'integrazione di reti virtuali e i firewall nello spazio dei nomi Hub eventi:https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Come configurare le regole del firewall IP per gli spazi dei nomi di Hub eventi di Azure:How to configure IP firewall rules for Azure Event Hubs namespaces:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Indicazioni:** usare il Centro sicurezza di Azure e seguire i consigli di protezione della rete per proteggere le risorse degli hub eventi in Azure.Guidance: Use Azure Security Center and follow network protection recommendations to help secure your Event Hubs resources in Azure. Se si usano macchine virtuali di Azure per accedere agli hub eventi, abilitare i log di flusso del gruppo di sicurezza di rete (NSG) e inviare i log in un account di archiviazione per il controllo del traffico.

Abilitazione dei registri di flusso del gruppo di sicurezza di rete:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Informazioni sulla sicurezza di rete fornita dal Centro sicurezza di Azure:Understanding Network Security provided by Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteggere le applicazioni Web critiche

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle applicazioni Web in esecuzione nel servizio app di Azure o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Guida:** abilitare DDoS Protection Standard nelle reti virtuali associate agli hub eventi per proteggersi dagli attacchi DDoS (Distributed Denial of Service). Usare Azure Security Center Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP Internet dannosi o inutilizzati noti.

Come configurare la protezione DDoS:[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](/azure/virtual-network/manage-ddos-protection)

Per altre informazioni su Azure Security Center Integrated Threat Intelligence:For more information about the Azure Security Center Integrated Threat Intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Registrare i pacchetti di rete e i registri di flusso

**Indicazioni:** se si usano macchine virtuali di Azure per accedere agli hub eventi, abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione per il controllo del traffico. È anche possibile inviare log di flusso del gruppo di sicurezza di rete a un'area di lavoro di Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso di traffico nel cloud di Azure.You may also send NSG flow logs to a Log Analytics workspace and use Traffic Analytics to provide insights into traffic flow in your Azure cloud. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare hot spot, identificare le minacce alla sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni errate della rete.

Se necessario per analizzare l'attività anomala, abilitare l'acquisizione di pacchetti di Network Watcher.If required for investigating anomalous activity, enable Network Watcher packet capture.

Abilitazione dei registri di flusso del gruppo di sicurezza di rete:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare e usare Analisi del traffico:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Come abilitare Network Watcher:https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Indicazioni:** se si usano macchine virtuali di Azure per accedere agli hub eventi, selezionare un'offerta di Azure Marketplace che supporta la funzionalità IDS/IPS con funzionalità di ispezione del payload. Se il rilevamento e/o la prevenzione delle intrusioni basati sull'ispezione del payload non è necessaria per l'organizzazione, è possibile usare la funzionalità firewall incorporata di Hub eventi di Azure.If intrusion detection and/or prevention based on payload inspection is not required for your organization, you may use Azure Event Hubs' built-in firewall feature. È possibile limitare l'accesso allo spazio dei nomi Hub eventi per un intervallo limitato di indirizzi IP o per un indirizzo IP specifico usando le regole del firewall.

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

Come aggiungere una regola del firewall negli hub eventi per un indirizzo IP specificato:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitoraggio del Centro sicurezza di Azure**: Non ancora disponibile

**Responsabilità**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gestire il traffico verso le applicazioni web

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle applicazioni Web in esecuzione nel servizio app di Azure o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e l'overhead amministrativo delle regole di sicurezza di rete

**Linee guida:** non applicabile, questo consiglio è destinato alle applicazioni Web in esecuzione nel servizio app di Azure o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida:** definire e implementare configurazioni di sicurezza standard per le risorse di rete associate agli spazi dei nomi di Hub eventi di Azure con Criteri di Azure.Guidance: Define and implement standard security configurations for network resources associated with your Azure Event Hubs namespaces with Azure Policy. Usare gli alias dei criteri di Azure negli spazi dei nomi "Microsoft.EventHub" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete degli spazi dei nomi Hub eventi. È anche possibile usare le definizioni dei criteri predefinite correlate agli hub eventi di Azure, ad esempio:You may also make using of built-in policy definitions related to Azure Event Hubs, such as:

- L'Hub eventi deve usare un endpoint del servizio di rete virtuale.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Criteri predefiniti di Azure per lo spazio dei nomi Hub eventi:Azure Built-in Policy for Event Hubs namespace:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



Esempi di criteri di Azure per la rete:Azure Policy samples for networking:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Come creare un blueprint di Azure:How to create an Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regole di configurazione del traffico dei documenti

**Linee guida:** usare i tag per le reti virtuali e altre risorse correlate alla sicurezza di rete e al flusso di traffico associati agli hub eventi.

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilizzare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate agli hub eventi di Azure.Guidance : Use Azure Activity Log to monitor network resource configurations and detect changes for network resources related to Azure Event Hubs. Creare avvisi all'interno di Monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse di rete critiche.

Come visualizzare e recuperare gli eventi del log attività di Azure:How to view and retrieve Azure Activity Log events:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Come creare avvisi in Monitoraggio di Azure:How to create alerts in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizzare origini di sincronizzazione dell'ora approvate

**Orientamento**: Non applicabile; Microsoft gestisce l'origine ora usata per le risorse di Azure, ad esempio Hub eventi di Azure, per i timestamp nei log.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurare la gestione dei registri di protezione centralizzati

**Indicazioni:** in Monitoraggio di Azure configurare i log correlati agli hub eventi all'interno del log attività e delle impostazioni di diagnostica dell'hub eventi per inviare i log in un'area di lavoro di Log Analytics su cui eseguire query o in un account di archiviazione per l'archiviazione a lungo termine.

Come configurare le impostazioni di diagnostica per gli hub eventi di Azure:How to configure Diagnostic Settings for Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Informazioni sul log attività di Azure:Understanding Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Abilitare la registrazione di controllo per le risorse di Azure2.3: Abilitare la registrazione di controllo per le risorse di Azure2.

**Guida:** abilitare le impostazioni di diagnostica per lo spazio dei nomi Hub eventi di Azure.Guidance : Enable Diagnostic settings for your Azure Event Hubs namespace. Esistono tre categorie di impostazioni di diagnostica per hub eventi di Azure: log di archiviazione, log operativi e log di scalabilità automatica. Abilitare i registri operativi per acquisire informazioni su ciò che accade durante le operazioni di Hub eventi, in particolare il tipo di operazione, tra cui la creazione dell'hub eventi, le risorse usate e lo stato dell'operazione.

Inoltre, è possibile abilitare le impostazioni di diagnostica del log attività di Azure e inviarle a un account di archiviazione di Azure, a un hub eventi o a un'area di lavoro di Log Analytics.Additionally, you may enable Azure Activity log diagnostic settings and send them to an Azure Storage Account, event hub, or a Log Analytics workspace. I log attività forniscono informazioni dettagliate sulle operazioni eseguite negli hub eventi di Azure e in altre risorse. Usando i log attività, è possibile determinare "cosa, chi e quando" per tutte le operazioni di scrittura (PUT, POST, DELETE) eseguite negli spazi dei nomi di Hub eventi di Azure.Using activity logs, you can determine the "what, who, and when" for any write operations (PUT, POST, DELETE) taken on your Azure Event Hubs namespaces.

Come abilitare le impostazioni di diagnostica per gli hub eventi di Azure:How to enable Diagnostic Settings for Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Come abilitare le impostazioni di diagnostica per il log attività di Azure:How to enable Diagnostic Settings for Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Raccogliere i registri di sicurezza dai sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dello spazio di archiviazione del registro di protezione

**Linee guida:** in Monitoraggio di Azure impostare il periodo di conservazione dell'area di lavoro di Log Analytics in base alle normative di conformità dell'organizzazione per acquisire ed esaminare gli eventi imprevisti correlati all'hub degli eventi.

Come impostare i parametri di conservazione dei log per le aree di lavoro di Log Analytics:How to set log retention parameters for Log Analytics workspaces:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i registri

**Guida**: Analizzare e monitorare i log per verificare il comportamento anomalo ed esaminare regolarmente i risultati relativi agli hub eventi. Usare Analisi dei log di Monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log. In alternativa, è possibile abilitare e on-board i dati di Azure Sentinel o di terze parti SIEM.
 

Per altre informazioni sull'area di lavoro Log Analytics:For more information about the Log Analytics workspace:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Come eseguire query personalizzate in Monitoraggio di Azure:How to perform custom queries in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Come eseguire l'onboarding di Azure Sentinel:How to onboard Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Abilitare gli avvisi per attività anomale

**Linee guida:** in Monitoraggio di Azure configurare i log correlati agli hub eventi di Azure all'interno del log attività e le impostazioni di diagnostica degli hub eventi per inviare i log in un'area di lavoro di Log Analytics su cui eseguire query o in un account di archiviazione per l'archiviazione a lungo termine. Usare l'area di lavoro di Log Analytics per creare avvisi per le attività anomale rilevate nei registri e negli eventi di sicurezza.

In alternativa, è possibile abilitare e on-board i dati in Azure Sentinel.Alternatively, you may enable and on-board data to Azure Sentinel. 

Comprendere il log attività di Azure:Understand the Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Come configurare le impostazioni di diagnostica per gli hub eventi di Azure:How to configure Diagnostic Settings for Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Come avvisare i dati del log dell'area di lavoro di Log Analytics:How to alert on Log Analytics workspace log data:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Come eseguire l'onboarding di Azure Sentinel:How to onboard Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: Non ancora disponibile

**Responsabilità**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizzare la registrazione antimalware

**Orientamento**: Non applicabile; Event Hub non elabora la registrazione antimalware.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: Abilitare la registrazione delle query DNS

**Orientamento**: Non applicabile; Gli hub eventi non elaborano né producono registri correlati al DNS.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: Abilitare la registrazione di controllo della riga di comando

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per ulteriori informazioni, vedere [Controllo di sicurezza: controllo delle identità e degli accessi](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Mantenere un inventario degli account amministrativi

**Linee guida:** Azure Active Directory (AD) dispone di ruoli predefiniti che devono essere assegnati in modo esplicito e sono queryable. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account membri di gruppi amministrativi. 

Come ottenere un ruolo di directory in Azure AD con PowerShell:How to get a directory role in Azure AD with PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Come ottenere i membri di un ruolo di directory in Azure AD con PowerShell:How to get members of a directory role in Azure AD with PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Modificare le password predefinite, ove applicabile

**Linee guida:** l'accesso aereo di controllo agli hub eventi viene controllato tramite Azure Active Directory (AD). Azure AD non ha il concetto di password predefinite.

L'accesso aereo dati agli hub eventi è controllato tramite Azure AD con identità gestite o registrazioni di app, nonché firme di accesso condiviso. Le firme di accesso condiviso vengono utilizzate dai client che si connettono agli hub eventi e possono essere rigenerate in qualsiasi momento.

Comprendere le firme di accesso condiviso per gli hub eventi:Understand shared access signatures for Event Hubs:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizzare account amministrativi dedicati

**Linee guida**: Creare procedure operative standard per quanto miri all'utilizzo di account amministrativi dedicati. Usare Gestione identità e accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare consigli dal Centro sicurezza di Azure o da Criteri di Azure predefiniti, ad esempio:Additionally, to help you keep track of dedicated administrative accounts, you may use recommendations from Azure Security Center or built-in Azure Policies, such as:

- Alla sottoscrizione deve essere assegnato più di un proprietario

- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

Come usare il Centro sicurezza di Azure per monitorare identità e accesso (anteprima):How to use Azure Security Center to monitor identity and access (Preview):https://docs.microsoft.com/azure/security-center/security-center-identity-access

Come usare Criteri di Azure:How to use Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Usare Single Sign-On (SSO) con Azure Active Directory

**Indicazioni:** Microsoft Azure offre la gestione integrata del controllo di accesso per le risorse e le applicazioni basate su Azure Active Directory (AD). Un vantaggio chiave dell'uso di Azure AD con hub eventi di Azure è che non è più necessario archiviare le credenziali nel codice. Al contrario, è possibile richiedere un token di accesso OAuth 2.0 dalla piattaforma Microsoft Identity.Instead, you can request an OAuth 2.0 access token from the Microsoft Identity platform. Il nome della risorsa https://eventhubs.azure.net/per richiedere un token è . Azure AD autentica l'entità di sicurezza (un utente, un gruppo o un'entità servizio) che esegue l'applicazione. Se l'autenticazione ha esito positivo, Azure AD restituisce un token di accesso all'applicazione e l'applicazione può quindi usare il token di accesso per autorizzare la richiesta alle risorse di Hub eventi di Azure.If the authentication succeeds, Azure AD returns an access token to the application, and the application can then use the access token to authorize request to Azure Event Hubs resources.

Come autenticare un'applicazione con Azure AD per accedere alle risorse di Hub eventi:How to authenticate an application with Azure AD to access Event Hubs resources:https://docs.microsoft.com/azure/event-hubs/authenticate-application

Informazioni su SSO con Azure AD:Understanding SSO with Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Indicazioni:** abilitare Azure Active Directory Multi-Factor Authentication (MFA) e seguire i consigli di gestione delle identità e degli accessi del Centro sicurezza di Azure per proteggere le risorse abilitate per Hub eventi.

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Come monitorare l'identità e l'accesso all'interno del Centro sicurezza di Azure:How to monitor identity and access within Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizzare macchine dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

**Guida**: Utilizzare le workstation con accesso privilegiato (PAW) con Multi-Factor Authentication (MFA) configurato per accedere e configurare le risorse abilitate per Hub eventi.

Ulteriori informazioni sulle workstation con accesso privilegiato:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registrare e avvisare su attività sospette da account amministrativi

**Linee guida:** usare Azure Active Directory (AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verifica un'attività sospetta o non sicura nell'ambiente. Usare i rilevamenti dei rischi di Azure AD per visualizzare avvisi e report sul comportamento rischioso degli utenti. Per altre registrazioni, inviare avvisi di rilevamento dei rischi del Centro sicurezza di Azure in Monitoraggio di Azure e configurare avvisi/notifiche personalizzati usando i gruppi di azioni.

Come distribuire Privileged Identity Management (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Comprendere i rilevamenti dei rischi di Azure AD:Understand Azure AD risk detections:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Come configurare i gruppi di azioni per avvisi e notifiche personalizzati:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo da posizioni approvate

**Linee guida**: utilizzare percorsi denominati con accesso condizionale per consentire l'accesso solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche.



Come configurare i percorsi denominati in Azure:How to configure Named Locations in Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida:** usare Azure Active Directory (AD) come sistema di autenticazione e autorizzazione centrale per le risorse di Azure, ad esempio Hub eventi. Ciò consente il controllo degli accessi in base al ruolo alle risorse sensibili amministrative.

 Come creare e configurare un'istanza di Azure AD:How to create and configure an Azure AD instance:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

To learn about how Azure Event Hubs integrates with Azure Active Directory (AAD), see Authorize access to Event Hubs resources using Azure Active Directory:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rivedere e riconciliare regolarmente l'accesso degli utenti

**Indicazioni:** Azure Active Directory (AD) fornisce log che consentono di individuare gli account non aggiornati. Inoltre, usare Le revisioni di Azure Identity Access per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso degli utenti può essere rivisto regolarmente per assicurarsi che solo gli utenti giusti abbiano accesso continuo.

In altre firme di accesso condiviso degli hub eventi, ruota regolarmente.

Informazioni sui report di Azure AD:Understand Azure AD reporting:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Come usare le recensioni di azure Identity Access:How to use Azure Identity Access Reviews:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Informazioni sulle firme di accesso condiviso per gli hub eventi:Understanding shared access signatures for Event Hubs:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorare i tentativi di accesso agli account disattivati

**Linee guida:** si ha accesso alle origini dei log degli eventi di accesso, controllo e rischio di Azure Active Directory (AD), che consentono di eseguire l'integrazione con qualsiasi strumento SIEM/Monitoring.

È possibile semplificare questo processo creando impostazioni di diagnostica per gli account utente di Azure AD e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics.You can streamline this process by creating diagnostic settings for Azure AD user accounts and sending the audit logs and sign-in logs to a Log Analytics workspace. È possibile configurare gli avvisi di log desiderati all'interno di Log Analytics.You can configure desired log alerts within Log Analytics.

Come integrare i log attività di Azure in Monitoraggio di Azure:How to integrate Azure Activity Logs into Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Autorizzare l'accesso alle risorse di Hub eventi usando Azure Active Directory:Authorize access to Event Hubs resources using Azure Active Directory:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Scostamenti comportamento di avviso per l'accesso all'account

**Linee guida:** usare le funzionalità di protezione delle identità e di rilevamento dei rischi di Azure Active Directory per configurare le risposte automatiche alle azioni sospette rilevate relative alle risorse abilitate agli hub eventi. È consigliabile abilitare le risposte automatiche tramite Azure Sentinel per implementare le risposte di sicurezza dell'organizzazione.

Come visualizzare gli accessi rischiosi di Azure AD:How to view Azure AD risky sign-ins:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Come configurare e abilitare i criteri di rischio di Identity Protection:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Come eseguire l'onboarding di Azure Sentinel:How to onboard Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: Attualmente non disponibile; Customer Lockbox non è ancora supportato per gli hub eventi.

Elenco dei servizi supportati da Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Attualmente non disponibile

## <a name="data-protection"></a>Protezione dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Mantenere un inventario di informazioni sensibili

**Indicazioni:** usare i tag nelle risorse correlate agli hub eventi per tenere traccia delle risorse di Azure che archiviano o elaborano informazioni riservate.

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolare i sistemi che memorizzano o elaborano informazioni sensibili

**Linee guida:** Implementare sottoscrizioni e/o gruppi di gestione separati per lo sviluppo, il test e la produzione. Gli spazi dei nomi di Hub eventi devono essere separati dalla rete virtuale con gli endpoint del servizio abilitati e contrassegnati in modo appropriato.

È anche possibile proteggere lo spazio dei nomi degli hub eventi di Azure usando i firewall. Hub eventi di Azure supporta i controlli di accesso basati su IP per il supporto del firewall in ingresso. È possibile impostare le regole del firewall usando il portale di Azure, i modelli di Azure Resource Manager o tramite l'interfaccia della riga di comando di Azure o Azure PowerShell.You can set firewall rules by using the Azure portal, Azure Resource Manager templates, or through the Azure CLI or Azure PowerShell.

Come creare sottoscrizioni di Azure aggiuntive:How to create additional Azure subscriptions:https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare i gruppi di gestione:https://docs.microsoft.com/azure/governance/management-groups/create

Configurare le regole del firewall IP per gli spazi dei nomi di Hub eventi di Azure:Configure IP firewall rules for Azure Event Hubs namespaces:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Come creare una rete virtuale:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorare e bloccare il trasferimento non autorizzato di informazioni sensibili

**Indicazioni:** quando si usano macchine virtuali per accedere agli hub eventi, usare reti virtuali, endpoint di servizio, firewall di Hub eventi, gruppi di sicurezza di rete e tag di servizio per ridurre la possibilità di esfiltrazione dei dati.

Microsoft gestisce l'infrastruttura sottostante per gli hub eventi di Azure e ha implementato controlli rigorosi per prevenire la perdita o l'esposizione dei dati dei clienti.

Configurare le regole del firewall IP per gli spazi dei nomi di Hub eventi di Azure:Configure IP firewall rules for Azure Event Hubs namespaces:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Comprendere gli endpoint del servizio di rete virtuale con hub eventi di Azure:Understand Virtual Network Service Endpoints with Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Integrare hub eventi di Azure con Azure Private Link:Integrate Azure Event Hubs with Azure Private Link:https://docs.microsoft.com/azure/event-hubs/private-link-service

Comprendere i gruppi di sicurezza di rete e i tag di servizio:Understand Network Security Groups and Service Tags:https://docs.microsoft.com/azure/virtual-network/security-overview

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Crittografare tutte le informazioni sensibili in transito

**Linee guida:** Hub eventi di Azure applica le comunicazioni crittografate con TLS per impostazione predefinita. TLS versioni 1.0, 1.1 e 1.2 sono attualmente supportate. Tuttavia, TLS 1.0 e 1.1 si trovano su un percorso a livello di settore deprecazione, quindi usa TLS 1.2 se possibile.

Per comprendere le funzionalità di sicurezza degli hub eventi, vedere Sicurezza di rete:To understand security features of Event Hubs, see Network security:https://docs.microsoft.com/azure/event-hubs/network-security

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilizzare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida:** le funzionalità di identificazione dei dati, classificazione e prevenzione delle perdite non sono ancora disponibili per gli hub eventi di Azure.Guidance: Data identification, classification, and loss prevention features are not yet available for Azure Event Hubs. Implementare una soluzione di terze parti, se necessario, ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse4.6: Usare il controllo degli accessi in base al ruolo di

**Indicazioni:** Hub eventi di Azure supporta l'uso di Azure Active Directory (AD) per autorizzare le richieste alle risorse di Hub eventi. Con Azure AD è possibile usare il controllo degli accessi in base al ruolo per concedere autorizzazioni a un'entità di sicurezza, che può essere un utente o un'entità servizio dell'applicazione.

Informazioni sul controllo degli accessi in base al ruolo di Azure AD e dei ruoli disponibili per gli hub eventi di Azure:Understand Azure AD RBAC and available roles for Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizzare la prevenzione della perdita di dati basata su host per applicare il controllo degli accessi

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per gli hub eventi e ha implementato controlli rigorosi per prevenire la perdita o l'esposizione dei dati dei clienti.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografare le informazioni sensibili inattivi

**Indicazioni:** Hub eventi di Azure supporta l'opzione di crittografare i dati inattivi con chiavi gestite da Microsoft o chiavi gestite dal cliente. Questa funzionalità consente di creare, ruotare, disabilitare e revocare l'accesso alle chiavi gestite dal cliente usate per crittografare i dati di Hub eventi di Azure inattivi.

Come configurare le chiavi gestite dal cliente per crittografare gli hub eventi di Azure:How to configure customer-managed keys for encrypting Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e avvisare le modifiche alle risorse critiche di Azure4.9: Log and alert on changes to critical Azure resources

**Indicazioni:** usare Monitoraggio di Azure con il log attività di Azure per creare avvisi per quando vengono apportate modifiche alle istanze di produzione di Hub eventi di Azure e ad altre risorse critiche o correlate.

Come creare avvisi per gli eventi del log attività di Azure:How to create alerts for Azure Activity Log events:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per ulteriori informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Eseguire strumenti automatici di scansione delle vulnerabilità

**Orientamento**: Non applicabile; Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano gli hub eventi.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuire una soluzione di gestione automatica delle patch del sistema operativo

**Orientamento**: Non applicabile; Microsoft esegue la gestione delle patch sui sistemi sottostanti che supportano gli hub eventi.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuire una soluzione di gestione automatica delle patch software di terze parti

**Orientamento**: Non applicabile; benchmark è destinato alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Confrontare le scansioni delle vulnerabilità back-to-back

**Orientamento**: Non applicabile; Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano gli hub eventi.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilizzare un processo di valutazione del rischio per assegnare priorità alla correzione delle vulnerabilità individuate

**Orientamento**: Non applicabile; Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano gli hub eventi.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per ulteriori informazioni, vedere [Controllo di sicurezza: inventario e gestione delle risorse](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Usare Azure Asset Discovery

**Indicazioni:** usare Azure Resource Graph per eseguire query e individuare tutte le risorse (inclusi gli spazi dei nomi di Hub eventi di Azure) all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni appropriate (di lettura) nel tenant e di essere in grado di enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Come creare query con Azure Graph:How to create queries with Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Come visualizzare le sottoscrizioni di Azure:How to view your Azure Subscriptions:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Informazioni sul controllo degli accessi in base al ruolo di Azure:Understandhttps://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Gestire i metadati delle risorse

**Linee guida:** applicare tag alle risorse di Azure che assiecano metadati per organizzarli logicamente in una tassonomia.

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminare risorse di Azure non autorizzate

**Linee guida:** usare il tagging, i gruppi di gestione e sottoscrizioni separate, se appropriato, per organizzare e tenere traccia degli spazi dei nomi di Hub eventi di Azure e delle risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate dalla sottoscrizione in modo tempestivo.

Come creare sottoscrizioni di Azure aggiuntive:How to create additional Azure subscriptions:https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare i gruppi di gestione:https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Gestire un inventario delle risorse di Azure approvate e dei titoli software

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo e ad Azure nel suo complesso.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorare le risorse di Azure non approvate

**Linee guida:** usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:Guidance : Use Azure policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare inoltre Azure Resource Graph per eseguire query/individuare risorse all'interno delle sottoscrizioni.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare query con Azure Graph:How to create queries with Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorare le applicazioni software non approvate all'interno delle risorse di calcolo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Rimuovere le risorse e le applicazioni software di Azure non approvate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo e ad Azure nel suo complesso.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="68-use-only-approved-applications"></a>6.8: Utilizzare solo applicazioni approvate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="69-use-only-approved-azure-services"></a>6.9: Usare solo i servizi di Azure approvati

**Linee guida:** usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:Guidance : Use Azure policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come negare un tipo di risorsa specifico con Criteri di Azure:How to deny a specific resource type with Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementare l'elenco delle applicazioni approvate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Limitare la capacità degli utenti di interagire con Azure Resource Manager tramite script</div>

**Guida:** configurare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app "Gestione di Microsoft Azure".

Come configurare l'accesso condizionale per bloccare l'accesso a Azure Resource Manager:How to configure Conditional Access to block access to Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitare la capacità degli utenti di eseguire script all'interno di risorse di calcolo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separare fisicamente o logicamente le applicazioni ad alto rischio

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle applicazioni Web in esecuzione nel servizio app di Azure o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per ulteriori informazioni, vedere [Controllo di sicurezza: configurazione protetta](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Stabilire configurazioni sicure per tutte le risorse di Azure7.1: Establish secure configurations for all Azure resources

**Linee guida:** definire e implementare configurazioni di sicurezza standard per le distribuzioni di Hub eventi di Azure.Guidance: Define and implement standard security configurations for your Azure Event Hubs deployments. Usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.EventHub" per creare criteri personalizzati per controllare o applicare configurazioni. È anche possibile usare le definizioni dei criteri predefinite per gli hub eventi di Azure, ad esempio:You may also make using of built-in policy definitions for Azure Event Hubs such as:

- È consigliabile abilitare i log di diagnostica in Hub eventi

- Gli hub eventi devono usare un endpoint servizio di rete virtuale

Criteri predefiniti di Azure per lo spazio dei nomi Hub eventi:Azure Built-in Policy for Event Hubs namespace:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

Come visualizzare gli alias dei criteri di Azure disponibili:How to view available Azure Policy Aliases:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Stabilire configurazioni sicure del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Gestire le configurazioni delle risorse di Azure protette

**Linee guida:** usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure tra le risorse abilitate per Hub eventi. 

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Per altre informazioni sugli effetti dei criteri di Azure:For more information about the Azure Policy Effects:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Mantenere le configurazioni sicure del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Archiviare in modo sicuro la configurazione delle risorse di Azure7.5: Securely store configuration of Azure resources

**Indicazioni:** se si usano definizioni di criteri di Azure personalizzate per gli hub eventi o le risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

Come archiviare il codice in DevOps di Azure:How to store code in Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos Documentation:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Memorizzare in modo sicuro immagini personalizzate del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida:** usare gli alias dei criteri di Azure nello spazio dei nomi "Microsoft.EventHub" per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Inoltre, sviluppare un processo e una pipeline per la gestione delle eccezioni dei criteri.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementare il monitoraggio automatico della configurazione per i servizi di Azure7.9: Implement automated configuration monitoring for Azure services

**Linee guida:** usare gli alias dei criteri di Azure nello spazio dei nomi "Microsoft.EventHub" per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [deploy if not exist] per applicare automaticamente le configurazioni per le distribuzioni di Hub eventi di Azure e le risorse correlate.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementare il monitoraggio automatico della configurazione per i sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gestire i segreti di Azure in modo sicuro

**Linee guida:** per le macchine virtuali o le applicazioni Web di Azure in esecuzione nel servizio app di Azure usate per accedere agli hub eventi, usare l'identità del servizio gestito insieme all'insieme a Archiviazione chiave di Azure per semplificare e proteggere la gestione delle firme di accesso condiviso per le distribuzioni di Hub eventi di Azure.Guidance : For Azure virtual machines or web applications running on Azure App Service being used to access your event hubs, use Managed Service Identity in conjunction with Azure Key Vault to simplify and secure shared access signature management for your Azure Event Hubs deployments. Verificare che l'eliminazione temporanea dell'insieme di credenziali delle chiavi sia abilitata.

Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse di Hub eventi:Authenticate a managed identity with Azure Active Directory to access Event Hubs resources:https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Configurare le chiavi gestite dal cliente per gli hub eventi:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Come integrare con le identità gestite di Azure:How to integrate with Azure Managed Identities:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Come creare un insieme di credenziali delle chiavi:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Come fornire l'autenticazione key Vault con un'identità gestita:How to provide Key Vault authentication with a managed identity:https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gestire le identità in modo sicuro e automatico

**Linee guida:** per le macchine virtuali o le applicazioni Web di Azure in esecuzione nel servizio app di Azure usate per accedere agli hub eventi, usare l'identità del servizio gestito insieme a Archiviazione chiave di Azure per semplificare e proteggere gli hub eventi di Azure.Guidance : For Azure virtual machines or web applications running on Azure App Service being used to access your event hubs, use Managed Service Identity in conjunction with Azure Key Vault to simplify and secure Azure Event Hubs. Verificare che l'eliminazione temporanea dell'insieme di credenziali delle chiavi sia abilitata.

Usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (AD). Managed Identities consente di eseguire l'autenticazione a qualsiasi servizio che supporta l'autenticazione di Azure AD, incluso l'insieme di credenziali delle chiavi di Azure, senza credenziali nel codice.

Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse degli hub eventi:Authenticate a managed identity with Azure Active Directory to access Event Hubs Resources:https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Configurare le chiavi gestite dal cliente per gli hub eventi:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Come configurare le identità gestite:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Come integrare con le identità gestite di Azure:How to integrate with Azure Managed Identities:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminare l'esposizione di credenziali indesiderate

**Linee guida**: Implementare Credential Scanner per identificare le credenziali all'interno del codice. Credential Scanner will also encourage moving discovered credentials to more secure locations such as Azure Key Vault.

Come configurare Credential Scanner:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="malware-defense"></a>Difesa da malware

*Per ulteriori informazioni, vedere [Controllo di sicurezza: Difesa da malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilizzare software antimalware gestito centralmente

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

L'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, servizio app di Azure), tuttavia non viene eseguito sul contenuto dei clienti.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: File pre-scansione da caricare in risorse di Azure non di calcolo

**Linee guida:** eseguire la preanalisi di qualsiasi contenuto caricato in risorse di Azure non di calcolo, ad esempio Hub eventi di Azure, Servizio app, Archiviazione data lake, Archiviazione BLOB, Database di Azure per PostgreSQL e così via. Microsoft non può accedere ai dati in questi casi.

L'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Cache di Azure per Redis), tuttavia non viene eseguito sul contenuto dei clienti.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Verificare che il software antimalware e le firme siano aggiornate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="data-recovery"></a>Ripristino dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatici regolari

**Linee guida:** configurare il ripristino di emergenza geografico per gli hub eventi di Azure.Guidance : Configure geo-disaster recovery for Azure Event Hubs. In caso di tempo di inattività di interi data center o aree di Azure (se non vengono usate zone di disponibilità), è essenziale che l'elaborazione dei dati continui in un'area o in un data center diverso. Di conseguenza, il ripristino di emergenza geografico e la replica geografica sono funzionalità importanti per qualsiasi azienda. Il servizio Hub eventi di Azure supporta il ripristino di emergenza geografico e la replica geografica a livello di spazio dei nomi. 

Informazioni sul ripristino di emergenza geografico per gli hub eventi di Azure:Understand geo-disaster recovery for Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida:** Hub eventi di Azure offre la crittografia dei dati inattivi con Crittografia del servizio di archiviazione di Azure (Azure SSE). Gli hub eventi si basano su Archiviazione di Azure per archiviare i dati e, per impostazione predefinita, tutti i dati archiviati con Archiviazione di Azure vengono crittografati usando chiavi gestite da Microsoft.Event Hubs relies on Azure Storage to store the data and by default, all the data that is stored with Azure Storage is encrypted using Microsoft-managed keys. Se si usa l'insieme di credenziali delle chiavi di Azure per archiviare le chiavi gestite dal cliente, assicurarsi che i backup automatici delle chiavi.

Garantire backup automatici regolari dei segreti dell'insieme di credenziali delle chiavi con il seguente comando di PowerShell: Backup-AzKeyVaultSecret

Come configurare le chiavi gestite dal cliente per crittografare i dati di Hub eventi di Azure inattivi:How to configure customer-managed keys for encrypting Azure Event Hubs data at rest:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Come eseguire il backup dei segreti dell'insieme di credenziali delle chiavi:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: Verificare il ripristino delle chiavi gestite dal cliente di cui è stato eseguito il backup.

 

Come ripristinare le chiavi dell'insieme di credenziali delle chiavi in Azure:How to restore key vault keys in Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dai clienti

**Guida:** abilitare l'eliminazione temporanea nell'insieme di credenziali delle chiavi per proteggere le chiavi da eliminazioni accidentali o dannose. Hub eventi di Azure richiede che le chiavi gestite dal cliente abbiano la configurazione di Eliminazione temporanea e Non eliminare.

Configurare l'eliminazione temporanea per l'account di archiviazione di Azure usato per l'acquisizione dei dati degli hub eventi. Si noti che questa funzionalità non è ancora supportata per Azure Data Lake Storage Gen 2.Note that this feature isn't supported for Azure Data Lake Storage Gen 2 yet.

Come abilitare l'eliminazione temporanea nell'insieme di credenziali delle chiavi:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

Impostare un insieme di credenziali delle chiavi con le chiavi:Set up a key vault with keys:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Eliminazione temporanea per i BLOB di Archiviazione di Azure:Soft delete for Azure Storage blobs:https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Creare una guida alla risposta agli incidenti

**Linee guida**: Assicurarsi che siano presenti piani di risposta agli incidenti scritti che definiscono i ruoli del personale e le fasi della gestione/gestione degli incidenti.

Come configurare le automazioni del flusso di lavoro all'interno del Centro sicurezza di Azure:How to configure Workflow Automations within Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Creare una procedura di assegnazione dei punteggi degli eventi imprevisti e di assegnazione delle priorità

**Guida:** il Centro sicurezza assegna un livello di gravità agli avvisi, per consentire di assegnare una priorità all'ordine di assistenza a ogni avviso, in modo che quando una risorsa viene compromessa, è possibile raggiungerlo immediatamente. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sull'analita utilizzato per emettere l'avviso, nonché il livello di probabilità che vi sia stato un intento dannoso dietro l'attività che ha portato all'avviso.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Testare le procedure di risposta alla sicurezza

**Linee guida**: Eseguire esercizi per testare le capacità di risposta agli incidenti dei sistemi a una cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

Fare riferimento alla pubblicazione del NIST: Guida ai programmi di test, formazione ed eserciziper piani e funzionalità IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornire i dettagli di contatto degli eventi imprevisti di sicurezza e configurare le notifiche di avviso per gli incidenti di sicurezza

**Linee guida:** le informazioni di contatto relative agli eventi imprevisti di protezione verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che i dati del cliente sono stati utilizzati da una parte non legale o non autorizzata.  Esaminare gli incidenti dopo il fatto per assicurarsi che i problemi vengano risolti. 

Come impostare il contatto di sicurezza del centro di sicurezza di Azure:How to set the Azure Security Center Security Contact:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporare avvisi di sicurezza nel sistema di risposta agli incidenti

**Indicazioni:** esportare gli avvisi e i suggerimenti del Centro sicurezza di Azure usando la funzionalità di esportazione continua. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi Sentinel.You may use the Azure Security Center data connector to stream the alerts Sentinel.

Come configurare l'esportazione continua:https://docs.microsoft.com/azure/security-center/continuous-export

Come trasmettere gli avvisi in Azure Sentinel:How to stream alerts into Azure Sentinel:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizzare la risposta agli avvisi di sicurezza

**Indicazioni:** usare la funzionalità automazione flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite "App per la logica" in avvisi e suggerimenti per la sicurezza.

Come configurare l'automazione del flusso di lavoro e le app per la logica:How to configure Workflow Automation and Logic Apps:https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per ulteriori informazioni, vedere Controllo di sicurezza: test di [penetrazione ed Esercizi del team rosso](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza entro 60 giorni

**Linee guida**: Seguire le regole di coinvolgimento Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.
Ulteriori informazioni sulla strategia di Microsoft e sull'esecuzione di Red Teaming e sui test di penetrazione dei siti in tempo reale su infrastruttura cloud, servizi e applicazioni gestiti Microsoft sono disponibili qui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

## <a name="next-steps"></a>Passaggi successivi

- Vedere il benchmark di sicurezza di [AzureSee](https://docs.microsoft.com/azure/security/benchmarks/overview) the Azure Security Benchmark
- Altre informazioni sulle linee di base per la sicurezza di AzureLearn more about [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
