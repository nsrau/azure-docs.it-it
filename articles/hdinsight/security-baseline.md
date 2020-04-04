---
title: Azure Security Baseline for HDInsight
description: Azure Security Baseline for HDInsight
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 61c2b914671020b822814fc283b5f2641c2e787b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657450"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Azure Security Baseline for HDInsight

La linea di base della sicurezza di Azure per HDInsight contiene suggerimenti che consentono di migliorare il livello di sicurezza della distribuzione.

La linea di base per questi servizi è ricavata da [Azure Security Benchmark versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce consigli su come proteggere le soluzioni cloud in Azure con le indicazioni sulle procedure consigliate.

Per altre informazioni, vedere [Panoramica delle linee di base](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)della sicurezza di Azure.For more information, see Azure Security Baselines overview .

## <a name="network-security"></a>Sicurezza di rete

*Per ulteriori informazioni, vedere [Controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale1.1: Protect resources using Network Security Groups or Azure Firewall on your Virtual Network

**Indicazioni:** La sicurezza perimetrale in Azure HDInsight viene ottenuta tramite reti virtuali. Un amministratore aziendale può creare un cluster all'interno di una rete virtuale e usare un gruppo di sicurezza di rete per limitare l'accesso alla rete virtuale. Solo gli indirizzi IP consentiti nelle regole del gruppo di sicurezza di rete in ingresso saranno in grado di comunicare con il cluster Azure HDInsight.Only the allowed IP addresses in the inbound Network Security Group rules will be able to communicate with the Azure HDInsight cluster. Questa configurazione offre sicurezza perimetrale. Tutti i cluster distribuiti in una rete virtuale dislasceranno anche un endpoint privato che si risolve in un indirizzo IP privato all'interno della rete virtuale per l'accesso HTTP privato ai gateway del cluster.


Come distribuire Azure HDInsight all'interno di una rete virtuale e Secure con un gruppo di sicurezza di rete:How to Deploy Azure HDInsight within a Virtual Network and Secure with a Network Security Group:

https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Indicazioni:** usare il Centro sicurezza di Azure e correggere i consigli di protezione della rete per la rete virtuale, la subnet e il gruppo di sicurezza di rete usati per proteggere il cluster HdInsight di Azure.Guidance: Use Azure Security Center and remediate network protection recommendations for the virtual network, subnet, and network security group being used to secure your Azure HDInsight cluster. Abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione di Azure al controllo del traffico. È anche possibile inviare log di flusso del gruppo di sicurezza di rete a un'area di lavoro di Azure Log Analytics e usare Analisi del traffico di Azure per fornire informazioni dettagliate sul flusso di traffico nel cloud di Azure.You may also send NSG flow logs to a Azure Log Analytics Workspace and use Azure Traffic Analytics to provide insights into traffic flow in your Azure cloud. Alcuni vantaggi di Analisi del traffico di Azure sono la possibilità di visualizzare l'attività di rete e identificare hot spot, identificare le minacce alla sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni errate della rete.


Abilitazione dei registri di flusso del gruppo di sicurezza di rete:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Come abilitare e usare Analisi del traffico di Azure:How to Enable and use Azure Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


Informazioni sulla sicurezza di rete fornita dal Centro sicurezza di Azure:Understand Network Security provided by Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteggere le applicazioni Web critiche

**Orientamento**: Non applicabile; Benchmark è destinato al servizio App di Azure o alle risorse di calcolo che ospitano le applicazioni Web.benchmark is intended for Azure Apps Service or compute resources hosting web applications.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida:** per le protezioni dagli attacchi DDoS, abilitare la protezione standard DDoS di Azure nella rete virtuale in cui viene distribuito Azure HDInsight.Guidance : For protections from DDoS attacks, enable Azure DDoS Standard protection on the virtual network where your Azure HDInsight is deployed. Usare l'intelligence delle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet dannosi o inutilizzati noti.


Come configurare la protezione DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Informazioni su Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Registrare i pacchetti di rete e i registri di flusso

**Guida:** abilitare i log di flusso del gruppo di sicurezza di rete per il gruppo di sicurezza di rete collegato alla subnet usata per proteggere il cluster Azure HDInsight.Guidance: Enable network security group (NSG) flow logs for the NSG attached to the subnet being used to protect your Azure HDInsight cluster. Registrare i log di flusso del gruppo di sicurezza di rete in un account di archiviazione di Azure per generare record di flusso. Se necessario per analizzare l'attività anomala, abilitare l'acquisizione di pacchetti di Watcher reti di Azure.If required for investigating anomalous activity, enable Azure Network Watcher packet capture.


Abilitazione dei registri di flusso del gruppo di sicurezza di rete:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Come abilitare Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Linee guida:** il requisito può essere soddisfatto ID controllo di sicurezza di Azure 1.1. Distribuire il cluster Azure HDInsight in una rete virtuale e proteggere con un gruppo di sicurezza di rete (NSG).

Esistono diverse dipendenze per Azure HDInsight che richiedono traffico in ingresso. Il traffico di gestione in ingresso non può essere inviato tramite un dispositivo firewall. Gli indirizzi di origine per il traffico di gestione richiesto sono noti e pubblicati. Creare regole del gruppo di sicurezza di rete con queste informazioni per consentire il traffico proveniente solo da percorsi attendibili, proteggendo il traffico in ingresso verso i cluster.

Come distribuire HDInsight all'interno di una rete virtuale e Secure con un gruppo di sicurezza di rete:How to Deploy HDInsight within a Virtual Network and Secure with a Network Security Group:https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Comprendere le dipendenze di HDInsight e l'utilizzo del firewall:Understand HDInsight dependencies and firewall usage:https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Indirizzi IP di gestione HDInsight:HDInsight management IP addresses:https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gestire il traffico verso le applicazioni web

**Orientamento**: Non applicabile; Benchmark è destinato al servizio App di Azure o alle risorse di calcolo che ospitano le applicazioni Web.benchmark is intended for Azure Apps Service or compute resources hosting web applications.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e l'overhead amministrativo delle regole di sicurezza di rete

**Linee guida:** usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete collegati alla subnet in cui è distribuito il cluster Azure HDInsight.Guidance: Use Virtual network service tags to define network access controls on network security groups (NSG) that are attached to the subnet your Azure HDInsight cluster is deployed in. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag del servizio (ad esempio, ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi degli indirizzi inclusi nel tag del servizio e aggiorna automaticamente il tag del servizio quando gli indirizzi cambiano.


Comprendere e usare i tag di servizio per Azure HDInsight:Understand and using Service Tags for Azure HDInsight:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida:** definire e implementare configurazioni di sicurezza standard per le risorse di rete correlate al cluster Azure HDInsight.Guidance: Define and implement standard security configurations for network resources related to your Azure HDInsight cluster. Usare gli alias dei criteri di Azure negli spazi dei nomi "Microsoft.HDInsight" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete del cluster HDInsight di Azure.Use Azure Policy aliases in the "Microsoft.HDInsight" and "Microsoft.Network" namespaces to create custom policies to audit or enforce the network configuration of your Azure HDInsight cluster.


È anche possibile usare Blueprint di Azure per semplificare le distribuzioni di Azure su larga scala creando una creazione di pacchetti di elementi dell'ambiente chiave, ad esempio modelli di Azure Resource Manager, controlli RBAC e criteri, in una singola definizione del blueprint. Applica facilmente il blueprint a nuove sottoscrizioni e ambienti e ottimizza il controllo e la gestione tramite il controllo delle versioni.


Come visualizzare gli alias dei criteri di Azure disponibili:How to view available Azure Policy Aliases:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Come creare un blueprint di Azure:How to create an Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regole di configurazione del traffico dei documenti

**Linee guida:** usare i tag per i gruppi di sicurezza di rete e altre risorse correlate alla sicurezza di rete e al flusso di traffico associate al cluster Azure HDInsight.Guidance: Use Tags for network security group (NSGs) and other resources related to network security and traffic flow that are associated with your Azure HDInsight cluster. Per le singole regole del gruppo di sicurezza di rete, utilizzare il campo "Descrizione" per specificare le esigenze e/o la durata dell'azienda (ecc.) per tutte le regole che consentono il traffico da/verso una rete.


Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e relativo valore" per assicurarsi che tutte le risorse vengano create con i tag e per notificare le risorse senza tag esistenti.


È possibile usare Azure PowerShell o l'interfaccia della riga di comando (CLI) di Azure per cercare o eseguire azioni sulle risorse in base ai relativi tag.


Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


Come creare una rete virtuale:How to create a virtual network:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal


Come creare un gruppo di sicurezza di base con una configurazione di sicurezza:How to create an NSG with a Security Config:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilizzare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle distribuzioni di Azure HDInsight.Guidance: Use Azure Activity Log to monitor network resource configurations and detect changes for network resources related to your Azure HDInsight deployments. Creare avvisi all'interno di Monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse di rete critiche.


Come visualizzare e recuperare gli eventi del log attività di Azure:How to view and retrieve Azure Activity Log events:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view


Come creare avvisi in Monitoraggio di Azure:How to create alerts in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizzare origini di sincronizzazione dell'ora approvate

**Indicazioni:** Microsoft gestisce le origini ora per i componenti del cluster di Azure HDInsight, è possibile aggiornare la sincronizzazione dell'ora per le distribuzioni di calcolo.


Come configurare la sincronizzazione dell'ora per le risorse di calcolo di Azure:How to configure time synchronization for Azure compute resources:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurare la gestione dei registri di protezione centralizzati

**Linee guida:** è possibile eseguire l'onboarding del cluster Azure HDInsight in Monitoraggio di Azure per aggregare i dati di sicurezza generati dal cluster. Sfrutta le query personalizzate per rilevare e rispondere alle minacce nell'ambiente. 


Come eseguire l'onboarding di un cluster di Azure HDInsight in Monitoraggio di Azure:How to onboard an Azure HDInsight cluster to Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


Come creare query personalizzate per un cluster Azure HDInsight:How to create custom queries for an Azure HDInsight cluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Abilitare la registrazione di controllo per le risorse di Azure2.3: Abilitare la registrazione di controllo per le risorse di Azure2.

**Linee guida:** abilitare Monitoraggio di Azure per il cluster HDInsight, indirizzarlo a un'area di lavoro di Log Analytics.Guidance: Enable Azure Monitor for the HDInsight cluster, direct it to a Log Analytics workspace. Verrà noto le informazioni rilevanti sul cluster e le metriche del sistema operativo per tutti i nodi del cluster HDInsight di Azure.This will log relevant cluster information and OS metrics for all Azure HDInsight cluster nodes.


Come abilitare la registrazione per HDInsight Cluster:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


Come eseguire una query sui log HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Raccogliere i registri di sicurezza dai sistemi operativi

**Linee guida:** eseguire l'onboarding del cluster HDInsight di Azure in Monitoraggio di Azure.Guidance: Onboard Azure HDInsight cluster to Azure Monitor. Verificare che nell'area di lavoro di Log Analytics utilizzata sia impostato il periodo di conservazione dei log in base alle normative di conformità dell'organizzazione.


Come eseguire l'onboarding di un cluster di Azure HDInsight in Monitoraggio di Azure:How to onboard an Azure HDInsight Cluster to Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


Come configurare il periodo di conservazione dell'area di lavoro di Log Analytics:How to configure Log Analytics Workspace Retention Period:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dello spazio di archiviazione del registro di protezione

**Linee guida:** eseguire l'onboarding del cluster HDInsight di Azure in Monitoraggio di Azure.Guidance: Onboard Azure HDInsight cluster to Azure Monitor. Verificare che nell'area di lavoro di Azure Log Analytics usata sia impostato il periodo di conservazione dei log in base alle normative di conformità dell'organizzazione.


Come eseguire l'onboarding di un cluster di Azure HDInsight in Monitoraggio di Azure:How to onboard an Azure HDInsight Cluster to Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


Come configurare il periodo di conservazione dell'area di lavoro di Log Analytics:How to configure Log Analytics Workspace Retention Period:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i registri

**Indicazioni:** usare le query dell'area di lavoro di Azure Log Analytics per eseguire query sui log di Azure HDInsight:Guidance: Use Azure Log Analytics workspace queries to query Azure HDInsight logs:


Come creare query personalizzate per i cluster di Azure HDInsight:How to Create Custom Queries for Azure HDInsight Clusters:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Abilitare gli avvisi per attività anomale

**Indicazioni:** usare l'area di lavoro di Azure Log Analytics per il monitoraggio e gli avvisi sulle attività anomale nei log di sicurezza e negli eventi correlati al cluster Azure HDInsight.Guidance: Use Azure Log Analytics workspace for monitoring and alerting on anomalous activities in security logs and events related to your Azure HDInsight cluster.


Come gestire gli avvisi nel Centro sicurezza di Azure:How to manage alerts in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts


How to alert on log analytics log data:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizzare la registrazione antimalware

**Guida:** Azure HDInsight viene fornito con Clamscan preinstallato e abilitato per le immagini del nodo del cluster, tuttavia è necessario gestire il software e aggregare/monitorare manualmente i log prodotti da Clamscan.Guidance: Azure HDInsight comes with Clamscan pre-installed and enabled for the cluster node images, however you must manage the software and manually aggregate/monitor any logs Clamscan produces.


Comprendere Clamscan:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Abilitare la registrazione delle query DNS

**Linee guida:** implementare una soluzione di terze parti per la registrazione dns.

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Abilitare la registrazione di controllo della riga di comando

**Guida**: configurare manualmente la registrazione della console in base al nodo.

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per ulteriori informazioni, vedere [Controllo di sicurezza: controllo delle identità e degli accessi](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Mantenere un inventario degli account amministrativi

**Linee guida:** Gestire il record dell'account amministrativo locale creato durante il provisioning del cluster del cluster Azure HDInsight e di qualsiasi altro account creato. Inoltre, se viene usata l'integrazione di Azure AD, Azure AD dispone di ruoli predefiniti che devono essere assegnati in modo esplicito e pertanto possono essere query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account membri di gruppi amministrativi.


Inoltre, è possibile usare i consigli per la gestione delle identità e degli accessi del Centro sicurezza di Azure.In addition, you may use Azure Security Center Identity and Access Management recommendations.


Come ottenere un ruolo di directory in Azure AD con PowerShell:How to get a directory role in Azure AD with PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


Come ottenere i membri di un ruolo di directory in Azure AD con PowerShell:How to get members of a directory role in Azure AD with PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0


Come monitorare l'identità e l'accesso con il Centro sicurezza di Azure:How to monitor identity and access with Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Modificare le password predefinite, ove applicabile

**Indicazioni:** quando si esegue il provisioning di un cluster, Azure richiede la creazione di nuove password per il portale Web e l'accesso Secure Shell (SSH). Non sono disponibili password predefinite da modificare, tuttavia è possibile specificare password diverse per l'accesso a SSH e al portale Web.


Come impostare le password quando si esegue il provisioning di un cluster Azure HDInsight:How to set passwords when provisioning an Azure HDInsight cluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizzare account amministrativi dedicati

**Linee guida:** integrare l'autenticazione per il cluster Azure HDInsight con Azure Active Directory.Guidance: Integrate Authentication for Azure HDInsight cluster with Azure Active Directory. Creare criteri e procedure sull'utilizzo di account amministrativi dedicati.


Inoltre, è possibile usare i consigli per la gestione delle identità e degli accessi del Centro sicurezza di Azure.In addition, you may use Azure Security Center Identity and Access Management recommendations.


Come integrare l'autenticazione di Azure HDInsight con Azure Active Directory:How to integrate Azure HDInsight authentication with Azure Active Directory:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds


Come monitorare l'identità e l'accesso con il Centro sicurezza di Azure:How to monitor identity and access with Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Usare Single Sign-On (SSO) con Azure Active Directory

**Indicazioni:** usare Azure HDInsight ID Broker per accedere ai cluster Enterprise Security Package (ESP) usando Multi-Factor Authentication, senza fornire password. Se è già stato eseguito l'accesso ad altri servizi di Azure, ad esempio il portale di Azure, è possibile accedere al cluster Azure HDInsight con un'esperienza Single Sign-On (SSO).


Come abilitare Azure HDInsight ID Broker:How to enable Azure HDInsight ID Broker:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Indicazioni:** abilitare l'autenticazione a più fattori di Azure AD e seguire i consigli per la gestione delle identità e degli accessi del Centro sicurezza sicurezza di Azure.Guidance: Enable Azure AD MFA and follow Azure Security Center Identity and Access Management recommendations. I cluster di Azure HDInsight con il pacchetto di sicurezza aziendale configurato possono essere connessi a un dominio in modo che gli utenti del dominio possano usare le credenziali di dominio per l'autenticazione con i cluster ed eseguire processi di Big Data.Azure HDInsight clusters with the Enterprise Security Package configured can be connected to a domain so that domain users can use their domain credentials to authenticate with the clusters and run big data jobs. Quando si esegue l'autenticazione con l'autenticazione a più fattori (MFA) abilitata, gli utenti dovranno fornire un secondo fattore di autenticazione.


Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


Come monitorare l'identità e l'accesso all'interno del Centro sicurezza di Azure:How to monitor identity and access within Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizzare macchine dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

**Guida:** usare PAW (workstation con accesso con privilegi) con l'autenticazione a più fattori configurata per accedere e configurare i cluster di Azure HDInsight e le risorse correlate.


Ulteriori informazioni sulle workstation con accesso privilegiato:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


Come abilitare l'autenticazione a più fattori in Azure:How to enable MFA in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registrare e avvisare su attività sospette da account amministrativi

**Indicazioni:** i cluster di Azure HDInsight con il pacchetto di sicurezza aziendale configurato possono essere connessi a un dominio in modo che gli utenti del dominio possano usare le credenziali di dominio per l'autenticazione. È possibile usare i report di sicurezza di Azure Active Directory (AAD) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente AAD. Usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso alle attività.


Come identificare gli utenti AAD segnalati per attività rischiose:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk


Come monitorare l'identità degli utenti e l'attività di accesso nel Centro sicurezza di Azure:How to monitor users identity and access activity in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo da posizioni approvate

**Indicazioni:** i cluster di Azure HDInsight con il pacchetto di sicurezza aziendale configurato possono essere connessi a un dominio in modo che gli utenti del dominio possano usare le credenziali di dominio per l'autenticazione. Utilizzare percorsi denominati con accesso condizionale per consentire l'accesso solo da raggruppamenti logici specifici di intervalli di indirizzi IP o paesi/aree geografiche.


Come configurare i percorsi denominati in Azure:How to configure Named Locations in Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida:** usare Azure Active Directory (AAD) come sistema di autenticazione e autorizzazione centrale. AAD protegge i dati utilizzando la crittografia avanzata per i dati inattivi e in transito. AAD inoltre consente di, gli ishe e l'archiviazione sicura delle credenziali utente.

I cluster di Azure HDInsight con Enterprise Security Package (ESP) configurato possono essere connessi a un dominio in modo che gli utenti del dominio possano usare le credenziali di dominio per l'autenticazione con i cluster.


Come creare e configurare un'istanza di AAD:How to create and configure an AAD instance:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


Come configurare il pacchetto di sicurezza aziendale con Servizi di dominio Azure Active Directory in Azure HDInsight:How to configure Enterprise Security Package with Azure Active Directory Domain Services in Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rivedere e riconciliare regolarmente l'accesso degli utenti

**Linee guida:** usare l'autenticazione di Azure Active Directory (AAD) con il cluster Azure HDInsight.Guidance: Use Azure Active Directory (AAD) authentication with your Azure HDInsight cluster. In AAD sono disponibili log che consentono di individuare gli account non aggiornati. Inoltre, usare Le revisioni di Azure Identity Access per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso dell'utente può essere rivisto regolarmente per assicurarsi che solo gli utenti giusti abbiano accesso continuo. 


Come usare le recensioni di azure Identity Access:How to use Azure Identity Access Reviews:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorare i tentativi di accesso agli account disattivati

**Linee guida:** usare i log di accesso e di controllo di Azure Active Directory (AAD) per monitorare i tentativi di accesso agli account disattivati. questi registri possono essere integrati in qualsiasi strumento SIEM/monitoraggio di terze parti.


È possibile semplificare questo processo creando impostazioni di diagnostica per gli account utente aAd, inviando i log di controllo e i log di accesso a un'area di lavoro di Azure Log Analytics.You can streamline this process by creating Diagnostic Settings for AAD user accounts, sending the audit logs and sign-in logs to a Azure Log Analytics workspace. Configurare gli avvisi desiderati nell'area di lavoro di Azure Log Analytics.Configure desired Alerts within Azure Log Analytics workspace.


Come integrare i log attività di Azure in Monitoraggio di Azure:How to integrate Azure Activity Logs into Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Scostamenti comportamento di avviso per l'accesso all'account

**Guida:** i cluster di Azure HDInsight configurati con Enterprise Security Package (ESP) possono essere connessi a un dominio in modo che gli utenti del dominio possano usare le credenziali di dominio per l'autenticazione con i cluster.  Usare la funzionalità Rilevamenti rischi di Azure Active Directory (AAD) e Protezione identità per configurare risposte automatiche alle azioni sospette rilevate relative alle identità utente. Inoltre, è possibile inserire dati in Azure Sentinel per ulteriori indagini.


Come visualizzare gli invii rischiosi di AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


Come configurare e abilitare i criteri di rischio di Identity Protection:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Guida**: Non disponibile; Customer Lockbox not yet supported for Azure HDInsight.

Elenco dei servizi supportati da Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

## <a name="data-protection"></a>Protezione dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Mantenere un inventario di informazioni sensibili

**Indicazioni:** usare i tag nelle risorse correlate alle distribuzioni di Azure HDInsight per facilitare il rilevamento delle risorse di Azure che archiviano o elaborano informazioni riservate.


Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolare i sistemi che memorizzano o elaborano informazioni sensibili

**Linee guida:** Implementare sottoscrizioni e/o gruppi di gestione separati per lo sviluppo, il test e la produzione. Azure HDInsight clusters and any associated storage accounts should be separated by virtual network/subnet, tagged appropriately, and secured within an network security group (NSG) or Azure Firewall. I dati del cluster devono essere contenuti in un account di archiviazione di Azure protetto o in Azure Data Lake Storage (Gen1 o Gen2).


Scegliere le opzioni di archiviazione per il cluster Azure HDInsight:Choose storage options for your Azure HDInsight cluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options


Come proteggere Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview


Come proteggere gli account di archiviazione di Azure:How to secure Azure Storage Accounts:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorare e bloccare il trasferimento non autorizzato di informazioni sensibili

**Indicazioni:** per i cluster di Azure HDInsight che archiviano o elaborano informazioni riservate, contrassegnare il cluster e le risorse correlate come riservate usando i tag. Per ridurre il rischio di perdita di dati tramite esfiltrazione, limitare il traffico di rete in uscita per i cluster Azure HDInsight usando Firewall di Azure.To reduce the risk of data loss via exfiltration, restrict outbound network traffic for Azure HDInsight clusters using Azure Firewall.


Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.


Come limitare il traffico in uscita per i cluster di Azure HDInsight con Firewall di Azure:How to restrict outbound traffic for Azure HDInsight Clusters with Azure Firewall:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic


Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Crittografare tutte le informazioni sensibili in transito

**Guida**: Crittografa tutte le informazioni sensibili in transito. Verificare che tutti i client che si connettono agli archivi dati del cluster o del cluster Azure HDInsight (Account di archiviazione di Azure o Archiviazione data Lake di Azure Gen1/Gen2) siano in grado di negoziare TLS 1.2 o versione successiva. Le risorse di Microsoft Azure negozieranno TLS 1.2 per impostazione predefinita. 


Informazioni sulla crittografia di Archiviazione di Azure Data Lake in transito:Understand Azure Data Lake Storage encryption in transit:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview


Informazioni sulla crittografia dell'account di archiviazione di Azure in transito:Understand Azure Storage Account encryption in transit:

https://docs.microsoft.com/azure/storage/blobs/security-recommendations

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilizzare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida:** le funzionalità di identificazione dei dati, classificazione e prevenzione delle perdite non sono ancora disponibili per Archiviazione di Azure o risorse di calcolo. Implementare una soluzione di terze parti, se necessario, ai fini della conformità.



Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.



Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse4.6: Usare il controllo degli accessi in base al ruolo di

**Linee guida:** con Azure HDInsight Enterprise Security Package (ESP), è possibile usare Apache Ranger per creare e gestire criteri di offuscamento dei dati e controllo qualità per i dati archiviati in file, cartelle, database, tabelle e righe/colonne. L'amministratore hadoop può configurare il controllo degli accessi in base al ruolo (RBAC) per proteggere Apache Hive, HBase, Kafka e Spark utilizzando questi plug-in in Apache Ranger.

La configurazione dei criteri RBAC con Apache Ranger consente di associare le autorizzazioni a un ruolo nell'organizzazione. Con questo livello di astrazione risulta ancor più semplice assicurarsi che le persone dispongano solo delle autorizzazioni necessarie per svolgere le proprie responsabilità lavorative.

Configurazioni dei pacchetti di sicurezza aziendali con Servizi di dominio Azure Active Directory in HDInsight:Enterprise Security Package configurations with Azure Active Directory Domain Services in HDInsight:https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Panoramica della sicurezza aziendale in Azure HDInsight:Overview of enterprise security in Azure HDInsight:https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizzare la prevenzione della perdita di dati basata su host per applicare il controllo degli accessi

**Indicazioni:** per i cluster di Azure HDInsight che archiviano o elaborano informazioni riservate, contrassegnare il cluster e le risorse correlate come riservate usando i tag. Le funzionalità di identificazione dei dati, classificazione e prevenzione delle perdite non sono ancora disponibili per archiviazione di Azure o risorse di calcolo. Implementare una soluzione di terze parti, se necessario, ai fini della conformità.


Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.


Comprendere la protezione dei dati dei clienti in Azure:Understand customer data protection in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Condiviso

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografare le informazioni sensibili inattivi

**Linee guida:** se si usa il database SQL di Azure per archiviare i metadati Apache Hive e Apache Oozie, assicurarsi che i dati SQL rimangano crittografati in qualsiasi momento. Per gli account di archiviazione di Azure e l'archiviazione data lake (Gen1 o Gen2), è consigliabile consentire a Microsoft di gestire le chiavi di crittografia, tuttavia, è possibile gestire le proprie chiavi.



Come gestire le chiavi di crittografia per gli account di archiviazione di Azure:How to manage encryption keys for Azure Storage Accounts:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal



Come creare Azure Data Lake Storage usando le chiavi di crittografia gestite del cliente:How to create Azure Data Lake Storage using customer managed encryption keys:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal



Informazioni sulla crittografia per il database SQL di Azure:Understand encryption for Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption



Come configurare Transparent Data Encryption per il database SQL utilizzando le chiavi gestite dal cliente:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e avvisare le modifiche alle risorse critiche di Azure4.9: Log and alert on changes to critical Azure resources

**Linee guida:** configurare le impostazioni di diagnostica per gli account di archiviazione di Azure associati ai cluster di Azure HDInsight per monitorare e registrare tutte le operazioni CRUD sui dati del cluster. Abilitare il controllo per tutti gli account di archiviazione o gli archivi dati associati al cluster Azure HDInsight.Enable Auditing for any Storage Accounts or Data Lake Stores associated with the Azure HDInsight cluster.


Come abilitare la registrazione/controllo aggiuntivo per un account di archiviazione di Azure:How to enable additional logging/auditing for an Azure Storage Account:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account


Come abilitare la registrazione/controllo aggiuntivo per Archiviazione data lake di Azure:How to enable additional logging/auditing for Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per ulteriori informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Eseguire strumenti automatici di scansione delle vulnerabilità

**Linee guida**: Implementare una soluzione di gestione delle vulnerabilità di terze parti.


Facoltativamente, se si dispone di una sottoscrizione rapid7, Qualys o di qualsiasi altra piattaforma di gestione delle vulnerabilità, è possibile usare azioni script per installare gli agenti di valutazione delle vulnerabilità nei nodi del cluster HDInsight di Azure e gestire i nodi tramite il rispettivo portale.


Come installare l'agente Rapid7 manualmente:

https://insightvm.help.rapid7.com/docs/azure-security-center


Come installare Qualys Agent manualmente:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf


Come utilizzare le azioni script:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuire una soluzione di gestione automatica delle patch del sistema operativo

**Linee guida:** gli aggiornamenti automatici del sistema sono stati abilitati per le immagini dei nodi del cluster, tuttavia è necessario riavviare periodicamente i nodi del cluster per garantire l'applicazione degli aggiornamenti.


Microsoft per gestire e aggiornare le immagini del nodo di Base HDInsight di Azure.Microsoft to maintain and update base Azure HDInsight node images.


Come configurare la pianificazione delle patch del sistema operativo per i cluster HDInsight:How to configure the OS patching schedule for HDInsight clusters:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Condiviso

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuire una soluzione di gestione automatica delle patch software di terze parti

**Linee guida:** usare azioni script o altri meccanismi per applicare patch ai cluster di Azure HDInsight.Guidance: Use script actions or other mechanisms to patch your Azure HDInsight clusters. I cluster appena creati avranno sempre gli ultimi aggiornamenti disponibili, incluse le patch di sicurezza più recenti.


Come configurare la pianificazione delle patch del sistema operativo per i cluster di Azure HDInsight basati su Linux:How to configure the OS patching schedule for Linux-based Azure HDInsight clusters:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching


Come utilizzare le azioni script:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Confrontare le scansioni delle vulnerabilità back-to-back

**Linee guida**: Implementare una soluzione di gestione delle vulnerabilità di terze parti che ha la capacità di confrontare le scansioni di vulnerabilità nel tempo. Se si dispone di un abbonamento Rapid7 o Qualys, è possibile utilizzare il portale di tale fornitore per visualizzare e confrontare le analisi delle vulnerabilità back-to-back.

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilizzare un processo di valutazione del rischio per assegnare priorità alla correzione delle vulnerabilità individuate

**Linee guida**: Utilizzare un programma di punteggio di rischio comune (ad esempio Common Vulnerability Scoring System) o le valutazioni di rischio predefinite fornite dallo strumento di scansione di terze parti.

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per ulteriori informazioni, vedere [Controllo di sicurezza: inventario e gestione delle risorse](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Usare Azure Asset Discovery

**Linee guida:** usare Azure Resource Graph per eseguire query/scoprire tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via), inclusi i cluster di Azure HDInsight, all'interno delle sottoscrizioni.  Assicurarsi di disporre delle autorizzazioni appropriate (di lettura) nel tenant e di essere in grado di enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.


Anche se le risorse di Azure classiche possono essere individuate tramite Azure Resource Graph, è consigliabile creare e usare le risorse di Azure Resource Manager in futuro.


Come creare query con Azure Resource Graph:How to create queries with Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


Come visualizzare le sottoscrizioni di Azure:How to view your Azure Subscriptions:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


Informazioni sul controllo degli accessi in base al ruolo di Azure:Understand

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

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


Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Gestire un inventario delle risorse di Azure approvate e dei titoli software

**Linee guida:** definire un elenco delle risorse di Azure approvate e del software approvato per le risorse di calcolo

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorare le risorse di Azure non approvate

**Linee guida:** usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:Guidance : Use Azure policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query/individuare risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare query con Azure Resource Graph:How to create queries with Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorare le applicazioni software non approvate all'interno delle risorse di calcolo

**Linee guida:** implementare una soluzione di terze parti per monitorare i nodi del cluster per le applicazioni software non approvate.

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Rimuovere le risorse e le applicazioni software di Azure non approvate

**Linee guida:** usare Azure Resource Graph per eseguire query/scoprire tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via), inclusi i cluster di Azure HDInsight, all'interno delle sottoscrizioni.  Rimuovere tutte le risorse di Azure non approvate individuate. Per i nodi del cluster di Azure HDInsight, implementare una soluzione di terze parti per rimuovere o avvisare il software non approvato.


Come creare query con Azure Resource Graph:How to create queries with Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilizzare solo applicazioni approvate

**Linee guida:** per i nodi del cluster AZURE HDInsight, implementare una soluzione di terze parti per impedire l'esecuzione di software non autorizzato.

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

**Linee guida:** per i nodi del cluster HDInsight di Azure, implementare una soluzione di terze parti per impedire l'esecuzione di tipi di file non autorizzati.

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resources Manager tramite script

**Linee guida:** usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app "Gestione di Microsoft Azure".


Come configurare l'accesso condizionale per bloccare l'accesso a Azure Resource Manager:How to configure Conditional Access to block access to Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitare la capacità degli utenti di eseguire script all'interno di risorse di calcolo

**Orientamento**: Non applicabile; Ciò non è applicabile ad Azure HDInsight poiché gli utenti (non amministratori) del cluster non devono accedere ai singoli nodi per eseguire i processi. L'amministratore del cluster ha accesso root a tutti i nodi del cluster.

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separare fisicamente o logicamente le applicazioni ad alto rischio

**Orientamento**: Non applicabile; Benchmark è destinato al servizio App di Azure o alle risorse di calcolo che ospitano le applicazioni Web.benchmark is intended for Azure Apps Service or compute resources hosting web applications.

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per ulteriori informazioni, vedere [Controllo di sicurezza: configurazione protetta](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Stabilire configurazioni sicure per tutte le risorse di Azure7.1: Establish secure configurations for all Azure resources

**Linee guida:** usare gli alias dei criteri di Azure nello spazio dei nomi "Microsoft.HDInsight" per creare criteri personalizzati per controllare o applicare la configurazione di rete del cluster HDInsight.Guidance : Use Azure Policy aliases in the "Microsoft.HDInsight" namespace to create custom policies to audit or enforce the network configuration of your HDInsight cluster.


Come visualizzare gli alias dei criteri di Azure disponibili:How to view available Azure Policy Aliases:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Stabilire configurazioni sicure del sistema operativo

**Linee guida:** Immagini del sistema operativo Azure HDInsight gestite e gestite da Microsoft.Guidance: Azure HDInsight Operating System Images managed and maintained by Microsoft. Cliente responsabile dell'implementazione di configurazioni sicure per il sistema operativo dei nodi del cluster. 

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Gestire le configurazioni delle risorse di Azure protette

**Linee guida:** usare i criteri di Azure [deny] e [deploy if not exist] per applicare le impostazioni sicure per i cluster di Azure HDInsight e le risorse correlate.


Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Informazioni sugli effetti dei criteri di Azure:Understand Azure Policy Effects:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Mantenere le configurazioni sicure del sistema operativo

**Linee guida:** Immagini del sistema operativo Azure HDInsight gestite e gestite da Microsoft.Guidance: Azure HDInsight Operating System Images managed and maintained by Microsoft. Cliente responsabile dell'implementazione della configurazione dello stato a livello di sistema operativo.

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Condiviso

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Archiviare in modo sicuro la configurazione delle risorse di Azure7.5: Securely store configuration of Azure resources

**Indicazioni:** se si usano definizioni di criteri di Azure personalizzate, usare DevOps di Azure o Azure Repos per archiviare e gestire il codice in modo sicuro.



Come archiviare il codice in DevOps di Azure:How to store code in Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops



Azure Repos Documentation:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Memorizzare in modo sicuro immagini personalizzate del sistema operativo

**Orientamento**: Non applicabile; immagini personalizzate non applicabili ad Azure HDInsight.Custom images not applicable to Azure HDInsight.

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida:** usare gli alias dei criteri di Azure nello spazio dei nomi "Microsoft.HDInsight" per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Inoltre, sviluppare un processo e una pipeline per la gestione delle eccezioni dei criteri.



Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Linee guida:** Implementare una soluzione di terze parti per mantenere lo stato desiderato per i sistemi operativi del nodo del cluster.

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementare il monitoraggio automatico della configurazione per i servizi di Azure7.9: Implement automated configuration monitoring for Azure services

**Linee guida:** usare gli alias dei criteri di Azure nello spazio dei nomi "Microsoft.HDInsight" per creare criteri personalizzati per controllare o applicare la configurazione del cluster HDInsight.Guidance : Use Azure Policy aliases in the "Microsoft.HDInsight" namespace to create custom policies to audit or enforce the configuration of your HDInsight cluster.


Come visualizzare gli alias dei criteri di Azure disponibili:How to view available Azure Policy Aliases:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementare il monitoraggio automatico della configurazione per i sistemi operativi

**Linee guida:** implementare una soluzione di terze parti per monitorare lo stato dei sistemi operativi del nodo del cluster.

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gestire i segreti di Azure in modo sicuro

**Linee guida:** Azure HDInsight include il supporto ByOK (Bring Your Own Key) per Apache Kafka. Questa funzionalità consente di essere proprietari delle chiavi usate per crittografare i dati inattivi e di gestirle.


Tutti i dischi gestiti in Azure HDInsight sono protetti con Azure Storage Service Encryption (SSE). Per impostazione predefinita, i dati su tali dischi vengono crittografati usando chiavi gestite da Microsoft. Se si abilita BYOK, si fornisce la chiave di crittografia per Azure HDInsight per usarla e gestirla usando l'insieme di credenziali delle chiavi di Azure.If you enable BYOK, you provide the encryption key for Azure HDInsight to use and manage it using Azure Key Vault.


L'insieme di credenziali delle chiavi può essere usato anche con le distribuzioni di Azure HDInsight per gestire le chiavi per l'archiviazione cluster (account di archiviazione di Azure e Archiviazione data lake di Azure)Key Vault may also be use with Azure HDInsight deployments to manage keys for cluster storage (Azure Storage Accounts, and Azure Data Lake Storage)


Come portare la propria chiave per Apache Kafka in Azure HDInsight:How to bring your own key for Apache Kafka on Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok


Come gestire le chiavi di crittografia per gli account di archiviazione di Azure:How to manage encryption keys for Azure Storage Accounts:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gestire le identità in modo sicuro e automatico

**Guida:** le identità gestite possono essere usate in Azure HDInsight per consentire ai cluster di accedere ai servizi di dominio di Azure Active Directory, accedere a Archiviazione delle chiavi di Azure o accedere ai file in Azure Data Lake Storage Gen2.Guidance: Managed identities can be used in Azure HDInsight to allow your clusters to access Azure Active Directory domain services, access Azure Key Vault, or access files in Azure Data Lake Storage Gen2.


Informazioni sulle identità gestite con Azure HDInsight:Understand Managed Identities with Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminare l'esposizione di credenziali indesiderate

**Linee guida:** se si usa codice relativo alla distribuzione di Azure HDInsight, è possibile implementare Credential Scanner per identificare le credenziali all'interno del codice. Credential Scanner will also encourage moving discovered credentials to more secure locations such as Azure Key Vault. 


Come configurare Credential Scanner:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="malware-defense"></a>Difesa da malware

*Per ulteriori informazioni, vedere [Controllo di sicurezza: Difesa da malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilizzare software antimalware gestito centralmente

**Guida:** Azure HDInsight viene fornito con Clamscan preinstallato e abilitato per le immagini del nodo del cluster, tuttavia è necessario gestire il software e aggregare/monitorare manualmente i log prodotti da Clamscan.Guidance: Azure HDInsight comes with Clamscan pre-installed and enabled for the cluster node images, however you must manage the software and manually aggregate/monitor any logs Clamscan produces.


Informazioni su Clamscan per Azure HDInsight:Understand Clamscan for Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: File pre-scansione da caricare in risorse di Azure non di calcolo

**Linee guida:** Microsoft Antimalware è abilitato nell'host sottostante che supporta i servizi di Azure, tuttavia non viene eseguito sul contenuto dei clienti.


Preeseguire la scansione di tutti i file caricati in tutte le risorse di Azure correlate alla distribuzione del cluster di Azure HDInsight, ad esempio Archiviazione data lake, Archiviazione BLOB e così via. Microsoft non può accedere ai dati dei clienti in questi casi.


Informazioni su Microsoft Antimalware per i servizi cloud di Azure e le macchine virtuali:Understand Microsoft Antimalware for Azure Cloud Services and Virtual Machines:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Condiviso

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Verificare che il software antimalware e le firme siano aggiornate

**Indicazioni:** Azure HDInsight viene fornito con Clamscan preinstallato e abilitato per le immagini del nodo del cluster. Clamscan eseguirà automaticamente gli aggiornamenti del motore e delle definizioni, tuttavia l'aggregazione e la gestione dei log dovranno essere eseguite manualmente.


Informazioni su Clamscan per Azure Azure HDInsight:Understand Clamscan for Azure Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

## <a name="data-recovery"></a>Ripristino dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatici regolari

**Indicazioni:** quando si usa un account di archiviazione di Azure per l'archivio dati del cluster HDInsight, scegliere l'opzione di ridondanza appropriata (LRS, 'RS, GRS, RA-GRS).  Quando si usa un database SQL di Azure per l'archivio dati del cluster Azure HDInsight, configurare la replica geografica attiva.


Come configurare la ridondanza di archiviazione per gli account di archiviazione di Azure:How to configure storage redundancy for Azure Storage Accounts:

https://docs.microsoft.com/azure/storage/common/storage-redundancy


Come configurare la ridondanza per i database SQL di Azure:How to configure redundancy for Azure SQL Databases:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni:** quando si usa un account di archiviazione di Azure per l'archivio dati del cluster Azure HDInsight, scegliere l'opzione di ridondanza appropriata (LRS, 'RS, GRS, RA-GRS). Se si usa l'insieme di credenziali delle chiavi di Azure per qualsiasi parte della distribuzione di Azure HDInsight, verificare che venga eseguito il backup delle chiavi.


Scegliere le opzioni di archiviazione per il cluster Azure HDInsight:Choose storage options for your Azure HDInsight cluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options


Come configurare la ridondanza di archiviazione per gli account di archiviazione di Azure:How to configure storage redundancy for Azure Storage Accounts:

https://docs.microsoft.com/azure/storage/common/storage-redundancy


Come eseguire il backup delle chiavi dell'insieme di credenziali delle chiavi in Azure:How to backup Key Vault keys in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni:** se l'insieme di credenziali delle chiavi di Azure viene usato con la distribuzione di Azure HDInsight, testare il ripristino delle chiavi gestite dal cliente di cui è stato eseguito il backup.


Come portare la propria chiave per Apache Kafka in Azure HDInsight:How to bring your own key for Apache Kafka on Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok


Come ripristinare le chiavi dell'insieme di credenziali delle chiavi in Azure:How to restore key vault keys in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dai clienti

**Linee guida:** se l'insieme di credenziali delle chiavi di Azure viene usato con la distribuzione di Azure HDInsight, abilitare l'eliminazione temporanea nell'insieme di credenziali delle chiavi per proteggere le chiavi da eliminazioni accidentali o dannose.


Come abilitare l'eliminazione temporanea nell'insieme di credenziali delle chiavi di Azure:How to enable Soft-Delete in Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Creare una guida alla risposta agli incidenti

**Linee guida**: Assicurarsi che siano presenti piani di risposta agli incidenti scritti che definiscono i ruoli del personale e le fasi della gestione/gestione degli incidenti.



Come configurare le automazioni del flusso di lavoro all'interno del Centro sicurezza di Azure:How to configure Workflow Automations within Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Creare una procedura di assegnazione dei punteggi degli eventi imprevisti e di assegnazione delle priorità

**Guida:** il Centro sicurezza assegna un livello di gravità agli avvisi, per consentire di assegnare una priorità all'ordine di assistenza a ogni avviso, in modo che quando una risorsa viene compromessa, è possibile raggiungerlo immediatamente. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sull'analita utilizzato per emettere l'avviso, nonché il livello di probabilità che vi sia stato un intento dannoso dietro l'attività che ha portato all'avviso.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Testare le procedure di risposta alla sicurezza

**Linee guida**: Eseguire esercizi per testare le capacità di risposta agli incidenti dei sistemi a una cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze. Fare riferimento alla pubblicazione del NIST: Guida ai programmi di test, formazione ed eserciziper piani e funzionalità IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornire i dettagli di contatto degli eventi imprevisti di sicurezza e configurare le notifiche di avviso per gli incidenti di sicurezza

**Linee guida:** le informazioni di contatto relative agli eventi imprevisti di protezione verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che i dati sono stati utilizzati da una parte illegale o non autorizzata.



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

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza entro 60 giorni

**Linee guida**: Seguire le regole di coinvolgimento Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.



Ulteriori informazioni sulla strategia di Microsoft e sull'esecuzione di Red Teaming e sui test di penetrazione dei siti in tempo reale su infrastruttura cloud, servizi e applicazioni gestiti Microsoft sono disponibili qui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere il benchmark di sicurezza di [AzureSee](https://docs.microsoft.com/azure/security/benchmarks/overview) the Azure Security Benchmark
- Altre informazioni sulle linee di base per la sicurezza di AzureLearn more about [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
