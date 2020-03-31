---
title: Azure Security Baseline for Data Explorer
description: Azure Security Baseline for Data Explorer
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 92a607e4c17a16d07f07c97f626ed98a213d509b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289719"
---
# <a name="azure-security-baseline-for-data-explorer"></a>Azure Security Baseline for Data Explorer

La linea di base della sicurezza di Azure per Esplora dati contiene suggerimenti che consentono di migliorare il livello di sicurezza della distribuzione.

La linea di base per questo servizio è ricavata da [Azure Security Benchmark versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce consigli su come proteggere le soluzioni cloud in Azure con le indicazioni sulle procedure consigliate.

Per altre informazioni, vedere [Panoramica delle linee di base](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)della sicurezza di Azure.For more information, see Azure Security Baselines overview .

## <a name="network-security"></a>Sicurezza di rete

*Per ulteriori informazioni, vedere [Controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale1.1: Protect resources using Network Security Groups or Azure Firewall on your Virtual Network

**Linee guida:** Azure Data Explorer supporta la distribuzione di un cluster in una subnet nella rete virtuale. Questa funzionalità consente di applicare regole del gruppo di sicurezza di rete (NSG) al traffico del cluster di Azure Data Explorer, connettere la rete locale alla subnet del cluster di Azure Data Explorer e proteggere le origini di connessione dati (Hub eventi e Griglia di eventi) con endpoint di servizio.

Come distribuire il cluster di Azure Data Explorer in una rete virtuale:How to deploy your Azure Data Explorer cluster into a virtual network:https://docs.microsoft.com/azure/data-explorer/vnet-deployment


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NICS

**Guida:** abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione per il controllo del traffico.

Abilitazione dei registri di flusso del gruppo di sicurezza di rete:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Informazioni sulla sicurezza di rete fornita dal Centro sicurezza di Azure:Understanding Network Security provided by Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteggere le applicazioni Web critiche

**Orientamento**: Non applicabile; L'opzione È destinata alle applicazioni Web in esecuzione nel servizio app di Azure o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Guida:** abilitare Azure DDoS Protection Standard nella rete virtuale che protegge i cluster di Data Explorer per la protezione dagli attacchi DDoS. Usare Azure Security Center Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP Internet dannosi o inutilizzati noti.

Come configurare la protezione DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Informazioni su Azure Security Center Integrated Threat Intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Registrare pacchetti di rete e registri di flusso

**Linee guida:** abilitare i log di flusso nei gruppi di sicurezza di rete usati per proteggere il cluster di Azure Data Explorer e inviare i log in un account di archiviazione per il controllo del traffico.

Abilitazione dei registri di flusso del gruppo di sicurezza di rete:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Implementazione di sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete

**Orientamento**: Non applicabile; Questo controllo viene eseguito in corrispondenza dell'endpoint o del firewall.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: Gestire il traffico verso le applicazioni Web

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle applicazioni Web in esecuzione nel servizio app di Azure o alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e l'overhead amministrativo delle regole di sicurezza di rete

**Indicazioni:** usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nei firewall di Azure associati ai cluster di Azure Data Explorer.Guidance: Use Virtual Network Service Tags to define network access controls on Network Security Groups or Azure Firewalls associated with your Azure Data Explorer clusters. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag del servizio (ad esempio, ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi degli indirizzi inclusi nel tag del servizio e aggiorna automaticamente il tag del servizio quando gli indirizzi cambiano.

Comprensione e utilizzo dei tag di servizio:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Requisiti di configurazione dei tag di servizio per Azure Data Explorer:Service Tags configuration requirements for Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Mantenere le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida:** cliente per definire e implementare configurazioni di sicurezza standard per le risorse di rete con Criteri di Azure.Guidance: Customer to define and implement standard security configurations for network resources with Azure Policy.

Il cliente può anche usare Azure Blueprints per semplificare le distribuzioni di Azure su larga scala creando pacchetti di elementi dell'ambiente chiave, ad esempio modelli ARM, controlli RBAC e criteri, in una singola definizione del blueprint. Applica facilmente il blueprint a nuove sottoscrizioni e ambienti e ottimizza il controllo e la gestione tramite il controllo delle versioni.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare un blueprint di Azure:How to create an Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regole di configurazione del traffico dei documenti

**Linee guida:** usare i tag per i gruppi di sicurezza di rete (NSG) e altre risorse correlate alla sicurezza di rete e al flusso di traffico per i cluster di Esplora dati. Per le singole regole del gruppo di sicurezza di rete, utilizzare il campo "Descrizione" per specificare le esigenze e/o la durata dell'azienda (ecc.) per tutte le regole che consentono il traffico da/verso una rete.

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilizzare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare Criteri di Azure per convalidare (o correggere) la configurazione per le risorse di rete.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizzare le origini di sincronizzazione dell'ora approvate

**Indicazioni:** Microsoft gestisce le origini ora per le risorse di Azure, i clienti possono aggiornare la sincronizzazione dell'ora per le distribuzioni di calcolo di proprietà del cliente.

Come configurare la sincronizzazione dell'ora per le risorse di calcolo di Azure:How to configure time synchronization for Azure compute resources:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurare la gestione dei registri di protezione centrale

**Indicazioni:** Azure Data Explorer usa i log di diagnostica per informazioni dettagliate sui successi e gli errori di inserimento. È possibile esportare i log delle operazioni in Archiviazione di Azure, Hub eventi o Log Analytics per monitorare lo stato di inserimento.

Come monitorare le operazioni di inserimento di Azure Data Explorer:How to monitor Azure Data Explorer ingestion operations:

https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Come inserire ed eseguire query sui dati di monitoraggio in Azure Data Explorer:How to ingest and query monitoring data in Azure Data Explorer:

https://docs.microsoft.com/azure/data-explorer/ingest-data-no-code

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Abilitare la registrazione di controllo per le risorse di Azure2.3: Abilitare la registrazione di controllo per le risorse di Azure2.

**Guida:** abilitare le impostazioni di diagnostica per Azure Data Explorer per l'accesso e la registrazione per eseguire operazioni e registrazione specifiche del servizio. I log attività di Azure in Monitoraggio di Azure, che include la registrazione di alto livello sulla risorsa, sono abilitati per impostazione predefinita.

Come monitorare le operazioni di inserimento di Azure Data Explorer:How to monitor Azure Data Explorer ingestion operations:https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Come raccogliere i log e le metriche della piattaforma con Monitoraggio di Azure:How to collect platform logs and metrics with Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Panoramica dei log della piattaforma Azure:Overview of Azure platform logs:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: Raccolta dei registri di protezione dal sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dello spazio di archiviazione del registro di protezione

**Linee guida:** in Monitoraggio di Azure impostare il periodo di conservazione dell'area di lavoro di Log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archiviazione.

Come impostare i parametri di conservazione dei log per le aree di lavoro di Log Analytics:How to set log retention parameters for Log Analytics Workspaces:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare e rivedere i registri

**Guida**: Analizzare e monitorare i log per verificare la ricerca di comportamenti anomali ed esaminare regolarmente i risultati. Dopo aver abilitato le impostazioni di diagnostica per Azure Data Explorer, usare l'area di lavoro Log Analytics di Monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log.

Informazioni sull'area di lavoro di Log Analytics:Understanding Log Analytics Workspace:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Come eseguire query personalizzate in Monitoraggio di Azure:How to perform custom queries in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Abilitare gli avvisi per attività anomale

**Orientamento**: Non applicabile; Azure Data Explorer non dispone di questa funzionalità.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizzare la registrazione antimalware

**Orientamento**: Non applicabile; Esplora dati di Azure non elabora la registrazione antimalware.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: Abilitare la registrazione delle query DNS

**Guida:** Non applicabile: la query DNS non è una funzione di Azure Data Explorer.Guidance : Not applicable: DNS query is not a function of Azure Data Explorer.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: Abilitare la registrazione di controllo dalla riga di comando

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per ulteriori informazioni, vedere [Controllo di sicurezza: controllo delle identità e degli accessi](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Gestire l'inventario dei conti amministrativi

**Linee guida:** in Esplora dati di Azure i ruoli di sicurezza definiscono le entità di sicurezza (utenti e applicazioni) che dispongono delle autorizzazioni per operare su una risorsa protetta, ad esempio un database o una tabella, e le operazioni consentite.  È possibile utilizzare la query Kusto per elencare i principi nel ruolo di amministratore per i cluster e i database di Azure Data Explorer.You can use Kusto query to list principles in the admin role for the Azure Data Explorer clusters and databasess.
Gestione dei ruoli di sicurezza in Azure Data Explorer usando la query Kusto:Security roles management in Azure Data Explorer using Kusto query:https://docs.microsoft.com/azure/kusto/management/security-roles



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Modificare le password predefinite dove applicabile

**Indicazioni:** Azure AD non ha il concetto di password predefinite. Altre risorse di Azure che richiedono una password forzala la creazione di una password con requisiti di complessità e una lunghezza minima della password, che varia a seconda del servizio. L'utente è responsabile delle applicazioni di terze parti e dei servizi del marketplace che possono utilizzare password predefinite.


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: Verificare l'utilizzo di account amministrativi dedicati

**Linee guida**: Cliente per la creazione di procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare Gestione identità e accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

I clienti possono anche abilitare un just-in-time / Just-Enough-Access utilizzando i ruoli privilegiati di Azure AD Gestione identità per i servizi Microsoft e Azure ARM. 

Che cos'è Azure AD Privileged Identity Management?:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilizzare Single Sign-On (SSO) con Azure Active Directory

**Linee guida:** ove possibile, cliente di usare SSO con Azure Active Directory (Azure AD) anziché configurare singole credenziali autonome per servizio. Usare i consigli per la gestione delle identità e degli accessi del Centro sicurezza di Azure.Use Azure Security Center Identity and Access Management recommendations.

Informazioni su SSO con Azure AD:Understanding SSO with Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory.

**Indicazioni:** abilitare l'autenticazione a più fattori (MFA) di Azure Active Directory (Azure AD) e seguire i consigli per la gestione delle identità e degli accessi del Centro sicurezza di Azure.Guidance: Enable Azure Active Directory (Azure AD) multi-factor authentication (MFA) and follow Azure Security Center Identity and Access Management recommendations.

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted 

Come monitorare l'identità e l'accesso all'interno del Centro sicurezza di Azure:How to monitor identity and access within Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="36-use-of-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizzo di macchine dedicate (workstation con accesso privilegiato) per tutte le attività amministrative

**Guida:** usare PAW (workstation con accesso con privilegi) con l'autenticazione a più fattori configurata per accedere alle risorse di Azure e configurarle.

Ulteriori informazioni sulle workstation con accesso privilegiato:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-on-administrative-accounts"></a>3.7: Registro e avviso per attività sospette sugli account amministrativi

**Indicazioni:** usare i report di sicurezza di Azure Active Directory (Azure AD) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e l'attività di accessoUse Azure Security Center to monitor identity and access activity

Come identificare gli utenti di Azure AD contrassegnati per l'attività rischiosa:How to identify Azure AD users flagged for risky activity:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Come monitorare l'identità degli utenti e l'attività di accesso nel Centro sicurezza di Azure:How to monitor users identity and access activity in Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo da posizioni approvate

**Linee guida**: Cliente utilizza percorsi denominati con accesso condizionale per consentire l'accesso solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche.

Come configurare i percorsi denominati in Azure:How to configure named locations in Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="39-utilize-azure-active-directory"></a>3.9: Utilizzare Azure Active Directory

**Linee guida:** Azure Active Directory (Azure AD) è il metodo preferito per l'autenticazione in Azure Data Explorer.Guidance: Azure Active Directory (Azure AD) is the preferred method for authenticating to Azure Data Explorer. Supporta diversi scenari di autenticazione:

Autenticazione utente (accesso interattivo): utilizzata per autenticare le entità utente.

Autenticazione dell'applicazione (accesso non interattivo): utilizzata per autenticare i servizi e le applicazioni che devono essere eseguiti/autenticati senza la presenza di alcun utente umano.

Panoramica del controllo di accesso di Esplora dati di AzureAzure Data Explorer Access Control Overview:https://docs.microsoft.com/azure/kusto/management/access-control

Autenticazione con Azure Active Directory:Authenticating with Azure Active Directory:https://docs.microsoft.com/azure/kusto/management/access-control/aad



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rivedere e riconciliare regolarmente l'accesso degli utenti

**Indicazioni:** Azure Active Directory (Azure AD) fornisce log che consentono di individuare gli account non aggiornati. Inoltre, usare Le revisioni di Azure Identity Access per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso dell'utente può essere rivisto regolarmente per assicurarsi che solo gli utenti giusti abbiano accesso continuo. 

Procedura di autenticazione con Azure AD per l'accesso ad Azure Data Explorer:How-To Authenticate with Azure AD for Azure Data Explorer Access:https://docs.microsoft.com/azure/kusto/management/access-control/how-to-authenticate-with-aad

Azure AD Reporting:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Come usare le recensioni di azure Identity Access:How to use Azure Identity Access Reviews:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorare i tentativi di accesso agli account disattivati

**Linee guida:** è possibile usare le origini Attività di accesso, controllo e log eventi di rischio di Azure Active Directory (Azure AD) per il monitoraggio che consente di integrarsi con qualsiasi strumento di monitoraggio delle informazioni di sicurezza e degli eventi (SIEM)/monitoraggio.

 È possibile semplificare questo processo creando impostazioni di diagnostica per gli account utente di Azure Active Directory, inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics.You can streamline this process by creating Diagnostic Settings for Azure Active Directory user accounts, sending the audit logs and sign-in logs to a Log Analytics Workspace. Cliente per configurare gli avvisi desiderati all'interno dell'area di lavoro di Log Analytics.

Come integrare i log attività di Azure in Monitoraggio di Azure:How to integrate Azure Activity Logs into Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Deviazione del comportamento di accesso dell'account

**Linee guida:** usare la funzionalità Rilevamenti rischi di Azure Active Directory (Azure AD) e Protezione identità per configurare le risposte automatiche alle azioni sospette rilevate relative alle identità utente. Inoltre, è possibile inserire dati in Azure Sentinel per ulteriori indagini.

Come visualizzare gli accessi rischiosi di Azure AD:How to view Azure AD risky sign-ins:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Come configurare e abilitare i criteri di rischio di Identity Protection:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: Negli scenari di supporto in cui Microsoft deve accedere ai dati dei clienti, Customer Lockbox fornisce un'interfaccia che consente ai clienti di esaminare e approvare o rifiutare le richieste di accesso ai dati dei clienti.

Informazioni su Customer Lockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

## <a name="data-protection"></a>Protezione dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Mantenere un inventario di informazioni sensibili

**Linee guida:** usare i tag per tenere traccia delle risorse di Azure che archiviano o elaborano informazioni riservate.

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolare i sistemi che memorizzano o elaborano informazioni sensibili

**Linee guida:** Implementare sottoscrizioni e/o gruppi di gestione separati per lo sviluppo, il test e la produzione. Azure Data Explorer clusters should be separated from other resources by virtual network/subnet, tagged appropriately, and secured within an network security group (NSG) or Azure Firewall. I cluster di Esplora dati che archiviano o elaborano dati sensibili devono essere sufficientemente isolati.

Come creare sottoscrizioni di Azure aggiuntive:How to create additional Azure subscriptions:https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare i gruppi di gestione:https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Come distribuire il cluster di Azure Data Explorer in una rete virtuale:How to deploy your Azure Data Explorer cluster into a virtual network:https://docs.microsoft.com/azure/data-explorer/vnet-deployment

Come creare un gruppo di sicurezza di base con una configurazione di sicurezza:How to create an NSG with a Security Config:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorare e bloccare il trasferimento non autorizzato di informazioni sensibili

**Orientamento**: Non applicabile; Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Crittografa tutte le informazioni sensibili in transito

**Linee guida:** il cluster Azure Data Explorer negozia TLS 1.2 per impostazione predefinita. Verificare che tutti i client che si connettono alle risorse di Azure siano in grado di negoziare TLS 1.2 o versione successiva.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

### <a name="45-divuse-an-active-discovery-tool-to-identify-sensitive-datadiv"></a>4.5: <div>Utilizzo di uno strumento di individuazione attivo per identificare i dati sensibiliUse an Active Discovery Tool to Identify Sensitive Data</div>

**Linee guida:** le funzionalità di identificazione dei dati, classificazione e prevenzione delle perdite non sono ancora disponibili per Azure Data Explorer.Guidance: Data identification, classification, and loss prevention features are not yet available for Azure Data Explorer. Implementare una soluzione di terze parti, se necessario, ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="46-divuse-azure-rbac-to-control-access-to-resourcesdiv"></a>4.6: <div>Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorseUse Azure RBAC to control access to</div>

**Indicazioni:** Azure Data Explorer consente di controllare l'accesso a database e tabelle usando un modello di controllo degli accessi in base al ruolo. In questo modello viene eseguito il mapping ai ruoli delleentità di sicurezza (utenti, gruppi e app). Le entità di sicurezza possono accedere alle risorse in base ai ruoli che vengono loro assegnati.

Elenco di ruoli, autorizzazioni e istruzioni su come configurare il controllo degli accessi in base al ruolo per Azure Data Explorer:List of roles and permissions and instructions on how to configure RBAC for Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/manage-database-permissions


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizzare Prevenzione perdita di dati basata su host per applicare il controllo degli accessi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per Azure Data Explorer e ha implementato controlli rigorosi per evitare la perdita o l'esposizione dei dati dei clienti.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografa informazioni sensibili inattivi

**Linee guida:** Crittografia disco di Azure consente di proteggere e proteggere i dati per soddisfare gli impegni di conformità e sicurezza dell'organizzazione. Fornisce la crittografia del volume per il sistema operativo e i dischi dati delle macchine virtuali del cluster. Si integra anche con l'insieme di credenziali delle chiavi di Azure che consente di controllare e gestire le chiavi e i segreti di crittografia del disco e di garantire che tutti i dati nei dischi delle macchine virtuali vengano crittografati inattivi mentre si trova in Archiviazione di Azure.It also integrate with Azure Key Vault which allows us to control and manage the disk encryption keys and secrets, and ensure all data on the VM disks is encrypted at rest while in Azure Storage.

Come abilitare la crittografia inattivi per i cluster di Azure Data Explorer:How to enable encryption at rest for Azure Data Explorer clusters:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e avvisare le modifiche alle risorse critiche di Azure4.9: Log and alert on changes to critical Azure resources

**Indicazioni:** usare Monitoraggio di Azure con il log attività di Azure per creare avvisi per l'organizzazione delle modifiche a livello di risorsa nei cluster di Azure Data Explorer.Guidance: Use Azure Monitor with the Azure Activity Log to create alerts for when resource-level changes take on your Azure Data Explorer clusters.

Come creare avvisi per gli eventi del log attività di Azure:How to create alerts for Azure Activity Log events:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Come creare avvisi per gli eventi del log attività di Azure:How to create alerts for Azure Activity Log events:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per ulteriori informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Eseguire strumenti di scansione delle vulnerabilità automatizzati

**Linee guida:** seguire le raccomandazioni del Centro sicurezza di Azure per proteggere le risorse di Azure Data Explorer.Guidance: Follow recommendations from Azure Security Center on securing your Azure Data Explorer resources.

Microsoft esegue inoltre la gestione delle vulnerabilità nei sistemi sottostanti che supportano Azure Data Explorer.

Informazioni sui consigli del Centro sicurezza di Azure:Understand Azure Security Center recommendations:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuire una soluzione di gestione automatica delle patch del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuire una soluzione di gestione automatica delle patch software di terze parti

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Confronto delle scansioni delle vulnerabilità back-to-back

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="55-utilize-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilizzare un processo di valutazione del rischio per assegnare priorità alla correzione delle vulnerabilità individuate.

**Indicazioni:** usare le classificazioni dei rischi predefinite (Secure Score) fornite dal Centro sicurezza di Azure.Guidance: Use the default risk ratings (Secure Score) provided by Azure Security Center.
Informazioni sul punteggio sicuro del Centro sicurezza di Azure:Understand Azure Security Center Secure Score:https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per ulteriori informazioni, vedere [Controllo di sicurezza: inventario e gestione delle risorse](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-utilize-azure-asset-discovery"></a>6.1: Utilizzare Azure Asset Discovery

**Linee guida:** usare Azure Resource Graph per eseguire query/individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) all'interno delle sottoscrizioni. Garantire le autorizzazioni appropriate (di lettura) nel tenant ed enumerare tutte le sottoscrizioni di Azure, nonché le risorse all'interno delle sottoscrizioni.

Anche se le risorse di Azure classiche possono essere individuate tramite Resource Graph, è consigliabile creare e usare le risorse di Azure Resource Manager in futuro.

Come creare query con Azure Resource Graph:How to create queries with Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

**Linee guida:** è possibile utilizzare le convenzioni di denominazione appropriate, l'assegnazione di tag, i gruppi di gestione o sottoscrizioni separate, se appropriato, per organizzare e tenere traccia delle risorse. È possibile usare Azure Resource Graph per riconciliare l'inventario a intervalli regolari e garantire che le risorse non autorizzate vengano eliminate dalla sottoscrizione in modo tempestivo. 

Come creare sottoscrizioni di Azure aggiuntive:How to create additional Azure subscriptions:https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare i gruppi di gestione:https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Come creare query con Azure Resource Graph:How to create queries with Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Gestire l'inventario delle risorse di Azure approvate e dei titoli software.

**Linee guida:** è necessario creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione.  


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorare le risorse di Azure non approvate

**Linee guida:** è possibile usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:Guidance : You may use Azure policies to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

    - Tipi di risorse non consentiti

    - Tipi di risorse consentiti

Sarà possibile monitorare gli eventi generati dai criteri 

Log attività che possono essere monitorati tramite Monitoraggio di Azure.Activity logs which can be monitored using Azure Monitor.

Inoltre, è possibile usare Azure Resource Graph per eseguire query/individuare le risorse all'interno delle sottoscrizioni.

Esercitazione: Creare e gestire i criteri per applicare la conformità:Tutorial: Create and manage policies to enforce compliance:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Guida introduttiva: Eseguire la prima query di Resource Graph usando Azure Resource Graph Explorer:Quickstart: Run your first Resource Graph query using Azure Resource Graph Explorer:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Creare, visualizzare e gestire gli avvisi del log attività tramite Monitoraggio di Azure:Create, view, and manage activity log alerts by using Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



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

### <a name="68-utilize-only-approved-applications"></a>6.8: Utilizzare solo le applicazioni approvate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="69-utilize-only-approved-azure-services"></a>6.9: Utilizzare solo i servizi di Azure approvati

**Linee guida:** è possibile usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:Guidance : You may use Azure policies to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

    - Tipi di risorse non consentiti

    - Tipi di risorse consentiti

Esercitazione: Creare e gestire i criteri per applicare la conformità:Tutorial: Create and manage policies to enforce compliance:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Esempi di criteri di Azure:Azure Policy Samples:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementare l'elenco delle applicazioni approvate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resources Manager tramite script

**Linee guida:** usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app "Gestione di Microsoft Azure". Ciò impedirà la creazione e le modifiche alle risorse all'interno delle sottoscrizioni di Azure.This will prevent the creation and changes to resources within your Azure subscriptions. 

Gestire l'accesso alla gestione di Azure con accesso condizionale:Manage access to Azure management with Conditional Access:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitare la capacità degli utenti di eseguire script all'interno delle risorse di calcolo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Applicazioni ad alto rischio fisicamente o logicamente

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle applicazioni Web in esecuzione nel servizio app di Azure o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per ulteriori informazioni, vedere [Controllo di sicurezza: configurazione protetta](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Stabilire configurazioni sicure per tutte le risorse di Azure7.1: Establish Secure Configurations for all Azure Resources

**Indicazioni:** usare gli alias dei criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione delle risorse di Azure.Guidance : Use Azure Policy aliases to create custom policies to audit or enforce the configuration of your Azure resources. È anche possibile usare definizioni di criteri di Azure predefinite.

Inoltre, Azure Resource Manager è in grado di esportare il modello in JSON (JavaScript Object Notation), che deve essere rivisto per garantire che le configurazioni soddisfino o superino i requisiti di sicurezza per l'organizzazione.

È anche possibile usare consigli dal Centro sicurezza di Azure come linea di base di configurazione sicura per le risorse di Azure.You may also use recommendations from Azure Security Center as a secure configuration baseline for your Azure resources.

Come visualizzare gli alias dei criteri di Azure disponibili:How to view available Azure Policy Aliases:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Esercitazione: Creare e gestire i criteri per applicare la conformità:Tutorial: Create and manage policies to enforce compliance:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Esportazione di una singola e più risorse in un modello nel portale di Azure:Single and multi-resource export to a template in Azure portal:https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal

Raccomandazioni sulla sicurezza - una guida di riferimento:Security recommendations - a reference guide:https://docs.microsoft.com/azure/security-center/recommendations-reference



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Stabilire configurazioni sicure per il sistema operativo

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Gestire le configurazioni sicure per tutte le risorse di Azure7.3: Maintain Secure Configurations for all Azure Resources

**Linee guida:** usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure tra le risorse di Azure.Guidance: Use Azure policy [deny] and [deploy if not exist] to enforce secure settings across your Azure resources.  È possibile utilizzare soluzioni quali il rilevamento delle modifiche, il dashboard di conformità dei criteri o una soluzione personalizzata per identificare facilmente le modifiche di sicurezza nell'ambiente.

Informazioni sugli effetti dei criteri di Azure:Understand Azure Policy effects:https://docs.microsoft.com/azure/governance/policy/concepts/effects

Creare e gestire i criteri per applicare la conformità:Create and manage policies to enforce compliance:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Tenere traccia delle modifiche nell'ambiente con la soluzione Rilevamento modifiche:Track changes in your environment with the Change Tracking solution:https://docs.microsoft.com/azure/automation/change-tracking

Ottenere i dati di conformità delle risorse di Azure:Get compliance data of Azure resources:https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Gestire configurazioni sicure per i sistemi operativi

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configurazione sicura dell'archivio delle risorse di Azure7.5: Securely Store Configuration of Azure Resources

**Linee guida:** usare Azure Repos per archiviare e gestire in modo sicuro il codice, ad esempio criteri di Azure personalizzati, modelli di Azure Resource Manager, script di configurazione dello stato desiderato e così via. Per accedere alle risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps o Active Directory se integrati con TFS.

Come archiviare il codice in DevOps di Azure:How to store code in Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Informazioni su autorizzazioni e gruppi in Azure DevOps:About permissions and groups in Azure DevOps:https://docs.microsoft.com/azure/devops/organizations/security/about-permissions



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Memorizzazione sicura delle immagini personalizzate del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida:** definire e implementare configurazioni di sicurezza standard per le risorse di Azure usando Criteri di Azure.Guidance: Define and implement standard security configurations for Azure resources using Azure Policy. Usare gli alias di Criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione di rete delle risorse di Azure.Use Azure Policy aliases to create custom policies to audit or enforce the network configuration of your Azure resources. È inoltre possibile utilizzare le definizioni dei criteri predefinite relative alle risorse specifiche.  Inoltre, è possibile usare Automazione di Azure per distribuire le modifiche alla configurazione.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come utilizzare gli alias:https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementare il monitoraggio della configurazione automatizzata per i servizi di Azure7.9: Implement Automated Configuration Monitoring for Azure Services

**Linee guida:** usare gli alias di Criteri di Azure per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [deploy if not exist] per applicare automaticamente le configurazioni per le risorse di Azure.Use Azure policy [audit], [deny] and [deploy if not exist] to automatically enforce configurations for your Azure resources.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementare il monitoraggio della configurazione automatizzata per i sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="711-securely-manage-azure-secrets"></a>7.11: Gestire in modo sicuro i segreti di Azure7.11: Securely manage Azure secrets

**Indicazioni:** Crittografia disco di Azure offre la crittografia del volume per il sistema operativo e i dischi dati delle macchine virtuali del cluster Azure Data Explorer.Guidance: Azure Disk Encryption provides volume encryption for the OS and data disks of your Azure Data Explorer cluster virtual machines. Si integra anche con l'insieme di credenziali delle chiavi di Azure che consente di controllare e gestire le chiavi e i segreti di crittografia del disco e di garantire che tutti i dati nei dischi delle macchine virtuali vengano crittografati inattivi mentre si trova in Archiviazione di Azure.It also integrate with Azure Key Vault which allows you to control and manage the disk encryption keys and secrets, and ensure all data on the VM disks is encrypted at rest while in Azure Storage.

Come proteggere il cluster in Azure Data Explorer:How to secure your cluster in Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Microsoft

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Gestire in modo sicuro e automatico le identità

**Linee guida:** usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure AD. Managed Identities consente di eseguire l'autenticazione a qualsiasi servizio che supporta l'autenticazione di Azure AD, incluso Key Vault, senza credenziali nel codice. Come configurare le identità gestite:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Configurare le identità gestite per il cluster Azure Data Explorer:Configure managed identities for your Azure Data Explorer cluster:https://docs.microsoft.com/azure/data-explorer/managed-identities


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminare l'esposizione di credenziali indesiderate

**Linee guida**: Implementare Credential Scanner per identificare le credenziali all'interno del codice. Credential Scanner will also encourage moving discovered credentials to more secure locations such as Azure Key Vault. 

Come configurare Credential Scanner:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="malware-defense"></a>Difesa da malware

*Per ulteriori informazioni, vedere [Controllo di sicurezza: Difesa da malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-utilize-centrally-managed-anti-malware-software"></a>8.1: Utilizzare software antimalware gestito centralmente

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

L'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Azure Data Explorer), tuttavia non viene eseguito sul contenuto dei clienti.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: File pre-scansione da caricare in risorse di Azure non di calcolo

**Linee guida:** l'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Azure Data Explorer), tuttavia non viene eseguito sul contenuto del cliente.

Pre-scan qualsiasi contenuto caricato in risorse di Azure non di calcolo, ad esempio Azure Data Explorer, Archiviazione data lake, Archiviazione BLOB, Database di Azure per PostgreSQL e così via. Microsoft non può accedere ai dati in questi casi.



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Verificare che il software e le firme antimalware siano aggiornati

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

L'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure, tuttavia non viene eseguito sul contenuto dei clienti.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="data-recovery"></a>Ripristino dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatici regolari

**Linee guida:** i dati nell'account di archiviazione di Microsoft Azure usato dal cluster di Esplora dati vengono sempre replicati per garantire durabilità e disponibilità elevata. Archiviazione di Azure copia i dati in modo che siano protetti da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni della rete o dell'alimentazione e forti calamità naturali. È possibile scegliere di replicare i dati nello stesso data center, tra data center di zona nella stessa area o tra aree separate geograficamente.

Informazioni sulla ridondanza di Archiviazione di Azure e sui contratti di servizio:Understanding Azure Storage redundancy and Service-Level Agreements:https://docs.microsoft.com/azure/storage/common/storage-redundancy

Esporta dati nell'archivio :https://docs.microsoft.com/azure/kusto/management/data-export/export-data-to-storage



**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup di sistema completi ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida:** Azure Data Explorer crittografa tutti i dati in un account di archiviazione inattivi. Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft.By default, data is encrypted with Microsoft-managed keys. Per un ulteriore controllo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente da utilizzare per la crittografia dei dati. Le chiavi gestite dal cliente devono essere archiviate in un insieme di credenziali delle chiavi di Azure.Customer-managed keys must be stored in an Azure Key Vault. 

Configurare le chiavi gestite dal cliente utilizzando C :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Configurare le chiavi gestite dal cliente usando il modello di Azure Resource Manager:Configure customer-managed-keys using the Azure Resource Manager template:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager

Come eseguire il backup dei certificati dell'insieme di chiavi di Azure:How to backup Azure Key Vault certificates:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida:** testare periodicamente il ripristino dei dati dei segreti dell'insieme di credenziali delle chiavi di Azure.Guidance : Periodically test data restoration of your Azure Key Vault secrets.

Come ripristinare i certificati dell'insieme di chiavi di Azure:How to restore Azure Key Vault certificates:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0 

Configurare le chiavi gestite dal cliente utilizzando C :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Configurare le chiavi gestite dal cliente usando il modello di Azure Resource Manager:Configure customer-managed-keys using the Azure Resource Manager template:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dai clienti

**Guida**: Cliente per abilitare Soft-Delete in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.  È inoltre possibile abilitare la protezione dall'eliminazione in modo che se un insieme di credenziali o un oggetto nello stato eliminato, non possa essere eliminato fino a quando non è trascorso il periodo di conservazione.  

Come abilitare la protezione Soft-Delete ed Purge in Key Vault:https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete

Configurare le chiavi gestite dal cliente utilizzando C :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Configurare le chiavi gestite dal cliente usando il modello di Azure Resource Manager:Configure customer-managed-keys using the Azure Resource Manager template:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10.1: Creare una guida alla risposta agli incidenti

**Linee guida**: Creare una guida alla risposta agli incidenti per l'organizzazione. Assicurarsi che siano presenti piani di risposta agli incidenti scritti che definiscono tutti i ruoli del personale, nonché fasi di gestione/gestione degli incidenti dal rilevamento alla revisione post-incidente.
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final

    



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Creare la procedura di creazione del punteggio degli eventi imprevisti e della definizione delle priorità

**Linee guida:** il Centro sicurezza assegna una gravità a ogni avviso per consentire di definire la priorità degli avvisi da analizzare per primi. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sull'analita utilizzato per emettere l'avviso, nonché il livello di probabilità che vi sia stato un intento dannoso dietro l'attività che ha portato all'avviso. 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

    Use tags to organize your Azure resources: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Testare le procedure di risposta alla sicurezza

**Linee guida:** eseguire esercizi per testare le funzionalità di risposta agli incidenti dei sistemi a cadenza regolare per proteggere le risorse di Azure.Guidance : Conduct exercises to test your systems' incident response capabilities on a regular cadeence to help protect your Azure resources. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: Fornire i dettagli di &nbsp;contatto per gli eventi imprevisti di sicurezza e configurare le notifiche di avviso per gli eventi imprevisti di sicurezza

**Linee guida:** le informazioni di contatto relative agli eventi imprevisti di protezione verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che i dati sono stati utilizzati da una parte illegale o non autorizzata. Esaminare gli incidenti dopo il fatto per assicurarsi che i problemi vengano risolti.
    

Come impostare il contatto di sicurezza del centro di sicurezza di Azure:How to set the Azure Security Center Security Contact:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporare avvisi di sicurezza nel sistema di risposta agli incidenti

**Indicazioni:** Esportare gli avvisi e i suggerimenti del Centro sicurezza di Azure usando la funzionalità di esportazione continua per identificare i rischi per le risorse di Azure.Guidance: Export your Azure Security Center alerts and recommendations using the Continuous Export feature to help identify risks to Azure resources. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.You may use the Azure Security Center data connector to stream the alerts to Azure Sentinel.
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizzare la risposta agli avvisi di sicurezza

**Indicazioni:** usare la funzionalità automazione flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite "App per la logica" negli avvisi e suggerimenti per proteggere le risorse di Azure.Guidance: Use the Workflow Automation feature in Azure Security Center to automatically trigger responses via "Logic Apps" on security Apps and recommendations to protect your Azure resources.
    

    How to configure Workflow Automation and Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation



**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per ulteriori informazioni, vedere Controllo di sicurezza: test di [penetrazione ed Esercizi del team rosso](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza entro 60 giorni

**Linee guida**: Seguire le regole di coinvolgimento Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Ulteriori informazioni sulla strategia di Microsoft e sull'esecuzione di Red Teaming e sui test di penetrazione dei siti in tempo reale su infrastruttura cloud, servizi e applicazioni gestiti Microsoft sono disponibili qui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere il benchmark di sicurezza di [AzureSee](https://docs.microsoft.com/azure/security/benchmarks/overview) the Azure Security Benchmark
- Altre informazioni sulle linee di base per la sicurezza di AzureLearn more about [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
