---
title: Baseline della sicurezza di Azure per HDInsight
description: Baseline della sicurezza di Azure per HDInsight
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7315ef68b3562a28ee2515077c1e7d2f23c3803f
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230433"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Baseline della sicurezza di Azure per HDInsight

La linea di base di sicurezza di Azure per HDInsight contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere [Panoramica delle baseline di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

**Linee guida**: la sicurezza perimetrale in Azure HDInsight viene eseguita tramite reti virtuali. Un amministratore dell'organizzazione può creare un cluster all'interno di una rete virtuale e usare un gruppo di sicurezza di rete (NSG) per limitare l'accesso alla rete virtuale. Solo gli indirizzi IP consentiti nelle regole del gruppo di sicurezza di rete in ingresso saranno in grado di comunicare con il cluster HDInsight di Azure. Questa configurazione offre sicurezza perimetrale. Tutti i cluster distribuiti in una rete virtuale avranno anche un endpoint privato che viene risolto in un indirizzo IP privato all'interno della rete virtuale per l'accesso HTTP privato ai gateway del cluster.

Per ridurre il rischio di perdita di dati tramite exfiltration, limitare il traffico di rete in uscita per i cluster Azure HDInsight usando il firewall di Azure.

Come distribuire Azure HDInsight all'interno di una rete virtuale e proteggere con un gruppo di sicurezza di rete: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Come limitare il traffico in uscita per i cluster HDInsight di Azure con il firewall di Azure: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Linee guida**: usare il Centro sicurezza di Azure e correggere i consigli sulla protezione della rete per la rete virtuale, la subnet e il gruppo di sicurezza di rete usato per proteggere il cluster HDInsight di Azure. Abilitare i log di flusso del gruppo di sicurezza di rete (NSG) e inviare i log in un account di archiviazione di Azure al controllo del traffico. È anche possibile inviare i log dei flussi di NSG a un'area di lavoro di Azure Log Analytics e usare Azure Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Alcuni vantaggi di Azure Analisi del traffico sono la possibilità di visualizzare le attività di rete e identificare le aree sensibili, identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni errate di rete.

Come abilitare i log dei flussi di NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare e usare Analisi del traffico di Azure:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Indicazioni**: Non applicabile; il benchmark è destinato al servizio App di Azure o alle risorse di calcolo che ospitano applicazioni Web.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: per le protezioni da attacchi DDoS, abilitare la protezione standard DDoS di Azure nella rete virtuale in cui è distribuita la HDInsight di Azure. Usare il Centro sicurezza di Azure Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP Internet dannosi noti o non usati.

Come configurare Protezione DDoS: 

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Informazioni sull'Intelligence per le minacce integrata nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: registrare i pacchetti di rete e i log dei flussi

**Linee guida**: abilitare il gruppo di sicurezza di rete (NSG) per i log di NSG collegati alla subnet usata per proteggere il cluster HDInsight di Azure. Registrare i log dei flussi di NSG in un account di archiviazione di Azure per generare record di flusso. Se necessario per l'analisi dell'attività anomala, abilitare l'acquisizione di pacchetti di Azure Network Watcher.

Come abilitare i log dei flussi di NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: distribuire sistemi di rilevamento intrusioni/prevenzione intrusioni (IDS/IPS) basati sulla rete

**Linee guida**: è possibile soddisfare il requisito ID di controllo di sicurezza di Azure 1,1; Distribuire il cluster HDInsight di Azure in una rete virtuale e proteggerlo con un gruppo di sicurezza di rete (NSG).

Sono disponibili diverse dipendenze per Azure HDInsight che richiedono il traffico in ingresso. Il traffico di gestione in ingresso non può essere inviato attraverso un dispositivo firewall. Gli indirizzi di origine per il traffico di gestione necessario sono noti e pubblicati. Creare regole del gruppo di sicurezza di rete con queste informazioni per consentire il traffico solo da percorsi attendibili, proteggendo il traffico in ingresso verso i cluster.

Per ridurre il rischio di perdita di dati tramite exfiltration, limitare il traffico di rete in uscita per i cluster Azure HDInsight usando il firewall di Azure.

Come distribuire HDInsight in una rete virtuale e proteggere con un gruppo di sicurezza di rete: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Informazioni sulle dipendenze di HDInsight e sull'utilizzo del firewall: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Indirizzi IP di gestione HDInsight: https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Indicazioni**: Non applicabile; il benchmark è destinato al servizio App di Azure o alle risorse di calcolo che ospitano applicazioni Web.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete (NSG) collegati alla subnet in cui è distribuito il cluster Azure HDInsight. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

Comprendere e usare i tag di servizio per Azure HDInsight:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete correlate al cluster HDInsight di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. HDInsight" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete del cluster Azure HDInsight.

È anche possibile usare i progetti di Azure per semplificare le distribuzioni di Azure su larga scala mediante la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio Azure Resource Manager modelli, controlli RBAC e criteri, in una singola definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

Come visualizzare gli alias dei criteri di Azure disponibili:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Come configurare e gestire i criteri di Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare un Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida**: usare i tag per il gruppo di sicurezza di rete (gruppi) e altre risorse correlate alla sicurezza di rete e al flusso del traffico associati al cluster HDInsight di Azure. Per le regole dei singoli gruppi di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

Come creare e usare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Come creare una rete virtuale:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Come creare una NSG con una configurazione di sicurezza:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle distribuzioni HDInsight di Azure. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

Come visualizzare e recuperare gli eventi del log attività di Azure: 

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Come creare avvisi in Monitoraggio di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Linee guida**: Microsoft gestisce le origini temporali per i componenti cluster di Azure HDInsight, è possibile aggiornare la sincronizzazione dell'ora per le distribuzioni di calcolo.

Come configurare la sincronizzazione dell'ora per le risorse di calcolo di Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: è possibile caricare il cluster HDInsight di Azure in monitoraggio di Azure per aggregare i dati di sicurezza generati dal cluster. Utilizzare query personalizzate per rilevare e rispondere alle minacce nell'ambiente. 

Come eseguire l'onboarding di un cluster HDInsight di Azure in monitoraggio di Azure:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Come creare query personalizzate per un cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare monitoraggio di Azure per il cluster HDInsight, indirizzarlo a un'area di lavoro log Analytics. Questa operazione registrerà le informazioni rilevanti sul cluster e le metriche del sistema operativo per tutti i nodi del cluster HDInsight di Azure.

Come abilitare la registrazione per il cluster HDInsight:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Come eseguire query nei log di HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Linee guida**: caricare il cluster Azure HDInsight in monitoraggio di Azure. Verificare che l'area di lavoro Log Analytics utilizzata includa il periodo di conservazione del log impostato in base alle normative di conformità dell'organizzazione.

Come eseguire l'onboarding di un cluster HDInsight di Azure in monitoraggio di Azure:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Come configurare Log Analytics periodo di memorizzazione dell'area di lavoro:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: caricare il cluster Azure HDInsight in monitoraggio di Azure. Assicurarsi che l'area di lavoro di Azure Log Analytics utilizzata disponga del periodo di memorizzazione del log impostato in base alle normative di conformità dell'organizzazione.

Come eseguire l'onboarding di un cluster HDInsight di Azure in monitoraggio di Azure:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Come configurare Log Analytics periodo di memorizzazione dell'area di lavoro:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida**: usare query dell'area di lavoro di Azure log Analytics per eseguire query nei log di Azure HDInsight:

Come creare query personalizzate per i cluster HDInsight di Azure:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: abilitare gli avvisi per le attività anomale

**Linee guida**: usare l'area di lavoro di Azure log Analytics per il monitoraggio e l'invio di avvisi sulle attività anomale nei registri di sicurezza e negli eventi correlati al cluster HDInsight di Azure.

Come gestire gli avvisi nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Come inviare un avviso sui dati del log di log Analytics:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida**: Azure HDInsight viene fornita con clamscan preinstallato e abilitato per le immagini del nodo del cluster, tuttavia è necessario gestire il software e aggregare/monitorare manualmente i log prodotti da clamscan.

Informazioni su clamscan:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida**: implementare una soluzione di terze parti per la registrazione DNS.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: configurare manualmente la registrazione della console in base ai singoli nodi.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: mantenere un inventario degli account amministrativi

**Linee guida**: mantenere il record dell'account amministrativo locale creato durante il provisioning del cluster del cluster HDInsight di Azure e di qualsiasi altro account creato dall'utente. Inoltre, se si utilizza Azure AD integrazione, Azure AD dispone di ruoli predefiniti che devono essere assegnati in modo esplicito e pertanto possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

Inoltre, è possibile usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

Come ottenere un ruolo della directory in Azure AD con PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Come monitorare l'identità e l'accesso con il Centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Indicazioni**: durante il provisioning di un cluster, Azure richiede la creazione di nuove password per il portale Web e l'accesso Secure Shell (SSH). Non sono disponibili password predefinite da modificare, ma è possibile specificare password diverse per l'accesso SSH e del portale Web.

Come impostare le password durante il provisioning di un cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Linee guida**: integrare l'autenticazione per il cluster Azure HDInsight con Azure Active Directory. Creare criteri e procedure per l'utilizzo di account amministrativi dedicati.

Inoltre, è possibile usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

Come integrare l'autenticazione HDInsight di Azure con Azure Active Directory:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Come monitorare l'identità e l'accesso con il Centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida**: usare Azure HDInsight ID Broker per accedere ai cluster Enterprise Security Package (ESP) con multi-factor authentication, senza fornire alcuna password. Se è già stato effettuato l'accesso ad altri servizi di Azure, ad esempio la portale di Azure, è possibile accedere al cluster Azure HDInsight con un'esperienza di Single Sign-On (SSO).

Come abilitare Azure HDInsight ID Broker:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Indicazioni**: abilitare Azure AD MFA e seguire le raccomandazioni sulla gestione delle identità e degli accessi nel Centro sicurezza di Azure. I cluster HDInsight di Azure con la Enterprise Security Package configurata possono essere connessi a un dominio in modo che gli utenti del dominio possano usare le proprie credenziali di dominio per l'autenticazione con i cluster ed eseguire Big Data processi. Quando si esegue l'autenticazione con l'autenticazione a più fattori abilitata, agli utenti verrà richiesto di fornire un secondo fattore di autenticazione.

Come abilitare l'autenticazione a più fattori in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Come monitorare identità e accesso nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni**: usare le workstation Paw (Privileged Access workstation) con multi-factor authentication (autenticazione a più fattori) configurate per accedere e configurare i cluster HDInsight di Azure e le risorse correlate.

Informazioni sulle workstation con accesso con privilegi:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare l'autenticazione a più fattori in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: registrare e inviare avvisi per le attività sospette dagli account amministrativi

**Linee guida**: i cluster HDInsight di Azure con il Enterprise Security Package configurato possono essere connessi a un dominio in modo che gli utenti del dominio possano usare le proprie credenziali di dominio per l'autenticazione. È possibile usare i report di sicurezza di Azure Active Directory (AAD) per la generazione di log e avvisi quando si verifica un'attività sospetta o non sicura nell'ambiente AAD. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

Come identificare gli utenti di AAD contrassegnati per le attività rischiose:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Come monitorare l'identità degli utenti e l'attività di accesso nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Linee guida**: i cluster HDInsight di Azure con il Enterprise Security Package configurato possono essere connessi a un dominio in modo che gli utenti del dominio possano usare le proprie credenziali di dominio per l'autenticazione. Usare i percorsi denominati di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

Come configurare le località denominate in Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (AAD) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. AAD inoltre sale, hash e archivia in modo sicuro le credenziali utente.

I cluster HDInsight di Azure con Enterprise Security Package (ESP) configurati possono essere connessi a un dominio in modo che gli utenti del dominio possano usare le proprie credenziali di dominio per l'autenticazione con i cluster.

Come creare e configurare un'istanza di AAD:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Come configurare Enterprise Security Package con Azure Active Directory Domain Services in Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Linee guida**: usare l'autenticazione Azure Active Directory (AAD) con il cluster HDInsight di Azure. AAD offre log che consentono di individuare gli account obsoleti. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso dell'utente può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo. 

Come usare le verifiche di accesso alle identità di Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: monitorare i tentativi di accesso agli account disattivati

**Indicazioni**: usare i registri di accesso e di controllo di Azure Active Directory (AAD) per monitorare i tentativi di accesso agli account disattivati; Questi log possono essere integrati in qualsiasi strumento di SIEM/monitoraggio di terze parti.

È possibile semplificare questo processo creando impostazioni di diagnostica per gli account utente di AAD, inviando i log di controllo e i log di accesso a un'area di lavoro di Azure Log Analytics. Configurare gli avvisi desiderati nell'area di lavoro di Azure Log Analytics.

Come integrare i log attività di Azure in Monitoraggio di Azure: 

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Linee guida**: i cluster Azure HDInsight con Enterprise Security Package (ESP) configurati possono essere connessi a un dominio in modo che gli utenti del dominio possano usare le proprie credenziali di dominio per l'autenticazione con i cluster.  Usare i rilevamenti di rischio e la funzionalità di protezione delle identità di Azure Active Directory (AAD) per configurare risposte automatiche per rilevare azioni sospette correlate alle identità utente. Inoltre, è possibile inserire dati in Sentinel di Azure per un'analisi più approfondita.

Come visualizzare gli accessi a rischio di AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Come configurare e abilitare i criteri di rischio di Identity Protection:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: non disponibile; Customer Lockbox non ancora supportata per Azure HDInsight.

Elenco dei servizi Customer Lockbox supportati: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: mantenere un inventario delle informazioni riservate

**Indicazioni**: usare i tag sulle risorse correlate alle distribuzioni HDInsight di Azure per semplificare il monitoraggio delle risorse di Azure che archiviano o elaborano informazioni riservate.

Come creare e usare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. I cluster HDInsight di Azure e tutti gli account di archiviazione associati devono essere separati da rete virtuale/subnet, contrassegnati in modo appropriato e protetti in un gruppo di sicurezza di rete (NSG) o in un firewall di Azure. I dati del cluster devono essere contenuti in un account di archiviazione di Azure protetto o Azure Data Lake Storage (Gen1 o Gen2).

Scegliere le opzioni di archiviazione per il cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Come proteggere Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Come proteggere gli account di archiviazione di Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: per i cluster HDInsight di Azure che archiviano o elaborano informazioni riservate, contrassegnare il cluster e le risorse correlate come sensibili usando i tag. Per ridurre il rischio di perdita di dati tramite exfiltration, limitare il traffico di rete in uscita per i cluster Azure HDInsight usando il firewall di Azure.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

Come limitare il traffico in uscita per i cluster HDInsight di Azure con il firewall di Azure:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Informazioni sulla protezione dei dati dei clienti in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: crittografare tutte le informazioni riservate in transito. Assicurarsi che tutti i client che si connettono al cluster HDInsight o agli archivi dati cluster di Azure (account di archiviazione di Azure o Azure Data Lake Storage Gen1/Gen2) siano in grado di negoziare TLS 1,2 o versione successiva. Microsoft Azure risorse negozieranno TLS 1,2 per impostazione predefinita. 

Informazioni sulla crittografia Azure Data Lake Storage in transito:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Informazioni sulla crittografia dell'account di archiviazione di Azure in transito:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni**: le funzionalità di identificazione dei dati, classificazione e prevenzione della perdita non sono ancora disponibili per le risorse di calcolo o di archiviazione di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

Informazioni sulla protezione dei dati dei clienti in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Linee guida**: con Azure HDInsight Enterprise Security Package (ESP), è possibile usare Apache Ranger per creare e gestire i criteri granulari di controllo degli accessi e di offuscamento dei dati per i dati archiviati in file, cartelle, database, tabelle e righe/colonne. L'amministratore di Hadoop può configurare il controllo degli accessi in base al ruolo (RBAC) per proteggere Apache Hive, HBase, Kafka e Spark usando questi plug-in Apache Ranger.

La configurazione dei criteri RBAC con Apache Ranger consente di associare le autorizzazioni a un ruolo nell'organizzazione. Con questo livello di astrazione risulta ancor più semplice assicurarsi che le persone dispongano solo delle autorizzazioni necessarie per svolgere le proprie responsabilità lavorative.

Enterprise Security Package configurazioni con Azure Active Directory Domain Services in HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Panoramica della sicurezza aziendale in Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: per i cluster HDInsight di Azure che archiviano o elaborano informazioni riservate, contrassegnare il cluster e le risorse correlate come sensibili usando i tag. Le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per le risorse di archiviazione o di calcolo di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

Informazioni sulla protezione dei dati dei clienti in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: se si usa il database SQL di Azure per archiviare i metadati di Apache hive e Apache oozie, assicurarsi che i dati SQL rimangano sempre crittografati. Per gli account di archiviazione di Azure e Data Lake Storage (Gen1 o Gen2), è consigliabile consentire a Microsoft di gestire le chiavi di crittografia, ma è possibile gestire le proprie chiavi.

Come gestire le chiavi di crittografia per gli account di archiviazione di Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

Come creare Azure Data Lake Storage usando chiavi di crittografia gestite dal cliente:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal

Informazioni sulla crittografia per il database SQL di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption

Come configurare Transparent Data Encryption per il database SQL usando chiavi gestite dal cliente:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: configurare le impostazioni di diagnostica per gli account di archiviazione di Azure associati ai cluster HDInsight di Azure per monitorare e registrare tutte le operazioni CRUD sui dati del cluster. Abilitare il controllo per tutti gli account di archiviazione o gli archivi di Data Lake associati al cluster HDInsight di Azure.

Come abilitare la registrazione o il controllo aggiuntivo per un account di archiviazione di Azure:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

Come abilitare la registrazione o il controllo aggiuntivi per Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni**: Implementare una soluzione di gestione delle vulnerabilità di terze parti.

Facoltativamente, se si dispone di un Rapid7, Qualys o qualsiasi altra sottoscrizione della piattaforma di gestione delle vulnerabilità, è possibile usare le azioni script per installare gli agenti di valutazione della vulnerabilità nei nodi del cluster HDInsight di Azure e gestire i nodi tramite il rispettivo portale.

Come installare Rapid7 Agent manualmente:

https://insightvm.help.rapid7.com/docs/install

Come installare Qualys Agent manualmente:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf

Come usare le azioni script:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Linee guida**: gli aggiornamenti automatici del sistema sono stati abilitati per le immagini dei nodi del cluster, tuttavia è necessario riavviare periodicamente i nodi del cluster per assicurarsi che gli aggiornamenti vengano applicati.

Microsoft per gestire e aggiornare le immagini del nodo di Azure HDInsight di base.

Come configurare la pianificazione dell'applicazione di patch del sistema operativo per i cluster HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: distribuire una soluzione di gestione delle patch automatizzata per il software di terze parti

**Linee guida**: usare azioni script o altri meccanismi per applicare patch ai cluster HDInsight di Azure. I cluster appena creati avranno sempre gli ultimi aggiornamenti disponibili, incluse le patch di sicurezza più recenti.

Come configurare la pianificazione dell'applicazione di patch del sistema operativo per i cluster Azure HDInsight basati su Linux:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

Come usare le azioni script:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee guida**: implementare una soluzione di gestione delle vulnerabilità di terze parti che abbia la possibilità di confrontare le analisi delle vulnerabilità nel tempo. Se si dispone di una sottoscrizione di Rapid7 o Qualys, è possibile usare il portale del fornitore per visualizzare e confrontare le analisi delle vulnerabilità back-to-back.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: usare un programma comune di assegnazione dei punteggi di rischio, ad esempio un sistema comune di valutazione delle vulnerabilità, oppure le valutazioni di rischio predefinite fornite dallo strumento di analisi di terze parti.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: usare l'individuazione di asset in Azure

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via), inclusi i cluster HDInsight di Azure, all'interno delle sottoscrizioni.  Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

Come creare query con Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Come visualizzare le sottoscrizioni di Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Informazioni su RBAC di Azure:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

Come creare e usare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia degli asset. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Come creare sottoscrizioni di Azure aggiuntive: 

https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare Gruppi di gestione:

https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e usare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: mantenere un inventario delle risorse di Azure approvate e dei titoli software

**Linee guida**: definire l'elenco delle risorse di Azure approvate e il software approvato per le risorse di calcolo

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare le risorse di Azure non approvate

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare query con Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: implementare una soluzione di terze parti per monitorare i nodi del cluster per le applicazioni software non approvate.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via), inclusi i cluster HDInsight di Azure, all'interno delle sottoscrizioni.  Se individuate, rimuovere eventuali risorse di Azure non approvate. Per i nodi del cluster Azure HDInsight, implementare una soluzione di terze parti per rimuovere o avvisare il software non approvato.

Come creare query con Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Linee guida**: per i nodi del cluster HDInsight di Azure, implementare una soluzione di terze parti per impedire l'esecuzione di software non autorizzato.


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come negare un tipo di risorsa specifico con Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="610-implement-approved-application-list"></a>6.10: implementare l'elenco di applicazioni approvate

**Linee guida**: per i nodi del cluster HDInsight di Azure, implementare una soluzione di terze parti per impedire l'esecuzione di tipi di file non autorizzati.


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: limitare la capacità degli utenti di interagire con Azure Resource Manager tramite script

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Linee guida**: non applicabile; Questa operazione non è applicabile ad Azure HDInsight perché gli utenti (non amministratori) del cluster non necessitano dell'accesso ai singoli nodi per l'esecuzione dei processi. L'amministratore del cluster dispone dell'accesso radice a tutti i nodi del cluster.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni**: Non applicabile; il benchmark è destinato al servizio App di Azure o alle risorse di calcolo che ospitano applicazioni Web.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Controllo di sicurezza: configurazione sicura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. HDInsight" per creare criteri personalizzati per controllare o applicare la configurazione di rete del cluster HDInsight.

Come visualizzare gli alias dei criteri di Azure disponibili:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Come configurare e gestire i criteri di Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Linee guida**: immagini del sistema operativo Azure HDInsight gestite e gestite da Microsoft. Cliente responsabile dell'implementazione di configurazioni sicure per il sistema operativo dei nodi del cluster. 


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare i criteri di Azure [deny] e [Deploy if not exist] per applicare impostazioni sicure per i cluster HDInsight di Azure e le risorse correlate.

Come configurare e gestire i criteri di Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Comprendere gli effetti dei criteri di Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Linee guida**: immagini del sistema operativo Azure HDInsight gestite e gestite da Microsoft. Cliente responsabile dell'implementazione della configurazione dello stato a livello di sistema operativo.


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Condiviso

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni di criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire il codice in modo sicuro.

Come archiviare il codice in Azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentazione Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Linee guida**: non applicabile; immagini personalizzate non applicabili ad Azure HDInsight.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: distribuire strumenti di gestione della configurazione di sistema

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. HDInsight" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

Come configurare e gestire Criteri di Azure: 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Linee guida**: implementare una soluzione di terze parti per mantenere lo stato desiderato per i sistemi operativi dei nodi del cluster.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. HDInsight" per creare criteri personalizzati per controllare o applicare la configurazione del cluster HDInsight.

Come visualizzare gli alias dei criteri di Azure disponibili:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Come configurare e gestire i criteri di Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: implementare una soluzione di terze parti per monitorare lo stato dei sistemi operativi del nodo del cluster.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: Azure HDInsight include il supporto Bring your own key (BYOK) per Apache Kafka. Questa funzionalità consente di essere proprietari delle chiavi usate per crittografare i dati inattivi e di gestirle.

Tutti i dischi gestiti in Azure HDInsight sono protetti con Azure crittografia del servizio di archiviazione (SSE). Per impostazione predefinita, i dati su tali dischi vengono crittografati usando chiavi gestite da Microsoft. Se si Abilita BYOK, è necessario specificare la chiave di crittografia per l'uso e la gestione da parte di Azure HDInsight usando Azure Key Vault.

Key Vault possono anche essere usati con le distribuzioni HDInsight di Azure per gestire le chiavi per l'archiviazione del cluster (account di archiviazione di Azure e Azure Data Lake Storage)

Come importare la propria chiave per Apache Kafka in Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Come gestire le chiavi di crittografia per gli account di archiviazione di Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: è possibile usare le identità gestite in Azure HDInsight per consentire ai cluster di accedere ai file Azure Active Directory Domain Services, accedere Azure Key Vault o accedere ai file in Azure Data Lake storage Gen2.

Informazioni sulle identità gestite con Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Linee guida**: se si usa un codice correlato alla distribuzione di Azure HDInsight, è possibile implementare Credential scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 

Come configurare Credential scanner:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Controllo di sicurezza: difesa da malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: usare software antimalware gestito in modo centralizzato

**Linee guida**: Azure HDInsight viene fornita con clamscan preinstallato e abilitato per le immagini del nodo del cluster, tuttavia è necessario gestire il software e aggregare/monitorare manualmente i log prodotti da clamscan.

Informazioni su clamscan per Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft antimalware è abilitato nell'host sottostante che supporta i servizi di Azure, ma non viene eseguito sui contenuti del cliente.

Eseguire la pre-analisi di tutti i file caricati in tutte le risorse di Azure correlate alla distribuzione del cluster HDInsight di Azure, ad esempio Data Lake Storage, archiviazione BLOB e così via. Microsoft non è in grado di accedere ai dati dei clienti in tali istanze.

Informazioni su Microsoft antimalware per servizi cloud e macchine virtuali di Azure:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: assicurarsi che le firme e il software antimalware siano aggiornati

**Linee guida**: Azure HDInsight viene fornita con clamscan pre-installato e abilitato per le immagini del nodo del cluster. Clamscan eseguirà automaticamente gli aggiornamenti del motore e delle definizioni, tuttavia l'aggregazione e la gestione dei log dovranno essere eseguite manualmente.

Informazioni su clamscan per Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Linee guida**: quando si usa un account di archiviazione di Azure per l'archivio dati del cluster HDInsight, scegliere l'opzione di ridondanza appropriata (con ridondanza locale, ZRS, GRS, RA-GRS).  Quando si usa un database SQL di Azure per l'archivio dati del cluster HDInsight di Azure, configurare la replica geografica attiva.

Come configurare la ridondanza di archiviazione per gli account di archiviazione di Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Come configurare la ridondanza per il database SQL di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: eseguire backup completi del sistema e il backup di tutte le chiavi gestite dal cliente

**Linee guida**: quando si usa un account di archiviazione di Azure per l'archivio dati del cluster HDInsight di Azure, scegliere l'opzione di ridondanza appropriata (con ridondanza locale, ZRS, GRS, RA-GRS). Se si usa Azure Key Vault per qualsiasi parte della distribuzione di Azure HDInsight, assicurarsi che venga eseguito il backup delle chiavi.

Scegliere le opzioni di archiviazione per il cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Come configurare la ridondanza di archiviazione per gli account di archiviazione di Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Come eseguire il backup di chiavi di Key Vault in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: se Azure Key Vault viene usato con la distribuzione di Azure HDInsight, testare il ripristino delle chiavi gestite dal cliente sottoposte a backup.

Come importare la propria chiave per Apache Kafka in Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Come ripristinare le chiavi di Key Vault in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: se Azure Key Vault viene usato con la distribuzione di Azure HDInsight, abilitare l'eliminazione temporanea in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.

Come abilitare l'eliminazione temporanea nei Azure Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare una guida per rispondere agli eventi imprevisti

**Indicazioni**: assicurarsi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono i ruoli del personale, nonché le fasi di gestione/gestione degli eventi imprevisti.

Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Linee guida**: il Centro sicurezza assegna un livello di gravità agli avvisi, in modo da consentire la priorità dell'ordine in cui si partecipa a ogni avviso, in modo che, quando una risorsa viene compromessa, è possibile accedervi immediatamente. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e i gap e rivedere il piano in base alle esigenze. Fare riferimento alla pubblicazione del NIST: Guida ai programmi di test, formazione e esercizio per i piani IT e le funzionalità:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati.

Come impostare il contatto di sicurezza del Centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi in Sentinel.

Come configurare l'esportazione continua:

https://docs.microsoft.com/azure/security-center/continuous-export

Come trasmettere gli avvisi in Sentinel di Azure:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" per gli avvisi e le raccomandazioni di sicurezza.

Come configurare l'automazione del flusso di lavoro e le app per la logica:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Controllo di sicurezza: test di penetrazione ed esercizi Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: eseguire con regolarità test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici in termini di sicurezza entro 60 giorni

**Linee guida**: seguire le regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

È possibile trovare altre informazioni sulla strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestiti da Microsoft, qui: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Vedere altre informazioni sulle [baseline di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
