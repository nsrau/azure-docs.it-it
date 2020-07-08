---
title: Baseline della sicurezza di Azure per batch
description: Baseline della sicurezza di Azure per batch
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 76312a55262d316c679bde2a69e8b98844addcb0
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963870"
---
# <a name="azure-security-baseline-for-batch"></a>Baseline della sicurezza di Azure per batch

La linea di base di sicurezza di Azure per batch contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](../security/benchmarks/overview.md), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere [Panoramica delle baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Controllo di sicurezza: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

**Linee guida**: distribuire pool di Azure batch all'interno della rete virtuale. Per consentire ai nodi di calcolo del pool di comunicare in modo sicuro con altre macchine virtuali o con una rete locale, è possibile effettuare il provisioning del pool in una subnet di una rete virtuale di Azure. Inoltre, la distribuzione del pool in una rete virtuale consente di controllare il gruppo di sicurezza di rete (NSG) usato per proteggere le interfacce di rete (NIC) dei singoli nodi e la subnet. Configurare NSG per consentire il traffico solo da indirizzi IP attendibili/locations su Internet.

Come creare un pool di Azure Batch all'interno di una rete virtuale:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Linee guida**: usare il Centro sicurezza di Azure e correggere le raccomandazioni sulla protezione della rete correlate alla rete virtuale o al gruppo di sicurezza di rete (NSG) associato al pool di batch. Abilitare i log di flusso nel NSG usato per proteggere il pool di batch e inviare i log a un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log dei flussi di NSG a un'area di lavoro di Azure Log Analytics e usare Azure Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Alcuni vantaggi di Azure Analisi del traffico sono la possibilità di visualizzare le attività di rete e identificare le aree sensibili, identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni errate di rete.

Come abilitare i log dei flussi di NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare e usare Analisi del traffico:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1,3: proteggere le applicazioni Web critiche

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare la protezione standard DDoS (Distributed Denial of Service) di Azure nella rete virtuale che protegge il pool di Azure batch per la protezione da attacchi DDoS. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

Come configurare Protezione DDoS: 

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Informazioni sull'Intelligence per le minacce integrata nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrare i pacchetti di rete e i log di flusso

**Linee guida**: abilitare i log di flusso nel gruppo di sicurezza di rete (NSG) usato per proteggere il pool di Azure batch e inviare i log a un account di archiviazione di Azure per il controllo del traffico.

Come abilitare i log dei flussi di NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1,6: distribuire sistemi di rilevamento delle intrusioni basati sulla rete/prevenzione delle intrusioni

**Linee guida**: se necessario ai fini della conformità, selezionare un'appliance virtuale di rete da Azure Marketplace che supporti gli ID e le funzionalità dei sistemi di rilevamento delle intrusioni (IPS) con funzionalità di ispezione del payload.

Se il rilevamento delle intrusioni e/o la prevenzione basata sull'ispezione del payload non è un requisito, è possibile usare il firewall di Azure con Intelligence per le minacce. Il filtro basato su Intelligence per le minacce del firewall di Azure può segnalare e negare il traffico da e verso domini e indirizzi IP dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

Distribuire il firewall di Azure con un indirizzo IP pubblico nella stessa rete virtuale dei nodi del pool di Azure Batch. Configurare le regole di Network Address Translation (NAT) tra i percorsi attendibili in Internet e gli indirizzi IP privati dei singoli nodi del pool. Nel firewall di Azure, in Intelligence per le minacce, configurare "Alert and Deny" per bloccare l'avviso e bloccare il traffico verso/da indirizzi IP dannosi noti e domini. Gli indirizzi IP e i domini vengono originati dal feed di intelligence per le minacce di Microsoft e sono inclusi solo i record con la massima confidenza. 

Come creare un pool di Azure Batch all'interno di una rete virtuale:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Come distribuire il firewall di Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-your-web-applications"></a>1,7: gestire il traffico per le applicazioni Web

**Linee guida**: non applicabile. il benchmark è destinato alle applicazioni Web in esecuzione in app Azure servizio o istanze IaaS.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nei firewall di Azure associati ai pool di Azure batch. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

Comprendere e usare i tag del servizio:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: mantenere le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete associate ai pool di Azure batch con i criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft.Batch" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete dei pool di Azure Batch.

Come configurare e gestire i criteri di Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1,10: regole di configurazione del traffico documento

**Linee guida**: usare i tag per i gruppi di servizi di rete (gruppi) e altre risorse correlate alla sicurezza di rete e al flusso del traffico associati ai pool di Azure batch. Per le regole dei singoli gruppi di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni su risorse basate sui rispettivi tag.

Come creare e usare i tag: 

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Come creare una rete virtuale:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Come creare un NSG:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usare gli strumenti automatici per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate ai pool di Azure batch. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

Come visualizzare e recuperare gli eventi del log attività di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

Come creare avvisi in Monitoraggio di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsibilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: utilizzare origini di sincronizzazione dell'ora approvate

**Linee guida**: per Azure batch, per impostazione predefinita, Microsoft fornisce la sincronizzazione dell'ora. Tuttavia, se si dispone di requisiti di sincronizzazione dell'ora specifici, è possibile implementare tali modifiche.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: configurare la gestione dei log di sicurezza centrale

**Linee guida**: caricare l'account Azure batch in monitoraggio di Azure per aggregare i dati di sicurezza generati dai dispositivi cluster. Utilizzare query personalizzate per rilevare e rispondere alle minacce nell'ambiente.  Per Azure Batch il monitoraggio a livello di risorsa, usare le API di batch per monitorare o eseguire query sullo stato delle risorse, inclusi processi, attività, nodi e pool.

Come eseguire l'onboarding di un account Azure Batch in monitoraggio di Azure:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: per il monitoraggio a livello di account Azure batch, monitorare ogni account batch usando le funzionalità di monitoraggio di Azure. Monitoraggio di Azure raccoglie metriche e facoltativamente i log di diagnostica per le risorse con ambito a livello di un account Batch, ad esempio, pool, processi e attività. Raccogliere e usare questi dati manualmente o a livello di codice per monitorare le attività nell'account Batch e per diagnosticare eventuali problemi.

Per Azure Batch il monitoraggio a livello di risorse, usare le API Azure Batch per monitorare o eseguire query sullo stato delle risorse, inclusi processi, attività, nodi e pool.

Come configurare Azure Batch il monitoraggio e la registrazione a livello di account:

https://docs.microsoft.com/azure/batch/monitoring-overview

Informazioni sul monitoraggio a livello di risorsa di batch:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="24-collect-security-logs-from-operating-system"></a>2,4: raccogliere i registri di sicurezza dal sistema operativo

**Linee guida**: monitoraggio di Azure raccoglie metriche e log di diagnostica per le risorse nell'account Azure batch. È possibile raccogliere e utilizzare questi dati in diversi modi per monitorare l'account Azure Batch e diagnosticare i problemi. È anche possibile configurare avvisi sulle metriche per ricevere notifiche quando una metrica raggiunge un valore specificato.

Se necessario, è possibile connettersi ai singoli nodi del pool tramite la shell protetta (SSH) o Remote Desktop Protocol (RDP) per accedere ai registri del sistema operativo locale.

Come raccogliere i log di diagnostica dall'account Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics

Come connettersi in remoto ai nodi del pool di Azure Batch:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2,5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: onboarding Azure batch account in monitoraggio di Azure. Assicurarsi che l'area di lavoro di Azure Log Analytics utilizzata abbia il periodo di conservazione del log impostato in base alle normative di conformità dell'organizzazione

Come configurare il monitoraggio e la registrazione di Azure Batch:

https://docs.microsoft.com/azure/batch/monitoring-overview

Come configurare il periodo di conservazione dell'area di lavoro di Azure Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: creare Azure batch avvisi della metrica che si attivano quando il valore di una metrica specificata supera una determinata soglia.

Come configurare gli avvisi della metrica Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: abilitare gli avvisi per attività anomale

**Linee guida**: creare Azure batch avvisi della metrica che si attivano quando il valore di una metrica specificata supera una determinata soglia.

Come configurare gli avvisi della metrica Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizzare la registrazione anti-malware

**Linee guida**: usare Windows Defender nei singoli nodi di batch nel caso dei sistemi operativi Windows o fornire una soluzione antimalware personalizzata se si usa Linux.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9: Abilitare la registrazione delle query DNS

**Linee guida**: implementare una soluzione di terze parti per la registrazione DNS

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2,10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: configurare manualmente la registrazione della console e la trascrizione di PowerShell in base ai singoli nodi.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3,1: gestire l'inventario degli account amministrativi

**Linee guida**: mantenere il record dell'account amministrativo locale creato durante il provisioning del pool di Azure batch e di qualsiasi altro account creato dall'utente. Inoltre, se si usa l'integrazione Azure Active Directory (AAD), AAD include ruoli predefiniti che devono essere assegnati in modo esplicito e pertanto possono essere sottoposte a query. Usare il modulo AAD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

Inoltre, è possibile usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

Come ottenere un ruolo della directory in AAD con PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Come ottenere i membri di un ruolo della directory in AAD con PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Come monitorare l'identità e l'accesso con il Centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3,2: modificare le password predefinite se applicabile

**Linee guida**: quando si effettua il provisioning di un pool di Azure batch, è possibile scegliere di creare account computer locali. Non sono disponibili password predefinite da modificare, ma è possibile specificare password diverse per l'accesso SSH (Secured Shell) e Remote Desktop Protocol (RDP). Dopo la configurazione del pool di Azure Batch, è possibile generare un utente casuale per i singoli nodi all'interno del portale di Azure o tramite Azure Resource Manager API.

Come aggiungere un utente a un nodo di calcolo specifico:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3,3: assicurarsi di usare account amministrativi dedicati

**Indicazioni**: integrazione dell'autenticazione per applicazioni Azure Batch con Azure Active Directory. Creare criteri e procedure per l'utilizzo di account amministrativi dedicati.

Inoltre, è possibile usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

Come autenticare le applicazioni batch con Azure Active Directory:

https://docs.microsoft.com/azure/batch/batch-aad-auth

Come monitorare l'identità e l'accesso con il Centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3,4: utilizzo di Single Sign-on (SSO) con Azure Active Directory

**Linee guida**: non applicabile, mentre Azure batch supporta l'autenticazione Azure AD, Single Sign-on non è supportata.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory.

**Linee guida**: integrare l'autenticazione per applicazioni Azure Batch con Azure Active Directory (AAD). Abilitare l'autenticazione a più fattori di AAD e seguire le indicazioni relative alla gestione delle identità e degli accessi nel centro sicurezza di Azure.

 

Come abilitare l'autenticazione a più fattori in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Come monitorare identità e accesso nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: usare macchine virtuali dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida**: usare le workstation Paw (Privileged Access workstation) con multi-factor authentication (autenticazione a più fattori) configurate per l'accesso e la configurazione delle risorse Azure batch.

Informazioni sulle workstation con accesso con privilegi:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare l'autenticazione a più fattori in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: registrare e segnalare l'attività sospetta da account amministrativi

**Linee guida**: se è stata integrata l'autenticazione per Azure batch applicazioni con Azure Active Directory (AAD), usare Azure Active Directory report di sicurezza per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

Come identificare Azure AD utenti contrassegnati per le attività rischiose:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Come monitorare l'identità degli utenti e l'attività di accesso nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3,8: gestire la risorsa di Azure solo da percorsi approvati

**Linee guida**: se è stata integrata l'autenticazione per Azure batch applicazioni con Azure Active Directory, è possibile usare percorsi denominati di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o di paesi/aree geografiche.

Come configurare le località denominate in Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (AAD) come sistema di autenticazione e autorizzazione centrale e integrare l'autenticazione per le applicazioni Azure batch con AAD. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. AAD inoltre sale, hash e archivia in modo sicuro le credenziali utente.

Come creare e configurare un'istanza di AAD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Come autenticare le applicazioni batch con AAD:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: rivedere e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure Active Directory (AAD) fornisce i log per individuare gli account obsoleti. Inoltre, è possibile usare le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso degli utenti può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo.

Come usare le verifiche di accesso alle identità di Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: il monitoraggio tenta di accedere agli account disattivati

**Linee guida**: creare le impostazioni di diagnostica per Azure Active Directory account utente, inviare i log di controllo e i log di accesso a un'area di lavoro di Azure log Analytics. Configurare gli avvisi desiderati nell'area di lavoro di Azure Log Analytics.

Come integrare i log attività di Azure in Monitoraggio di Azure: 

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento dell'account di accesso

**Linee guida**: usare i rilevamenti di rischio e la funzionalità di protezione delle identità di Azure Active Directory (AAD) per configurare risposte automatiche per le azioni sospette rilevate correlate alle identità utente. Inoltre, è possibile inserire dati in Sentinel di Azure per un'analisi più approfondita.

Come visualizzare gli accessi a rischio di AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Come configurare e abilitare i criteri di rischio di Identity Protection:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Come eseguire l'onboarding di Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3,13: <div>Fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto<br></div>

**Linee guida**: non disponibile; Customer Lockbox non ancora supportata per Azure Batch.
 
Elenco dei servizi Customer Lockbox supportati: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

Come creare e usare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. I pool di Azure Batch devono essere separati da rete virtuale/subnet, contrassegnati in modo appropriato e protetti con i gruppi di sicurezza di rete (NSG). I dati di Azure Batch devono essere contenuti in un account di archiviazione di Azure protetto.

Come creare un pool di Azure Batch all'interno di una rete virtuale:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Come proteggere gli account di archiviazione di Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorare e bloccare il trasferimento non autorizzato di informazioni riservate.

**Linee guida**: per gli account di archiviazione di Azure associati ai Pool di Azure batch che contengono informazioni riservate, contrassegnarli come sensibili usando i tag e proteggerli con le procedure consigliate di Azure.

Le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per le risorse di archiviazione o di calcolo di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

Informazioni sulla protezione dei dati dei clienti in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Come proteggere gli account di archiviazione di Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: crittografare tutte le informazioni riservate in transito. Microsoft Azure risorse negozieranno TLS 1,2 per impostazione predefinita. Assicurarsi che tutti i client che si connettono ai pool di Azure Batch o agli archivi dati (account di archiviazione di Azure) siano in grado di negoziare TLS 1,2 o versione successiva.

Assicurarsi che HTTPS sia necessario per l'accesso all'account di archiviazione contenente i dati Azure Batch.

Informazioni sulla crittografia dell'account di archiviazione di Azure in transito:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: per gli account di archiviazione di Azure associati ai Pool di Azure batch che contengono informazioni riservate, contrassegnarli come sensibili usando i tag e proteggerli con le procedure consigliate di Azure.

Le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per le risorse di archiviazione o di calcolo di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

Informazioni sulla protezione dei dati dei clienti in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Come proteggere gli account di archiviazione di Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo Azure Active Directory (AAD) per controllare l'accesso al piano di gestione delle risorse di Azure, inclusi account batch, pool di batch e account di archiviazione.

Informazioni su RBAC di Azure:

https://docs.microsoft.com/azure/role-based-access-control/overview

Come configurare il controllo degli accessi in base al ruolo di Azure: 

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni**: le funzionalità di identificazione dei dati, classificazione e prevenzione della perdita non sono ancora disponibili per le risorse di calcolo o di archiviazione di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

Informazioni sulla protezione dei dati dei clienti in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: crittografare le informazioni riservate inattive

**Linee guida**: per gli account di archiviazione associati all'account di Azure batch, è consigliabile consentire a Microsoft di gestire le chiavi di crittografia, tuttavia, se necessario, è possibile gestire le proprie chiavi.

Come gestire le chiavi di crittografia per gli account di archiviazione di Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi relativi al momento in cui vengono apportate modifiche alle risorse di Azure critiche correlate o associate ai Azure batch account/pool.

Configurare le impostazioni di diagnostica per gli account di archiviazione associati al pool Azure Batch per monitorare e registrare tutte le operazioni CRUD sui dati del pool.

Come creare avvisi per gli eventi del log attività di Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Come abilitare la registrazione o il controllo aggiuntivo per un account di archiviazione di Azure:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: eseguire strumenti di analisi automatica delle vulnerabilità

**Indicazioni**: per i nodi del Pool di Azure batch, l'utente è responsabile della gestione della soluzione di gestione delle vulnerabilità.

Facoltativamente, se si dispone di un Rapid7, Qualys o qualsiasi altra sottoscrizione della piattaforma di gestione delle vulnerabilità, è possibile installare manualmente gli agenti di valutazione della vulnerabilità nei nodi del pool di batch e gestire i nodi tramite il rispettivo portale.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: distribuire la soluzione di gestione delle patch per il sistema operativo automatizzata

**Linee guida**: Microsoft per gestire e aggiornare le immagini del nodo del pool Azure batch di base. Verificare che il sistema operativo dei nodi del pool di Azure Batch rimanga patch per la durata della durata del cluster che potrebbe richiedere l'abilitazione di aggiornamenti automatici, il monitoraggio dei nodi o l'esecuzione di riavvii periodici.


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: distribuire la soluzione di gestione delle patch software di terze parti automatizzata

**Linee guida**: assicurarsi che le applicazioni di terze parti dei nodi del pool Azure batch rimangano valide per la durata della durata del cluster, che potrebbe richiedere l'abilitazione di aggiornamenti automatici, il monitoraggio dei nodi o l'esecuzione di riavvii periodici.


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: confrontare le analisi delle vulnerabilità back-to-back

**Linee guida**: se si dispone di un Rapid7, Qualys o qualsiasi altra sottoscrizione della piattaforma di gestione delle vulnerabilità, è possibile usare il portale del fornitore per visualizzare e confrontare le analisi delle vulnerabilità back-to-back.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate.

**Indicazioni**: usare un programma comune di assegnazione dei punteggi di rischio, ad esempio un sistema comune di valutazione delle vulnerabilità, oppure le valutazioni di rischio predefinite fornite dallo strumento di analisi di terze parti.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: usare l'individuazione di asset in Azure

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse, ad esempio calcolo, archiviazione, rete e così via, all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure classiche possano essere individuate tramite Graph di risorse, è consigliabile creare e usare le risorse di Azure Resource Manager (ARM) in futuro.

Come creare query con Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Come visualizzare le sottoscrizioni di Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Informazioni su RBAC di Azure:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6,2: gestire i metadati dell'asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

Come creare e usare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia degli asset. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Come creare sottoscrizioni di Azure aggiuntive: 

https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare Gruppi di gestione:

https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e tag utente:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Mantenere un inventario delle risorse di Azure approvate e dei titoli software.

**Linee guida**: definire l'elenco delle risorse di Azure approvate e del software approvato per le risorse di calcolo


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorare le risorse di Azure non approvate

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare query con Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: per i nodi del pool Azure batch implementare una soluzione di terze parti per monitorare i nodi del cluster per le applicazioni software non approvate.


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: per i nodi del pool Azure batch implementare una soluzione di terze parti per monitorare i nodi del cluster per le applicazioni software non approvate.


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Linee guida**: per i nodi del pool Azure batch implementare una soluzione di terze parti per impedire l'esecuzione di software non autorizzato.


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="69-use-only-approved-azure-services"></a>6,9: usare solo i servizi di Azure approvati

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come negare un tipo di risorsa specifico con Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Customer

### <a name="610-implement-approved-application-list"></a>6.10: implementare l'elenco di applicazioni approvate

**Indicazioni**: per i nodi del pool Azure batch implementare una soluzione di terze parti per impedire l'esecuzione di tipi di file non autorizzati.


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Limitare la capacità degli utenti di interagire con Azure Resource Manager tramite script</div>

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Linee guida**: non applicabile,

Questa operazione non è applicabile a Azure Batch perché gli utenti (non amministratori) dei pool di Azure Batch non necessitano dell'accesso ai singoli nodi per l'esecuzione dei processi. L'amministratore del cluster dispone già dell'accesso radice a tutti i nodi.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: non applicabile. il benchmark è destinato alle applicazioni Web in esecuzione in app Azure servizio o istanze IaaS.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Controllo di sicurezza: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft.Batch" per creare criteri personalizzati per controllare o applicare la configurazione degli account e dei pool di Azure batch.

Come visualizzare gli alias dei criteri di Azure disponibili:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Come configurare e gestire i criteri di Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7,2: stabilire configurazioni sicure per il sistema operativo

**Linee guida**: definire configurazioni sicure per il sistema operativo dei nodi del pool di batch.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7,3: mantenere le configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure per le risorse di Azure correlate all'account e ai pool di batch, ad esempio reti virtuali, subnet, firewall di Azure, account di archiviazione di Azure e così via. Per creare criteri personalizzati, è possibile usare gli alias di criteri di Azure tra gli spazi dei nomi seguenti:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Informazioni sugli effetti di Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7,4: mantenere le configurazioni sicure per i sistemi operativi

**Linee guida**: Azure batch le immagini del sistema operativo del pool gestite e gestite da Microsoft. L'utente è responsabile di implementare la configurazione dello stato a livello di sistema operativo.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Condiviso

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni di criteri di Azure personalizzate per gli account Azure batch, i pool o le risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

Come archiviare il codice in Azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentazione Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: archiviare in modo sicuro immagini del sistema operativo personalizzate

**Linee guida**: se si usano immagini personalizzate per i pool di Azure batch, usare il controllo degli accessi in base al ruolo (RBAC) per garantire che solo gli utenti autorizzati possano accedere alle immagini.

Comprendere il RBAC in Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Come configurare il controllo degli accessi in base al ruolo di Azure: 

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida**: usare le definizioni di criteri di Azure predefinite per avvisare, controllare e applicare le configurazioni delle risorse correlate a Azure batch.  Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft.Batch" per creare criteri personalizzati per gli account e i pool di Azure Batch. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

Come configurare e gestire Criteri di Azure: 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Linee guida**: implementare una soluzione di terze parti per mantenere lo stato desiderato per i sistemi operativi dei nodi del pool Azure batch.


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft.Batch" per creare criteri personalizzati per controllare o applicare la configurazione dell'istanza di Azure batch. È anche possibile usare qualsiasi criterio incorporato creato in modo specifico per Azure Batch o le risorse usate da Azure Batch, ad esempio:

- Le subnet devono essere associate a un gruppo di sicurezza di rete

-Gli account di archiviazione devono usare un endpoint di servizio di rete virtuale

- È consigliabile abilitare i log di diagnostica negli account Batch

Come visualizzare gli alias di Criteri di Azure disponibili: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: implementare una soluzione di terze parti per monitorare lo stato dei sistemi operativi dei nodi del Pool di Azure batch.


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="711-securely-manage-azure-secrets"></a>7,11: gestire in modo sicuro i segreti di Azure

**Linee guida**: Azure Key Vault possibile usare con distribuzioni Azure batch per gestire le chiavi per l'archiviazione del pool negli account di archiviazione di Azure.

Come eseguire l'integrazione con identità gestite di Azure: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Come creare un Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Come fornire l'autenticazione di Key Vault con un'identità gestita: 

https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="712-securely-and-automatically-manage-identities"></a>7,12: gestire in modo sicuro e automatico le identità

**Linee guida**: non disponibile, identità del servizio gestita non supportato da Azure batch


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 

Come impostare Credential Scanner: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Controllo di sicurezza: difesa da malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: utilizzo del software antimalware gestito centralmente

**Indicazioni**: usare Windows Defender nei singoli nodi del pool di Azure batch nel caso dei sistemi operativi Windows o fornire una soluzione antimalware personalizzata se si usa Linux.


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft antimalware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio Azure batch, ma non viene eseguito sui contenuti del cliente.

Pre-analizza tutti i file caricati in risorse di Azure non di calcolo, ad esempio servizio app, Data Lake Storage, archiviazione BLOB e così via. Microsoft non è in grado di accedere ai dati dei clienti in tali istanze.

Informazioni su Microsoft antimalware per servizi cloud e macchine virtuali di Azure:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: assicurarsi che il software e le firme anti-malware siano aggiornati

**Indicazioni**: usare Windows Defender nei singoli nodi del pool di Azure batch nel caso dei sistemi operativi Windows e verificare che sia abilitato l'aggiornamento automatico. Fornire una soluzione antimalware personalizzata se si usa Linux.


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire l'esecuzione di backup automatici regolari

**Linee guida**: quando si usa un account di archiviazione di Azure per l'archivio dati del Pool di Azure batch, scegliere l'opzione di ridondanza appropriata (con ridondanza locale, ZRS, GRS, RA-GRS). 

Come configurare la ridondanza di archiviazione per gli account di archiviazione di Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: quando si usa un account di archiviazione di Azure per l'archivio dati del Pool di Azure batch, scegliere l'opzione di ridondanza appropriata (con ridondanza locale, ZRS, GRS, RA-GRS).  Se si usa Azure Key Vault per qualsiasi parte della distribuzione Azure Batch, assicurarsi che venga eseguito il backup delle chiavi.

Come configurare la ridondanza di archiviazione per gli account di archiviazione di Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Come eseguire il backup delle chiavi di Key Vault in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: se si gestiscono chiavi personalizzate per gli account di archiviazione di Azure o qualsiasi altra risorsa correlata all'implementazione di Azure batch, testare periodicamente il ripristino delle chiavi di cui è stato eseguito il backup.

Come eseguire il backup delle chiavi di Key Vault in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

Come ripristinare una chiave gestita dal cliente con PowerShell:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: se Azure Key Vault viene usato per mantenere le chiavi relative agli account di archiviazione del pool Azure batch, abilitare l'eliminazione temporanea in Azure Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.

Come abilitare l'eliminazione temporanea in Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-incident-response-guide"></a>10,1: creare una guida alla risposta agli eventi imprevisti

**Indicazioni**: assicurarsi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono i ruoli del personale, nonché le fasi di gestione/gestione degli eventi imprevisti.

Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10,2: creare una procedura per assegnazione di punteggi e priorità

**Linee guida**: il Centro sicurezza assegna un livello di gravità agli avvisi, in modo da consentire la priorità dell'ordine in cui si partecipa a ogni avviso, in modo che, quando una risorsa viene compromessa, è possibile accedervi immediatamente. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

Fare riferimento alla pubblicazione NIST: Guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10,4: specificare i dettagli del contatto per gli eventi imprevisti della sicurezza e configurare le notifiche &nbsp; di avviso per gli eventi

**Linee guida**: le informazioni di contatto per gli eventi imprevisti di sicurezza verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati da parte di utenti non autorizzati o non autorizzati.

Come impostare il contatto di sicurezza del Centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

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

*Per altre informazioni, vedere [Controllo di sicurezza: test di penetrazione ed esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e assicurarsi di correggere tutti i risultati critici della sicurezza entro 60 giorni.

**Linee guida**: seguire le regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

È possibile trovare altre informazioni sulla strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestiti da Microsoft, qui: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Benchmark di sicurezza di Azure](../security/benchmarks/overview.md)
- Vedere altre informazioni sulle [baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)
