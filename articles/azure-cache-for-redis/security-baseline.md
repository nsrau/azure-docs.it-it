---
title: Azure Security Baseline for Azure Cache for Redis
description: Azure Security Baseline for Azure Cache for Redis
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 172ac4e13201457f62d722236dff130a312cfdeb
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81755508"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Azure Security Baseline for Azure Cache for Redis

La base di sicurezza di Azure per la cache di Azure per Redis contiene suggerimenti che consentono di migliorare il livello di sicurezza della distribuzione.

La linea di base per questo servizio è ricavata da [Azure Security Benchmark versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce consigli su come proteggere le soluzioni cloud in Azure con le indicazioni sulle procedure consigliate.

Per altre informazioni, vedere [Panoramica delle linee di base](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)della sicurezza di Azure.For more information, see Azure Security Baselines overview .

## <a name="network-security"></a>Sicurezza di rete

*Per ulteriori informazioni, vedere [Controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale1.1: Protect resources using Network Security Groups or Azure Firewall on your Virtual Network

**Guida:** distribuire l'istanza della cache di Azure per Redis all'interno di una rete virtuale (VNet). Una rete virtuale è una rete privata nel cloud. Quando un'istanza di Cache Redis di Azure viene configurata con una rete virtuale, non è indirizzabile pubblicamente ed è accessibile solo da macchine virtuali e applicazioni all'interno della rete virtuale.

È inoltre possibile specificare regole firewall con un intervallo di indirizzi IP iniziale e finale. Quando le regole del firewall sono configurate, solo le connessioni client degli intervalli di indirizzi IP specificati possono connettersi alla cache.

Come configurare il supporto della rete virtuale per una cache di Azure Premium per Redis:How to configure Virtual Network Support for a Premium Azure Cache for Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Come configurare la cache di Azure per le regole del firewall Redis:How to configure Azure Cache for Redis firewall rules:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Linee guida:** quando le macchine virtuali vengono distribuite nella stessa rete virtuale della cache di Azure per l'istanza Redis, è possibile usare i gruppi di sicurezza di rete (NSG) per ridurre il rischio di esfiltrazione dei dati. Abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare log di flusso del gruppo di sicurezza di rete a un'area di lavoro di Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso di traffico nel cloud di Azure.You may also send NSG flow logs to a Log Analytics workspace and use Traffic Analytics to provide insights into traffic flow in your Azure cloud. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare hot spot, identificare le minacce alla sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni errate della rete.

Abilitazione dei registri di flusso del gruppo di sicurezza di rete:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare e usare Analisi del traffico:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteggere le applicazioni Web critiche

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle applicazioni Web in esecuzione nel servizio app di Azure o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida:** la distribuzione della rete virtuale di Azure offre sicurezza e isolamento avanzati per la cache di Azure per Redis, nonché subnet, criteri di controllo degli accessi e altre funzionalità per limitare ulteriormente l'accesso. Quando viene distribuito in una rete virtuale, la cache di Azure per Redis non è indirizzabile pubblicamente ed è accessibile solo da macchine virtuali e applicazioni all'interno della rete virtuale.

Abilitare DDoS Protection Standard nelle reti virtuali associate alla cache di Azure per le istanze Redis per evitare attacchi DDoS (Distributed Denial of Service). Usare Azure Security Center Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP Internet dannosi o inutilizzati noti.

Come configurare il supporto della rete virtuale per una cache di Azure Premium per Redis:How to configure Virtual Network Support for a Premium Azure Cache for Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Gestire Azure DDoS Protection Standard usando il portale di Azure:Manage Azure DDoS Protection Standard using the Azure portal:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Registrare i pacchetti di rete e i registri di flusso

**Linee guida:** quando le macchine virtuali vengono distribuite nella stessa rete virtuale della cache di Azure per l'istanza Redis, è possibile usare i gruppi di sicurezza di rete (NSG) per ridurre il rischio di esfiltrazione dei dati. Abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare log di flusso del gruppo di sicurezza di rete a un'area di lavoro di Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso di traffico nel cloud di Azure.You may also send NSG flow logs to a Log Analytics workspace and use Traffic Analytics to provide insights into traffic flow in your Azure cloud. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare hot spot, identificare le minacce alla sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni errate della rete.

Abilitazione dei registri di flusso del gruppo di sicurezza di rete:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare e usare Analisi del traffico:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Linee guida:** quando si usa la cache di Azure per Redis con le applicazioni Web in esecuzione nel servizio app di Azure o nelle istanze di calcolo, distribuire tutte le risorse all'interno di una rete virtuale di Azure (VNet) e protette con un Firewall applicazione Web di Azure (WAF) nel gateway applicazione Web.Guidance: When using Azure Cache for Redis with your web applications running on Azure App Service or compute instances, deploy all resources within an Azure Virtual Network (VNet) and secure with an Azure Web Application Firewall (WAF) on Web Application Gateway. Configurare il WAF per l'esecuzione in "Modalità Prevenzione". La modalità di prevenzione blocca le intrusioni e gli attacchi rilevati dalle regole. L'autore dell'attacco riceve un'eccezione di "accesso non autorizzato 403" e la connessione viene chiusa. La modalità di prevenzione registra tali attacchi nei log di WAF.

In alternativa, è possibile selezionare un'offerta di Azure Marketplace che supporta la funzionalità IDS/IPS con funzionalità di ispezione del payload e/o rilevamento delle anomalie.

Comprendere le funzionalità WAF di Azure:Understand Azure WAF capabilities:

https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview

Come distribuire Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gestire il traffico verso le applicazioni web

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle applicazioni Web in esecuzione nel servizio app di Azure o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e l'overhead amministrativo delle regole di sicurezza di rete

**Linee guida:** usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete (NSG) o nel firewall di Azure.Guidance: Use virtual network service tags to define network access controls on network security groups (NSG) or Azure Firewall. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag del servizio (ad esempio, ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi degli indirizzi inclusi nel tag del servizio e aggiorna automaticamente il tag del servizio quando gli indirizzi cambiano.

È inoltre possibile utilizzare i gruppi di sicurezza delle applicazioni (ASG) per semplificare la configurazione della sicurezza complessa. Gli ASG consentono di configurare la sicurezza di rete come estensione naturale della struttura di un'applicazione, consentendo di raggruppare macchine virtuali e definire criteri di sicurezza di rete basati su tali gruppi.

Tag del servizio di rete virtuale:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Gruppi di sicurezza delle applicazioni:Application Security Groups:

https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida:** definire e implementare configurazioni di sicurezza standard per le risorse di rete correlate alla cache di Azure per le istanze Redis con Criteri di Azure.Guidance: Define and implement standard security configurations for network resources related to your Azure Cache for Redis instances with Azure Policy. Usare gli alias dei criteri di Azure negli spazi dei nomi "Microsoft.Cache" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete della cache di Azure per le istanze Redis.Use Azure Policy aliases in the "Microsoft.Cache" and "Microsoft.Network" namespaces to create custom policies to audit or enforce the network configuration of your Azure Cache for Redis instances. È inoltre possibile utilizzare definizioni dei criteri predefinite, ad esempio:

Devono essere abilitate solo connessioni sicure alla Cache Redis

Lo standard di protezione DDoS deve essere abilitato

È anche possibile usare Blueprint di Azure per semplificare le distribuzioni di Azure su larga scala creando pacchetti di elementi dell'ambiente chiave, ad esempio modelli di Azure Resource Manager (ARM), controllo degli accessi in base al ruolo e criteri, in una singola definizione del blueprint. Applica facilmente il blueprint a nuove sottoscrizioni e ambienti e ottimizza il controllo e la gestione tramite il controllo delle versioni.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare un blueprint di Azure:How to create an Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regole di configurazione del traffico dei documenti

**Linee guida:** usare i tag per le risorse di rete associate alla cache di Azure per la distribuzione Redis per organizzarli logicamente in una tassonomia.

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilizzare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alla cache di Azure per le istanze Redis.Guidance: Use the Azure Activity log to monitor network resource configurations and detect changes for network resources related to your Azure Cache for Redis instances. Creare avvisi all'interno di Monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse di rete critiche.

Come visualizzare e recuperare gli eventi del log attività di Azure:How to view and retrieve Azure Activity Log events:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Come creare avvisi in Monitoraggio di Azure:How to create alerts in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizzare origini di sincronizzazione dell'ora approvate

**Indicazioni:** Microsoft gestisce l'origine ora usata per le risorse di Azure, ad esempio Cache di Azure per Redis per i timestamp nei log.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurare la gestione dei registri di protezione centralizzati

**Indicazioni:** abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro di Log Analytics, a un hub eventi di Azure o a un account di archiviazione di Azure per l'archiviazione. I log attività forniscono informazioni dettagliate sulle operazioni eseguite nella cache di Azure per le istanze Redis a livello del piano di controllo. Usando i dati del log attività di Azure, è possibile determinare "cosa, chi e quando" per tutte le operazioni di scrittura (PUT, POST, DELETE) eseguite a livello del piano di controllo per la cache di Azure per le istanze Redis.

Come abilitare le impostazioni di diagnostica per il log attività di Azure:How to enable Diagnostic Settings for Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Abilitare la registrazione di controllo per le risorse di Azure2.3: Abilitare la registrazione di controllo per le risorse di Azure2.

**Indicazioni:** abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro di Log Analytics, a un hub eventi di Azure o a un account di archiviazione di Azure per l'archiviazione. I log attività forniscono informazioni dettagliate sulle operazioni eseguite nella cache di Azure per le istanze Redis a livello del piano di controllo. Usando i dati del log attività di Azure, è possibile determinare "cosa, chi e quando" per tutte le operazioni di scrittura (PUT, POST, DELETE) eseguite a livello del piano di controllo per la cache di Azure per le istanze Redis.

Mentre le metriche sono disponibili abilitando le impostazioni di diagnostica, la registrazione di controllo sul piano dati non è ancora disponibile per la cache di Azure per Redis.

Come abilitare le impostazioni di diagnostica per il log attività di Azure:How to enable Diagnostic Settings for Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Raccogliere i registri di sicurezza dai sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dello spazio di archiviazione del registro di protezione

**Linee guida:** in Monitoraggio di Azure impostare il periodo di conservazione dei log per le aree di lavoro di Log Analytics associate alla cache di Azure per le istanze Redis in base alle normative di conformità dell'organizzazione.

Si noti che la registrazione di controllo sul piano dati non è ancora disponibile per la cache di Azure per Redis.Note that audit logging at the data plane is not yet available for Azure Cache for Redis.

Come impostare i parametri di conservazione dei log:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i registri

**Linee guida:** abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro di Log Analytics.Guidance : Enable Azure Activity Log diagnostic settings and send the logs to a Log Analytics workspace. Eseguire query in Log Analytics per cercare termini, identificare tendenze, analizzare modelli e fornire molte altre informazioni dettagliate in base ai dati del log attività che potrebbero essere stati raccolti per la cache di Azure per Redis.

Si noti che la registrazione di controllo sul piano dati non è ancora disponibile per la cache di Azure per Redis.Note that audit logging at the data plane is not yet available for Azure Cache for Redis.

Come abilitare le impostazioni di diagnostica per il log attività di Azure:How to enable Diagnostic Settings for Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Come raccogliere e analizzare i log delle attività di Azure nell'area di lavoro log di Log Analytics in Monitoraggio di Azure:How to collect and analyze Azure activity logs in Log Analytics workspace in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Abilitare gli avvisi per attività anomale

**Indicazioni:** è possibile configurare la ricezione di avvisi in base alle metriche e ai log attività correlati alla cache di Azure per le istanze Redis.Guidance : You can configure to receive alerts based on metrics and activity logs related to your Azure Cache for Redis instances. Azure Monitor allows you to configure an alert to send an email notification, call a webhook, or invoke an Azure Logic App.

Mentre le metriche sono disponibili abilitando le impostazioni di diagnostica, la registrazione di controllo sul piano dati non è ancora disponibile per la cache di Azure per Redis.

Come configurare gli avvisi per la cache di Azure per Redis:How to configure alerts for Azure Cache for Redis:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#alerts

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizzare la registrazione antimalware

**Orientamento**: Non applicabile; Cache di Azure per Redis non elabora né produce log correlati antimalware.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: Abilitare la registrazione delle query DNS

**Orientamento**: Non applicabile; La cache di Azure per Redis non elabora né produce log correlati al DNS.

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

**Linee guida:** l'accesso aereo di controllo alla cache di Azure per Redis è controllato tramite Azure Active Directory (AD). Azure AD non ha il concetto di password predefinite. 

L'accesso piano dati alla cache di Azure per Redis viene controllato tramite chiavi di accesso. Queste chiavi vengono utilizzate dai client che si connettono alla cache e possono essere rigenerate in qualsiasi momento.

Non è consigliabile creare password predefinite nell'applicazione. È invece possibile archiviare le password in Archiviazione delle chiavi di Azure e quindi usare Azure Active Directory per recuperarle.

Come rigenerare la cache di Azure per le chiavi di accesso Redis:How to Regenerate Azure Cache for Redis access keys:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

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

**Indicazioni:** la cache di Azure per Redis usa le chiavi di accesso per autenticare gli utenti e non supporta l'accesso Single Sign-On (SSO) a livello di piano dati. L'accesso al piano di controllo per la cache di Azure per Redis è disponibile tramite l'API REST e supporta SSO. To authenticate, set the Authorization header for your requests to a JSON Web Token that you obtain from Azure Active Directory.

Informazioni sulla cache di Azure per l'API REST Redis:Understand Azure Cache for Redis REST API:https://docs.microsoft.com/rest/api/redis/

Comprendere SSO con Azure AD:Understand SSO with Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Indicazioni:** abilitare Azure Active Directory (AD) Multi-Factor Authentication (MFA) e seguire i consigli per la gestione delle identità e degli accessi del Centro sicurezza di Azure.Guidance: Enable Azure Active Directory (AD) Multi-Factor Authentication (MFA) and follow Azure Security Center Identity and Access Management recommendations.

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Come monitorare l'identità e l'accesso all'interno del Centro sicurezza di Azure:How to monitor identity and access within Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizzare macchine dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

**Guida:** usare le workstation con accesso con privilegi (PAW) con Multi-Factor Authentication (MFA) configurato per accedere alle risorse di Azure e configurarle.

Ulteriori informazioni sulle workstation con accesso privilegiato:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registrare e avvisare su attività sospette da account amministrativi

**Linee guida:** usare Azure Active Directory (AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verifica un'attività sospetta o non sicura nell'ambiente.

Usare inoltre i rilevamenti dei rischi di Azure AD per visualizzare avvisi e report sul comportamento rischioso degli utenti.

Come distribuire Privileged Identity Management (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Comprendere i rilevamenti dei rischi di Azure AD:Understand Azure AD risk detections:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo da posizioni approvate

**Guida:** configurare percorsi denominati in Azure Active Directory (AD) Accesso condizionale per consentire l'accesso solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche.

Come configurare i percorsi denominati in Azure:How to configure Named Locations in Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida:** usare Azure Active Directory (AD) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD inoltre consente di archiviare le credenziali utente in Azure AD.

L'autenticazione di Azure AD non può essere usata per l'accesso diretto alla cache di Azure per il piano dati di Redis, tuttavia, le credenziali di Azure AD possono essere usate per l'amministrazione a livello del piano di controllo (ad esempio il portale di Azure) per controllare la cache di Azure per le chiavi di accesso Redis.


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rivedere e riconciliare regolarmente l'accesso degli utenti

**Indicazioni:** Azure Active Directory (AD) fornisce log che consentono di individuare gli account non aggiornati. Inoltre, usare Le revisioni di Azure Identity Access per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso degli utenti può essere rivisto regolarmente per assicurarsi che solo gli utenti giusti abbiano accesso continuo. 

Informazioni sui report di Azure AD:Understand Azure AD reporting:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Come usare le recensioni di azure Identity Access:How to use Azure Identity Access Reviews:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorare i tentativi di accesso agli account disattivati

**Linee guida:** si ha accesso alle origini dei log degli eventi di accesso, controllo e rischio di Azure Active Directory (AD), che consentono di integrarsi con Azure Sentinel o un SIEM di terze parti.

È possibile semplificare questo processo creando impostazioni di diagnostica per gli account utente di Azure AD e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics.You can streamline this process by creating diagnostic settings for Azure AD user accounts and sending the audit logs and sign-in logs to a Log Analytics workspace. È possibile configurare gli avvisi di log desiderati all'interno di Log Analytics.You can configure desired log alerts within Log Analytics.

Come integrare i log attività di Azure in Monitoraggio di Azure:How to integrate Azure Activity Logs into Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Come abordo di Azure Sentinel:How to on-board Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Scostamenti comportamento di avviso per l'accesso all'account

**Guida:** per la deviazione del comportamento di accesso dell'account sul piano di controllo, usare le funzionalità di protezione delle identità di Azure Active Directory (AD) e di rilevamento dei rischi per configurare le risposte automatiche alle azioni sospette rilevate relative alle identità utente. È anche possibile inserire dati in Azure Sentinel per ulteriori indagini.

Come visualizzare gli accessi rischiosi di Azure AD:How to view Azure AD risky sign-ins:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Come configurare e abilitare i criteri di rischio di Identity Protection:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Come eseguire l'onboarding di Azure Sentinel:How to onboard Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Guida**: Non ancora disponibile; Customer Lockbox non è ancora supportato per la cache di Azure per Redis.

Elenco dei servizi supportati da Customer Lockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Non applicabile

## <a name="data-protection"></a>Protezione dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Mantenere un inventario di informazioni sensibili

**Linee guida:** usare i tag per tenere traccia delle risorse di Azure che archiviano o elaborano informazioni riservate.

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolare i sistemi che memorizzano o elaborano informazioni sensibili

**Linee guida:** Implementare sottoscrizioni e/o gruppi di gestione separati per lo sviluppo, il test e la produzione. La cache di Azure per le istanze Redis deve essere separata da rete virtuale/subnet e contrassegnata in modo appropriato. Facoltativamente, usare il firewall Cache di Azure per Redis per definire le regole in modo che solo le connessioni client provenienti da intervalli di indirizzi IP specificati possano connettersi alla cache.

Come creare sottoscrizioni di Azure aggiuntive:How to create additional Azure subscriptions:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare i gruppi di gestione:

https://docs.microsoft.com/azure/governance/management-groups/create

Come distribuire la cache di Azure per Redis in una rete virtuale:How to deploy Azure Cache for Redis into a Vnet:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Come configurare la cache di Azure per le regole del firewall Redis:How to configure Azure Cache for Redis firewall rules:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorare e bloccare il trasferimento non autorizzato di informazioni sensibili

**Guida**: Non ancora disponibile; Le funzionalità di identificazione dei dati, classificazione e prevenzione delle perdite non sono ancora disponibili per la cache di Azure per Redis.

Microsoft gestisce l'infrastruttura sottostante per la cache di Azure per Redis e ha implementato controlli rigorosi per prevenire la perdita o l'esposizione dei dati dei clienti.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Crittografare tutte le informazioni sensibili in transito

**Linee guida:** la cache di Azure per Redis richiede comunicazioni crittografate con TLS per impostazione predefinita. TLS versioni 1.0, 1.1 e 1.2 sono attualmente supportate. Tuttavia, TLS 1.0 e 1.1 si trovano su un percorso a livello di settore deprecazione, quindi usa TLS 1.2 se possibile. Se la libreria client o lo strumento non supporta TLS, è possibile abilitare le connessioni non crittografate tramite il portale di Azure o le API di gestione. In questi casi in cui le connessioni crittografate non sono possibili, è consigliabile inserire la cache e l'applicazione client in una rete virtuale.

Informazioni sulla crittografia in transito per la cache di Azure per Redis:Understand encryption in transit for Azure Cache for Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-best-practices

Comprendere le porte necessarie utilizzate negli scenari di cache Vnet:Understand required ports used in Vnet cache scenarios:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet#outbound-port-requirements

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilizzare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida:** le funzionalità di identificazione dei dati, classificazione e prevenzione delle perdite non sono ancora disponibili per la cache di Azure per Redis.Guidance: Data Identification, classification, and loss prevention features are not yet available for Azure Cache for Redis. Contrassegnare le istanze contenenti informazioni riservate in quanto tali e implementare soluzioni di terze parti, se necessario, ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse4.6: Usare il controllo degli accessi in base al ruolo di

**Linee guida:** usare il controllo degli accessi in base al ruolo di Azure Active Directory (AAD) per controllare l'accesso al piano di controllo della cache di Azure per Le rime (ad esempio il portale di Azure). 

Come configurare il controllo degli accessi in base al ruolo in Azure:How to configure RBAC in Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizzare la prevenzione della perdita di dati basata su host per applicare il controllo degli accessi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per la cache di Azure per Redis e ha implementato controlli rigorosi per prevenire la perdita o l'esposizione dei dati dei clienti.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografare le informazioni sensibili inattivi

**Linee guida:** la cache di Azure per Redis archivia i dati dei clienti in memoria e, sebbene fortemente protetta da molti controlli implementati da Microsoft, la memoria non viene crittografata per impostazione predefinita. Se richiesto dall'organizzazione, crittografare il contenuto prima di archiviarlo nella cache di Azure per Redis.If required by your organization, encrypt content before storing in Azure Cache for Redis.

Se si usa la cache di Azure per la funzionalità Redis "Persistenza dei dati Redis", i dati vengono inviati a un account di archiviazione di Azure di cui si è proprietari e gestiti. È possibile configurare la persistenza dal pannello "Nuova cache di Azure per redis" durante la creazione della cache e nel menu Risorsa per le cache premium esistenti.

I dati in Archiviazione di Azure vengono crittografati e decrittografati in modo trasparente usando la crittografia AES a 256 bit, una delle più forti crittografie a blocchi disponibili e compatibile con FIPS 140-2. La crittografia di Archiviazione di Azure non può essere disabilitata. È possibile fare affidamento sulle chiavi gestite da Microsoft per la crittografia dell'account di archiviazione oppure è possibile gestire la crittografia con chiavi personalizzate.

Come configurare la persistenza nella cache di Azure per Redis:How to configure persistence in Azure Cache for Redis:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Informazioni sulla crittografia per gli account di archiviazione di Azure:Understand encryption for Azure Storage accounts:https://docs.microsoft.com/azure/storage/common/storage-service-encryption

Comprendere la protezione dei dati dei clienti di Azure:Understand Azure customer data protection:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e avvisare le modifiche alle risorse critiche di Azure4.9: Log and alert on changes to critical Azure resources

**Indicazioni:** usare Monitoraggio di Azure con il log attività di Azure per creare avvisi per quando vengono apportate modifiche alle istanze di produzione della cache di Azure per Redis e altre risorse critiche o correlate.

Come creare avvisi per gli eventi del log attività di Azure:How to create alerts for Azure Activity Log events:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per ulteriori informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Eseguire strumenti automatici di scansione delle vulnerabilità

**Linee guida:** seguire le raccomandazioni del Centro sicurezza di Azure sulla protezione della cache di Azure per le istanze Redis e le risorse correlate.

Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano la cache di Azure per Redis.

Informazioni sui consigli del Centro sicurezza di Azure:Understand Azure Security Center recommendations:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuire una soluzione di gestione automatica delle patch del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuire una soluzione di gestione automatica delle patch software di terze parti

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Confrontare le scansioni delle vulnerabilità back-to-back

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilizzare un processo di valutazione del rischio per assegnare priorità alla correzione delle vulnerabilità individuate

**Linee guida:** Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano la cache di Azure per Redis.Guidance : Microsoft performs vulnerability management on the underlying systems that support Azure Cache for Redis.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

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

**Linee guida:** applicare tag alle risorse di Azure che assiecano metadati per organizzarli logicamente in una tassonomia.

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminare risorse di Azure non autorizzate

**Linee guida:** usare il tagging, i gruppi di gestione e sottoscrizioni separate, se appropriato, per organizzare e tenere traccia della cache di Azure per le istanze Redis e le risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate dalla sottoscrizione in modo tempestivo.

Inoltre, usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:In addition, use Azure policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

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

Tipi di risorse non consentiti

Tipi di risorse consentiti

Usare inoltre Azure Resource Graph per eseguire query/individuare risorse all'interno delle sottoscrizioni.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare query con Azure Graph:How to create queries with Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

**Linee guida:** usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:Guidance : Use Azure Policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

Tipi di risorse non consentiti

Tipi di risorse consentiti

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come negare un tipo di risorsa specifico con Criteri di Azure:How to deny a specific resource type with Azure Policy:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementare l'elenco delle applicazioni approvate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resources Manager tramite script

**Guida:** configurare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager (ARM) configurando "Blocca accesso" per l'app "Gestione di Microsoft Azure".

Come configurare l'accesso condizionale per bloccare l'accesso a ARM:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

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

**Linee guida:** definire e implementare configurazioni di sicurezza standard per la cache di Azure per le istanze Redis con Criteri di Azure.Guidance: Define and implement standard security configurations for your Azure Cache for Redis instances with Azure Policy. Usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.Cache" per creare criteri personalizzati per controllare o applicare la configurazione della cache di Azure per le istanze Redis.Use Azure Policy aliases in the "Microsoft.Cache" namespace to create custom policies to audit or enforce the configuration of Azure Cache for Redis instances. È anche possibile usare le definizioni dei criteri predefinite correlate alla cache di Azure per le istanze Redis, ad esempio:You may also make using of built-in policy definitions related to your Azure Cache for Redis instances, such as:

Devono essere abilitate solo connessioni sicure alla Cache Redis

Come visualizzare gli alias di Criteri di Azure disponibili:How to view available Azure Policy aliases:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

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

**Indicazioni:** se si usano definizioni di criteri di Azure personalizzate o modelli di Azure Resource Manager per la cache di Azure per le istanze Redis e le risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

Come archiviare il codice in DevOps di Azure:How to store code in Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos Documentation:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Memorizzare in modo sicuro immagini personalizzate del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida:** usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.Cache" per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Inoltre, sviluppare un processo e una pipeline per la gestione delle eccezioni dei criteri.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementare il monitoraggio automatico della configurazione per i servizi di Azure7.9: Implement automated configuration monitoring for Azure services

**Linee guida:** usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.Cache" per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [deploy if not exist] per applicare automaticamente le configurazioni per la cache di Azure per le istanze Redis e le risorse correlate.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementare il monitoraggio automatico della configurazione per i sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gestire i segreti di Azure in modo sicuro

**Linee guida:** per le macchine virtuali o le applicazioni Web di Azure in esecuzione nel servizio app di Azure usate per accedere alla cache di Azure per le istanze Redis, usare l'identità del servizio gestito insieme all'insieme a Archiviazione delle chiavi di Azure per semplificare e proteggere la cache di Azure per la gestione dei segreti Redis. Verificare che l'eliminazione temporanea dell'insieme di credenziali delle chiavi sia abilitata.

Come integrare con le identità gestite di Azure:How to integrate with Azure Managed Identities:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Come creare un insieme di credenziali delle chiavi:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Come fornire l'autenticazione key Vault con un'identità gestita:How to provide Key Vault authentication with a managed identity:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gestire le identità in modo sicuro e automatico

**Linee guida:** per le macchine virtuali o le applicazioni Web di Azure in esecuzione nel servizio app di Azure usate per accedere alla cache di Azure per le istanze Redis, usare l'identità del servizio gestito insieme all'insieme a Archiviazione delle chiavi di Azure per semplificare e proteggere la cache di Azure per la gestione dei segreti Redis. Verificare che l'eliminazione temporanea dell'insieme di credenziali delle chiavi sia abilitata.

Usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory.Use Managed Identities to provide Azure services with an automatically managed identity in Azure Active Directory. Managed Identities consente di eseguire l'autenticazione a qualsiasi servizio che supporta l'autenticazione AAD, incluso l'insieme di credenziali delle chiavi di Azure, senza credenziali nel codice.

Come configurare le identità gestite:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Come integrare con le identità gestite di Azure:How to integrate with Azure Managed Identities:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

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

**Linee guida:** l'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Cache di Azure per Redis), tuttavia non viene eseguito sul contenuto dei clienti.

Pre-analisi di qualsiasi contenuto caricato in risorse di Azure non di calcolo, ad esempio servizio app, archiviazione Data Lake, archiviazione BLOB, Database di Azure per PostgreSQL e così via. Microsoft non può accedere ai dati in questi casi.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Verificare che il software antimalware e le firme siano aggiornate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

L'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Cache di Azure per Redis), tuttavia non viene eseguito sul contenuto dei clienti.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="data-recovery"></a>Ripristino dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatici regolari

**Guida:** abilitare la persistenza Redis.Guidance : Enable Redis persistence. La persistenza di Redis consente di rendere persistenti i dati archiviati in Redis. È inoltre possibile creare snapshot ed eseguire il backup dei dati, per consentirne il caricamento in caso di errore hardware. Si tratta di un enorme vantaggio rispetto al livello Basic o Standard in cui tutti i dati vengono archiviati in memoria ed esiste il rischio di potenziali perdite di dati in caso di errore quando i nodi della cache sono inattivi.

È anche possibile usare la cache di Azure per l'esportazione Redis.You may also use Azure Cache for Redis Export. L'esportazione consente di esportare i dati archiviati in Cache Redis in file RDB compatibili con Redis. È possibile usare questa funzionalità per spostare i dati da un'istanza di Cache Redis di Azure a un'altra o su un altro server Redis. Durante il processo di esportazione, viene creato un file temporaneo nella macchina virtuale che ospita la cache di Azure per l'istanza del server Redis e il file viene caricato nell'account di archiviazione designato. Quando l'operazione di esportazione viene completata con esito positivo o negativo, il file temporaneo viene eliminato.

Come abilitare la persistenza Redis:How to enable Redis persistence:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Come usare la cache di Azure per l'esportazione Redis:How to use Azure Cache for Redis Export:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Guida:** abilitare la persistenza Redis.Guidance : Enable Redis persistence. La persistenza di Redis consente di rendere persistenti i dati archiviati in Redis. È inoltre possibile creare snapshot ed eseguire il backup dei dati, per consentirne il caricamento in caso di errore hardware. Si tratta di un enorme vantaggio rispetto al livello Basic o Standard in cui tutti i dati vengono archiviati in memoria ed esiste il rischio di potenziali perdite di dati in caso di errore quando i nodi della cache sono inattivi.

È anche possibile usare la cache di Azure per l'esportazione Redis.You may also use Azure Cache for Redis Export. L'esportazione consente di esportare i dati archiviati in Cache Redis in file RDB compatibili con Redis. È possibile usare questa funzionalità per spostare i dati da un'istanza di Cache Redis di Azure a un'altra o su un altro server Redis. Durante il processo di esportazione, viene creato un file temporaneo nella macchina virtuale che ospita la cache di Azure per l'istanza del server Redis e il file viene caricato nell'account di archiviazione designato. Quando l'operazione di esportazione viene completata con esito positivo o negativo, il file temporaneo viene eliminato.

Se si usa L'insieme di credenziali delle chiavi di Azure per archiviare le credenziali per la cache di Azure per le istanze Redis, assicurarsi che i backup automatici delle chiavi siano regolari.

Come abilitare la persistenza Redis:How to enable Redis persistence:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Come usare la cache di Azure per l'esportazione Redis:How to use Azure Cache for Redis Export:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Come eseguire il backup delle chiavi dell'insieme di credenziali:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida:** usare la cache di Azure per l'importazione delle ridine. L'importazione può essere utilizzata per portare i file RDB compatibili con Redis da qualsiasi server Redis in esecuzione in qualsiasi cloud o ambiente, inclusi Redis in esecuzione su Linux, Windows o qualsiasi provider cloud come Amazon Web Services e altri. L'importazione dei dati è un modo semplice per creare una cache con dati già popolati. Durante il processo di importazione Cache Redis di Azure carica i file RDB dall'archiviazione di Azure nella memoria e quindi inserisce le chiavi nella cache.

Testare periodicamente il ripristino dei dati dei segreti dell'insieme di credenziali delle chiavi di Azure.Periodically test data restoration of your Azure Key Vault secrets.

Come usare la cache di Azure per l'importazione Redis:How to use Azure Cache for Redis Import:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Come ripristinare i segreti dell'insieme di credenziali delle chiavi:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dai clienti

**Linee guida:** la cache di Azure per i backup Redis da Redis Export e la persistenza Redis vengono archiviate nell'account di archiviazione di Azure selezionato. I dati in Archiviazione di Azure vengono crittografati e decrittografati in modo trasparente usando la crittografia AES a 256 bit, una delle più forti crittografie a blocchi disponibili e compatibile con FIPS 140-2. La crittografia di Archiviazione di Azure non può essere disabilitata. È possibile fare affidamento sulle chiavi gestite da Microsoft per la crittografia dell'account di archiviazione oppure è possibile gestire la crittografia con chiavi personalizzate.

Informazioni sulla crittografia per gli account di archiviazione di Azure:Understand encryption for Azure Storage accounts:https://docs.microsoft.com/azure/storage/common/storage-service-encryption

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Microsoft

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Creare una guida alla risposta agli incidenti

**Linee guida**: Creare una guida alla risposta agli incidenti per l'organizzazione. Assicurarsi che siano presenti piani di risposta agli incidenti scritti che definiscono tutti i ruoli del personale, nonché fasi di gestione/gestione degli incidenti dal rilevamento alla revisione post-incidente.

Come configurare le automazioni del flusso di lavoro all'interno del Centro sicurezza di Azure:How to configure Workflow Automations within Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Linee guida per creare un processo di risposta agli incidenti di sicurezza:Guidance on building your own security incident response process:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia di Microsoft Security Response Center di un incidente:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Il cliente può anche sfruttare la Guida alla gestione degli incidenti di sicurezza del COMPUTER del NIST per facilitare la creazione del proprio piano di risposta agli incidenti:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Creare una procedura di assegnazione dei punteggi degli eventi imprevisti e di assegnazione delle priorità

**Linee guida:** il Centro sicurezza assegna una gravità a ogni avviso per consentire di definire la priorità degli avvisi da analizzare per primi. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sull'analita utilizzato per emettere l'avviso, nonché il livello di probabilità che vi sia stato un intento dannoso dietro l'attività che ha portato all'avviso.

Inoltre, contrassegnare chiaramente gli abbonamenti (per ex. produzione, non-prod) e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Testare le procedure di risposta alla sicurezza

**Linee guida**: Eseguire esercizi per testare le capacità di risposta agli incidenti dei sistemi a una cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

Fare riferimento alla pubblicazione del NIST: Guida ai programmi di test, formazione ed eserciziper piani e funzionalità IT:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornire i dettagli di contatto degli eventi imprevisti di sicurezza e configurare le notifiche di avviso per gli incidenti di sicurezza

**Linee guida:** le informazioni di contatto relative agli eventi imprevisti di protezione verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che i dati del cliente sono stati utilizzati da una parte non legale o non autorizzata.  Esaminare gli incidenti dopo il fatto per assicurarsi che i problemi vengano risolti.

Come impostare il contatto di sicurezza del centro di sicurezza di Azure:How to set the Azure Security Center Security Contact:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporare avvisi di sicurezza nel sistema di risposta agli incidenti

**Indicazioni:** esportare gli avvisi e i suggerimenti del Centro sicurezza di Azure usando la funzionalità di esportazione continua. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi Sentinel.You may use the Azure Security Center data connector to stream the alerts Sentinel.

Come configurare l'esportazione continua:

https://docs.microsoft.com/azure/security-center/continuous-export

Come trasmettere gli avvisi in Azure Sentinel:How to stream alerts into Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizzare la risposta agli avvisi di sicurezza

**Indicazioni:** usare la funzionalità automazione flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite "App per la logica" in avvisi e suggerimenti per la sicurezza.

Come configurare l'automazione del flusso di lavoro e le app per la logica:How to configure Workflow Automation and Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per ulteriori informazioni, vedere Controllo di sicurezza: test di [penetrazione ed Esercizi del team rosso](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza entro 60 giorni

**Linee guida**: Seguire le regole di coinvolgimento Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Ulteriori informazioni sulla strategia di Microsoft e sull'esecuzione di Red Teaming e sui test di penetrazione dei siti in tempo reale sull'infrastruttura cloud, i servizi e le applicazioni gestiti da Microsoft sono disponibili qui: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere il benchmark di sicurezza di [AzureSee](https://docs.microsoft.com/azure/security/benchmarks/overview) the Azure Security Benchmark
- Altre informazioni sulle linee di base per la sicurezza di AzureLearn more about [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
