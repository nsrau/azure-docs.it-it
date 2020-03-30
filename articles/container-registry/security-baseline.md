---
title: Azure Security Baseline for Azure Container Registry
description: Azure Security Baseline for Azure Container Registry
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2970ebf42acb87e93f8b827b3687b219da5867c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244291"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Azure Security Baseline for Azure Container Registry

Il Registro di sistema dei contenitori di Azure Security Baseline for Azure contiene suggerimenti che consentono di migliorare il livello di sicurezza della distribuzione.

La linea di base per questo servizio è ricavata da [Azure Security Benchmark versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce consigli su come proteggere le soluzioni cloud in Azure con le indicazioni sulle procedure consigliate.

Per altre informazioni, vedere [Panoramica delle linee di base](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)della sicurezza di Azure.For more information, see Azure Security Baselines overview .

## <a name="network-security"></a>Sicurezza di rete

*Per ulteriori informazioni, vedere [Controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale1.1: Protect resources using Network Security Groups or Azure Firewall on your Virtual Network

**Linee guida:** Rete virtuale di Azure offre una rete privata sicura per le risorse di Azure e locali. Limitando l'accesso al registro dei contenitori di Azure privato da una rete virtuale di Azure, si garantisce che solo le risorse nella rete virtuale accedano al Registro di sistema. Per gli scenari cross-premise, è anche possibile configurare le regole del firewall per consentire l'accesso al Registro di sistema solo da indirizzi IP specifici. Da dietro un firewall, configurare le regole di accesso al firewall e i tag di servizio per accedere al registro contenitori.

Limitare l'accesso a un registro contenitori di Azure usando una rete virtuale di Azure o le regole del firewall:Restrict access to an Azure container registry using an Azure virtual network or firewall rules:https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

Configurare le regole per accedere a un registro di contenitori di Azure in base a un firewall:Configure rules to access an Azure container registry behind a firewall:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Indicazioni:** usare il Centro sicurezza di Azure e correggere i consigli di protezione della rete per proteggere le risorse di rete in Azure.Guidance: Use Azure Security Center and remediate network protection recommendations to help protect your network resources in Azure. Abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione per il controllo del traffico.

Come abilitare i registri di flusso del gruppo di sicurezza di rete:How to enable NSG Flow Logs:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Proteggere le risorse di rete:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteggere le applicazioni Web critiche

**Guida**: Non applicabile. Il benchmark è destinato al servizio app di Azure o alle risorse di calcolo che ospitano applicazioni Web.Benchmark is intended for Azure App Service or compute resources hosting web applications.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Guida:** Abilitare la protezione DDoS Standard sulle reti virtuali per le protezioni dagli attacchi DDoS. Usare Azure Security Center Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP Internet dannosi o inutilizzati noti.  Distribuire Firewall di Azure a ciascuno dei limiti di rete dell'organizzazione con Threat Intelligence abilitato e configurato per "Avvisa e nega" per il traffico di rete dannoso.

È possibile usare l'accesso Just-In Time Network al Centro sicurezza di Azure per configurare i gruppi di sicurezza di rete per limitare l'esposizione degli endpoint agli indirizzi IP approvati per un periodo limitato. Inoltre, usare protezione avanzata della rete adattiva del Centro sicurezza di Azure per consigliare configurazioni del gruppo di sicurezza di rete che limitano le porte e gli indirizzi IP di origine in base al traffico effettivo e all'intelligence sulle minacce.

Come configurare la protezione DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Come distribuire Firewall di Azure:How to deploy Azure Firewall:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Informazioni su Azure Security Center Integrated Threat Intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Informazioni sulla protezione avanzata della rete adattiva del Centro sicurezza di Azure:Understand Azure Security Center Adaptive Network Hardening:https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Controllo dell'accesso alla rete del Centro sicurezza di Azure:Azure Security Center Just In Time Network Access Control:https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Registrare i pacchetti di rete e i registri di flusso

**Guida:** abilitare i log di flusso del gruppo di sicurezza di rete per il gruppo di sicurezza di rete collegato alla subnet in uso per proteggere il Registro di sistema del contenitore di Azure.Guidance: Enable network security group (NSG) flow logs for the NSG attached to the subnet being used to protect your Azure container registry. È possibile registrare i log di flusso del gruppo di sicurezza di rete in un account di archiviazione di Azure per generare i record di flusso. Se necessario per analizzare l'attività anomala, abilitare l'acquisizione di pacchetti di Watcher reti di Azure.If required for investigating anomalous activity, enable Azure Network Watcher packet capture.

Come abilitare i registri di flusso del gruppo di sicurezza di rete:How to enable NSG Flow Logs:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare Network Watcher:https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Linee guida:** selezionare un'offerta di Azure Marketplace che supporti la funzionalità IDS/IPS con funzionalità di ispezione del payload. Se non è un requisito il rilevamento e/o la prevenzione delle intrusioni basati sull'ispezione del payload, è possibile usare Firewall di Azure con Threat Intelligence.If intrusion detection and/or prevention based on payload inspection is not a requirement, Azure Firewall with Threat Intelligence can be used. Il filtro basato su Azure Firewall Threat Intelligence può avvisare e negare il traffico da e verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

Distribuire la soluzione firewall scelta a ciascuno dei limiti di rete dell'organizzazione per rilevare e/o negare traffico dannoso.

Azure Marketplace:https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

Come distribuire Firewall di Azure:How to deploy Azure Firewall:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Come configurare gli avvisi con Firewall di Azure:How to configure alerts with Azure Firewall:https://docs.microsoft.com/azure/firewall/threat-intel


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gestire il traffico verso le applicazioni web

**Guida**: Non applicabile. Il benchmark è destinato alle applicazioni Web in esecuzione nel servizio app di Azure o nelle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e l'overhead amministrativo delle regole di sicurezza di rete

**Linee guida:** per le risorse che devono accedere al Registro di sistema del contenitore, usare i tag del servizio di rete virtuale per il servizio Registro di sistema del contenitore di Azure per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure.Guidance : For resources that need access to your container registry, use virtual network service tags for the Azure Container Registry service to define network access controls on Network Security Groups or Azure Firewall. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag del servizio "AzureContainerRegistry" nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi degli indirizzi inclusi nel tag del servizio e aggiorna automaticamente il tag del servizio quando gli indirizzi cambiano.

Consenti accesso tramite tag di servizio:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida:** definire e implementare configurazioni di sicurezza standard per le risorse di rete associate ai registri del contenitore di Azure con Criteri di Azure.Guidance: Define and implement standard security configurations for network resources associated with your Azure container registries with Azure Policy. Usare gli alias dei criteri di Azure negli spazi dei nomi "Microsoft.ContainerRegistry" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete dei registri contenitore. 

È possibile usare Blueprint di Azure per semplificare le distribuzioni di Azure su larga scala creando una creazione di pacchetti di elementi dell'ambiente chiave, ad esempio modelli di Azure Resource Manager, controlli RBAC e criteri, in una singola definizione del blueprint. Applica facilmente il blueprint alle nuove sottoscrizioni e ottimizza il controllo e la gestione tramite il controllo delle versioni.

Controllare la conformità dei registri dei contenitori di Azure usando Criteri di Azure:Audit compliance of Azure container registries using Azure Policy:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Come creare un blueprint di Azure:How to create an Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regole di configurazione del traffico dei documenti

**Linee guida:** il cliente può usare Azure Blueprints per semplificare le distribuzioni di Azure su larga scala creando un pacchetto di elementi dell'ambiente chiave, ad esempio modelli di Azure Resource Manager, controlli RBAC e criteri, in una singola definizione del blueprint. Applica facilmente il blueprint alle nuove sottoscrizioni e ottimizza il controllo e la gestione tramite il controllo delle versioni.

Come creare un blueprint di Azure:How to create an Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilizzare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate ai registri contenitori. Creare avvisi all'interno di Monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse di rete critiche.

Come visualizzare e recuperare gli eventi del log attività di Azure:How to view and retrieve Azure Activity Log events:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Come creare avvisi in Monitoraggio di Azure:How to create alerts in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizzare origini di sincronizzazione dell'ora approvate

**Linee guida:** Microsoft gestisce le origini ora per le risorse di Azure, tuttavia, è possibile gestire le impostazioni di sincronizzazione dell'ora per le risorse di calcolo.

Come configurare la sincronizzazione dell'ora per le risorse di calcolo di Azure:How to configure time synchronization for Azure compute resources:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurare la gestione dei registri di protezione centralizzati

**Indicazioni:** eseguire l'inserimento di log tramite Monitoraggio di Azure per aggregare i dati di sicurezza generati da un registro contenitori di Azure.Guidance: Ingest logs via Azure Monitor to aggregate security data generated by an Azure container registry. In Monitoraggio di Azure usare Log Analytics Workspace(s) per eseguire query ed eseguire analisi e gli account di archiviazione di Azure per l'archiviazione a lungo termine/archiviazione.

Log del Registro di sistema del contenitore di Azure per la valutazione diagnostica e il controllo:Azure Container Registry logs for diagnostic evaluation and auditing:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Abilitare la registrazione di controllo per le risorse di Azure2.3: Abilitare la registrazione di controllo per le risorse di Azure2.

**Indicazioni:** Monitoraggio di Azure raccoglie i log delle risorse (precedentemente denominati log di diagnostica) per gli eventi guidati dall'utente nel Registro di sistema. Raccogliere e utilizzare questi dati per controllare gli eventi di autenticazione del Registro di sistema e fornire un percorso di attività completo sugli elementi del Registro di sistema, ad esempio gli eventi pull e push in modo da poter diagnosticare i problemi di sicurezza con il Registro di sistema.

Log del Registro di sistema del contenitore di Azure per la valutazione diagnostica e il controllo:Azure Container Registry logs for diagnostic evaluation and auditing:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Raccogliere i registri di sicurezza dai sistemi operativi

**Guida**: Non applicabile. Benchmark è destinato alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dello spazio di archiviazione del registro di protezione

**Linee guida:** in Monitoraggio di Azure impostare il periodo di conservazione dell'area di lavoro di Log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archiviazione.

Come impostare i parametri di conservazione dei log per le aree di lavoro di Log Analytics:How to set log retention parameters for Log Analytics Workspaces:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i registri

**Linee guida:** analizzare e monitorare i log del Registro di sistema del contenitore di Azure per ottenere un comportamento anomalo ed esaminare regolarmente i risultati. Usare l'area di lavoro Log Analytics di Monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log.

Log del Registro di sistema del contenitore di Azure per la valutazione diagnostica e il controllo:Azure Container Registry logs for diagnostic evaluation and auditing:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Comprendere l'area di lavoro di Log Analytics:Understand Log Analytics Workspace:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Come eseguire query personalizzate in Monitoraggio di Azure:How to perform custom queries in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Abilitare gli avvisi per attività anomale

**Indicazioni:** usare l'area di lavoro di Azure Log Analytics per il monitoraggio e gli avvisi sulle attività anomale nei log di sicurezza e negli eventi correlati al Registro di sistema del contenitore di Azure.Guidance: Use Azure Log Analytics workspace for monitoring and alerting on anomalous activities in security logs and events related to your Azure container registry.

Log del Registro di sistema del contenitore di Azure per la valutazione diagnostica e il controllo:Azure Container Registry logs for diagnostic evaluation and auditing:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

How to alert on log analytics log data:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizzare la registrazione antimalware

**Guida**: Non applicabile. Registro di sistema del contenitore di Azure non elabora né produce log correlati antimalware.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Abilitare la registrazione delle query DNS

**Guida**: Non applicabile. Il Registro di sistema del contenitore di Azure è un endpoint e non avvia la comunicazione e il servizio non esegue query sul DNS.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Abilitare la registrazione di controllo della riga di comando

**Guida**: Non applicabile. Benchmark è destinato alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per ulteriori informazioni, vedere [Controllo di sicurezza: controllo delle identità e degli accessi](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Mantenere un inventario degli account amministrativi

**Linee guida:** Azure Active Directory (Azure AD) dispone di ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account membri di gruppi amministrativi.

Per ogni registro dei contenitori di Azure, tenere traccia dell'abilitazione o disabilitazione dell'account amministratore predefinito. Disabilitare l'account quando non è in uso.

Come ottenere un ruolo di directory in Azure AD con PowerShell:How to get a directory role in Azure AD with PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Come ottenere i membri di un ruolo di directory in Azure AD con PowerShell:How to get members of a directory role in Azure AD with PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Account amministratore del Registro di sistema del contenitore di Azure:Azure Container Registry admin account:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Modificare le password predefinite, ove applicabile

**Indicazioni:** Azure Active Directory (Azure AD) non ha il concetto di password predefinite. Altre risorse di Azure che richiedono una password forzano la creazione di una password con requisiti di complessità e una lunghezza minima della password, che variano a seconda del servizio. L'utente è responsabile delle applicazioni di terze parti e dei servizi di Marketplace che possono utilizzare password predefinite.

Se l'account amministratore predefinito di un registro contenitori di Azure è abilitato, le password complesse vengono create automaticamente e devono essere ruotate. Disabilitare l'account quando non è in uso.

Account amministratore del Registro di sistema del contenitore di Azure:Azure Container Registry admin account:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizzare account amministrativi dedicati

**Linee guida**: Creare procedure operative standard per quanto miri all'utilizzo di account amministrativi dedicati. Usare Gestione identità e accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, creare procedure per abilitare l'account amministratore predefinito di un registro contenitori. Disabilitare l'account quando non è in uso.

Informazioni sull'identità e l'accesso del Centro sicurezza di Azure:Understand Azure Security Center Identity and Access:https://docs.microsoft.com/azure/security-center/security-center-identity-access

Account amministratore del Registro di sistema del contenitore di Azure:Azure Container Registry admin account:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida:** ove possibile, usare Azure Active Directory SSO anziché configurare singole credenziali autonome per servizio. Usare i consigli per la gestione delle identità e degli accessi del Centro sicurezza di Azure.Use Azure Security Center Identity and Access Management recommendations.

Per l'accesso individuale al registro contenitori, usare l'accesso individuale integrato con Azure Active Directory.For individual access to the container registry, use individual login integrated with Azure Active Directory.

Comprendere SSO con Azure AD:Understand SSO with Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

Accesso individuale a un registro contenitori:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Indicazioni:** abilitare l'autenticazione a più fattori (MFA) di Azure Active Directory (Azure AD) e seguire i consigli per la gestione delle identità e degli accessi del Centro sicurezza di Azure.Guidance: Enable Azure Active Directory (Azure AD) multi-factor authentication (MFA) and follow Azure Security Center Identity and Access Management recommendations.

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Come monitorare l'identità e l'accesso all'interno del Centro sicurezza di Azure:How to monitor identity and access within Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizzare macchine dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida:** usare PAW (workstation con accesso privilegiato) con l'autenticazione a più fattori configurata per accedere alle risorse di Azure e configurarle.

Ulteriori informazioni sulle workstation con accesso privilegiato:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Monitoraggio del Centro sicurezza di Azure**: N/DAzure Security Center monitoring : N/A

**Responsabilità**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registrare e avvisare su attività sospette da account amministrativi

**Indicazioni:** usare i report di sicurezza di Azure Active Directory (Azure AD) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso alle attività.

Come identificare gli utenti di Azure AD contrassegnati per l'attività rischiosa:How to identify Azure AD users flagged for risky activity:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Come monitorare l'identità e l'attività di accesso degli utenti nel Centro sicurezza di Azure:How to monitor users' identity and access activity in Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo da posizioni approvate

**Linee guida**: utilizzare percorsi denominati con accesso condizionale per consentire l'accesso solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche.

Come configurare i percorsi denominati in Azure:How to configure Named Locations in Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida:** usare Azure Active Directory (Azure AD) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD inoltre consente di archiviare le credenziali utente in Azure AD.

Come creare e configurare un'istanza di Azure AD:How to create and configure an Azure AD instance:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rivedere e riconciliare regolarmente l'accesso degli utenti

**Indicazioni:** Azure Active Directory (Azure AD) fornisce log che consentono di individuare gli account non aggiornati. Inoltre, usare Le revisioni di Azure Identity Access per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso degli utenti può essere rivisto regolarmente per assicurarsi che solo gli utenti giusti abbiano accesso continuo.

Informazioni sui report di Azure AD:Understand Azure AD reporting:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Come usare le verifiche di accesso alle identità di Azure:How to use Azure identity access reviews:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorare i tentativi di accesso agli account disattivati

**Linee guida:** è possibile accedere alle origini del log attività di accesso, di controllo e di rischio di Azure Active Directory (Azure AD), che consentono l'integrazione con qualsiasi strumento di monitoraggio delle informazioni di sicurezza e degli eventi (SIEM)/Monitoring.Guidance : You have access to Azure Active Directory (Azure AD) Sign-in Activity, Audit and Risk Event log sources, which allow you to integrate with any Security Information and Event Management (SIEM) /Monitoring tool.

È possibile semplificare questo processo creando impostazioni di diagnostica per gli account utente di Azure Active Directory e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics.You can streamline this process by creating Diagnostic Settings for Azure Active Directory user accounts and sending the audit logs and sign-in logs to a Log Analytics Workspace. È possibile configurare gli avvisi desiderati all'interno dell'area di lavoro di Log Analytics.You can configure desired Alerts within Log Analytics Workspace.

Come integrare i log attività di Azure in Monitoraggio di Azure:How to integrate Azure Activity Logs into Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Scostamenti comportamento di avviso per l'accesso all'account

**Linee guida:** usare le funzionalità di protezione dei rischi e delle identità di Azure Active Directory (Azure AD) per configurare le risposte automatiche alle azioni sospette rilevate relative alle identità utente. 

Come visualizzare gli accessi rischiosi di Azure AD:How to view Azure AD risky sign-ins:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Come configurare e abilitare i criteri di rischio di Identity Protection:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Guida**: Non disponibile; Customer Lockbox not currently supported for Azure Container Registry.

Elenco dei servizi supportati da Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="data-protection"></a>Protezione dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Mantenere un inventario di informazioni sensibili

**Linee guida:** usare i tag di risorsa per facilitare il rilevamento dei registri dei contenitori di Azure che archiviano o elaborano informazioni riservate.

Taggare e versione le immagini del contenitore o altri elementi in un Registro di sistema e bloccare immagini o repository per facilitare il rilevamento delle immagini che archiviano o elaborano informazioni riservate.

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Consigli per l'assegnazione di tag e il controllo delle versioni delle immagini del contenitore:Recommendations for tagging and versioning container images:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

Bloccare un'immagine del contenitore in un registro del contenitore di Azure:Lock a container image in an Azure container registry:https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolare i sistemi che memorizzano o elaborano informazioni sensibili

**Linee guida:** implementare registri contenitori, sottoscrizioni e/o gruppi di gestione separati per lo sviluppo, il test e la produzione. Le risorse che archiviano o elaborano dati sensibili devono essere sufficientemente isolate.

Le risorse devono essere separate da una rete virtuale o da una subnet, contrassegnate in modo appropriato e protette da un gruppo di sicurezza di rete o da un firewall di Azure.Resources should be separated by virtual network or subnet, tagged appropriately, and secured by an network security group (NSG) or Azure Firewall.

Come creare sottoscrizioni di Azure aggiuntive:How to create additional Azure subscriptions:https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare i gruppi di gestione:https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Limitare l'accesso a un registro contenitori di Azure usando una rete virtuale di Azure o le regole del firewall:Restrict access to an Azure container registry using an Azure virtual network or firewall rules:https://docs.microsoft.com/azure/container-registry/container-registry-vnet

Come creare un gruppo di sicurezza di gruppo con una configurazione di sicurezza:How to create an NSG with a security config:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Come distribuire Firewall di Azure:How to deploy Azure Firewall:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Come configurare avvisi o avvisi e rifiuto con Firewall di Azure:How to configure alert or alert and deny with Azure Firewall:

https://docs.microsoft.com/azure/firewall/threat-intel



**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorare e bloccare il trasferimento non autorizzato di informazioni sensibili

**Linee guida**: Distribuire uno strumento automatizzato su perimetri di rete che monitora il trasferimento non autorizzato di informazioni riservate e blocca tali trasferimenti, avvisando i professionisti della sicurezza delle informazioni.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Crittografare tutte le informazioni sensibili in transito

**Linee guida**: Verificare che tutti i client che si connettono al Registro di sistema del contenitore di Azure siano in grado di negoziare TLS 1.2 o versione successiva. Le risorse di Microsoft Azure negoziano TLS 1.2 per impostazione predefinita.

Seguire i consigli del Centro sicurezza di Azure per la crittografia inattivi e la crittografia in transito, se applicabile.

Comprendere la crittografia in transito con Azure:Understand encryption in transit with Azure:https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilizzare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida:** le funzionalità di identificazione dei dati, classificazione e prevenzione delle perdite non sono ancora disponibili per il Registro di sistema del contenitore di Azure.Guidance: Data identification, classification, and loss prevention features are not yet available for Azure Container Registry. Implementare una soluzione di terze parti, se necessario, ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse4.6: Usare il controllo degli accessi in base al ruolo di

**Linee guida:** usare il controllo degli accessi in base al ruolo di Azure Active Directory (Azure AD) per controllare l'accesso a dati e risorse in un registro contenitori di Azure.Guidance: Use Azure Active Directory (Azure AD) RBAC to control access to data and resources in an Azure container registry. 

Come configurare il controllo degli accessi in base al ruolo in Azure:How to configure RBAC in Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Ruoli e autorizzazioni del Registro di sistema del contenitore di Azure:Azure Container Registry roles and permissions:https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizzare la prevenzione della perdita di dati basata su host per applicare il controllo degli accessi

**Linee guida:** se necessario per la conformità alle risorse di elaborazione, implementare uno strumento di terze parti, ad esempio una soluzione di prevenzione della perdita di dati automatizzata basata su host, per applicare i controlli di accesso ai dati anche quando i dati vengono copiati all'esterno di un sistema.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografare le informazioni sensibili inattivi

**Linee guida:** usare la crittografia inattivi in tutte le risorse di Azure.Guidance: Use encryption at rest on all Azure resources. Per impostazione predefinita, tutti i dati in un registro contenitori di Azure vengono crittografati inattivi usando le chiavi gestite da Microsoft.By default, all data in an Azure container registry is encrypted at restto on be rest using Microsoft-managed keys.

Comprendere la crittografia inattivi in Azure:Understand encryption at rest in Azure:https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Chiavi gestite dal cliente nel Registro di sistema del contenitore di Azure:Customer-managed keys in Azure Container Registry:https://aka.ms/acr/cmk



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e avvisare le modifiche alle risorse critiche di Azure4.9: Log and alert on changes to critical Azure resources

**Indicazioni:** Monitoraggio di Azure raccoglie i log delle risorse (precedentemente denominati log di diagnostica) per gli eventi guidati dall'utente nel Registro di sistema. Raccogliere e utilizzare questi dati per controllare gli eventi di autenticazione del Registro di sistema e fornire un percorso di attività completo sugli elementi del Registro di sistema, ad esempio gli eventi pull e pull, in modo da poter diagnosticare i problemi operativi con il Registro di sistema.

Log del Registro di sistema del contenitore di Azure per la valutazione diagnostica e il controllo:Azure Container Registry logs for diagnostic evaluation and auditing:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per ulteriori informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Eseguire strumenti automatici di scansione delle vulnerabilità

**Indicazioni:** seguire i consigli del Centro sicurezza di Azure sull'esecuzione di valutazioni delle vulnerabilità nelle immagini del contenitore. Facoltativamente, distribuire soluzioni di terze parti da Azure Marketplace per eseguire valutazioni delle vulnerabilità delle immagini.

Come implementare i consigli per la valutazione della vulnerabilità del Centro sicurezza di Azure:How to implement Azure Security Center vulnerability assessment recommendations:https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Integrazione del Registro di sistema del contenitore di Azure con il Centro sicurezza (anteprima):Azure Container Registry integration with Security Center (Preview):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuire una soluzione di gestione automatica delle patch del sistema operativo

**Linee guida:** Microsoft esegue la gestione delle patch nei sistemi sottostanti che supportano il Registro di sistema del contenitore di Azure.Guidance: Microsoft performs patch management on the underlying systems that support Azure Container Registry.

Automatizza gli aggiornamenti delle immagini del contenitore quando vengono rilevati aggiornamenti alle immagini di base dal sistema operativo e da altre patch.

Informazioni sugli aggiornamenti delle immagini di base per le attività del Registro di sistema del contenitore di Azure:About base image updates for Azure Container Registry tasks:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuire una soluzione di gestione automatica delle patch software di terze parti

**Guida**: È possibile utilizzare una soluzione di terze parti per applicare patch alle immagini delle applicazioni.  Inoltre, è possibile eseguire attività del Registro di sistema contenitore di Azure per automatizzare gli aggiornamenti alle immagini dell'applicazione in un registro contenitori in base alle patch di sicurezza o ad altri aggiornamenti nelle immagini di base.

Informazioni sugli aggiornamenti delle immagini di base per le attività ACR:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Confrontare le scansioni delle vulnerabilità back-to-back

**Linee guida:** Integrare il Registro di sistema contenitore di Azure (ACR) con il Centro sicurezza di Azure per consentire l'analisi periodica delle immagini dei contenitori per le vulnerabilità. Facoltativamente, distribuire soluzioni di terze parti da Azure Marketplace per eseguire analisi periodiche delle vulnerabilità delle immagini.

Integrazione del Registro di sistema del contenitore di Azure con il Centro sicurezza (anteprima):Azure Container Registry integration with Security Center (Preview):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilizzare un processo di valutazione del rischio per assegnare priorità alla correzione delle vulnerabilità individuate

**Linee guida:** Integrare il Registro di sistema contenitore di Azure (ACR) con il Centro sicurezza di Azure per consentire l'analisi periodica delle immagini dei contenitori per individuare eventuali vulnerabilità e classificare i rischi. Facoltativamente, distribuire soluzioni di terze parti da Azure Marketplace per eseguire analisi periodiche delle vulnerabilità delle immagini e classificazione dei rischi.

Integrazione del Registro di sistema del contenitore di Azure con il Centro sicurezza (anteprima):Azure Container Registry integration with Security Center (Preview):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Monitoraggio del Centro sicurezza di Azure**: N/DAzure Security Center monitoring : N/A

**Responsabilità**: Cliente

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per ulteriori informazioni, vedere [Controllo di sicurezza: inventario e gestione delle risorse](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Usare Azure Asset Discovery

**Linee guida:** usare Azure Resource Graph per eseguire query/individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) all'interno delle sottoscrizioni.  Garantire le autorizzazioni appropriate (di lettura) nel tenant ed enumerare tutte le sottoscrizioni di Azure, nonché le risorse all'interno delle sottoscrizioni.

Anche se le risorse di Azure classiche possono essere individuate tramite Resource Graph, è consigliabile creare e usare le risorse di Azure Resource Manager in futuro.

Come creare query con Azure Resource Graph:How to create queries with Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Come visualizzare le sottoscrizioni di Azure:How to view your Azure Subscriptions:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

Informazioni sul controllo degli accessi in base al ruolo di Azure:Understandhttps://docs.microsoft.com/azure/role-based-access-control/overview



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Gestire i metadati delle risorse

**Linee guida:** il Registro di sistema del contenitore di Azure gestisce i metadati, inclusi tag e manifesti per le immagini in un Registro di sistema. Seguire le procedure consigliate per contrassegnare gli elementi.

Informazioni su registri, archivi e immagini:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Consigli per l'assegnazione di tag e il controllo delle versioni delle immagini del contenitore:Recommendations for tagging and versioning container images:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminare risorse di Azure non autorizzate

**Linee guida:** il Registro di sistema del contenitore di Azure gestisce i metadati, inclusi tag e manifesti per le immagini in un Registro di sistema. Seguire le procedure consigliate per contrassegnare gli elementi.

Informazioni su registri, archivi e immagini:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Consigli per l'assegnazione di tag e il controllo delle versioni delle immagini del contenitore:Recommendations for tagging and versioning container images:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Gestire un inventario delle risorse di Azure approvate e dei titoli software

**Linee guida:** è necessario creare un inventario delle risorse di Azure approvate in base alle esigenze dell'organizzazione.  

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorare le risorse di Azure non approvate

**Linee guida:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

Usare Azure Resource Graph per eseguire query/individuare risorse all'interno delle sottoscrizioni.  Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

Controllare la conformità dei registri dei contenitori di Azure usando Criteri di Azure:Audit compliance of Azure container registries using Azure Policy:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare query con Azure Graph:How to create queries with Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorare le applicazioni software non approvate all'interno delle risorse di calcolo

**Linee guida:** analizzare e monitorare i log del Registro di sistema del contenitore di Azure per ottenere un comportamento anomalo ed esaminare regolarmente i risultati. Usare l'area di lavoro Log Analytics di Monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log.

Log del Registro di sistema del contenitore di Azure per la valutazione diagnostica e il controllo:Azure Container Registry logs for diagnostic evaluation and auditing:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Comprendere l'area di lavoro di Log Analytics:Understand Log Analytics Workspace:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Come eseguire query personalizzate in Monitoraggio di Azure:How to perform custom queries in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Rimuovere le risorse e le applicazioni software di Azure non approvate

**Linee guida:** Automazione di Azure offre il controllo completo durante la distribuzione, le operazioni e la rimozione delle autorizzazioni di carichi di lavoro e risorse.  È possibile implementare una soluzione personalizzata per la rimozione di risorse di Azure non autorizzate. Introduzione all'automazione di Azure:An introduction to Azure Automation:https://docs.microsoft.com/azure/automation/automation-intro


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilizzare solo applicazioni approvate

**Guida**: Non applicabile. Benchmark è progettato per le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usare solo i servizi di Azure approvati

**Linee guida:** sfruttare i criteri di Azure per limitare i servizi di cui è possibile eseguire il provisioning nell'ambiente.

Controllare la conformità dei registri dei contenitori di Azure usando Criteri di Azure:Audit compliance of Azure container registries using Azure Policy:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come negare un tipo di risorsa specifico con Criteri di Azure:How to deny a specific resource type with Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementare l'elenco delle applicazioni approvate

**Guida**: Non applicabile. Benchmark è progettato per le risorse di calcolo.



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Limitare la capacità degli utenti di interagire con AzureResources Manager tramite script

**Linee guida:** usare configurazioni specifiche del sistema operativo o risorse di terze parti per limitare la capacità degli utenti di eseguire script all'interno di risorse di calcolo di Azure.Guidance : Use operating system-specific configurations or third-party resources to limit users' ability to execute scripts within Azure compute resources.

Come configurare l'accesso condizionale per bloccare l'accesso a Azure Resources Manager:How to configure Conditional Access to block access to Azure Resources Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitare la capacità degli utenti di eseguire script all'interno di risorse di calcolo

**Linee guida:** usare configurazioni specifiche del sistema operativo o risorse di terze parti per limitare la capacità degli utenti di eseguire script all'interno di risorse di calcolo di Azure.Guidance : Use operating system specific configurations or third-party resources to limit users' ability to execute scripts within Azure compute resources.

Ad esempio, come controllare l'esecuzione di script di PowerShell in ambienti Windows:For example, how to control PowerShell script execution in Windows Environments:https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida:** il software necessario per le operazioni aziendali, ma che può comportare rischi più elevati per l'organizzazione, deve essere isolato all'interno della propria macchina virtuale e/o di una rete virtuale e sufficientemente protetto con un firewall di Azure o un gruppo di sicurezza di rete.

Come creare una rete virtuale:How to create a virtual network:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Come creare un gruppo di sicurezza di gruppo con una configurazione di sicurezza:How to create an NSG with a security config:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="secure-configuration"></a>Configurazione sicura

*Per ulteriori informazioni, vedere [Controllo di sicurezza: configurazione protetta](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Stabilire configurazioni sicure per tutte le risorse di Azure7.1: Establish secure configurations for all Azure resources

**Indicazioni:** usare Criteri di Azure o Centro sicurezza di Azure per gestire le configurazioni di sicurezza per tutte le risorse di Azure.Guidance: Use Azure Policy or Azure Security Center to maintain security configurations for all Azure Resources.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Controllare la conformità dei registri dei contenitori di Azure usando Criteri di Azure:Audit compliance of Azure container registries using Azure Policy:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Stabilire configurazioni sicure del sistema operativo

**Linee guida:** utilizzare la raccomandazione del Centro sicurezza di Azure "Correggere le vulnerabilità nelle configurazioni di sicurezza nelle macchine virtuali" per gestire le configurazioni di sicurezza in tutte le risorse di calcolo.

Come monitorare i consigli del Centro sicurezza di Azure:How to monitor Azure Security Center recommendations:https://docs.microsoft.com/azure/security-center/security-center-recommendations

Come risolvere i suggerimenti del Centro sicurezza di Azure:How to remediate Azure Security Center recommendations:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Gestire le configurazioni delle risorse di Azure protette

**Linee guida:** usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure tra le risorse di Azure.Guidance: Use Azure policy [deny] and [deploy if not exist] to enforce secure settings across your Azure resources.

Controllare la conformità dei registri dei contenitori di Azure usando Criteri di Azure:Audit compliance of Azure container registries using Azure Policy:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Informazioni sugli effetti dei criteri di Azure:Understand Azure Policy effects:https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Mantenere le configurazioni sicure del sistema operativo

**Guida**: Non applicabile. Benchmark è destinato alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Archiviare in modo sicuro la configurazione delle risorse di Azure7.5: Securely store configuration of Azure resources

**Indicazioni:** se si usano definizioni di criteri di Azure personalizzate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

Come archiviare il codice in DevOps di Azure:How to store code in Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos Documentation:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Memorizzare in modo sicuro immagini personalizzate del sistema operativo

**Guida**: Non applicabile. Il benchmark si applica alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida:** usare Criteri di Azure per avvisare, controllare e applicare le configurazioni di sistema. Inoltre, sviluppare un processo e una pipeline per la gestione delle eccezioni dei criteri.

Controllare la conformità dei registri dei contenitori di Azure usando Criteri di Azure:Audit compliance of Azure container registries using Azure Policy:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Guida**: Non applicabile. Il benchmark si applica alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementare il monitoraggio automatico della configurazione per i servizi di Azure7.9: Implement automated configuration monitoring for Azure services

**Indicazioni:** usare il Centro sicurezza di Azure per eseguire analisi di base per le risorse di Azure.Guidance : Use Azure Security Center to perform baseline scans for your Azure Resources.

Usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

Come correggere i consigli nel Centro sicurezza di Azure:How to remediate recommendations in Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

Controllare la conformità dei registri dei contenitori di Azure usando Criteri di Azure:Audit compliance of Azure container registries using Azure Policy:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementare il monitoraggio automatico della configurazione per i sistemi operativi

**Guida**: Non applicabile. Il benchmark si applica alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gestire i segreti di Azure in modo sicuro

**Linee guida:** usare l'identità del servizio gestito insieme a Archiviazione delle chiavi di Azure per semplificare e proteggere la gestione dei segreti per le applicazioni cloud.

Come integrare con le identità gestite di Azure:How to integrate with Azure Managed Identities:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Come creare un insieme di credenziali delle chiavi:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Come fornire l'autenticazione key Vault con un'identità gestita:How to provide Key Vault authentication with a managed identity:https://docs.microsoft.com/azure/key-vault/managed-identity

Usare un'identità gestita da Azure nelle attività del Registro di sistema del contenitore di Azure:Use an Azure-managed identity in Azure Container Registry tasks:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gestire le identità in modo sicuro e automatico

**Linee guida:** usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure AD. Managed Identities consente di eseguire l'autenticazione a qualsiasi servizio che supporta l'autenticazione di Azure AD, incluso Key Vault, senza credenziali nel codice.

Come configurare le identità gestite:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Usare un'identità gestita per l'autenticazione in un registro contenitori di Azure:Use a managed identity to authenticate to an Azure container registry:https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminare l'esposizione di credenziali indesiderate

**Linee guida**: Implementare Credential Scanner per identificare le credenziali all'interno del codice. Credential Scanner will also encourage moving discovered credentials to more secure locations such as Azure Key Vault.

Come configurare Credential Scanner:https://secdevtools.azurewebsites.net/helpcredscan.html


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="malware-defense"></a>Difesa da malware

*Per ulteriori informazioni, vedere [Controllo di sicurezza: Difesa da malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilizzare software antimalware gestito centralmente

**Linee guida:** usare Microsoft Antimalware per i servizi cloud di Azure e le macchine virtuali per monitorare e difendere continuamente le risorse. Per Linux, utilizzare una soluzione antimalware di terze parti.

Come configurare Microsoft Antimalware per i servizi cloud e le macchine virtuali:https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: File pre-scansione da caricare in risorse di Azure non di calcolo

**Linee guida:** Microsoft Antimalware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Registro di sistema del contenitore di Azure), tuttavia non viene eseguito sul contenuto dei clienti.

Pre-analisi di tutti i file caricati in risorse di Azure non di calcolo, ad esempio il servizio app, l'archiviazione Data Lake, l'archiviazione BLOB e così via.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Verificare che il software antimalware e le firme siano aggiornate

**Guida**: Non applicabile. Benchmark è destinato alle risorse di calcolo. Microsoft gestisce l'antimalware per la piattaforma sottostante.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="data-recovery"></a>Ripristino dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatici regolari

**Linee guida:** i dati nel Registro di sistema del contenitore di Microsoft Azure vengono sempre replicati automaticamente per garantire durabilità e disponibilità elevata. Il Registro di sistema del contenitore di Azure copia i dati in modo che siano protetti da eventi pianificati e non pianificatiAzure Container Registry copies your data so that it is protected from planned and unplanned events

Facoltativamente, eseguire la replica geografica di un registro contenitori per gestire le repliche del Registro di sistema in più aree di Azure.Optionally geo-replicate a container registry to maintain registry replicas in multiple Azure regions. 

Replica geografica nel Registro di sistema del contenitore di Azure:Geo-replication in Azure Container Registry:https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Guidance (Indicazioni):** facoltativamente, eseguire il backup delle immagini contenitore importandole da un Registro di sistema a un altro.

Eseguire il backup delle chiavi gestite dal cliente in Archiviazione delle chiavi di Azure usando gli strumenti da riga di comando di Azure o gli SDK.

Importare immagini del contenitore in un registro contenitori:Import container images to a container registry:https://docs.microsoft.com/azure/container-registry/container-registry-import-images

Come eseguire il backup delle chiavi dell'insieme di credenziali in Azure:How to backup key vault keys in Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida:** verificare il ripristino delle chiavi gestite dal cliente di cui è stato eseguito il backup in Azure Key Vault usando gli strumenti da riga di comando di Azure o gli SDK.

Come ripristinare le chiavi dell'insieme di credenziali delle chiavi di Azure in Azure:How to restore Azure Key Vault keys in Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dai clienti

**Guida:** è possibile abilitare l'eliminazione temporanea nell'insieme di credenziali delle chiavi di Azure per proteggere le chiavi da eliminazioni accidentali o dannose.

Come abilitare l'eliminazione temporanea nell'insieme di credenziali delle chiavi:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Creare una guida alla risposta agli incidenti

**Linee guida**: Creare una guida alla risposta agli incidenti per l'organizzazione. Assicurarsi che siano presenti piani di risposta agli incidenti scritti che definiscono tutti i ruoli del personale, nonché fasi di gestione/gestione degli incidenti dal rilevamento alla revisione post-incidente.

Come configurare le automazioni del flusso di lavoro all'interno del Centro sicurezza di Azure:How to configure Workflow Automations within Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Linee guida per creare un processo di risposta agli incidenti di sicurezza:Guidance on building your own security incident response process:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia di Microsoft Security Response Center di un incidente:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Il cliente può anche sfruttare la Guida alla gestione degli incidenti di sicurezza del COMPUTER del NIST per facilitare la creazione del proprio piano di risposta agli incidenti:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Creare una procedura di assegnazione dei punteggi degli eventi imprevisti e di assegnazione delle priorità

**Indicazioni:** Il Centro sicurezza di Azure assegna una gravità a ogni avviso per definire la priorità degli avvisi da esaminare per primi. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sull'analita utilizzato per emettere l'avviso, nonché il livello di probabilità che vi sia stato un intento dannoso dietro l'attività che ha portato all'avviso.

Inoltre, contrassegnare chiaramente gli abbonamenti (per ex. produzione, non-prod) e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Testare le procedure di risposta alla sicurezza

**Linee guida**: Eseguire esercizi per testare le capacità di risposta agli incidenti dei sistemi a una cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

Fare riferimento alla pubblicazione del NIST: Guida ai programmi di test, formazione ed eserciziper piani e funzionalità IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornire i dettagli di contatto degli eventi imprevisti di sicurezza e configurare le notifiche di avviso per gli incidenti di sicurezza

**Linee guida:** le informazioni di contatto relative agli eventi imprevisti di protezione verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che i dati del cliente sono stati utilizzati da una parte non legale o non autorizzata.  Esaminare gli incidenti dopo il fatto per assicurarsi che i problemi vengano risolti.

Come impostare il contatto di sicurezza del Centro sicurezza di Azure:How to set the Azure Security Center security contact:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

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

**Linee guida**: Seguire le regole di coinvolgimento Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Ulteriori informazioni sulla strategia di Microsoft e sull'esecuzione di Red Teaming e sui test di penetrazione dei siti in tempo reale sull'infrastruttura cloud, i servizi e le applicazioni gestiti da Microsoft sono disponibili qui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere il benchmark di sicurezza di [AzureSee](https://docs.microsoft.com/azure/security/benchmarks/overview) the Azure Security Benchmark
- Altre informazioni sulle linee di base per la sicurezza di AzureLearn more about [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
