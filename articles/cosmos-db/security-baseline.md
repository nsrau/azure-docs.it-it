---
title: Azure Security Baseline for Cosmos DB
description: Azure Security Baseline for Cosmos DB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fd2706b6a6880d7c9454619e1315b2b5b5404561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244274"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Azure Security Baseline for Cosmos DB

La base di riferimento per la sicurezza di Azure per Cosmos DB contiene suggerimenti che consentono di migliorare il livello di sicurezza della distribuzione.

La linea di base per questo servizio è ricavata da [Azure Security Benchmark versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce consigli su come proteggere le soluzioni cloud in Azure con le indicazioni sulle procedure consigliate.

Per altre informazioni, vedere [Panoramica delle linee di base](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)della sicurezza di Azure.For more information, see Azure Security Baselines overview .

## <a name="network-security"></a>Sicurezza di rete

*Per ulteriori informazioni, vedere [Controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale1.1: Protect resources using Network Security Groups or Azure Firewall on your Virtual Network

**Indicazioni:** usando Azure Private Link, è possibile connettersi a un account Azure Cosmos tramite un endpoint privato. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È inoltre possibile ridurre il rischio di esfiltrazione dei dati configurando un set rigoroso di regole in uscita in un gruppo di sicurezza di rete (NSG) e associando tale gruppo di sicurezza di rete alla subnet.

È anche possibile usare gli endpoint di servizio per proteggere l'account Cosmos di Azure.You can also use Service Endpoints to secure your Azure Cosmos account. Abilitando un endpoint del servizio, è possibile configurare gli account Cosmos di Azure per consentire l'accesso solo da una subnet specifica di una rete virtuale di Azure.By enabling a Service Endpoint, you can configure Azure Cosmos accounts to allow access from only a specific subnet of an Azure virtual network. Dopo aver abilitato l'endpoint del servizio di database Cosmos di Azure, è possibile limitare l'accesso a un account Cosmos di Azure con connessioni da una subnet in una rete virtuale.

È anche possibile proteggere i dati archiviati nell'account Cosmos di Azure usando i firewall IP. Azure Cosmos DB supporta i controlli di accesso IP per il supporto del firewall in ingresso. È possibile impostare un firewall IP nell'account Cosmos di Azure usando il portale di Azure, i modelli di Azure Resource Manager o tramite l'interfaccia della riga di comando di Azure o Azure PowerShell.You can set an IP firewall on the Azure Cosmos account by using the Azure portal, Azure Resource Manager templates, or through the Azure CLI or Azure PowerShell.

Azure Private Link Overview:https://docs.microsoft.com/azure/private-link/private-link-overview

Come configurare un endpoint privato per il database Cosmos di Azure:How to configure a Private Endpoint for Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza:How to create a Network Security Group with a Security Config:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Come configurare il firewall IP in Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Indicazioni:** usare il Centro sicurezza di Azure e seguire i consigli di protezione della rete per proteggere le risorse di rete correlate all'account Cosmos di Azure.Guidance: Use Azure Security Center and follow network protection recommendations to help secure network resources related to your Azure Cosmos account.

Quando le macchine virtuali vengono distribuite nella stessa rete virtuale dell'account Cosmos di Azure, è possibile usare un gruppo di sicurezza di rete (NSG) per ridurre il rischio di esfiltrazione dei dati. Abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione di Azure per i controlli del traffico. È anche possibile inviare log di flusso del gruppo di sicurezza di rete a un'area di lavoro di Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso di traffico nel cloud di Azure.You may also send NSG flow logs to a Log Analytics workspace and use Traffic Analytics to provide insights into traffic flow in your Azure cloud. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare hot spot, identificare le minacce alla sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni errate della rete.

Informazioni sulla sicurezza di rete fornita dal Centro sicurezza di Azure:Understand Network Security provided by Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Abilitazione dei registri di flusso del gruppo di sicurezza di rete:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare e usare Analisi del traffico:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteggere le applicazioni Web critiche

**Linee guida:** utilizzare la funzionalità di condivisione delle risorse tra origini (CORS, Cross-Origin Resource Sharing) per consentire a un'applicazione Web in esecuzione in un dominio di accedere alle risorse in un altro dominio. I Web browser implementano una limitazione di sicurezza nota come criteri di corrispondenza dell'origine, che impedisce a una pagina Web di chiamare API in un dominio diverso. Tuttavia, CORS fornisce un modo sicuro per consentire al dominio di origine di chiamare le API in un altro dominio. Dopo l'attivazione del supporto di CORS per l'account Azure Cosmos vengono valutate solo le richieste autenticate, per determinare se sono consentite in base alle regole specificate.

Configurare la condivisione di risorse tra origini:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida:** usare Advanced Threat Protection (ATP) per Azure Cosmos DB . ATP per Azure Cosmos DB offre un ulteriore livello di informazioni sulla sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account Cosmos di Azure.ATP for Azure Cosmos DB provides an additional layer of security intelligence that detects unusual and potentially harmful attempts to access or exploit Azure Cosmos accounts. Questo livello di protezione consente di affrontare le minacce e integrarle con i sistemi di monitoraggio della sicurezza centralizzati.

Abilitare DDoS Protection Standard nelle reti virtuali associate alle istanze del database Cosmos di Azure per proteggersi dagli attacchi DDoS.Enable DDoS Protection Standard on the Virtual Networks associated with your Azure Cosmos DB instances to protection against DDoS attacks. Usare Azure Security Center Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP Internet dannosi o inutilizzati noti.

Come configurareAzure Cosmos DB Advanced Threat Protection:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Come configurare la protezione DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Informazioni su Azure Security Center Integrated Threat Intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Registrare i pacchetti di rete e i registri di flusso

**Guida:** abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione per il controllo del traffico. È possibile inviare log di flusso del gruppo di sicurezza di rete a un'area di lavoro di Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso di traffico nel cloud di Azure.You can send NSG flow logs to a Log Analytics workspace and use Traffic Analytics to provide insights into traffic flow in your Azure cloud. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare hot spot, identificare le minacce alla sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni errate della rete.

Abilitazione dei registri di flusso del gruppo di sicurezza di rete:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare e usare Analisi del traffico:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Linee guida:** usare Advanced Threat Protection (ATP) per Azure Cosmos DB. ATP per Azure Cosmos DB offre un ulteriore livello di informazioni sulla sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account Cosmos di Azure.ATP for Azure Cosmos DB provides an additional layer of security intelligence that detects unusual and potentially harmful attempts to access or exploit Azure Cosmos accounts. Questo livello di protezione consente di affrontare le minacce e integrarle con i sistemi di monitoraggio della sicurezza centralizzati. 

Come configurare Cosmos DB Advanced Threat Protection:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gestire il traffico verso le applicazioni web

**Orientamento**: Non applicabile; È consigliabile per le applicazioni Web in esecuzione nel servizio app di Azure o nelle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e l'overhead amministrativo delle regole di sicurezza di rete

**Indicazioni:** per le risorse che devono accedere all'account Cosmos di Azure, usare i tag del servizio Rete virtuale per definire i controlli di accesso alla rete in securitygGruppi di rete o Firewall di Azure.Guidance : For resources that need access to your Azure Cosmos account, use Virtual Network service tags to define network access controls on network securitygGroups or Azure Firewall. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag del servizio (ad esempio, AzureCosmosDB) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi degli indirizzi inclusi nel tag del servizio e aggiorna automaticamente il tag del servizio quando gli indirizzi cambiano.

Per altre informazioni sull'uso dei tag di servizio:For more information about using service tags:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida:** definire e implementare configurazioni di sicurezza standard per le risorse di rete con Criteri di Azure.Guidance: Define and implement standard security configurations for network resources with Azure Policy. Usare gli alias dei criteri di Azure negli spazi dei nomi "Microsoft.DocumentDB" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle istanze di Azure Cosmos DB. È anche possibile usare le definizioni dei criteri predefinite per Azure Cosmos DB, ad esempio:You may also make use of built-in policy definitions for Azure Cosmos DB, such as:

- Distribuisci Advanced Threat Protection per gli account Cosmos DB

- I database Cosmos DB devono usare un endpoint servizio di rete virtuale

È anche possibile usare Blueprint di Azure per semplificare le distribuzioni di Azure su larga scala creando una creazione di pacchetti di elementi dell'ambiente chiave, ad esempio modelli di Azure Resource Manager, controllo degli accessi in base al ruolo e criteri in una singola definizione del blueprint. È possibile applicare facilmente il blueprint a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare un blueprint di Azure:How to create an Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regole di configurazione del traffico dei documenti

**Linee guida:** usare i tag per le risorse di rete associate alla distribuzione di Database Cosmos di Azure per organizzarli logicamente in una tassonomia.

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilizzare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze del database Cosmos di Azure.Guidance : Use Azure Activity Log to monitor network resource configurations and detect changes for network resources related to your Azure Cosmos DB instances. Creare avvisi all'interno di Monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse di rete critiche. 

Come visualizzare e recuperare gli eventi del log attività di Azure:How to view and retrieve Azure Activity Log events:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Come creare avvisi in Monitoraggio di Azure:How to create alerts in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizzare origini di sincronizzazione dell'ora approvate

**Linee guida:** Microsoft gestisce l'origine ora usata per le risorse di Azure, ad esempio Azure Cosmos DB per i timestamp nei log.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurare la gestione dei registri di protezione centralizzati

**Indicazioni:** eseguire l'inserimento dei log tramite Monitoraggio di Azure per aggregare i dati di sicurezza generati da Azure Cosmos DB. In Monitoraggio di Azure usare le aree di lavoro di Log Analytics per eseguire query ed eseguire analisi e gli account di archiviazione per l'archiviazione a lungo termine/archiviazione. In alternativa, è possibile abilitare e a bordo i dati in Azure Sentinel o in una gestione di eventi e incidenti di sicurezza di terze parti. 

Come abilitare i log di diagnostica per azure Cosmos DB:How to enable diagnostic logs for Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Come eseguire l'onboarding di Azure Sentinel:How to onboard Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Abilitare la registrazione di controllo per le risorse di Azure2.3: Abilitare la registrazione di controllo per le risorse di Azure2.

**Guida:** abilitare le impostazioni di diagnostica per Il database Cosmos di Azure e inviare i log a un'area di lavoro di Log Analytics o a un account di archiviazione. Le impostazioni di diagnostica in Azure Cosmos DB vengono usate per raccogliere i log delle risorse. Questi registri vengono acquisiti per ogni richiesta e vengono anche definiti "registri piano dati". Alcuni esempi di operazioni del piano dati includono l'eliminazione, l'inserimento e la lettura. È anche possibile abilitare le impostazioni di diagnostica del log attività di Azure e inviarle alla stessa area di lavoro Log Analytics.You may also enable Azure Activity Log Diagnostic Settings and send them to the same Log Analytics Workspace.

Come abilitare le impostazioni di diagnostica per il database Cosmos di Azure:How to enable Diagnostic Settings for Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Come abilitare le impostazioni di diagnostica per il log attività di Azure:How to enable Diagnostic Settings for Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Raccogliere i registri di sicurezza dai sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dello spazio di archiviazione del registro di protezione

**Linee guida:** in Monitoraggio di Azure impostare il periodo di conservazione dei log per le aree di lavoro di Log Analytics associate alle istanze di Azure Cosmos DB in base alle normative di conformità dell'organizzazione.

Come impostare i parametri di conservazione dei log:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i registri

**Linee guida:** è possibile eseguire query in Log Analytics in un'area di lavoro per cercare termini, identificare tendenze, analizzare i modelli e fornire molte altre informazioni in base ai log di database Cosmos di Azure raccolti.

Come eseguire query per il database Cosmos di Azure nelle aree di lavoro di Log Analytics:How to perform queries for Azure Cosmos DB in Log Analytics Workspaces:https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Abilitare gli avvisi per attività anomale

**Indicazioni:** in Centro sicurezza di Azure abilitare Advanced Threat Protection per Azure Cosmos DB per monitorare le attività anomale nei log e negli eventi di sicurezza. Abilitare le impostazioni di diagnostica in Azure Cosmos DB e inviare log a un'area di lavoro di Log Analytics.Enable diagnostic settings in Azure Cosmos DB and send logs to a Log Analytics workspace.

 

È anche possibile eseguire l'onboarding dell'area di lavoro log Analytics in Azure Sentinel in quanto fornisce una soluzione di risposta automatica (SOAR) di orchestrazione di sicurezza. Ciò consente di creare e utilizzare playbook (soluzioni automatizzate) per risolvere i problemi di sicurezza. Inoltre, è possibile creare avvisi di log personalizzati nell'area di lavoro di Log Analytics usando Monitoraggio di Azure.Additionally, you can create custom log alerts in your Log Analytics workspace using Azure Monitor.

Elenco di avvisi di protezione dalle minacce per Azure Cosmos DB:List of threat protection alerts for Azure Cosmos DB:https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Come eseguire l'onboarding di Azure Sentinel:How to onboard Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Creare, visualizzare e gestire gli avvisi di log tramite Monitoraggio di Azure:Create, view, and manage log alerts using Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizzare la registrazione antimalware

**Orientamento**: Non applicabile; Azure Cosmos DB non elabora né produce log correlati antimalware.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: Abilitare la registrazione delle query DNS

**Orientamento**: Non applicabile; Azure Cosmos DB non elabora né produce log correlati al DNS.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: Abilitare la registrazione di controllo della riga di comando

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per ulteriori informazioni, vedere [Controllo di sicurezza: controllo delle identità e degli accessi](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Mantenere un inventario degli account amministrativi

**Linee guida:** è possibile usare il riquadro Controllo identità e accessi (IAM) nel portale di Azure per configurare il controllo degli accessi in base al ruolo e gestire l'inventario delle risorse di Azure Cosmos DB.Guidance : You can use the Identity and Access control (IAM) pane in the Azure portal to configure role-based access control (RBAC) and maintain inventory on Azure Cosmos DB resources. I ruoli vengono applicati a utenti, gruppi, entità servizio e identità gestite in Active Directory. È possibile utilizzare ruoli predefiniti o ruoli personalizzati per singoli utenti e gruppi.

Il database Cosmos di Azure fornisce RBAC predefiniti per scenari di gestione comuni in Azure Cosmos DB. Una persona che dispone di un profilo in Azure Active Directory (AD) può assegnare questi ruoli RBAC a utenti, gruppi, entità servizio o identità gestite per concedere o negare l'accesso alle risorse e alle operazioni nelle risorse del database Cosmos di Azure.A individual who has a profile in Azure Active Directory (AD) can assign these RBAC roles to users, groups, service principals, or managed identities to grant or deny access to resources and operations on Azure Cosmos DB resources.

È anche possibile usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account membri di gruppi amministrativi. 

Inoltre, alcune azioni in Azure Cosmos DB possono essere controllate con Azure Active Directory e chiavi master specifiche dell'account.  Utilizzare l'impostazione dell'account 'disableKeyBasedMetadataWriteAccess' per controllare l'accesso ai tasti.

Comprendere il controllo degli accessi in base al ruolo in Azure Cosmos DB:Understand role-based access control in Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Creare ruoli personalizzati usando le azioni del database Cosmos di Azure (spazio dei nomi Microsoft.DocumentDB):Build your own custom roles using Azure Cosmos DB Actions (Microsoft.DocumentDB namespace):https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Creare un nuovo ruolo in Azure Active Directory:Create a new role in Azure Active Directory:https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Come ottenere un ruolo di directory in Azure Active Directory con PowerShell:How to get a directory role in Azure Active Directory with PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Come ottenere i membri di un ruolo di directory in Azure Active Directory con PowerShell:How to get members of a directory role in Azure Active Directory with PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Limitare l'accesso degli utenti solo alle operazioni sui dati:Restrict user access to data operations only:https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Modificare le password predefinite, ove applicabile

**Linee guida:** il concetto di password predefinite o vuote non esiste in relazione ad Azure AD o Azure Cosmos DB. Al contrario, Azure Cosmos DB usa due tipi di chiavi per autenticare gli utenti e fornire l'accesso ai dati e alle risorse. chiavi master e token di risorse. Le chiavi possono essere rigenerate in qualsiasi momento.

Informazioni sull'accesso sicuro ai dati in Azure Cosmos DB:Understanding secure access to data in Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Come rigenerare le chiavi del database Cosmos di Azure:How to regenerate Azure Cosmos DB Keys:https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Come accedere a livello di codice alle chiavi usando Azure Active Directory:How to programmatically access keys using Azure Active Directory:https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizzare account amministrativi dedicati

**Orientamento**: Non applicabile; Azure Cosmos DB non supporta gli account amministratore.  Tutti gli accessi sono integrati con Azure Active Directory e il controllo degli accessi in base al ruolo di Azure.All access is integrated with Azure Active Directory and Azure role-based access control (RBAC).



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida:** Azure Cosmos DB usa due tipi di chiavi per autorizzare gli utenti e non supporta Single Sign-On (SSO) a livello di piano dati. L'accesso al piano di controllo per Cosmos DB è disponibile tramite API REST e supporta SSO. To authenticate, set the Authorization header for your requests to a JSON Web Token that you obtain from Azure Active Directory.

Informazioni sull'API REST di Azure Database for Cosmos DB:Understand Azure Database for Cosmos DB REST API:https://docs.microsoft.com/rest/api/cosmos-db/

Comprendere SSO con Azure Active Directory:Understand SSO with Azure Active Directory:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Indicazioni:** abilitare Azure Active Directory Multi-Factor Authentication e seguire le raccomandazioni di gestione delle identità e degli accessi del Centro sicurezza di Azure.Guidance: Enable Azure Active Directory Multi-Factor Authentication and follow Azure Security Center Identity and Access Management recommendations.

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Come monitorare l'identità e l'accesso all'interno del Centro sicurezza di Azure:How to monitor identity and access within Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizzare macchine dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

**Guida:** usare le workstation con accesso privilegiato (PAW) con Multi-Factor Authentication configurato per accedere alle risorse di Azure e configurarlo.

Ulteriori informazioni sulle workstation con accesso privilegiato:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registrare e avvisare su attività sospette da account amministrativi

**Linee guida:** usare Advanced Threat Protection (ATP) per Azure Cosmos DB. ATP per Azure Cosmos DB offre un ulteriore livello di informazioni sulla sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account Cosmos di Azure.ATP for Azure Cosmos DB provides an additional layer of security intelligence that detects unusual and potentially harmful attempts to access or exploit Azure Cosmos accounts. Questo livello di protezione consente di affrontare le minacce e integrarle con i sistemi di monitoraggio della sicurezza centralizzati. 

Inoltre, è possibile usare Azure Active Directory (AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verifica un'attività sospetta o non sicura nell'ambiente.

Usare Rilevamenti dei rischi di Azure AD per visualizzare avvisi e report sul comportamento rischioso degli utenti.

Come distribuire Privileged Identity Management (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Comprendere i rilevamenti dei rischi di Azure AD:Understand Azure AD risk detections:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo da posizioni approvate

**Guida**: Configurare la condizione di posizione di un criterio di accesso condizionale e gestire i percorsi denominati. Con le posizioni denominate, è possibile creare raggruppamenti logici di intervalli di indirizzi IP o paesi e aree geografiche. È possibile limitare l'accesso alle risorse sensibili, ad esempio le istanze di Azure Cosmos DB, alle posizioni denominate configurate.

Come configurare i percorsi denominati in Azure:How to configure Named Locations in Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida:** usare Azure Active Directory (AD) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD inoltre consente di archiviare le credenziali utente in Azure AD.

Come creare e configurare un'istanza di Azure Active Directory:How to create and configure an Azure Active Directory instance:https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Come configurare e gestire l'autenticazione di Azure Active Directory con SQL di Azure:How to configure and manage Azure Active Directory authentication with Azure SQL:https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rivedere e riconciliare regolarmente l'accesso degli utenti

**Indicazioni:** Azure Active Directory fornisce i log per individuare gli account non aggiornati. Inoltre, è possibile usare le revisioni di Azure Identity Access per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso dell'utente può essere rivisto regolarmente per assicurarsi che solo gli utenti giusti abbiano accesso continuo.

Come usare le recensioni di azure Identity Access:How to use Azure Identity Access Reviews:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorare i tentativi di accesso agli account disattivati

**Indicazioni:** è possibile creare impostazioni di diagnostica per gli account utente di Azure Active Directory, inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics in cui è possibile configurare gli avvisi desiderati.

Come integrare i log attività di Azure in Monitoraggio di Azure:How to integrate Azure Activity Logs into Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Scostamenti comportamento di avviso per l'accesso all'account

**Linee guida:** usare Advanced Threat Protection (ATP) per Azure Cosmos DB. ATP per Azure Cosmos DB offre un ulteriore livello di informazioni sulla sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account Cosmos di Azure.ATP for Azure Cosmos DB provides an additional layer of security intelligence that detects unusual and potentially harmful attempts to access or exploit Azure Cosmos accounts. Questo livello di protezione consente di affrontare le minacce e integrarle con i sistemi di monitoraggio della sicurezza centralizzati. 

È anche possibile usare la funzionalità Azure AD Identity Protection e il rilevamento dei rischi per configurare le risposte automatiche alle azioni sospette rilevate relative alle identità utente. Inoltre, è possibile inserire i log in Azure Sentinel per ulteriori indagini.

Come visualizzare gli accessi rischiosi di Azure Active Directory:How to view Azure Active Directory risky sign-ins:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Come configurare e abilitare i criteri di rischio di Identity Protection:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Come eseguire l'onboarding di Azure Sentinel:How to onboard Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: Attualmente non disponibile; Customer Lockbox non ancora supportato per Database di Azure per Cosmos DB.

Elenco dei servizi supportati da Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Non applicabile

## <a name="data-protection"></a>Protezione dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Mantenere un inventario di informazioni sensibili

**Linee guida:** usare i tag per facilitare il rilevamento delle istanze di Azure Cosmos DB che archiviano o elaborano informazioni riservate.

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolare i sistemi che memorizzano o elaborano informazioni sensibili

**Linee guida:** Implementare sottoscrizioni e/o gruppi di gestione separati per lo sviluppo, il test e la produzione. Azure Cosmos DB instances are separated by virtual network/subnet, tagged appropriately, and secured within a network security group (NSG) or Azure Firewall. Le istanze di Azure Cosmos DB che archiviano dati sensibili devono essere isolate. Usando Azure Private Link, è possibile connettersi a un account dell'istanza del database Cosmos di Azure tramite un endpoint privato. L'endpoint privato è un set di indirizzi IP privati in una subnet all'interno della rete virtuale. È quindi possibile limitare l'accesso agli indirizzi IP privati selezionati. 

Come creare sottoscrizioni di Azure aggiuntive:How to create additional Azure subscriptions:https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare i gruppi di gestione:https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Come configurare un endpoint privato per il database Cosmos di Azure:How to configure a Private Endpoint for Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza:How to create a Network Security Group with a Security Config:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorare e bloccare il trasferimento non autorizzato di informazioni sensibili

**Guida:** è possibile distribuire Advanced Threat Protection per Azure Cosmos DB, che rileverà attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database. Attualmente può attivare i seguenti avvisi:

- Accesso da luoghi insoliti

- Estrazione di dati insoliti

Inoltre, quando si usano macchine virtuali per accedere alle istanze del database Cosmos di Azure, usare Private Link, Firewall, gruppi di sicurezza di rete e tag di servizio per ridurre la possibilità di esfiltrazione dei dati. Microsoft gestisce l'infrastruttura sottostante per Azure Cosmos DB e ha implementato controlli rigorosi per prevenire la perdita o l'esposizione dei dati dei clienti.

Come configurare Cosmos DB Advanced Threat Protection:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Crittografare tutte le informazioni sensibili in transito

**Linee guida:** tutte le connessioni a Azure Cosmos DB supportano HTTPS. Azure Cosmos DB also supports TLS1.2. È possibile applicare una versione TLS minima lato server. Per farlo, si [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)prega di contattare .

Panoramica di Cosmos DB Security:https://docs.microsoft.com/azure/cosmos-db/database-security

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilizzare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida:** le funzionalità automatiche di identificazione dei dati, classificazione e prevenzione delle perdite non sono ancora disponibili per Azure Cosmos DB. Tuttavia, è possibile usare l'integrazione di Ricerca cognitiva di Azure per la classificazione e l'analisi dei dati. È inoltre possibile implementare una soluzione di terze parti, se necessario, ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.

Indicizzare i dati del https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&ampdatabase Cosmos di Azure con Ricerca cognitiva di Azure: ;bc/azure/cosmos-db/breadcrumb/toc.jsonIndex Azure Cosmos DB data with Azure Cognitive Search: ;bc/azure/cosmos-db/breadcrumb/toc.json

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse4.6: Usare il controllo degli accessi in base al ruolo di

**Linee guida:** Azure Cosmos DB fornisce il controllo degli accessi in base al ruolo incorporato per scenari di gestione comuni in Azure Cosmos DB. Una persona che dispone di un profilo in Azure Active Directory può assegnare questi ruoli RBAC a utenti, gruppi, entità servizio o identità gestite per concedere o negare l'accesso alle risorse e alle operazioni nelle risorse del database Cosmos di Azure.A individual who has a profile in Azure Active Directory can assign these RBAC roles to users, groups, service principals, or managed identities to grant or deny access to resources and operations on Azure Cosmos DB resources. L'ambito delle assegnazioni di ruolo è limitato al solo piano di controllo, che include l'accesso ad account Cosmos di Azure, database, contenitori e offerte (velocità effettiva).

Come implementare il controllo degli accessi in base al ruolo in Azure Cosmos DB:How to implement RBAC in Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizzare la prevenzione della perdita di dati basata su host per applicare il controllo degli accessi

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per Cosmos DB e ha implementato controlli rigorosi per prevenire la perdita o l'esposizione dei dati dei clienti.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografare le informazioni sensibili inattivi

**Linee guida**: Tutti i dati utente memorizzati in Cosmos DB sono criptati inattivi per impostazione predefinita. Non ci sono controlli per disattivarlo. Azure Cosmos DB usa la crittografia AES-256 in tutte le aree in cui è in esecuzione l'account.

Per impostazione predefinita, Microsoft gestisce le chiavi usate per crittografare i dati nell'account Cosmos di Azure.By default, Microsoft manages the keys that are used to encrypt the data in your Azure Cosmos account. Facoltativamente, è possibile scegliere di aggiungere un secondo livello di crittografia con le proprie chiavi.

Informazioni sulla crittografia inattivi con Azure Cosmos DB:Understanding encryption at rest with Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Informazioni sulla gestione delle chiavi per la crittografia inattivi con Azure Cosmos DB:Understanding key management for encryption at rest with Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Come configurare le chiavi gestite dal cliente per l'account del database Cosmos di Azure:How to configure customer-managed keys for your Azure Cosmos DB account:https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e avvisare le modifiche alle risorse critiche di Azure4.9: Log and alert on changes to critical Azure resources

**Linee guida:** usare Monitoraggio di Azure con il log attività di Azure per creare avvisi per quando vengono apportate modifiche alle istanze di produzione di Azure Cosmos DB.

Come creare avvisi per gli eventi del log attività di Azure:How to create alerts for Azure Activity Log events:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Come creare avvisi per gli eventi del log attività di Azure:How to create alerts for Azure Activity Log events:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per ulteriori informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Eseguire strumenti automatici di scansione delle vulnerabilità

**Linee guida:** seguire le raccomandazioni dal Centro sicurezza di Azure per le istanze del database Cosmos di Azure.Guidance : Follow recommendations from Azure Security Center for your Azure Cosmos DB instances. 

Microsoft esegue l'applicazione di patch di sistema e la gestione delle vulnerabilità negli host sottostanti che supportano le istanze di Azure Cosmos DB. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.

Funzionalità supportate disponibili nel Centro sicurezza di Azure:Supported features available in Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuire una soluzione di gestione automatica delle patch del sistema operativo

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuire una soluzione di gestione automatica delle patch software di terze parti

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Confrontare le scansioni delle vulnerabilità back-to-back

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilizzare un processo di valutazione del rischio per assegnare priorità alla correzione delle vulnerabilità individuate

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per ulteriori informazioni, vedere [Controllo di sicurezza: inventario e gestione delle risorse](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Usare Azure Asset Discovery

**Linee guida:** usare il portale di Azure o Azure Resource Graph per individuare tutte le risorse (non limitate al database Cosmos di Azure, ma anche le risorse come calcolo, altre risorse di archiviazione, rete, porte e protocolli e così via) all'interno delle sottoscrizioni.  Assicurarsi di disporre delle autorizzazioni appropriate nel tenant e di essere in grado di enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Anche se le risorse di Azure classiche possono essere individuate tramite Resource Graph, è consigliabile creare e usare le risorse di Azure Resource Manager in futuro.

Come creare query con Azure Graph:How to create queries with Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Come visualizzare le sottoscrizioni di Azure:How to view your Azure Subscriptions:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Informazioni sul controllo degli accessi in base al ruolo di Azure:Understanding Azure role-based access control:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Gestire i metadati delle risorse

**Linee guida:** applicare tag alle istanze di Azure Cosmos DB e alle risorse correlate con metadati per organizzarle logicamente in una tassonomia.

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Quali risorse Cosmos DB di Azure supportano i tag:Which Azure Cosmos DB resources support tags:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminare risorse di Azure non autorizzate

**Linee guida:** usare il tagging, i gruppi di gestione e sottoscrizioni separate, se appropriato, per organizzare e tenere traccia delle risorse, incluse, ma non limitate, alle risorse di Azure Cosmos DB. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate dalla sottoscrizione in modo tempestivo.

Come creare sottoscrizioni di Azure aggiuntive:How to create additional Azure subscriptions:https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare i gruppi di gestione:https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e utilizzare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Gestire un inventario delle risorse di Azure approvate e dei titoli software

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo e ad Azure nel suo complesso.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorare le risorse di Azure non approvate

**Linee guida:** usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:Guidance : Use Azure policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Inoltre, usare Azure Resource Graph per eseguire query/individuare risorse all'interno delle sottoscrizioni.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare query con Azure Graph:How to create queries with Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorare le applicazioni software non approvate all'interno delle risorse di calcolo

**Orientamento**: Non applicabile; questa linea di base è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Rimuovere le risorse e le applicazioni software di Azure non approvate

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo e ad Azure nel suo complesso.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="68-use-only-approved-applications"></a>6.8: Utilizzare solo applicazioni approvate

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="69-use-only-approved-azure-services"></a>6.9: Usare solo i servizi di Azure approvati

**Linee guida:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:Guidance : Use Azure Policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

- Tipi di risorse non consentiti 

- Tipi di risorse consentiti

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come negare un tipo di risorsa specifico con Criteri di Azure:How to deny a specific resource type with Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementare l'elenco delle applicazioni approvate

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Limitare la capacità degli utenti di interagire con AzureResources Manager tramite script

**Linee guida:** usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app "Gestione di Microsoft Azure". Ciò può impedire la creazione e le modifiche alle risorse all'interno di un ambiente ad alta sicurezza.

Come configurare l'accesso condizionale per bloccare l'accesso a Azure Resource Manager:How to configure Conditional Access to block access to Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitare la capacità degli utenti di eseguire script all'interno di risorse di calcolo

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separare fisicamente o logicamente le applicazioni ad alto rischio

**Orientamento**: Non applicabile; questa linea guida è destinata alle applicazioni Web in esecuzione nel servizio app di Azure o nelle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per ulteriori informazioni, vedere [Controllo di sicurezza: configurazione protetta](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Stabilire configurazioni sicure per tutte le risorse di Azure7.1: Establish secure configurations for all Azure resources

**Linee guida:** definire e implementare configurazioni di sicurezza standard per le istanze del database Cosmos con Criteri di Azure.Guidance : Define and implement standard security configurations for your Cosmos DB instances with Azure Policy. Usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.DocumentDB" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze del database Cosmos.Use Azure Policy aliases in the "Microsoft.DocumentDB" namespace to create custom policies to audit or enforce the configuration of your Cosmos DB instances. È anche possibile usare le definizioni dei criteri predefinite per Azure Cosmos DB, ad esempio:You may also make use of built-in policy definitions for Azure Cosmos DB, such as:

- Distribuisci Advanced Threat Protection per gli account Cosmos DB

- I database Cosmos DB devono usare un endpoint servizio di rete virtuale

Come visualizzare gli alias dei criteri di Azure disponibili:How to view available Azure Policy Aliases:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Stabilire configurazioni sicure del sistema operativo

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Gestire le configurazioni delle risorse di Azure protette

**Linee guida:** usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure tra le risorse di Azure.Guidance: Use Azure policy [deny] and [deploy if not exist] to enforce secure settings across your Azure resources.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Informazioni sugli effetti dei criteri di Azure:Understand Azure Policy Effects:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Mantenere le configurazioni sicure del sistema operativo

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Archiviare in modo sicuro la configurazione delle risorse di Azure7.5: Securely store configuration of Azure resources

**Linee guida:** se si usano definizioni di criteri di Azure personalizzate per il database Cosmos o le risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

Documentazione di https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops Azure Repos:Azure Repos Documentation:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Memorizzare in modo sicuro immagini personalizzate del sistema operativo

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida:** usare gli alias dei criteri di Azure nello spazio dei nomi "Microsoft.DocumentDB" per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Inoltre, sviluppare un processo e una pipeline per la gestione delle eccezioni dei criteri.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementare il monitoraggio automatico della configurazione per i servizi di Azure7.9: Implement automated configuration monitoring for Azure services

**Linee guida:** usare gli alias dei criteri di Azure nello spazio dei nomi "Microsoft.DocumentDB" per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [deploy if not exist] per applicare automaticamente le configurazioni per le istanze del database di Azure Cosmos e le risorse correlate. 

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementare il monitoraggio automatico della configurazione per i sistemi operativi

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gestire i segreti di Azure in modo sicuro

**Linee guida:** per le macchine virtuali di Azure o le applicazioni Web in esecuzione nel servizio app di Azure usate per accedere alle istanze del database Cosmos di Azure, usare l'identità del servizio gestito insieme a Vault delle chiavi di Azure per semplificare e proteggere la gestione dei segreti del database di Azure Cosmos.Guidance : For Azure virtual machines or web applications running on Azure App Service being used to access your Azure Cosmos DB instances, use Managed Service Identity in conjunction with Azure Key Vault to simplify and secure Azure Cosmos DB secret management. Verificare che l'eliminazione temporanea dell'insieme di credenziali delle chiavi sia abilitata.

Come integrare con le identità gestite di Azure:How to integrate with Azure Managed Identities:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Come creare un insieme di credenziali delle chiavi:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Come fornire l'autenticazione key Vault con un'identità gestita:How to provide Key Vault authentication with a managed identity:https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gestire le identità in modo sicuro e automatico

**Linee guida:** per le macchine virtuali di Azure o le applicazioni Web in esecuzione nel servizio app di Azure usate per accedere alle istanze del database Cosmos di Azure, usare l'identità del servizio gestito insieme a Vault delle chiavi di Azure per semplificare e proteggere la gestione dei segreti del database di Azure Cosmos.Guidance : For Azure virtual machines or web applications running on Azure App Service being used to access your Azure Cosmos DB instances, use Managed Service Identity in conjunction with Azure Key Vault to simplify and secure Azure Cosmos DB secret management.

Usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (AD). Managed Identities consente di eseguire l'autenticazione a qualsiasi servizio che supporta l'autenticazione di Azure AD, incluso Key Vault, senza credenziali nel codice.

Come configurare le identità gestite:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Come integrare con le identità gestite di Azure:How to integrate with Azure Managed Identities:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminare l'esposizione di credenziali indesiderate

**Linee guida**: Implementare Credential Scanner per identificare le credenziali all'interno del codice. Credential Scanner will also encourage moving discovered credentials to more secure locations such as Azure Key Vault.

Come impostare Credential Scanner:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="malware-defense"></a>Difesa da malware

*Per ulteriori informazioni, vedere [Controllo di sicurezza: Difesa da malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilizzare software antimalware gestito centralmente

**Orientamento**: Non applicabile; questa linea guida è destinata alle risorse di calcolo. Microsoft Antimalware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, servizio app di Azure), tuttavia non viene eseguito sul contenuto dei clienti.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: File pre-scansione da caricare in risorse di Azure non di calcolo

**Linee guida:** Microsoft Antimalware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Servizio app di Azure), tuttavia non viene eseguito sul contenuto dei clienti.

È responsabilità dell'utente eseguire la preanalisi di tutti i file caricati in risorse di Azure non di calcolo, incluso Azure Cosmos DB. Microsoft non può accedere ai dati dei clienti e pertanto non può eseguire scansioni antimalware dei contenuti dei clienti per conto dell'utente.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Verificare che il software antimalware e le firme siano aggiornate

**Orientamento**: Non applicabile; benchmark è destinato alle risorse di calcolo. Microsoft Antimalware è abilitato nell'host sottostante che supporta i servizi di Azure, tuttavia non viene eseguito sul contenuto dei clienti.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="data-recovery"></a>Ripristino dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatici regolari

**Linee guida:** Azure Cosmos DB crea snapshot dei dati ogni quattro ore. Tutti i backup sono archiviati separatamente in un servizio di archiviazione e replicati a livello globale per garantire la resilienza in caso di emergenze a livello di area. In qualsiasi momento risultano disponibili solo gli ultimi due snapshot. Tuttavia, se il contenitore o il database viene eliminato, Azure Cosmos DB conserva gli snapshot esistenti di un determinato contenitore o database per 30 giorni. Contattare il supporto di Azure per il ripristino da un backup.

Informazioni sui backup automatici di Azure Cosmos DB:Understanding Azure Cosmos DB Automated Backups:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: Azure Cosmos DB esegue automaticamente il backup dei dati a intervalli regolari. Se il database o il contenitore viene eliminato, è possibile presentare un ticket di supporto o chiamare il supporto di Azure per ripristinare i dati dai backup online automatici. Il supporto di Azure è disponibile solo per i piani selezionati, ad esempio Standard, Developer e piani superiori a loro. Per il ripristino di uno snapshot specifico del backup, Azure Cosmos DB richiede che i dati siano stati disponibili per la durata del ciclo di backup per tale snapshot. 

Se si utilizza Key Vault per archiviare le credenziali per le istanze Cosmos DB, assicurarsi backup automatici regolari delle chiavi.

Informazioni sui backup automatici di Azure Cosmos DB:Understand Azure Cosmos DB Automated Backups:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Come ripristinare i dati in Azure Cosmos DB:How to restore data in Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Come eseguire il backup delle chiavi dell'insieme di credenziali:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Condiviso

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida:** se il database o il contenitore viene eliminato, è possibile presentare un ticket di supporto o chiamare il supporto di Azure per ripristinare i dati dai backup automatici online. Il supporto di Azure è disponibile solo per i piani selezionati, ad esempio Standard, Developer e piani superiori a loro. Per il ripristino di uno snapshot specifico del backup, Azure Cosmos DB richiede che i dati siano stati disponibili per la durata del ciclo di backup per tale snapshot.

Testare il ripristino dei segreti archiviati in Azure Key Vault tramite PowerShell.Test restoration of your secrets stored in Azure Key Vault using PowerShell. Il cmdlet Restore-AzureKeyVaultKey crea una chiave nell'insieme di credenziali delle chiavi specificato. Questa chiave è una replica della chiave di backup nel file di input e ha lo stesso nome della chiave originale.

Informazioni sui backup automatici di Azure Cosmos DB:Understand Azure Cosmos DB Automated Backups:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Come ripristinare i dati in Azure Cosmos DB:How to restore data in Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Come ripristinare i segreti dell'insieme di credenziali delle chiavi di Azure:How to restore Azure Key Vault Secrets:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dai clienti

**Linee guida**: Poiché tutti i dati utente memorizzati in Cosmos DB vengono crittografati a riposo e nel trasporto, non è necessario intraprendere alcuna azione. In altre parole, per impostazione predefinita, la crittografia dei dati inattivi è attiva. Non è possibile attivarla o disattivarla. Azure Cosmos DB usa la crittografia AES-256 in tutte le aree in cui è in esecuzione l'account.

Abilitare l'eliminazione temporanea nell'insieme di credenziali delle chiavi per proteggere le chiavi da eliminazioni accidentali o dannose.

Comprendere la crittografia dei dati in Azure Cosmos DB:Understand data encryption in Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Come abilitare l'eliminazione temporanea nell'insieme di credenziali delle chiavi:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Creare una guida alla risposta agli incidenti

**Linee guida**: Creare una guida alla risposta agli incidenti per l'organizzazione. Assicurarsi che siano presenti piani di risposta agli incidenti scritti che definiscono tutti i ruoli del personale, nonché fasi di gestione/gestione degli incidenti dal rilevamento alla revisione post-incidente.

È inoltre possibile utilizzare la Guida alla gestione degli incidenti di sicurezza del computer del NIST per facilitare la creazione del proprio piano di risposta agli incidenti:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Come configurare le automazioni del flusso di lavoro all'interno del Centro sicurezza di Azure:How to configure Workflow Automations within Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Linee guida per creare un processo di risposta agli incidenti di sicurezza:Guidance on building your own security incident response process:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia di Microsoft Security Response Center di un incidente:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Creare una procedura di assegnazione dei punteggi degli eventi imprevisti e di assegnazione delle priorità

**Linee guida:** il Centro sicurezza assegna una gravità a ogni avviso per consentire di definire la priorità degli avvisi da analizzare per primi. La gravità si basa sulla sicurezza del Centro sicurezza nel trovare o nell'analisi utilizzata per emettere l'avviso, nonché sul livello di probabilità che vi sia stato un intento dannoso dietro l'attività che ha portato all'avviso.

Inoltre, contrassegnare chiaramente gli abbonamenti (per ex. produzione, non-prod) e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

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

**Linee guida**: Seguire le regole di coinvolgimento Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Ulteriori informazioni sulla strategia di Microsoft e sull'esecuzione di Red Teaming e sui test di penetrazione dei siti in tempo reale sull'infrastruttura cloud, i servizi e le applicazioni gestiti da Microsoft sono disponibili qui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere il benchmark di sicurezza di [AzureSee](https://docs.microsoft.com/azure/security/benchmarks/overview) the Azure Security Benchmark
- Altre informazioni sulle linee di base per la sicurezza di AzureLearn more about [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
