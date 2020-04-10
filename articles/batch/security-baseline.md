---
title: Azure Security Baseline for Batch
description: Azure Security Baseline for Batch
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fef1afb9748fd6f2f59b972c451d95ae1204ef70
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011634"
---
# <a name="azure-security-baseline-for-batch"></a>Azure Security Baseline for Batch

La linea di base della sicurezza di Azure per Batch contiene suggerimenti che consentono di migliorare il livello di sicurezza della distribuzione.

La linea di base per questo servizio è ricavata da [Azure Security Benchmark versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce consigli su come proteggere le soluzioni cloud in Azure con le indicazioni sulle procedure consigliate.

Per altre informazioni, vedere [Panoramica delle linee di base](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)della sicurezza di Azure.For more information, see Azure Security Baselines overview .

## <a name="network-security"></a>Sicurezza di rete

*Per ulteriori informazioni, vedere [Controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale1.1: Protect resources using Network Security Groups or Azure Firewall on your Virtual Network

**Linee guida:** distribuire pool batch di Azure all'interno di una rete virtuale. Per consentire ai nodi di calcolo del pool di comunicare in modo sicuro con altre macchine virtuali o con una rete locale, è possibile eseguire il provisioning del pool in una subnet di una rete virtuale di Azure.To allow pool compute nodes to communicate securely with other virtual machines, or with an on-premises network, you can provision the pool in a subnet of an Azure virtual network. Inoltre, la distribuzione del pool all'interno di una rete virtuale consente di controllare il gruppo di sicurezza di rete (NSG) utilizzato per proteggere le interfacce di rete (NIC) dei singoli nodi, nonché la subnet. Configurare il gruppo di sicurezza di rete per consentire il traffico proveniente solo da indirizzi IP attendibili/posizioni su Internet.

Come creare un pool batch di Azure all'interno di una rete virtuale:How to create an Azure Batch Pool within a Virtual Network:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NICS

**Indicazioni:** usare il Centro sicurezza di Azure e correggere i consigli di protezione della rete relativi alla rete virtuale o al gruppo di sicurezza di rete (NSG) associato al pool Batch.Guidance: Use Azure Security Center and remediate network protection recommendations related to the virtual network/network security group (NSG) associated with your Batch pool. Abilitare i log di flusso nel gruppo di sicurezza di rete usato per proteggere il pool Batch e inviare i log in un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare log di flusso del gruppo di sicurezza di rete a un'area di lavoro di Azure Log Analytics e usare Analisi del traffico di Azure per fornire informazioni dettagliate sul flusso di traffico nel cloud di Azure.You may also send NSG flow logs to a Azure Log Analytics workspace and use Azure Traffic Analytics to provide insights into traffic flow in your Azure cloud. Alcuni vantaggi di Analisi del traffico di Azure sono la possibilità di visualizzare l'attività di rete e identificare hot spot, identificare le minacce alla sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni errate della rete.

Abilitazione dei registri di flusso del gruppo di sicurezza di rete:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare e usare Analisi del traffico:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Informazioni sulla sicurezza di rete fornita dal Centro sicurezza di Azure:Understand Network Security provided by Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteggere le applicazioni Web critiche

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida:** Abilitare la protezione standard di Azure DDoS (distributed Denial-of-service) nella rete virtuale che protegge il pool Batch di Azure per la protezione dagli attacchi DDoS.Guidance: Enable Azure DDoS (distributed Denial-of-service) Standard protection on the virtual network protecting your Azure Batch pool for protection against DDoS attacks. Usare Azure Security Center Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP Internet dannosi o inutilizzati noti.

Come configurare la protezione DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Informazioni su Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Registrare pacchetti di rete e registri di flusso

**Linee guida:** abilitare i log di flusso nel gruppo di sicurezza di rete (NSG) utilizzato per proteggere il pool batch di Azure e inviare i log in un account di archiviazione di Azure per il controllo del traffico.

Abilitazione dei registri di flusso del gruppo di sicurezza di rete:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Implementazione di sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete

**Linee guida:** se necessario ai fini della conformità, selezionare un'appliance virtuale di rete da Azure Marketplace che supporta le funzionalità di rilevamento delle intrusioni (IDS) e dei sistemi di prevenzione delle intrusioni (IPS) con funzionalità di ispezione del payload.

Se il rilevamento e/o la prevenzione delle intrusioni basati sull'ispezione del payload non è un requisito, è possibile usare Firewall di Azure con informazioni sulle minacce. Il filtro basato su threat intelligence di Azure Firewall può avvisare e negare il traffico da e verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

Distribuire Firewall di Azure con un indirizzo IP pubblico nella stessa rete virtuale dei nodi del pool batch di Azure.Deploy Azure Firewall with a public IP address in the same virtual network as your Azure Batch Pool nodes. Configurare le regole NAT (Network Address Translation) tra i percorsi attendibili su Internet e gli indirizzi IP privati dei singoli nodi del pool. Nel firewall di Azure, in Threat Intelligence, configurare "Avvisa e nega" per bloccare l'avviso e bloccare il traffico da/verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini provengono dal feed Microsoft Threat Intelligence e vengono inclusi solo i record di attendibilità più elevati. 

Come creare un pool batch di Azure all'interno di una rete virtuale:How to create an Azure Batch Pool within a Virtual Network:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Come distribuire Firewall di Azure:How to deploy Azure Firewall:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: Gestire il traffico verso le applicazioni Web

**Linee guida:** non applicabile, Benchmark è destinato alle applicazioni Web in esecuzione nel servizio app di Azure o nelle istanze di IaaS.Guidance: Not applicable, Benchmark is intended for web applications running on Azure App Service or IaaS instances.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e l'overhead amministrativo delle regole di sicurezza di rete

**Linee guida:** usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nei firewall di Azure associati ai pool batch di Azure.Guidance: Use virtual network service tags to define network access controls on network security groups or Azure Firewalls associated with your Azure Batch pool(s). È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag del servizio (ad esempio, ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi degli indirizzi inclusi nel tag del servizio e aggiorna automaticamente il tag del servizio quando gli indirizzi cambiano.

Comprendere e utilizzare i tag di servizio:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Mantenere le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida:** definire e implementare configurazioni di sicurezza standard per le risorse di rete associate ai pool batch di Azure con Criteri di Azure.Guidance: Define and implement standard security configurations for network resources associated with your Azure Batch pool(s) with Azure Policy. Usare gli alias dei criteri di Azure negli spazi dei nomi "Microsoft.Batch" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete dei pool di Azure Batch.Use Azure Policy aliases in the "Microsoft.Batch" and "Microsoft.Network" namespaces to create custom policies to audit or enforce the network configuration of your Azure Batch pools.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regole di configurazione del traffico dei documenti

**Linee guida:** usare i tag per i gruppi di servizi di rete e altre risorse correlate alla sicurezza di rete e al flusso di traffico associate ai pool batch di Azure.Guidance : Use tags for network service groups (NSGs) and other resources related to network security and traffic flow that are associated with your Azure batch pools. Per le singole regole del gruppo di sicurezza di rete, utilizzare il campo "Descrizione" per specificare le esigenze e/o la durata dell'azienda (ecc.) per tutte le regole che consentono il traffico da/verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e relativo valore" per assicurarsi che tutte le risorse vengano create con i tag e per notificare le risorse senza tag esistenti.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai relativi tag.

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Come creare una rete virtuale:How to create a virtual network:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Come creare un gruppo di sicurezza di gruppo:How to create an NSG:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilizzare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate ai pool batch di Azure.Guidance : Use Azure Activity Log to monitor network resource configurations and detect changes for network resources related to your Azure Batch pools. Creare avvisi all'interno di Monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse di rete critiche.

Come visualizzare e recuperare gli eventi del log attività di Azure:How to view and retrieve Azure Activity Log events:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

Come creare avvisi in Monitoraggio di Azure:How to create alerts in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizzare le origini di sincronizzazione dell'ora approvate

**Linee guida**: per Azure Batch, per impostazione predefinita, Microsoft fornisce la sincronizzazione dell'ora. Tuttavia, se si dispone di requisiti di sincronizzazione dell'ora specifici, è possibile implementare tali modifiche.

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurare la gestione dei registri di protezione centrale

**Linee guida:** eseguire l'onboarding dell'account Batch di Azure in Monitoraggio di Azure per aggregare i dati di sicurezza generati dai dispositivi del cluster. Sfrutta le query personalizzate per rilevare e rispondere alle minacce nell'ambiente.  Per il monitoraggio a livello di risorsa di Azure Batch, usare le API Batch per monitorare o eseguire query sullo stato delle risorse, inclusi processi, attività, nodi e pool.

Come eseguire l'onboarding di un account Batch di Azure in Monitoraggio di Azure:How to onboard an Azure Batch account to Azure Monitor:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Abilitare la registrazione di controllo per le risorse di Azure2.3: Abilitare la registrazione di controllo per le risorse di Azure2.

**Indicazioni:** per il monitoraggio a livello di account Batch di Azure, monitorare ogni account Batch usando le funzionalità di Monitoraggio di Azure.Guidance : For Azure Batch account level monitoring, monitor each Batch account using features of Azure Monitor. Monitoraggio di Azure raccoglie metriche e facoltativamente i log di diagnostica per le risorse con ambito a livello di un account Batch, ad esempio, pool, processi e attività. Raccogliere e usare questi dati manualmente o a livello di codice per monitorare le attività nell'account Batch e per diagnosticare eventuali problemi.

Per il monitoraggio a livello di risorsa di Azure Batch, usare le API di Azure Batch per monitorare o eseguire query sullo stato delle risorse, inclusi processi, attività, nodi e pool.

Come configurare il monitoraggio e la registrazione a livello di account di Azure Batch:How to configure Azure Batch account-level monitoring and logging:

https://docs.microsoft.com/azure/batch/monitoring-overview

Comprendere il monitoraggio a livello di risorsa Batch:Understand Batch resource-level monitoring:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: Raccolta dei registri di protezione dal sistema operativo

**Indicazioni:** Monitoraggio di Azure raccoglie metriche e log di diagnostica per le risorse nell'account Batch di Azure.Guidance: Azure Monitor collects metrics and diagnostic logs for resources in your Azure Batch account. Raccogliere e usare questi dati in diversi modi per monitorare l'account Batch di Azure e diagnosticare i problemi. È anche possibile configurare avvisi sulle metriche per ricevere notifiche quando una metrica raggiunge un valore specificato.

Se necessario, è possibile connettersi ai singoli nodi del pool tramite Shell protetta (SSH) o Remote Desktop Protocol (RDP) per accedere ai registri del sistema operativo locale.

Come raccogliere i log di diagnostica dall'account Batch di Azure:How to collect diagnostic logs from your Azure Batch account:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics

Come connettersi in remoto ai nodi del pool Batch di Azure:How to remotely connect to your Azure Batch pool nodes:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dello spazio di archiviazione del registro di protezione

**Linee guida:** Eseguire l'onboarding dell'account batch di Azure in Monitoraggio di Azure.Guidance: Onboard Azure Batch Account to Azure Monitor. Verificare che nell'area di lavoro di Azure Log Analytics usata sia impostato il periodo di conservazione dei log in base alle normative di conformità dell'organizzazioneEnsure that the Azure Log Analytics workspace used has the log retention period set according to your organization's compliance regulations

Come configurare il monitoraggio e la registrazione di Azure Batch:How to configure Azure Batch monitoring and logging:

https://docs.microsoft.com/azure/batch/monitoring-overview

Come configurare il periodo di conservazione dell'area di conservazione dell'area di lavoro di Azure Log Analytics:How to configure Azure Log Analytics workspace retention period:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare e rivedere i registri

**Linee guida:** creare avvisi di metrica Batch di Azure che si attivano quando il valore di una metrica specificata supera una determinata soglia.

Come configurare gli avvisi di metrica batch di Azure:How to configure Azure Batch metric alerts:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Abilitare gli avvisi per attività anomale

**Linee guida:** creare avvisi di metrica Batch di Azure che si attivano quando il valore di una metrica specificata supera una determinata soglia.

Come configurare gli avvisi di metrica batch di Azure:How to configure Azure Batch metric alerts:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizzare la registrazione antimalware

**Linee guida**: Utilizzare Windows Defender sui singoli nodi batch nel caso di sistemi operativi Windows o fornire la propria soluzione antimalware se si utilizza Linux.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Abilitare la registrazione delle query DNS

**Linee guida**: Implementare una soluzione di terze parti per la registrazione dns

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Abilitare la registrazione di controllo dalla riga di comando

**Guida:** configurare manualmente la registrazione della console e la trascrizione di PowerShell in base al nodo.

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per ulteriori informazioni, vedere [Controllo di sicurezza: controllo delle identità e degli accessi](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Gestire l'inventario dei conti amministrativi

**Linee guida:** Gestire il record dell'account amministrativo locale creato durante il provisioning del pool batch di Azure e di tutti gli altri account creati. Inoltre, se viene utilizzata l'integrazione di Azure Active Directory (AAD), AAD dispone di ruoli predefiniti che devono essere assegnati in modo esplicito e pertanto possono essere query. Utilizzare il modulo PowerShell di AAD per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

Inoltre, è possibile usare i consigli per la gestione delle identità e degli accessi del Centro sicurezza di Azure.In addition, you may use Azure Security Center Identity and Access Management recommendations.

Come ottenere un ruolo di directory in AAD con PowerShell:How to get a directory role in AAD with PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Come ottenere i membri di un ruolo di directory in AAD con PowerShell:How to get members of a directory role in AAD with PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Come monitorare l'identità e l'accesso con il Centro sicurezza di Azure:How to monitor identity and access with Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Modificare le password predefinite dove applicabile

**Indicazioni:** quando si esegue il provisioning di un pool di Azure Batch, è possibile creare account del computer locale. Non sono disponibili password predefinite da modificare, tuttavia è possibile specificare password diverse per l'accesso A Shell protetta (SSH) e Remote Desktop Protocol (RDP). Dopo aver configurato il pool batch di Azure, è possibile generare un utente casuale per i singoli nodi all'interno del portale di Azure o tramite l'API di Azure Resource Manager.After Azure Batch Pool has been configured, you can generate a random user for individual nodes within the Azure portal, or via Azure Resource Manager API.

Come aggiungere un utente a un nodo di calcolo specifico:How to add a user to specific compute node:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: Verificare l'utilizzo di account amministrativi dedicati

**Linee guida:** Integrare l'autenticazione per le applicazioni batch di Azure con Azure Active Directory.Guidance : Integrate Authentication for Azure Batch Applications with Azure Active Directory. Creare criteri e procedure sull'utilizzo di account amministrativi dedicati.

Inoltre, è possibile usare i consigli per la gestione delle identità e degli accessi del Centro sicurezza di Azure.In addition, you may use Azure Security Center Identity and Access Management recommendations.

Come autenticare le applicazioni Batch con Azure Active Directory:How to authenticate Batch applications with Azure Active Directory:

https://docs.microsoft.com/azure/batch/batch-aad-auth

Come monitorare l'identità e l'accesso con il Centro sicurezza di Azure:How to monitor identity and access with Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilizzare Single Sign-On (SSO) con Azure Active Directory

**Linee guida:** non applicabile, mentre Azure Batch supporta l'autenticazione di Azure AD, Single Sign-On non è supportato.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory.

**Linee guida:** Integrare l'autenticazione per le applicazioni batch di Azure con Azure Active Directory (AAD). Abilitare aAD multi-fattore autenticazione (MFA) e seguire i consigli di gestione di identità e accessi del Centro sicurezza di Azure.Enable AAD multi-factor authentication (MFA) and follow Azure Security Center Identity and Access Management recommendations.

 

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Come monitorare l'identità e l'accesso all'interno del Centro sicurezza di Azure:How to monitor identity and access within Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizzare macchine dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida:** usare PAW (workstation con accesso con privilegi) con l'autenticazione a più fattori (MFA) configurata per accedere alle risorse di Azure Batch e configurarle.

Ulteriori informazioni sulle workstation con accesso privilegiato:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registro e avviso per attività sospette da account amministrativi

**Linee guida:** se si dispone dell'autenticazione integrata per le applicazioni batch di Azure con Azure Active Directory (AAD), usare i report di sicurezza di Azure Active Directory per la generazione di log e avvisi quando si verifica un'attività sospetta o non sicura nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso alle attività.

Come identificare gli utenti di Azure AD contrassegnati per l'attività rischiosa:How to identify Azure AD users flagged for risky activity:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Come monitorare l'identità degli utenti e l'attività di accesso nel Centro sicurezza di Azure:How to monitor users identity and access activity in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo da posizioni approvate

**Linee guida:** se si dispone dell'autenticazione integrata per le applicazioni batch di Azure con Azure Active Directory, è possibile usare Percorsi denominati con accesso condizionale per consentire l'accesso solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche.

Come configurare i percorsi denominati in Azure:How to configure Named Locations in Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida:** usare Azure Active Directory (AAD) come sistema di autenticazione e autorizzazione centrale e integrare l'autenticazione per le applicazioni batch di Azure con AAD. AAD protegge i dati utilizzando la crittografia avanzata per i dati inattivi e in transito. AAD inoltre consente di, gli ishe e l'archiviazione sicura delle credenziali utente.

Come creare e configurare un'istanza di AAD:How to create and configure an AAD instance:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Come autenticare le applicazioni Batch con AAD:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rivedere e riconciliare regolarmente l'accesso degli utenti

**Indicazioni:** Azure Active Directory (AAD) fornisce log che consentono di individuare gli account non aggiornati. Inoltre, è possibile usare le revisioni di Azure Identity Access per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso degli utenti può essere esaminato regolarmente per assicurarsi che solo gli utenti giusti abbiano l'accesso continuo.

Come usare le recensioni di azure Identity Access:How to use Azure Identity Access Reviews:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorare i tentativi di accesso agli account disattivati

**Indicazioni:** creare impostazioni di diagnostica per gli account utente di Azure Active Directory, inviando i log di controllo e i log di accesso a un'area di lavoro di Azure Log Analytics.Guidance : Create Diagnostic Settings for Azure Active Directory user accounts, sending the audit logs and sign-in logs to a Azure Log Analytics workspace. Configurare gli avvisi desiderati nell'area di lavoro di Azure Log Analytics.Configure desired Alerts within Azure Log Analytics workspace.

Come integrare i log attività di Azure in Monitoraggio di Azure:How to integrate Azure Activity Logs into Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Deviazione del comportamento di accesso dell'account

**Linee guida:** usare la funzionalità Rilevamenti dei rischi di Azure Active Directory (AAD) e Protezione identità per configurare le risposte automatiche alle azioni sospette rilevate relative alle identità utente. Inoltre, è possibile inserire dati in Azure Sentinel per ulteriori indagini.

Come visualizzare gli invii rischiosi di AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Come configurare e abilitare i criteri di rischio di Identity Protection:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Come eseguire l'onboarding di Azure Sentinel:How to onboard Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3.13: <div>Fornire a Microsoft l'accesso ai dati rilevanti dei clienti durante gli scenari di supporto<br></div>

**Guida**: Non disponibile; Customer Lockbox non ancora supportato per Azure Batch.
 
Elenco dei servizi supportati da Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

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

**Linee guida:** Implementare sottoscrizioni e/o gruppi di gestione separati per lo sviluppo, il test e la produzione. I pool batch di Azure devono essere separati da rete virtuale/subnet, contrassegnati in modo appropriato e protetti con un gruppo di sicurezza di rete (NSG). I dati di Azure Batch devono essere contenuti in un account di archiviazione di Azure protetto.

Come creare un pool batch di Azure all'interno di una rete virtuale:How to create an Azure Batch Pool within a Virtual Network:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Come proteggere gli account di archiviazione di Azure:How to secure Azure Storage Accounts:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorare e bloccare il trasferimento non autorizzato di informazioni sensibili.

**Linee guida:** per gli account di archiviazione di Azure associati ai pool batch di Azure che contengono informazioni riservate, contrassegnarli come sensibili usando i tag e proteggerli con le procedure consigliate di Azure.Guidance : For Azure Storage Accounts associated with your Azure Batch Pool(s) that contain sensitive information, mark them as sensitive using Tags and secure them with Azure best practices.

Le funzionalità di identificazione dei dati, classificazione e prevenzione delle perdite non sono ancora disponibili per archiviazione di Azure o risorse di calcolo. Implementare una soluzione di terze parti, se necessario, ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Come proteggere gli account di archiviazione di Azure:How to secure Azure Storage Accounts:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Crittografare tutte le informazioni sensibili in transito

**Guida**: Crittografa tutte le informazioni sensibili in transito. Le risorse di Microsoft Azure negozieranno TLS 1.2 per impostazione predefinita. Verificare che i client che si connettono ai pool batch o agli archivi dati di Azure (account di archiviazione di Azure) siano in grado di negoziare TLS 1.2 o versione successiva.

Verificare che HTTPS sia necessario per accedere all'account di archiviazione contenente i dati di Azure Batch.Ensure HTTPS is required for accessing the Storage Account containing your Azure Batch data.

Informazioni sulla crittografia dell'account di archiviazione di Azure in transito:Understand Azure Storage Account Encryption in Transit:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilizzare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida:** per gli account di archiviazione di Azure associati ai pool batch di Azure che contengono informazioni riservate, contrassegnarli come sensibili usando i tag e proteggerli con le procedure consigliate di Azure.Guidance : For Azure Storage Accounts associated with your Azure Batch Pool(s) that contain sensitive information, mark them as sensitive using tags and secure them with Azure best practices.

Le funzionalità di identificazione dei dati, classificazione e prevenzione delle perdite non sono ancora disponibili per archiviazione di Azure o risorse di calcolo. Implementare una soluzione di terze parti, se necessario, ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Come proteggere gli account di archiviazione di Azure:How to secure Azure Storage Accounts:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse4.6: Usare il controllo degli accessi in base al ruolo di

**Linee guida:** usare il controllo degli accessi in base al ruolo di Azure Active Directory (AAD) per controllare l'accesso al piano di gestione delle risorse di Azure, tra cui Account batch, Pool batch e Account di archiviazione.

Informazioni sul controllo degli accessi in base al ruolo di Azure:Understand

https://docs.microsoft.com/azure/role-based-access-control/overview

Come configurare il controllo degli accessi in base al ruolo in Azure:How to configure RBAC in Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizzare Prevenzione perdita di dati basata su host per applicare il controllo degli accessi

**Linee guida:** le funzionalità di identificazione dei dati, classificazione e prevenzione delle perdite non sono ancora disponibili per Archiviazione di Azure o risorse di calcolo. Implementare una soluzione di terze parti, se necessario, ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.

Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Condiviso

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografa informazioni sensibili inattivi

**Linee guida:** per gli account di archiviazione associati all'account Batch di Azure, è consigliabile consentire a Microsoft di gestire le chiavi di crittografia, tuttavia, è possibile gestire le proprie chiavi, se necessario.

Come gestire le chiavi di crittografia per gli account di archiviazione di Azure:How to manage encryption keys for Azure Storage Accounts:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e avvisare le modifiche alle risorse critiche di Azure4.9: Log and alert on changes to critical Azure resources

**Indicazioni:** usare Monitoraggio di Azure con il log attività di Azure per creare avvisi per quando vengono apportate modifiche alle risorse di Azure critiche correlate o associate agli account o ai pool di Batch di Azure.Guidance: Use Azure Monitor with the Azure Activity Log to create alerts for when changes place place to critical Azure resources related to or associated with your Azure Batch accounts/pools.

Configurare le impostazioni di diagnostica per gli account di archiviazione associati al pool batch di Azure per monitorare e registrare tutte le operazioni CRUD nei dati del pool.

Come creare avvisi per gli eventi del log attività di Azure:How to create alerts for Azure Activity Log events:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Come abilitare la registrazione/controllo aggiuntivo per un account di archiviazione di Azure:How to enable additional logging/auditing for an Azure Storage Account:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per ulteriori informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Eseguire strumenti di scansione delle vulnerabilità automatizzati

**Linee guida**: Per i nodi del pool batch di Azure, l'utente è responsabile della gestione della soluzione di gestione delle vulnerabilità.

Facoltativamente, se si dispone di una piattaforma di gestione delle vulnerabilità Rapid7, Qualys o qualsiasi altra sottoscrizione della piattaforma di gestione delle vulnerabilità, è possibile installare manualmente gli agenti di valutazione delle vulnerabilità nei nodi del pool Batch e gestire i nodi tramite il rispettivo portale.

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuire una soluzione di gestione automatica delle patch del sistema operativo

**Linee guida:** Microsoft per gestire e aggiornare le immagini del nodo del pool batch di Azure di base. Verificare che il sistema operativo dei nodi del pool batch di Azure rimanga aggiornato per tutta la durata della durata del cluster, che potrebbe richiedere l'abilitazione degli aggiornamenti automatici, il monitoraggio dei nodi o l'esecuzione di riavvii periodici.


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuire una soluzione di gestione automatica delle patch software di terze parti

**Linee guida:** assicurarsi che le applicazioni di terze parti dei nodi del pool batch di Azure rimangano patch per tutta la durata della durata del cluster, il che potrebbe richiedere l'abilitazione degli aggiornamenti automatici, il monitoraggio dei nodi o l'esecuzione di riavvii periodici.


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Confronto delle scansioni delle vulnerabilità back-to-back

**Linee guida**: Se si dispone di un rapido7, Qualys o qualsiasi altro abbonamento alla piattaforma di gestione delle vulnerabilità, è possibile utilizzare il portale di tale fornitore per visualizzare e confrontare le scansioni di vulnerabilità back-to-back.

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilizzare un processo di valutazione del rischio per assegnare priorità alla correzione delle vulnerabilità individuate.

**Linee guida**: Utilizzare un programma di punteggio di rischio comune (ad esempio Common Vulnerability Scoring System) o le valutazioni di rischio predefinite fornite dallo strumento di scansione di terze parti.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per ulteriori informazioni, vedere [Controllo di sicurezza: inventario e gestione delle risorse](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Usare Azure Asset Discovery

**Linee guida:** usare Azure Resource Graph per eseguire query/individuare tutte le risorse (ad esempio calcolo, archiviazione, rete e così via) all'interno delle sottoscrizioni. Assicurarsi di disporre di autorizzazioni appropriate (di lettura) nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Anche se le risorse di Azure classiche possono essere individuate tramite Resource Graph, è consigliabile creare e usare le risorse di Azure Resource Manager (ARM) in futuro.

Come creare query con Azure Graph:How to create queries with Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Come visualizzare le sottoscrizioni di Azure:How to view your Azure Subscriptions:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Informazioni sul controllo degli accessi in base al ruolo di Azure:Understand

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Gestire i metadati delle risorse

**Linee guida:** applicare tag alle risorse di Azure che assiecano metadati per organizzarli logicamente in una tassonomia.

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminare risorse di Azure non autorizzate

**Linee guida**: utilizzare l'assegnazione di tag, gruppi di gestione e sottoscrizioni separate, se appropriato, per organizzare e tenere traccia delle risorse. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate dalla sottoscrizione in modo tempestivo.

Come creare sottoscrizioni di Azure aggiuntive:How to create additional Azure subscriptions:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare i gruppi di gestione:

https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e tag utente:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Gestire l'inventario delle risorse di Azure approvate e dei titoli software.

**Linee guida:** definire un elenco delle risorse di Azure approvate e del software approvato per le risorse di calcolo


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorare le risorse di Azure non approvate

**Linee guida:** usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:Guidance : Use Azure policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query/individuare risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare query con Azure Graph:How to create queries with Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorare le applicazioni software non approvate all'interno delle risorse di calcolo

**Linee guida:** per i nodi del pool batch di Azure, implementare una soluzione di terze parti per monitorare i nodi del cluster per le applicazioni software non approvate.


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Rimuovere le risorse e le applicazioni software di Azure non approvate

**Linee guida:** per i nodi del pool batch di Azure, implementare una soluzione di terze parti per monitorare i nodi del cluster per le applicazioni software non approvate.


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilizzare solo applicazioni approvate

**Linee guida:** per i nodi del pool batch di Azure, implementare una soluzione di terze parti per impedire l'esecuzione di software non autorizzato.


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usare solo i servizi di Azure approvati

**Linee guida:** usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:Guidance : Use Azure policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come negare un tipo di risorsa specifico con Criteri di Azure:How to deny a specific resource type with Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementare l'elenco delle applicazioni approvate

**Linee guida:** per i nodi del pool batch di Azure, implementare una soluzione di terze parti per impedire l'esecuzione di tipi di file non autorizzati.


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Limitare la possibilità degli utenti di interagire con Azure Resource Manager tramite scriptLimit Users' Ability to interact with Azure Resource Manager via Scripts</div>

**Linee guida:** usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app "Gestione di Microsoft Azure".

Come configurare l'accesso condizionale per bloccare l'accesso a Azure Resource Manager:How to configure Conditional Access to block access to Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitare la capacità degli utenti di eseguire script all'interno delle risorse di calcolo

**Guida**: Non applicabile,

Ciò non è applicabile ad Azure Batch, poiché gli utenti (non amministratori) dei pool di Azure Batch non devono accedere ai singoli nodi per eseguire i processi. L'amministratore del cluster dispone già dell'accesso root a tutti i nodi.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Applicazioni ad alto rischio fisicamente o logicamente

**Linee guida:** non applicabile, Benchmark è destinato alle applicazioni Web in esecuzione nel servizio app di Azure o nelle istanze di IaaS.Guidance: Not applicable, Benchmark is intended for web applications running on Azure App Service or IaaS instances.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per ulteriori informazioni, vedere [Controllo di sicurezza: configurazione protetta](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Stabilire configurazioni sicure per tutte le risorse di Azure7.1: Establish Secure Configurations for all Azure Resources

**Linee guida:** usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.Batch" per creare criteri personalizzati per controllare o applicare la configurazione degli account e dei pool di Azure Batch.Guidance : Use Azure Policy aliases in the "Microsoft.Batch" namespace to create custom policies to audit or enforce the configuration of your Azure Batch accounts and pools.

Come visualizzare gli alias dei criteri di Azure disponibili:How to view available Azure Policy Aliases:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Stabilire configurazioni sicure per il sistema operativo

**Linee guida**: Stabilire configurazioni sicure per il sistema operativo dei nodi del pool batch.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Gestire le configurazioni sicure per tutte le risorse di Azure7.3: Maintain Secure Configurations for all Azure Resources

**Linee guida:** usare i criteri di Azure [nega] e [distribuire se non esistono] per applicare impostazioni sicure per le risorse di Azure correlate all'account Batch e ai pool (ad esempio reti virtuali, subnet, firewall di Azure, account di archiviazione di Azure e così via). È possibile usare alias dei criteri di Azure dagli spazi dei nomi seguenti per creare criteri personalizzati:You may use Azure Policy Aliases from the following namespaces to create custom policies:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Informazioni sugli effetti dei criteri di Azure:Understand Azure Policy Effects:https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Gestire configurazioni sicure per i sistemi operativi

**Linee guida:** immagini del sistema operativo del pool batch di Azure gestite e gestite da Microsoft.Guidance: Azure Batch Pool Operating System Images managed and maintained by Microsoft. L'utente è responsabile dell'implementazione della configurazione dello stato a livello di sistema operativo.

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Condiviso

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configurazione sicura dell'archivio delle risorse di Azure7.5: Securely Store Configuration of Azure Resources

**Indicazioni:** se si usano definizioni di criteri di Azure personalizzate per gli account, i pool o le risorse correlate di Azure, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

Come archiviare il codice in DevOps di Azure:How to store code in Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos Documentation:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Memorizzazione sicura delle immagini personalizzate del sistema operativo

**Indicazioni:** se si usano immagini personalizzate per i pool di Azure Batch, usare il controllo degli accessi in base al ruolo per garantire che solo gli utenti autorizzati possano accedere alle immagini.

Informazioni sul controllo degli accessi in base al ruolo in Azure:Understand RBAC in Azure

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Come configurare il controllo degli accessi in base al ruolo in Azure:How to configure RBAC in Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida:** usare le definizioni dei criteri di Azure predefinite per avvisare, controllare e applicare le configurazioni delle risorse correlate ad Azure Batch.Guidance : Use built-in Azure Policy definitions to alert, audit, and enforce Azure Batch-related resource configurations.  Usare gli alias dei criteri di Azure nello spazio dei nomi "Microsoft.Batch" per creare criteri personalizzati per gli account e i pool batch di Azure.Use Azure Policy aliases in the "Microsoft.Batch" namespace to create custom policies for your Azure Batch accounts and pools. Inoltre, sviluppare un processo e una pipeline per la gestione delle eccezioni dei criteri.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Linee guida:** Implementare una soluzione di terze parti per mantenere lo stato desiderato per i sistemi operativi dei nodi del pool batch di Azure.Guidance : Implement a third-party solution to maintain desired state for your Azure Batch Pool nodes' operating systems.


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementare il monitoraggio della configurazione automatizzata per i servizi di Azure7.9: Implement Automated Configuration Monitoring for Azure Services

**Linee guida:** usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.Batch" per creare criteri personalizzati per controllare o applicare la configurazione dell'istanza di Azure Batch.Guidance : Use Azure Policy aliases in the "Microsoft.Batch" namespace to create custom policies to audit or enforce the configuration of your Azure Batch instance. È anche possibile usare i criteri predefiniti creati in modo specifico per Azure Batch o le risorse usate da Azure Batch, ad esempio:You may also use any built-in policies created specifically for Azure Batch or the resources used by Azure Batch, such as:

- Le subnet devono essere associate a un gruppo di sicurezza di rete

-Gli account di archiviazione devono usare un endpoint del servizio di rete virtuale-Storage Accounts should use a virtual network service endpoint

- È consigliabile abilitare i log di diagnostica negli account Batch

Come visualizzare gli alias dei criteri di Azure disponibili:How to view available Azure Policy Aliases:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementare il monitoraggio della configurazione automatizzata per i sistemi operativi

**Linee guida:** Implementare una soluzione di terze parti per monitorare lo stato dei sistemi operativi dei nodi del pool batch di Azure.Guidance : Implement a third-party solution to monitor the state of your Azure Batch Pool nodes' operating systems.


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="711-securely-manage-azure-secrets"></a>7.11: Gestire in modo sicuro i segreti di Azure7.11: Securely manage Azure secrets

**Indicazioni:** L'insieme di credenziali delle chiavi di Azure può essere usato con le distribuzioni di Azure Batch per gestire le chiavi per l'archiviazione del pool all'interno degli account di archiviazione di Azure.Guidance: Azure Key Vault can be used with Azure Batch deployments to manage keys for pool storage within Azure Storage Accounts.

Come integrare con le identità gestite di Azure:How to integrate with Azure Managed Identities:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Come creare un insieme di credenziali delle chiavi di Azure:How to create a Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Come fornire l'autenticazione key Vault con un'identità gestita:How to provide Key Vault authentication with a managed identity:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Gestire in modo sicuro e automatico le identità

**Linee guida:** non disponibile, identità del servizio gestito non supportata da Azure Batch


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminare l'esposizione di credenziali indesiderate

**Linee guida**: Implementare Credential Scanner per identificare le credenziali all'interno del codice. Credential Scanner will also encourage moving discovered credentials to more secure locations such as Azure Key Vault. 

Come configurare Credential Scanner:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="malware-defense"></a>Difesa da malware

*Per ulteriori informazioni, vedere [Controllo di sicurezza: Difesa da malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilizzare software antimalware gestito centralmente

**Linee guida**: Usare Windows Defender nei singoli nodi del pool Batch di Azure nel caso di sistemi operativi Windows o fornire una soluzione antimalware personalizzata se si usa Linux.Guidance : Use Windows Defender on your individual Azure Batch pool nodes in the case of Windows operating systems, or provide your own anti-malware solution if you are using Linux.


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: File pre-scansione da caricare in risorse di Azure non di calcolo

**Linee guida:** Microsoft Antimalware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Azure Batch), tuttavia non viene eseguito sul contenuto dei clienti.

Pre-analisi di tutti i file caricati in risorse di Azure non di calcolo, ad esempio il servizio app, l'archiviazione Data Lake, l'archiviazione BLOB e così via. Microsoft non può accedere ai dati dei clienti in questi casi.

Informazioni su Microsoft Antimalware per i servizi cloud di Azure e le macchine virtuali:Understand Microsoft Antimalware for Azure Cloud Services and Virtual Machines:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Verificare che il software e le firme antimalware siano aggiornati

**Linee guida**: Usare Windows Defender nei singoli nodi del pool Batch di Azure nel caso di sistemi operativi Windows e verificare che l'aggiornamento automatico sia abilitato. Fornire la propria soluzione anti-malware se si utilizza Linux.


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

## <a name="data-recovery"></a>Ripristino dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatici regolari

**Indicazioni:** quando si usa un account di archiviazione di Azure per l'archivio dati del pool batch di Azure, scegliere l'opzione di ridondanza appropriata (LRS, 'RS, GRS, RA-GRS). 

Come configurare la ridondanza di archiviazione per gli account di archiviazione di Azure:How to configure storage redundancy for Azure Storage Accounts:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup di sistema completi ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** quando si usa un account di archiviazione di Azure per l'archivio dati del pool batch di Azure, scegliere l'opzione di ridondanza appropriata (LRS, 'RS, GRS, RA-GRS).  Se si usa L'insieme di credenziali delle chiavi di Azure per qualsiasi parte della distribuzione di Azure Batch, verificare che venga eseguito il backup delle chiavi.

Come configurare la ridondanza di archiviazione per gli account di archiviazione di Azure:How to configure storage redundancy for Azure Storage Accounts:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Come eseguire il backup delle chiavi dell'insieme di credenziali in Azure:How to backup key vault keys in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida:** se si gestiscono chiavi personalizzate per gli account di archiviazione di Azure o qualsiasi altra risorsa correlata all'implementazione di Azure Batch, testare periodicamente il ripristino delle chiavi di cui è stato eseguito il backup.

Come eseguire il backup delle chiavi dell'insieme di credenziali in Azure:How to backup key vault keys in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

Come ripristinare una chiave gestita dal cliente con PowerShell:How to restore a Customer Managed Key with PowerShell:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dai clienti

**Linee guida:** se l'insieme di credenziali delle chiavi di Azure viene usato per contenere le chiavi correlate agli account di archiviazione del pool batch di Azure, abilitare l'eliminazione temporanea nell'insieme di credenziali delle chiavi di Azure per proteggere le chiavi da eliminazioni accidentali o dannose.

Come abilitare l'eliminazione soft nell'insieme di credenziali delle chiavi di Azure:How to enable Soft Delete in Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10.1: Creare una guida alla risposta agli incidenti

**Linee guida**: Assicurarsi che siano presenti piani di risposta agli incidenti scritti che definiscono i ruoli del personale e le fasi della gestione/gestione degli incidenti.

Come configurare le automazioni del flusso di lavoro all'interno del Centro sicurezza di Azure:How to configure Workflow Automations within Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Creare la procedura di creazione del punteggio degli eventi imprevisti e della definizione delle priorità

**Guida:** il Centro sicurezza assegna un livello di gravità agli avvisi, per consentire di assegnare una priorità all'ordine di assistenza a ogni avviso, in modo che quando una risorsa viene compromessa, è possibile raggiungerlo immediatamente. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sull'analita utilizzato per emettere l'avviso, nonché il livello di probabilità che vi sia stato un intento dannoso dietro l'attività che ha portato all'avviso.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Testare le procedure di risposta alla sicurezza

**Linee guida**: Eseguire esercizi per testare le capacità di risposta agli incidenti dei sistemi a una cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

Fare riferimento alla pubblicazione del NIST: Guida ai programmi di test, formazione ed eserciziper piani e funzionalità IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: Fornire i dettagli di &nbsp;contatto per gli eventi imprevisti di sicurezza e configurare le notifiche di avviso per gli eventi imprevisti di sicurezza

**Linee guida:** le informazioni di contatto relative agli eventi imprevisti di protezione verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che i dati sono stati utilizzati da una parte non legale o non autorizzata.

Come impostare il contatto di sicurezza del centro di sicurezza di Azure:How to set the Azure Security Center Security Contact:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporare avvisi di sicurezza nel sistema di risposta agli incidenti

**Indicazioni:** esportare gli avvisi e i suggerimenti del Centro sicurezza di Azure usando la funzionalità di esportazione continua. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.You may use the Azure Security Center data connector to stream the alerts to Azure Sentinel.

Come configurare l'esportazione continua:

https://docs.microsoft.com/azure/security-center/continuous-export

Come trasmettere gli avvisi in Azure Sentinel:How to stream alerts into Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizzare la risposta agli avvisi di sicurezza

**Indicazioni:** usare la funzionalità automazione flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite "App per la logica" in avvisi e suggerimenti per la sicurezza.

Come configurare l'automazione del flusso di lavoro e le app per la logica:How to configure Workflow Automation and Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per ulteriori informazioni, vedere Controllo di sicurezza: test di [penetrazione ed Esercizi del team rosso](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici sulla sicurezza entro 60 giorni.

**Linee guida**: Seguire le regole di coinvolgimento Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Ulteriori informazioni sulla strategia di Microsoft e sull'esecuzione di Red Teaming e sui test di penetrazione dei siti in tempo reale su infrastruttura cloud, servizi e applicazioni gestiti Microsoft sono disponibili qui: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere il benchmark di sicurezza di [AzureSee](https://docs.microsoft.com/azure/security/benchmarks/overview) the Azure Security Benchmark
- Altre informazioni sulle linee di base per la sicurezza di AzureLearn more about [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
