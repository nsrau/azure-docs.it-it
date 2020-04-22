---
title: Baseline di sicurezza di Azure per Key Vault
description: Baseline di sicurezza di Azure per Key Vault
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 6e660c1244dd5566fbfb45a6da37d39294354ccb
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756994"
---
# <a name="azure-security-baseline-for-key-vault"></a>Baseline di sicurezza di Azure per Key Vault

La linea di base della sicurezza di Azure per l'insieme di credenziali delle chiavi contiene suggerimenti che consentono di migliorare il livello di sicurezza della distribuzione.

La linea di base per questo servizio è ricavata da [Azure Security Benchmark versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce consigli su come proteggere le soluzioni cloud in Azure con le indicazioni sulle procedure consigliate.

Per altre informazioni, vedere [Panoramica delle linee di base](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)della sicurezza di Azure.For more information, see Azure Security Baselines overview .

## <a name="network-security"></a>Sicurezza di rete

*Per ulteriori informazioni, vedere [Controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale1.1: Protect resources using Network Security Groups or Azure Firewall on your Virtual Network

**Indicazioni:** Integrare l'insieme di credenziali delle chiavi di Azure con Azure Private Link.Guidance : Integrate Azure Key Vault with Azure Private Link. 

Il servizio di collegamento privato di Azure consente di accedere ai servizi di Azure (ad esempio, Archiviazione chiave di Azure) e ai servizi cliente/partner ospitati di Azure su un endpoint privato nella rete virtuale.

Un endpoint privato di Azure è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su Collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.

Come integrare Key Vault con Azure Private Link:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Indicazioni:** usare il Centro sicurezza di Azure e seguire i consigli di protezione della rete per proteggere le risorse configurate da Key Vault in Azure.Guidance : Use Azure Security Center and follow network protection recommendations to help secure your Key Vault-configurato resources in Azure. 

Per altre informazioni sulla sicurezza di rete fornita dal Centro sicurezza di Azure:For more information about the Network Security provided by Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteggere le applicazioni Web critiche

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle applicazioni Web in esecuzione nel servizio app di Azure o alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Guida:** abilitare Azure DDoS Protection Standard nelle istanze di Azure Virtual Networks associate alle istanze di Key Vault per la protezione da attacchi denial-of-service distribuiti. Usare Azure Security Center Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP Internet dannosi o inutilizzati noti.

 
Gestire Azure DDoS Protection Standard usando il portale di Azure:Manage Azure DDoS Protection Standard using the Azure portal:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Rilevamento delle minacce per il livello di servizio di Azure nel Centro sicurezza di Azure:Threat detection for the Azure service layer in Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Registrare i pacchetti di rete e i registri di flusso

**Indicazioni:** L'insieme di credenziali delle chiavi di Azure non usa i gruppi di sicurezza di rete (NSG) e i log di flusso per l'insieme di credenziali delle chiavi di Azure non vengono acquisiti. Usare invece Collegamento privato di Azure per proteggere le istanze dell'insieme di credenziali delle chiavi di Azure e abilitare le impostazioni di diagnostica per registrare le metriche e controllare gli eventi.

Integrare l'insieme di credenziali delle chiavi con Azure Private Link:Integrate Key Vault with Azure Private Link:

https://docs.microsoft.com/azure/key-vault/private-link-service

Registrazione dell'insieme di credenziali delle chiavi di Azure:Azure Key Vault logging:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

**Linee guida:** questo requisito può essere soddisfatto configurando la protezione avanzata dalle minacce (ATP) per l'insieme di credenziali delle chiavi di Azure.Guidance: This requirement can be met by configuring advanced threat protection (ATP) for Azure Key Vault. ATP fornisce un ulteriore livello di intelligence di sicurezza. Questo strumento rileva i tentativi potenzialmente dannosi di accedere o sfruttare gli account dell'insieme di credenziali delle chiavi di Azure.This tool detects potentially harmful attempts to access or exploit Azure Key Vault accounts.

Quando Il Centro sicurezza di Azure rileva attività anomale, visualizza avvisi. Invia inoltre un messaggio di posta elettronica all'amministratore della sottoscrizione con i dettagli delle attività sospette e consigli su come analizzare e correggere le minacce identificate.

Configurare la protezione avanzata dalle minacce per l'insieme di credenziali delle chiavi di Azure:Set up advanced threat protection for Azure Key Vault:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gestire il traffico verso le applicazioni web

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle applicazioni Web in esecuzione nel servizio app di Azure o alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e l'overhead amministrativo delle regole di sicurezza di rete

**Indicazioni:** per le risorse che devono accedere alle istanze dell'insieme di chiavi di Azure, usare i tag del servizio di Azure per l'insieme di credenziali delle chiavi di Azure per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure.Guidance : For resources that need access to your Azure Key Vault instances, use Azure service tags for the Azure Key Vault to define network access controls on network security groups or Azure Firewall. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag del servizio (ad esempio, ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi degli indirizzi inclusi nel tag del servizio e aggiorna automaticamente il tag del servizio quando gli indirizzi cambiano.

Panoramica dei tag di servizio di Azure:Azure service tags overview:https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida:** definire e implementare configurazioni di sicurezza standard per le risorse di rete associate alle istanze dell'insieme di credenziali delle chiavi di Azure con Criteri di Azure.Guidance: Define and implement standard security configurations for network resources associated with your Azure Key Vault instances with Azure Policy. Usare gli alias dei criteri di Azure negli spazi dei nomi "Microsoft.KeyVault" e "Microsoft.Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle istanze di Archiviazione delle chiavi di Azure.Use Azure Policy aliases in the "Microsoft.KeyVault" and "Microsoft.Network" namespaces to create custom policies to audit or enforce the network configuration of your Azure Key Vault instances. È anche possibile usare le definizioni dei criteri predefinite correlate all'insieme di credenziali delle chiavi di Azure, ad esempio:You may also make using of built-in policy definitions related to Azure Key Vault, such as:

Gli insieme di credenziali delle chiavi devono usare un endpoint servizio di rete virtuale

Esercitazione: Creare e gestire i criteri per applicare la conformità:Tutorial: Create and manage policies to enforce compliance:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Esempi di criteri di Azure:Azure Policy Samples:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Guida introduttiva: Definire e assegnare un blueprint nel portale:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regole di configurazione del traffico dei documenti

**Linee guida:** usare i tag per le risorse correlate alla sicurezza di rete e al flusso di traffico per le istanze di Azure Key Vault per fornire metadati e organizzazione logica.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e relativo valore" per assicurarsi che tutte le risorse vengano create con i tag e per notificare le risorse senza tag esistenti.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai relativi tag.

Usare i tag per organizzare le risorse di Azure:Use tags to organize your Azure resources:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilizzare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni:** usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze dell'insieme di credenziali delle chiavi di Azure.Guidance : Use Azure Activity Log to monitor network resource configurations and detect changes for network resources related to your Azure Key Vault instances. Creare avvisi all'interno di Monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse di rete critiche.

Visualizzare e recuperare gli eventi del log attività di Azure:View and retrieve Azure Activity Log events:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Creare, visualizzare e gestire gli avvisi del log attività tramite Monitoraggio di Azure:Create, view, and manage activity log alerts by using Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizzare origini di sincronizzazione dell'ora approvate

**Orientamento**: Non applicabile; Microsoft gestisce l'origine ora usata per le risorse di Azure, ad esempio L'insieme di credenziali delle chiavi di Azure, per i timestamp nei log.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurare la gestione dei registri di protezione centralizzati

**Indicazioni:** eseguire l'inserimento dei log tramite Monitoraggio di Azure per aggregare i dati di sicurezza generati dall'insieme di credenziali delle chiavi di Azure.Guidance: Ingest logs via Azure Monitor to aggregate security data generated by Azure Key Vault. In Monitoraggio di Azure usare l'area di lavoro di Azure Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archiviazione. In alternativa, è possibile abilitare e on-board i dati di Azure Sentinel o un SIEM di terze parti. 

Registrazione dell'insieme di credenziali delle chiavi di Azure:Azure Key Vault logging:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Guida introduttiva: Come eseguire l'onboarding di Azure Sentinel:Quickstart: How to on-board Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Abilitare la registrazione di controllo per le risorse di Azure2.3: Abilitare la registrazione di controllo per le risorse di Azure2.

**Indicazioni:** abilitare le impostazioni di diagnostica nelle istanze dell'insieme di chiavi di Azure per l'accesso ai log di controllo, sicurezza e diagnostica. I log attività, che sono automaticamente disponibili, includono l'origine dell'evento, la data, l'utente, il timestamp, gli indirizzi di origine, gli indirizzi di destinazione e altri elementi utili.

Registrazione dell'insieme di credenziali delle chiavi di Azure:Azure Key Vault logging:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Raccogliere i registri di sicurezza dai sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dello spazio di archiviazione del registro di protezione

**Linee guida:** in Monitoraggio di Azure, per l'area di lavoro Log Analytics usata per contenere i log dell'insieme di credenziali delle chiavi di Azure, impostare il periodo di conservazione in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archiviazione.

Modificare il periodo di conservazione dei dati:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i registri

**Linee guida:** analizzare e monitorare i log per verificare il comportamento anomalo ed esaminare regolarmente i risultati per le risorse protette da Archiviazione chiavi di Azure.Guidance : Analyze and monitor logs for anomalous behavior and regularly review results for your Azure Key Vault-protected resources. Usare l'area di lavoro Log Analytics di Monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log. In alternativa, è possibile abilitare e on-board i dati di Azure Sentinel o di terze parti SIEM. 

Guida introduttiva: Sentinella di Azure integrata:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Introduzione a Log Analytics in Monitoraggio di Azure:Get started with Log Analytics in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Introduzione alle query di log in Monitoraggio di Azure:Get started with log queries in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Abilitare gli avvisi per attività anomale

**Indicazioni:** nel Centro sicurezza di Azure abilitare la protezione avanzata dalle minacce (ATP) per l'insieme di credenziali delle chiavi. Abilitare le impostazioni di diagnostica in Archiviazione delle chiavi di Azure e inviare log a un'area di lavoro di Log Analytics.Enable diagnostic settings in Azure Key Vault and send logs to a Log Analytics workspace. Eseguire l'onboarding dell'area di lavoro di Log Analytics in Azure Sentinel in quanto fornisce una soluzione SOAR (Security Orchestration Automated Response). Ciò consente di creare e utilizzare playbook (soluzioni automatizzate) per risolvere i problemi di sicurezza.

Guida introduttiva: Sentinella di Azure integrata:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Gestire e rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure:Manage and respond to security alerts in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Rispondere agli eventi con Avvisi di Monitoraggio di Azure:Respond to events with Azure Monitor Alerts:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizzare la registrazione antimalware

**Orientamento**: Non applicabile; L'insieme di credenziali delle chiavi di Azure non elabora né produce log correlati al malware.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: Abilitare la registrazione delle query DNS

**Orientamento**: Non applicabile; L'insieme di credenziali delle chiavi di Azure non elabora né produce log correlati al DNS.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: Abilitare la registrazione di controllo della riga di comando

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per ulteriori informazioni, vedere [Controllo di sicurezza: controllo delle identità e degli accessi](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Mantenere un inventario degli account amministrativi

**Linee guida:** gestire un inventario delle applicazioni registrate in Azure Active Directory, nonché di tutti gli account utente che hanno accesso a chiavi, segreti e certificati dell'insieme di credenziali delle chiavi di Azure.Guidance : Maintain an inventory of your Azure Active Directory-registered applications, as well as any user accounts that have access to your Azure Key Vault keys, secrets, and certificates. È possibile usare il portale di Azure o PowerShell per eseguire query e riconciliare l'accesso all'insieme di credenziali delle chiavi. Per visualizzare l'accesso in PowerShell, usare il comando seguente:To view access in PowerShell, use the following command:

(Get-AzResource -ResourceId [KeyVaultResourceID]). Proprietà.AccessPolicies

Registrazione di un'applicazione con Azure Active Directory:Registering an application with Azure Active Directory:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Accesso sicuro a un insieme di credenziali delle chiavi:Secure access to a key vault:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Modificare le password predefinite, ove applicabile

**Orientamento**: Non applicabile; L'insieme di credenziali delle chiavi di Azure non ha il concetto di password predefinite poiché l'autenticazione viene fornita da Active Directory e protetta con il controllo degli accessi in base al ruolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizzare account amministrativi dedicati

**Linee guida:** creare procedure operative standard per l'uso di account amministrativi dedicati che hanno accesso alle istanze dell'archivio delle chiavi di Azure.Guidance : Create standard operating procedures around the use of dedicated administrative accounts that have access to your Azure Key Vault instances. Usare Gestione identità e accessi del Centro sicurezza di Azure (attualmente in anteprima) per monitorare il numero di account amministrativi attivi.

Monitorare l'identità e l'accesso (anteprima):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida:** usare un'entità servizio di Azure insieme a AppId, TenantID e ClientSecret per autenticare l'applicazione senza problemi e recuperare il token che verrà usato per accedere ai segreti dell'insieme di credenziali delle chiavi di Azure.Guidance: Use an Azure service principal in conjunction with the AppId, TenantID, and ClientSecret, to seamlessly authenticate your application and retrieve the token that will be used to access your Azure Key Vault secrets.

Autenticazione da servizio a servizio in Azure Key Vault tramite .NET:Service-to-service authentication to Azure Key Vault using .NET:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Indicazioni:** abilitare Azure Active Directory Multi-Factor Authentication e seguire i consigli di Gestione delle identità e degli accessi del Centro sicurezza di Azure (attualmente in anteprima) per proteggere le risorse abilitate per l'hub eventi.

Pianificazione di una distribuzione di Azure Multi-Factor Authentication basata su cloud:Planning a cloud-based Azure Multi-Factor Authentication deployment:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Monitorare l'identità e l'accesso (anteprima):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizzare macchine dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

**Guida:** usare una workstation con accesso privilegiato (PAW) con Azure Multi-Factor Authentication (MFA) configurato per accedere alle risorse abilitate per l'insieme di credenziali delle chiavi. 

Workstation con accesso privilegiato:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

Pianificazione di una distribuzione di Azure Multi-Factor Authentication basata su cloud:Planning a cloud-based Azure Multi-Factor Authentication deployment:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registrare e avvisare su attività sospette da account amministrativi

**Linee guida:** usare Azure Active Directory (AAD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verifica un'attività sospetta o non sicura nell'ambiente. Utilizzare i rilevamenti dei rischi di AAD per visualizzare avvisi e report sul comportamento rischioso degli utenti. Per altre registrazioni, inviare avvisi di rilevamento dei rischi del Centro sicurezza di Azure in Monitoraggio di Azure e configurare avvisi/notifiche personalizzati usando i gruppi di azioni.

Abilitare la protezione avanzata dalle minacce (ATP) per l'insieme di credenziali delle chiavi di Azure per generare avvisi per attività sospette.

Distribuire Azure AD Privileged Identity Management (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Configurare la protezione avanzata dalle minacce per l'insieme di credenziali delle chiavi di Azure (anteprima):Set up advanced threat protection for Azure Key Vault (preview):https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Avvisi per l'insieme di credenziali delle chiavi di Azure (anteprima):Alerts for Azure Key Vault (Preview):https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Rilevamenti dei rischi di Azure Active Directory:Azure Active Directory risk detections:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Creare e gestire gruppi di azioni nel portale di Azure:Create and manage action groups in the Azure portal:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo da posizioni approvate

**Guida**: Configurare la condizione di posizione di un criterio di accesso condizionale e gestire i percorsi denominati. Con le posizioni denominate, è possibile creare raggruppamenti logici di intervalli di indirizzi IP o paesi e aree geografiche. È possibile limitare l'accesso alle risorse riservate, ad esempio i segreti dell'insieme di credenziali delle chiavi, alle posizioni denominate configurate.

Qual è la condizione di posizione in Azure Active Directory Conditional Access?:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida:** usare Azure Active Directory (AAD) come sistema di autenticazione e autorizzazione centrale per le risorse di Azure, ad esempio L'insieme di credenziali delle chiavi. Ciò consente al controllo degli accessi in base al ruolo di amministrare le risorse sensibili.

 

Guida introduttiva: Creare un nuovo tenant in Azure Active Directory:Quickstart: Create a new tenant in Azure Active Directory:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rivedere e riconciliare regolarmente l'accesso degli utenti

**Linee guida:** esaminare i log di Azure Active Directory (AAD) per individuare gli account non aggiornati con i ruoli amministrativi di Azure Key Vault.Guidance: Review Azure Active Directory (AAD) logs to help discover stale accounts with Azure Key Vault administrative roles. Inoltre, usare le verifiche di accesso a AAD per gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali che possono essere usate per accedere a Amministratore delle chiavi di Azure e le assegnazioni di ruolo. L'accesso degli utenti deve essere esaminato regolarmente, ad esempio ogni 90 giorni per assicurarsi che solo gli utenti giusti abbiano accesso continuo.

Report di Azure Active Directory e documentazione di monitoraggio:Azure Active Directory reports and monitoring documentation:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Che cosa sono le verifiche di accesso di Azure AD?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorare i tentativi di accesso agli account disattivati

**Guida:** abilitare le impostazioni di diagnostica per L'insieme di credenziali delle chiavi di Azure e Azure Active Directory, inviando tutti i log a un'area di lavoro di Log Analytics.Guidance : Enable diagnostic settings for Azure Key Vault and Azure Active Directory, sending all logs to a Log Analytics workspace. Configurare gli avvisi desiderati, ad esempio i tentativi di accesso ai segreti disabilitati, all'interno di Log Analytics.Configure desired alerts (such as attempts to access disabled secrets) within Log Analytics.

Integrare i log di Azure AD con i log di Monitoraggio di Azure:Integrate Azure AD logs with Azure Monitor logs:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Migrazione dalla vecchia soluzione Key Vault:https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Scostamenti comportamento di avviso per l'accesso all'account

**Linee guida:** usare le funzionalità di protezione delle identità e di rilevamento dei rischi di Azure Active Directory per configurare le risposte automatiche alle azioni sospette rilevate relative alle risorse protette dell'insieme di credenziali delle chiavi di Azure.Guidance : Use Azure Active Directory's Identity Protection and risk detection features to configure automated responses to detected suspicious actions related to your Azure Key Vault protected resources. È consigliabile abilitare le risposte automatiche tramite Azure Sentinel per implementare le risposte di sicurezza dell'organizzazione. 

Report di accesso rischioso nel portale di Azure Active Directory:Risky sign ins report in the Azure Active Directory portal:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Procedura: configurare e abilitare i criteri di rischio:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Come eseguire l'onboarding di Azure Sentinel:How to onboard Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Orientamento**: Non applicabile; Customer Lockbox not supported for Azure Key Vault.

Servizi e scenari supportati nella disponibilità generale:Supported services and scenarios in general availability:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="data-protection"></a>Protezione dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Mantenere un inventario di informazioni sensibili

**Indicazioni:** usare i tag per tenere traccia delle risorse di Azure che archiviano o elaborano informazioni riservate nelle risorse abilitate per L'insieme di credenziali delle chiavi di Azure.Guidance : Use tags to assist in tracking Azure resources that store or process sensitive information on Azure Key Vault enabled resources. 

Usare i tag per organizzare le risorse di Azure:Use tags to organize your Azure resources:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolare i sistemi che memorizzano o elaborano informazioni sensibili

**Linee guida:** è possibile proteggere l'accesso all'insieme di credenziali delle chiavi di Azure utilizzando gli endpoint del servizio di rete virtuale configurati per limitare l'accesso a subnet specifiche.

Dopo l'esecuzione delle regole del firewall, è possibile eseguire le operazioni dei piani dei dati dell'insieme di credenziali delle chiavi di Azure solo quando la richiesta proviene da subnet consentite o intervalli di indirizzi IP. Questo vale anche per l'accesso a Archiviazione delle chiavi di Azure nel portale di Azure.This also applies to Azure Key Vault access in the Azure portal. Sebbene sia possibile passare a un insieme di credenziali delle chiavi dal portale di Azure, potrebbe non essere possibile elencare chiavi, segreti o certificati se il computer client non è presente nell'elenco dei computer consentiti. Ciò influisce anche su Selezione archivio chiavi di Azure e su altri servizi di Azure.This also affects the Azure Key Vault Picker and other Azure services. Potrebbe essere possibile visualizzare elenchi di insiemi di credenziali delle chiavi, ma non di chiavi di elenco, se le regole del firewall impediscono al computer client di farlo.

Configurare i firewall e le reti virtuali dell'insieme di credenziali delle chiavi di Azure:Configure Azure Key Vault firewalls and virtual networks:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Endpoint del servizio di rete virtuale per l'insieme di credenziali delle chiavi di Azure:Virtual network service endpoints for Azure Key Vault:https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorare e bloccare il trasferimento non autorizzato di informazioni sensibili

**Linee guida:** tutti i dati archiviati all'interno dell'insieme di credenziali delle chiavi di Azure sono considerati sensibili. Usare i controlli di accesso piano dati dell'insieme di credenziali delle chiavi di Azure per controllare l'accesso ai segreti dell'insieme di credenziali delle chiavi di Azure.Use Azure Key Vault data access controls to control access to Azure Key Vault secrets. È inoltre possibile utilizzare il firewall incorporato di Key Vault per controllare l'accesso a livello di rete. Per monitorare l'accesso all'insieme di credenziali delle chiavi di Azure, abilitare le impostazioni di diagnostica dell'insieme di credenziali delle chiavi e inviare i log a un'area di lavoro di Log Analytics o account di archiviazione di Azure.To monitor access to Azure Key Vault, enable Key Vault Diagnostic Settings and send logs to an Azure Storage Account or Log Analytics workspace.

Accesso sicuro a un insieme di credenziali delle chiavi:Secure access to a key vault:https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Configurare i firewall e le reti virtuali dell'insieme di credenziali delle chiavi di Azure:Configure Azure Key Vault firewalls and virtual networks:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Registrazione dell'insieme di credenziali delle chiavi di Azure:Azure Key Vault logging:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Crittografare tutte le informazioni sensibili in transito

**Guida:** tutto il traffico verso l'insieme di credenziali delle chiavi di Azure per l'autenticazione, la gestione e l'accesso al piano dati è crittografato e passa tramite HTTPS: porta 443.Guidance: All traffic to Azure Key Vault for authentication, management, and data plane access, is encrypted and goes over HTTPS: port 443. (Tuttavia, ci sarà occasionalmente http [porta 80] traffico per CRL.) 

Accedere all'insieme di credenziali delle chiavi di Azure tramite un firewall:Access Azure Key Vault behind a firewall:https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilizzare uno strumento di individuazione attivo per identificare i dati sensibili

**Orientamento**: Non applicabile; tutti i dati all'interno di Azure Key Vault (segreti, chiavi e certificati) sono considerati sensibili.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse4.6: Usare il controllo degli accessi in base al ruolo di

**Indicazioni:** accesso sicuro alla gestione e al piano dati delle istanze dell'insieme di chiavi di Azure.Guidance: Secure access to the management and data plane of your Azure Key Vault instances.

Accesso sicuro a un insieme di credenziali delle chiavi:Secure access to a key vault:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizzare la prevenzione della perdita di dati basata su host per applicare il controllo degli accessi

**Indicazioni:** Microsoft gestisce l'infrastruttura sottostante per L'insieme di credenziali delle chiavi di Azure e ha implementato controlli rigorosi per prevenire la perdita o l'esposizione dei dati dei clienti.

Informazioni sull'insieme di credenziali delle chiavi di Azure

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Protezione dei dati dei clienti di Azure:Azure customer data protection:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografare le informazioni sensibili inattivi

**Linee guida:** tutti gli oggetti gestiti (chiave, certificati e segreti) vengono crittografati inattivi in Archiviazione delle chiavi di Azure.Guidance: All managed objects (key, certificates, and secrets) are encrypted at rest in Azure Key Vault.

Documentazione di supporto:

- [Modello di crittografia e tabella di gestione delle chiavi](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#encryption-model-and-key-management-table)


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e avvisare le modifiche alle risorse critiche di Azure4.9: Log and alert on changes to critical Azure resources

**Indicazioni:** usare la soluzione Azure Key Vault Analytics in Monitoraggio di Azure per esaminare i log eventi di controllo dell'insieme di credenziali delle chiavi di Azure.Guidance : Use the Azure Key Vault Analytics solution in Azure Monitor to review Azure Key Vault audit event logs.

Azure Key Vault Analytics solution in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per ulteriori informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Eseguire strumenti automatici di scansione delle vulnerabilità

**Linee guida:** Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano l'insieme di chiavi di Azure.Guidance: Microsoft performs vulnerability management on the underlying systems that support Azure Key Vault.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuire una soluzione di gestione automatica delle patch del sistema operativo

**Guida**: N/D; Microsoft esegue la gestione delle patch sui sistemi sottostanti che supportano Key Vault.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuire una soluzione di gestione automatica delle patch software di terze parti

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Confrontare le scansioni delle vulnerabilità back-to-back

**Guida**: Microsoft esegue la gestione delle vulnerabilità sui sistemi sottostanti che supportano Key Vault.

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilizzare un processo di valutazione del rischio per assegnare priorità alla correzione delle vulnerabilità individuate

**Indicazioni:** usare le classificazioni dei rischi predefinite (Secure Score) fornite dal Centro sicurezza di Azure.Guidance: Use the default risk ratings (Secure Score) provided by Azure Security Center.

Migliorare il punteggio sicuro nel Centro sicurezza di Azure:Improve your Secure Score in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per ulteriori informazioni, vedere [Controllo di sicurezza: inventario e gestione delle risorse](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Usare Azure Asset Discovery

**Indicazioni:** usare Azure Resource Graph per eseguire query e individuare tutte le risorse (incluse le istanze dell'insieme di chiavi di Azure) all'interno della sottoscrizione. Assicurarsi di disporre delle autorizzazioni appropriate (di lettura) nel tenant e di essere in grado di enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Guida introduttiva: Eseguire la prima query di Resource Graph usando Azure Resource Graph Explorer:Quickstart: Run your first Resource Graph query using Azure Resource Graph Explorer:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Ottenere le sottoscrizioni a cui l'account corrente può accedere.:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Che cos'è il controllo degli accessi in base al ruolo per le risorse di Azure?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Gestire i metadati delle risorse

**Linee guida:** applicare tag alle risorse dell'insieme di chiavi di Azure fornendo metadati per organizzarli logicamente in una tassonomia.

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminare risorse di Azure non autorizzate

**Linee guida:** usare il tagging, i gruppi di gestione e sottoscrizioni separate, se appropriato, per organizzare e tenere traccia delle istanze dell'insieme di credenziali delle chiavi di Azure e delle risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate dalla sottoscrizione in modo tempestivo.

Creare una sottoscrizione di Azure aggiuntiva:Create an additional Azure subscription:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Creare gruppi di gestione per l'organizzazione e la gestione delle risorse:Create management groups for resource organization and management:

https://docs.microsoft.com/azure/governance/management-groups/create

Usare i tag per organizzare le risorse di Azure:Use tags to organize your Azure resources:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Gestire un inventario delle risorse di Azure approvate e dei titoli software

**Linee guida:** definire un elenco delle risorse di Azure approvate e del software approvato per le risorse di calcolo

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorare le risorse di Azure non approvate

**Linee guida:** usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:Guidance: Use Azure policies to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Inoltre, usare Azure Resource Graph per eseguire query/individuare risorse all'interno delle sottoscrizioni.

Esercitazione: Creare e gestire i criteri per applicare la conformità:Tutorial: Create and manage policies to enforce compliance:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Guida introduttiva: Eseguire la prima query di Resource Graph usando Azure Resource Graph Explorer:Quickstart: Run your first Resource Graph query using Azure Resource Graph Explorer:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorare le applicazioni software non approvate all'interno delle risorse di calcolo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Rimuovere le risorse e le applicazioni software di Azure non approvate

**Orientamento**: Non applicabile; questa raccomandazione è destinata ad Azure nel suo complesso e alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="68-use-only-approved-applications"></a>6.8: Utilizzare solo applicazioni approvate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="69-use-only-approved-azure-services"></a>6.9: Usare solo i servizi di Azure approvati

**Linee guida:** usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:Guidance: Use Azure policies to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:

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

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Limitare la capacità degli utenti di interagire con AzureResources Manager tramite script

**Linee guida:** usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager (ARM) configurando "Blocca accesso" per l'app "Gestione di Microsoft Azure". Ciò può impedire la creazione e le modifiche alle risorse all'interno di un ambiente ad alta sicurezza, ad esempio quelle con configurazione dell'insieme di credenziali delle chiavi.

Gestire l'accesso alla gestione di Azure con accesso condizionale:Manage access to Azure management with Conditional Access:

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

**Linee guida:** usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.KeyVault" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze di Archiviazione chiavi di Azure.Guidance : Use Azure Policy aliases in the "Microsoft.KeyVault" namespace to create custom policies to audit or enforce the configuration of your Azure Key Vault instances. È anche possibile usare le definizioni dei criteri di Azure predefinite per l'insieme di credenziali delle chiavi di Azure, ad esempio:You may also use built-in Azure Policy definitions for Azure Key Vault such as:

Gli oggetti Key Vault devono essere recuperabili

Distribuisci le impostazioni di diagnostica per Key Vault nell'area di lavoro Log Analytics

I log di diagnostica in Key Vault devono essere abilitati

Gli insieme di credenziali delle chiavi devono usare un endpoint servizio di rete virtuale

Distribuisci le impostazioni di diagnostica per Key Vault nell'hub eventi

Usare le raccomandazioni del Centro sicurezza di Azure come linea di base di configurazione sicura per le istanze dell'insieme di credenziali delle chiavi di Azure.Use recommendations from Azure Security Center as a secure configuration baseline for your Azure Key Vault instances.

Come visualizzare gli alias di Criteri di Azure disponibili:How to view available Azure Policy aliases:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Esercitazione: Creare e gestire i criteri per applicare la conformità:Tutorial: Create and manage policies to enforce compliance:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Stabilire configurazioni sicure del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Gestire le configurazioni delle risorse di Azure protette

**Indicazioni:** usare i criteri di Azure [nega] e [distribuzione se non esistono] per applicare impostazioni sicure tra le risorse abilitate per L'insieme di credenziali delle chiavi di Azure.Guidance : Use Azure policy [deny] and [deploy if not exist] to enforce secure settings across your Azure Key Vault-enabled resources. 

Esercitazione: Creare e gestire i criteri per applicare la conformità:Tutorial: Create and manage policies to enforce compliance:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Informazioni sugli effetti dei criteri di Azure:Understand Azure Policy effects: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Mantenere le configurazioni sicure del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Archiviare in modo sicuro la configurazione delle risorse di Azure7.5: Securely store configuration of Azure resources

**Indicazioni:** se si usano definizioni di criteri di Azure personalizzate per le risorse abilitate per l'insieme di credenziali delle chiavi di Azure, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

Come archiviare il codice in DevOps di Azure:How to store code in Azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Azure Repos Documentation: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Memorizzare in modo sicuro immagini personalizzate del sistema operativo

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida:** usare gli alias dei criteri di Azure nello spazio dei nomi "Microsoft.KeyVault" per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Inoltre, sviluppare un processo e una pipeline per la gestione delle eccezioni dei criteri.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementare il monitoraggio automatico della configurazione per i servizi di Azure7.9: Implement automated configuration monitoring for Azure services

**Linee guida:** Usare il Centro sicurezza di Azure per eseguire analisi di base per le risorse protette da Archiviazione guidata chiave di Azure 

  

Come correggere i consigli nel Centro sicurezza di Azure:How to remediate recommendations in Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementare il monitoraggio automatico della configurazione per i sistemi operativi

**Orientamento**: Non applicabile; questo benchmark è destinato alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gestire i segreti di Azure in modo sicuro

**Linee guida:** usare l'identità del servizio gestito insieme a Archiviazione delle chiavi di Azure per semplificare e proteggere la gestione dei segreti per le applicazioni cloud. Verificare che l'eliminazione temporanea dell'account di accesso automatico delle chiavi di Azure sia abilitata.

Come integrare con le identità gestite di Azure:How to integrate with Azure Managed Identities:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Come creare un insieme di credenziali delle chiavi:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Come fornire l'autenticazione key Vault con un'identità gestita:How to provide Key Vault authentication with a managed identity: 

https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gestire le identità in modo sicuro e automatico

**Linee guida:** usare l'identità del servizio gestito insieme a Archiviazione delle chiavi di Azure per semplificare e proteggere la gestione dei segreti per le applicazioni cloud. 

  

Come integrare con le identità gestite di Azure:How to integrate with Azure Managed Identities: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity  

Come creare un insieme di credenziali delle chiavi: 

https://docs.microsoft.com/azure/key-vault/quick-create-portal    

Come fornire l'autenticazione key Vault con un'identità gestita:How to provide Key Vault authentication with a managed identity:  
https://docs.microsoft.com/azure/key-vault/managed-identity

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

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo. Microsoft gestisce l'antimalware per la piattaforma sottostante.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: File pre-scansione da caricare in risorse di Azure non di calcolo

**Linee guida:** l'antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Archiviazione delle chiavi di Azure), tuttavia non viene eseguito sul contenuto dei clienti.

Pre-scan any content being uploaded or sent to non-compute Azure resources such as Azure Key Vault. Microsoft non può accedere ai dati in questi casi.

Informazioni su Microsoft Antimalware per i servizi cloud di Azure e le macchine virtuali:Understand Microsoft Antimalware for Azure Cloud Services and Virtual Machines:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Verificare che il software antimalware e le firme siano aggiornate

**Orientamento**: Non applicabile; questa raccomandazione è destinata alle risorse di calcolo. Microsoft gestisce l'antimalware per la piattaforma sottostante.


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Non applicabile

## <a name="data-recovery"></a>Ripristino dei dati

*Per ulteriori informazioni, vedere [Controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire backup automatici regolari

**Linee guida**: Garantire backup automatici regolari dei certificati, delle chiavi, degli account di archiviazione gestiti e dei segreti dell'insieme di credenziali delle chiavi, con i comandi di PowerShell seguenti:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Facoltativamente, è possibile archiviare i backup dell'insieme di credenziali delle chiavi in Backup di Azure.Optionally, you may store your Key Vault backups within Azure Backup.

Come eseguire il backup dei certificati dell'insieme di credenziali delle chiavi:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Come eseguire il backup delle chiavi dell'insieme di credenziali:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Come eseguire il backup degli account di archiviazione gestiti dell'insieme di credenziali delle chiavi:How to backup Key Vault Managed Storage Accounts:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Come eseguire il backup dei segreti dell'insieme di credenziali delle chiavi:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Come abilitare Backup di Azure:How to enable Azure Backup:https://docs.microsoft.com/azure/backup



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida:** eseguire i backup di certificati, chiavi, account di archiviazione gestiti e segreti dell'insieme di credenziali delle chiavi, con i comandi di PowerShell seguenti:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Facoltativamente, è possibile archiviare i backup dell'insieme di credenziali delle chiavi in Backup di Azure.Optionally, you may store your Key Vault backups within Azure Backup.

Come eseguire il backup dei certificati dell'insieme di credenziali delle chiavi:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Come eseguire il backup delle chiavi dell'insieme di credenziali:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Come eseguire il backup degli account di archiviazione gestiti dell'insieme di credenziali delle chiavi:How to backup Key Vault Managed Storage Accounts:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Come eseguire il backup dei segreti dell'insieme di credenziali delle chiavi:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Come abilitare Backup di Azure:How to enable Azure Backup:https://docs.microsoft.com/azure/backup



**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Guida:** eseguire periodicamente il ripristino dei dati dei certificati, delle chiavi, degli account di archiviazione gestiti e dei segreti dell'insieme di credenziali delle chiavi, con i comandi di PowerShell seguenti:

- Restore-AzKeyVaultCertificate

- Ripristina-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Come ripristinare i certificati dell'insieme di credenziali delle chiavi:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Come ripristinare le chiavi dell'Insieme di credenziali:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Come ripristinare gli account di archiviazione gestiti dell'insieme di credenziali delle chiavi:How to restore Key Vault Managed Storage Accounts:https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Come ripristinare i segreti dell'insieme di credenziali delle chiavi:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dai clienti

**Guida:** verificare che l'eliminazione temporanea sia abilitata per l'insieme di credenziali delle chiavi di Azure.Guidance : Ensure that soft-delete is enabled for Azure Key Vault. L'eliminazione temporanea consente il ripristino degli insiemi di credenziali delle chiavi eliminati e degli oggetti dell'insieme di credenziali, ad esempio chiavi, segreti e certificati. 

Come usare l'eliminazione temporanea dell'insieme di credenziali delle chiavi di Azure:How to use Azure Key Vault's Soft Delete: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Creare una guida alla risposta agli incidenti

**Linee guida**: Creare una guida alla risposta agli incidenti per l'organizzazione. Assicurarsi che siano presenti piani di risposta agli incidenti scritti che definiscono tutti i ruoli del personale, nonché fasi di gestione/gestione degli incidenti dal rilevamento alla revisione post-incidente. Questi processi devono concentrarsi sulla protezione dei sistemi sensibili, ad esempio quelli che utilizzano i segreti dell'insieme di credenziali delle chiavi.

Come configurare le automazioni del flusso di lavoro all'interno del Centro sicurezza di Azure:How to configure Workflow Automations within Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Linee guida per creare un processo di risposta agli incidenti di sicurezza:Guidance on building your own security incident response process:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia di Microsoft Security Response Center di un incidente:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

Il cliente può anche sfruttare la Guida alla gestione degli incidenti di sicurezza del COMPUTER del NIST per facilitare la creazione del proprio piano di risposta agli incidenti: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Creare una procedura di assegnazione dei punteggi degli eventi imprevisti e di assegnazione delle priorità

**Linee guida:** il Centro sicurezza assegna una gravità a ogni avviso per consentire di definire la priorità degli avvisi da analizzare per primi. La gravità si basa sulla sicurezza del Centro sicurezza nella ricerca o sull'analita utilizzato per emettere l'avviso, nonché il livello di probabilità che vi sia stato un intento dannoso dietro l'attività che ha portato all'avviso. Inoltre, contrassegnare chiaramente gli abbonamenti (per ex. produzione, non prod) e creare un sistema di denominazione per identificare chiaramente e classificare le risorse di Azure, in particolare quelle che elaborano dati sensibili, ad esempio i segreti dell'insieme di chiavi di Azure.Production, non-prod) and create a naming system to clearly identify and categorizement Azure resources, especially those processing sensitive data such as Azure Key Vault secrets.


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Testare le procedure di risposta alla sicurezza

**Linee guida:** eseguire esercizi per testare le funzionalità di risposta agli incidenti dei sistemi a cadenza regolare per proteggere le istanze di Azure Key Vault e le risorse correlate. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

Fare riferimento alla pubblicazione del NIST: Guida ai programmi di test, formazione ed eserciziper piani e funzionalità IT: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornire i dettagli di contatto degli eventi imprevisti di sicurezza e configurare le notifiche di avviso per gli incidenti di sicurezza

**Linee guida:** le informazioni di contatto relative agli eventi imprevisti di protezione verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che i dati sono stati utilizzati da una parte illegale o non autorizzata.  Esaminare gli incidenti dopo il fatto per assicurarsi che i problemi vengano risolti.

Come impostare il contatto di sicurezza del centro di sicurezza di Azure:How to set the Azure Security Center Security Contact:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporare avvisi di sicurezza nel sistema di risposta agli incidenti

**Indicazioni:** Esportare avvisi e suggerimenti del Centro sicurezza di Azure usando la funzionalità di esportazione continua per identificare i rischi per le risorse abilitate per L'insieme di credenziali delle chiavi di Azure.Guidance: Export your Azure Security Center alerts and recommendations using the Continuous Export feature to help identify risks to Azure Key Vault-enabled resources. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo.  È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.You may use the Azure Security Center data connector to stream the alerts to Azure Sentinel. 

 

Come configurare l'esportazione continua: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Come trasmettere gli avvisi in Azure Sentinel:How to stream alerts into Azure Sentinel: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizzare la risposta agli avvisi di sicurezza

**Indicazioni:** usare la funzionalità automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite "App per la logica" negli avvisi e suggerimenti per proteggere le risorse protette da Azure Key Vault.Guidance: Use the Workflow Automation feature in Azure Security Center to automatically trigger responses via "Logic Apps" on security Apps and recommendations to protect your Azure Key Vault-protected resources. 

 

Come configurare l'automazione del flusso di lavoro e le app per la logica:How to configure Workflow Automation and Logic Apps: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoraggio del Centro sicurezza di Azure:** attualmente non disponibileAzure Security Center monitoring : Currently not available

**Responsabilità**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per ulteriori informazioni, vedere Controllo di sicurezza: test di [penetrazione ed Esercizi del team rosso](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza entro 60 giorni

**Linee guida:** non è necessario eseguire direttamente il test della penna nel servizio dell'insieme di chiavi di Azure, tuttavia è consigliabile testare le risorse di Azure che usano l'insieme di credenziali delle chiavi per garantire la sicurezza dei segreti.

È necessario seguire le regole di coinvolgimento Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Ulteriori informazioni sulla strategia di Microsoft e sull'esecuzione di Red Teaming e sui test di penetrazione dei siti in tempo reale sull'infrastruttura cloud, i servizi e le applicazioni gestiti da Microsoft sono disponibili qui: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure:** non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere il benchmark di sicurezza di [AzureSee](https://docs.microsoft.com/azure/security/benchmarks/overview) the Azure Security Benchmark
- Altre informazioni sulle linee di base per la sicurezza di AzureLearn more about [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
