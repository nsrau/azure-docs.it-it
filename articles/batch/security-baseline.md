---
title: Baseline della sicurezza di Azure per batch
description: La linea di base di sicurezza di batch fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: batch
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e7be42b2a6e9f2cdc1aa0258f218fea9fd963093
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532033"
---
# <a name="azure-security-baseline-for-batch"></a>Baseline della sicurezza di Azure per batch

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 1,0](../security/benchmarks/overview-v1.md) al batch. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili a batch. I **controlli** non applicabili al batch sono stati esclusi.

 
Per informazioni sul mapping completo del batch al benchmark di sicurezza di Azure, vedere il file di mapping di base per la [sicurezza di batch completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: distribuire pool di Azure batch all'interno di una rete virtuale. Per consentire ai nodi di calcolo del pool di comunicare in modo sicuro con altre macchine virtuali o con una rete locale, è possibile effettuare il provisioning del pool in una subnet di una rete virtuale di Azure. Inoltre, la distribuzione del pool in una rete virtuale consente di controllare il gruppo di sicurezza di rete (NSG) usato per proteggere le interfacce di rete (NIC) dei singoli nodi e la subnet. Configurare NSG per consentire il traffico solo da indirizzi IP attendibili/locations su Internet.

Quando applicabile, disabilitare l'accesso alla rete pubblica usando il collegamento privato di Azure per connettersi all'account Azure Batch tramite un endpoint privato. Il servizio di collegamento privato di Azure è protetto e accetta connessioni solo da endpoint privati autenticati e autorizzati. È inoltre possibile limitare la connettività e l'individuabilità disabilitando gli endpoint RDP/SSH esposti pubblicamente per i nodi di calcolo in un pool di batch.

- [Come creare un pool di Azure Batch all'interno di una rete virtuale](batch-virtual-network.md)

- [Come creare un account Azure Batch con accesso alla rete disabilitato](private-connectivity.md)

- [Come creare un endpoint privato](../private-link/create-private-endpoint-portal.md)

- [Come negare l'accesso al traffico RDP/SSH](pool-endpoint-configuration.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee guida**: usare il Centro sicurezza di Azure e correggere le raccomandazioni sulla protezione della rete correlate alla rete virtuale o al gruppo di sicurezza di rete (NSG) associato al pool di batch. Abilitare i log di flusso nel NSG usato per proteggere il pool di batch e inviare i log a un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log dei flussi di NSG a un'area di lavoro di Azure Log Analytics e usare Azure Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Alcuni vantaggi di Azure Analisi del traffico sono la possibilità di visualizzare le attività di rete e identificare le aree sensibili, identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni errate di rete.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare la protezione standard DDoS (Distributed Denial of Service) di Azure nella rete virtuale che protegge il pool di Azure batch per la protezione da attacchi DDoS. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

- [Come configurare la protezione DDoS](/azure/virtual-network/manage-ddos-protection)

- [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](/azure/security-center/security-center-alerts-service-layer)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: abilitare i log di flusso nel gruppo di sicurezza di rete (NSG) usato per proteggere il pool di Azure batch e inviare i log a un account di archiviazione di Azure per il controllo del traffico.

- [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida**: se necessario ai fini della conformità, selezionare un'appliance virtuale di rete da Azure Marketplace che supporti gli ID e le funzionalità dei sistemi di rilevamento delle intrusioni (IPS) con funzionalità di ispezione del payload.

Se il rilevamento delle intrusioni e/o la prevenzione basata sull'ispezione del payload non è un requisito, è possibile usare il firewall di Azure con Intelligence per le minacce. Il filtro basato su Intelligence per le minacce del firewall di Azure può segnalare e negare il traffico da e verso domini e indirizzi IP dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

Distribuire il firewall di Azure con un indirizzo IP pubblico nella stessa rete virtuale dei nodi del pool di Azure Batch. Configurare le regole di Network Address Translation (NAT) tra i percorsi attendibili in Internet e gli indirizzi IP privati dei singoli nodi del pool. Nel firewall di Azure, in Intelligence per le minacce, configurare "Alert and Deny" per bloccare l'avviso e bloccare il traffico verso/da indirizzi IP dannosi noti e domini. Gli indirizzi IP e i domini vengono originati dal feed di intelligence per le minacce di Microsoft e sono inclusi solo i record con la massima confidenza. 

- [Come creare un pool di Azure Batch all'interno di una rete virtuale](batch-virtual-network.md)

- [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nei firewall di Azure associati ai pool di Azure batch. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Comprendere e usare i tag di servizio](../virtual-network/service-tags-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete associate ai pool di Azure batch con i criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft.Batch" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete dei pool di Azure Batch.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida**: usare i tag per i gruppi di servizi di rete (gruppi) e altre risorse correlate alla sicurezza di rete e al flusso del traffico associati ai pool di Azure batch. Per le regole dei singoli gruppi di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni su risorse basate sui rispettivi tag.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate ai pool di Azure batch. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](/azure/azure-monitor/platform/activity-log-view) 

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: caricare l'account Azure batch in monitoraggio di Azure per aggregare i dati di sicurezza generati dai dispositivi cluster. Utilizzare query personalizzate per rilevare e rispondere alle minacce nell'ambiente.  Per Azure Batch il monitoraggio a livello di risorsa, usare le API di batch per monitorare o eseguire query sullo stato delle risorse, inclusi processi, attività, nodi e pool.

- [Come eseguire l'onboarding di un account Azure Batch in monitoraggio di Azure](batch-diagnostics.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: per il monitoraggio a livello di account Azure batch, monitorare ogni account batch usando le funzionalità di monitoraggio di Azure. Monitoraggio di Azure raccoglie metriche e facoltativamente i log di diagnostica per le risorse con ambito a livello di un account Batch, ad esempio, pool, processi e attività. Raccogliere e usare questi dati manualmente o a livello di codice per monitorare le attività nell'account Batch e per diagnosticare eventuali problemi.

Per Azure Batch il monitoraggio a livello di risorse, usare le API Azure Batch per monitorare o eseguire query sullo stato delle risorse, inclusi processi, attività, nodi e pool.

- [Come configurare il monitoraggio e la registrazione a livello di account Azure Batch](monitoring-overview.md)

- [Informazioni sul monitoraggio a livello di risorsa di batch](monitoring-overview.md#batch-resource-monitoring)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

#### <a name="azure-policy-built-in-definitions"></a>Definizioni predefinite di criteri di Azure

[!INCLUDE [microsoft.batch-2-3](../../includes/policy/standards/asb/rp-controls/microsoft.batch-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Linee guida**: monitoraggio di Azure raccoglie metriche e log di diagnostica per le risorse nell'account Azure batch. È possibile raccogliere e utilizzare questi dati in diversi modi per monitorare l'account Azure Batch e diagnosticare i problemi. È anche possibile configurare avvisi sulle metriche per ricevere notifiche quando una metrica raggiunge un valore specificato.

Se necessario, è possibile connettersi ai singoli nodi del pool tramite la shell protetta (SSH) o Remote Desktop Protocol (RDP) per accedere ai registri del sistema operativo locale.

- [Come raccogliere i log di diagnostica dall'account Azure Batch](batch-diagnostics.md#batch-diagnostics)

- [Come connettersi in remoto ai nodi del pool di Azure Batch](/azure/batch/batch-api-basics#error-handling)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: onboarding Azure batch account in monitoraggio di Azure. Assicurarsi che l'area di lavoro di Azure Log Analytics utilizzata abbia il periodo di conservazione del log impostato in base alle normative di conformità dell'organizzazione

- [Come configurare il monitoraggio e la registrazione di Azure Batch](monitoring-overview.md)

- [Come configurare il periodo di conservazione dell'area di lavoro di Azure Log Analytics](../azure-monitor/platform/manage-cost-storage.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: creare Azure batch avvisi della metrica che si attivano quando il valore di una metrica specificata supera una determinata soglia.

- [Come configurare gli avvisi della metrica Azure Batch](batch-diagnostics.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida**: creare Azure batch avvisi della metrica che si attivano quando il valore di una metrica specificata supera una determinata soglia.

- [Come configurare gli avvisi della metrica Azure Batch](batch-diagnostics.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida**: usare Windows Defender nei singoli nodi di batch nel caso dei sistemi operativi Windows o fornire una soluzione antimalware personalizzata se si usa Linux.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida**: implementare una soluzione di terze parti per la registrazione DNS.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: configurare manualmente la registrazione della console e la trascrizione di PowerShell in base ai singoli nodi.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida**: mantenere il record dell'account amministrativo locale creato durante il provisioning del pool di Azure batch e di qualsiasi altro account creato dall'utente. Inoltre, se si utilizza Azure Active Directory integrazione, Azure AD dispone di ruoli predefiniti che devono essere assegnati in modo esplicito e pertanto possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

Inoltre, è possibile usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Come monitorare l'identità e l'accesso con il Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: quando si effettua il provisioning di un pool di Azure batch, è possibile scegliere di creare account computer locali. Non sono disponibili password predefinite da modificare, ma è possibile specificare password diverse per l'accesso SSH (Secured Shell) e Remote Desktop Protocol (RDP). Dopo la configurazione del pool di Azure Batch, è possibile generare un utente casuale per i singoli nodi all'interno del portale di Azure o tramite Azure Resource Manager API.

- [Come aggiungere un utente a un nodo di calcolo specifico](/rest/api/batchservice/computenode/adduser)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: integrazione dell'autenticazione per applicazioni Azure Batch con Azure Active Directory. Creare criteri e procedure per l'utilizzo di account amministrativi dedicati.

Inoltre, è possibile usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

- [Come autenticare le applicazioni batch con Azure Active Directory](batch-aad-auth.md)

- [Come monitorare l'identità e l'accesso con il Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Indicazioni**: integrazione dell'autenticazione per applicazioni Azure Batch con Azure Active Directory. Abilitare l'autenticazione a più fattori Azure AD e seguire le indicazioni relative alla gestione delle identità e degli accessi nel centro sicurezza di Azure.

 

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usare workstation sicure gestite da Azure per le attività amministrative

**Indicazioni**: usare le workstation Paw (Privileged Access workstation) con l'autenticazione a più fattori configurata per l'accesso e la configurazione delle risorse Azure batch.

- [Informazioni sulle workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Come abilitare l'autenticazione a più fattori in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida**: se è stata integrata l'autenticazione per Azure batch applicazioni con Azure Active Directory, utilizzare Azure Active Directory report di sicurezza per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Come monitorare l'identità degli utenti e l'attività di accesso nel centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Linee guida**: se è stata integrata l'autenticazione per Azure batch applicazioni con Azure Active Directory, è possibile usare percorsi denominati di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o di paesi/aree geografiche.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Indicazioni**: usare Azure Active Directory come sistema di autenticazione e autorizzazione centrale e integrare l'autenticazione per le applicazioni Azure Batch con Azure ad. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

- [Come creare e configurare un'istanza di Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Come autenticare le applicazioni batch con Azure AD](batch-aad-auth.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure Active Directory mette a disposizione i log necessari per individuare gli account obsoleti. Inoltre, è possibile usare le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso degli utenti può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo.

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: creare le impostazioni di diagnostica per Azure Active Directory account utente, inviare i log di controllo e i log di accesso a un'area di lavoro di Azure log Analytics. Configurare gli avvisi desiderati nell'area di lavoro di Azure Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Linee guida**: usare Azure Active Directory rilevamento del rischio e la funzionalità di protezione delle identità per configurare risposte automatiche per rilevare azioni sospette correlate alle identità utente. Inoltre, è possibile inserire dati in Sentinel di Azure per un'analisi più approfondita.

- [Come visualizzare gli accessi a rischio per Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto  

**Linee guida**: non disponibile; Customer Lockbox non ancora supportata per Azure Batch.
 
- [Elenco dei servizi Customer Lockbox supportati](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. I pool di Azure Batch devono essere separati da rete virtuale/subnet, contrassegnati in modo appropriato e protetti con i gruppi di sicurezza di rete (NSG). I dati di Azure Batch devono essere contenuti in un account di archiviazione di Azure protetto.

- [Come creare un pool di Azure Batch all'interno di una rete virtuale](batch-virtual-network.md)

- [Come proteggere gli account di archiviazione di Azure](/azure/storage/common/storage-security-guide)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: per gli account di archiviazione di Azure associati ai Pool di Azure batch che contengono informazioni riservate, contrassegnarli come sensibili usando i tag e proteggerli con le procedure consigliate di Azure.

Le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per le risorse di archiviazione o di calcolo di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

- [Come proteggere gli account di archiviazione di Azure](/azure/storage/common/storage-security-guide)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: crittografare tutte le informazioni riservate in transito. Microsoft Azure risorse negozieranno TLS 1,2 per impostazione predefinita. Assicurarsi che tutti i client che si connettono ai pool di Azure Batch o agli archivi dati (account di archiviazione di Azure) siano in grado di negoziare TLS 1,2 o versione successiva.

Assicurarsi che HTTPS sia necessario per l'accesso all'account di archiviazione contenente i dati Azure Batch.

- [Informazioni sulla crittografia dell'account di archiviazione di Azure in transito](../storage/blobs/security-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: per gli account di archiviazione di Azure associati ai Pool di Azure batch che contengono informazioni riservate, contrassegnarli come sensibili usando i tag e proteggerli con le procedure consigliate di Azure.

Le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per le risorse di archiviazione o di calcolo di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

- [Come proteggere gli account di archiviazione di Azure](/azure/storage/common/storage-security-guide)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per controllare l'accesso al piano di gestione delle risorse di Azure, inclusi account batch, pool di batch e account di archiviazione.

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

- [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni**: le funzionalità di identificazione dei dati, classificazione e prevenzione della perdita non sono ancora disponibili per le risorse di calcolo o di archiviazione di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: per gli account di archiviazione associati all'account di Azure batch, è consigliabile consentire a Microsoft di gestire le chiavi di crittografia, tuttavia, se necessario, è possibile gestire le proprie chiavi.

Crittografia dischi di Azure può essere usata per proteggere e salvaguardare i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Tutti i dischi gestiti, gli snapshot, le immagini e i dati scritti nei dischi esistenti vengono crittografati automaticamente con chiavi gestite dalla piattaforma.

- [Come gestire le chiavi di crittografia per gli account di archiviazione di Azure](/azure/storage/common/storage-encryption-keys-portal)

- [Come configurare le chiavi di crittografia gestite dal cliente](/azure/storage/common/storage-encryption-keys-portal)

- [Come creare un pool con crittografia dischi abilitata](disk-encryption.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi relativi al momento in cui vengono apportate modifiche alle risorse di Azure critiche correlate o associate ai Azure batch account/pool.

Configurare le impostazioni di diagnostica per gli account di archiviazione associati al pool Azure Batch per monitorare e registrare tutte le operazioni CRUD sui dati del pool.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/platform/alerts-activity-log.md)

- [Come abilitare la registrazione o il controllo aggiuntivo per un account di archiviazione di Azure](../storage/common/storage-monitor-storage-account.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni**: per i nodi del Pool di Azure batch, l'utente è responsabile della gestione della soluzione di gestione delle vulnerabilità.

Facoltativamente, se si dispone di un Rapid7, Qualys o qualsiasi altra sottoscrizione della piattaforma di gestione delle vulnerabilità, è possibile installare manualmente gli agenti di valutazione della vulnerabilità nei nodi del pool di batch e gestire i nodi tramite il rispettivo portale.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Linee guida**: Microsoft per gestire e aggiornare le immagini del nodo del pool Azure batch di base. Verificare che il sistema operativo dei nodi del pool di Azure Batch rimanga patch per la durata della durata del cluster che potrebbe richiedere l'abilitazione di aggiornamenti automatici, il monitoraggio dei nodi o l'esecuzione di riavvii periodici.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: distribuire la soluzione di gestione delle patch automatizzata per i titoli software di terze parti

**Linee guida**: assicurarsi che le applicazioni di terze parti dei nodi del pool Azure batch rimangano valide per la durata della durata del cluster, che potrebbe richiedere l'abilitazione di aggiornamenti automatici, il monitoraggio dei nodi o l'esecuzione di riavvii periodici.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee guida**: se si dispone di un Rapid7, Qualys o qualsiasi altra sottoscrizione della piattaforma di gestione delle vulnerabilità, è possibile usare il portale del fornitore per visualizzare e confrontare le analisi delle vulnerabilità back-to-back.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: usare un programma comune di assegnazione dei punteggi di rischio, ad esempio un sistema comune di valutazione delle vulnerabilità, oppure le valutazioni di rischio predefinite fornite dallo strumento di analisi di terze parti.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse, ad esempio calcolo, archiviazione, rete e così via, all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure classiche possano essere individuate tramite Esplora risorse di Azure, è consigliabile creare e usare Azure Resource Manager risorse in futuro.

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia degli asset. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](/azure/billing/billing-create-subscription)

- [Come creare gruppi di gestione](/azure/governance/management-groups/create)

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Linee guida**: definire l'elenco delle risorse di Azure approvate e del software approvato per le risorse di calcolo

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare le risorse di Azure non approvate

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: per i nodi del pool Azure batch implementare una soluzione di terze parti per monitorare i nodi del cluster per le applicazioni software non approvate.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: per i nodi del pool Azure batch implementare una soluzione di terze parti per monitorare i nodi del cluster per le applicazioni software non approvate.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Linee guida**: per i nodi del pool Azure batch implementare una soluzione di terze parti per impedire l'esecuzione di software non autorizzato.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti: 
- Tipi di risorse non consentiti 
- Tipi di risorse consentiti 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

**Indicazioni**: per i nodi del pool Azure batch implementare una soluzione di terze parti per impedire l'esecuzione di tipi di file non autorizzati.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft.Batch" per creare criteri personalizzati per controllare o applicare la configurazione degli account e dei pool di Azure batch.

- [Come visualizzare gli alias dei criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Linee guida**: definire configurazioni sicure per il sistema operativo dei nodi del pool di batch.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure per le risorse di Azure correlate all'account e ai pool di batch, ad esempio reti virtuali, subnet, firewall di Azure, account di archiviazione di Azure e così via. Per creare criteri personalizzati, è possibile usare gli alias di criteri di Azure tra gli spazi dei nomi seguenti:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Linee guida**: Azure batch le immagini del sistema operativo del pool gestite e gestite da Microsoft. L'utente è responsabile di implementare la configurazione dello stato a livello di sistema operativo.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Condiviso

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni di criteri di Azure personalizzate per gli account Azure batch, i pool o le risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Documentazione di Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Linee guida**: se si usano immagini personalizzate per i pool di Azure batch, usare il controllo degli accessi in base al ruolo (RBAC) per garantire che solo gli utenti autorizzati possano accedere alle immagini.

- [Informazioni sul RBAC in Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Come configurare il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: usare le definizioni di criteri di Azure predefinite per avvisare, controllare e applicare le configurazioni delle risorse correlate a Azure batch.  Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft.Batch" per creare criteri personalizzati per gli account e i pool di Azure Batch. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Linee guida**: implementare una soluzione di terze parti per mantenere lo stato desiderato per i sistemi operativi dei nodi del pool Azure batch.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft.Batch" per creare criteri personalizzati per controllare o applicare la configurazione dell'istanza di Azure batch. È anche possibile usare qualsiasi criterio incorporato creato in modo specifico per Azure Batch o le risorse usate da Azure Batch, ad esempio:
- Le subnet devono essere associate a un gruppo di sicurezza di rete. gli account di archiviazione devono usare un endpoint di servizio di rete virtuale
- È consigliabile abilitare i log di diagnostica negli account Batch

- [Come visualizzare gli alias dei criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: implementare una soluzione di terze parti per monitorare lo stato dei sistemi operativi dei nodi del Pool di Azure batch.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: Azure Key Vault possibile usare con distribuzioni Azure batch per gestire le chiavi per l'archiviazione del pool negli account di archiviazione di Azure.

- [Come eseguire l'integrazione con le identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Come creare una Azure Key Vault](../key-vault/general/quick-create-portal.md)

- [Come eseguire l'autenticazione a Key Vault](../key-vault/general/authentication.md)
- [Come assegnare un criterio di accesso Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usare il software antimalware gestito centralmente

**Indicazioni**: usare Windows Defender nei singoli nodi del pool di Azure batch nel caso dei sistemi operativi Windows o fornire una soluzione antimalware personalizzata se si usa Linux.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft antimalware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio Azure batch, ma non viene eseguito sui contenuti del cliente.

Pre-analizza tutti i file caricati in risorse di Azure non di calcolo, ad esempio servizio app, Data Lake Storage, archiviazione BLOB e così via. Microsoft non è in grado di accedere ai dati dei clienti in tali istanze.

- [Informazioni su Microsoft antimalware per servizi cloud e macchine virtuali di Azure](../security/fundamentals/antimalware.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: assicurarsi che le firme e il software antimalware siano aggiornati

**Indicazioni**: usare Windows Defender nei singoli nodi del pool di Azure batch nel caso dei sistemi operativi Windows e verificare che sia abilitato l'aggiornamento automatico. Fornire una soluzione antimalware personalizzata se si usa Linux.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Linee guida**: quando si usa un account di archiviazione di Azure per l'archivio dati del Pool di Azure batch, scegliere l'opzione di ridondanza appropriata (con ridondanza locale, ZRS, GRS, RA-GRS). 

- [Come configurare la ridondanza di archiviazione per gli account di archiviazione di Azure](../storage/common/storage-redundancy.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: quando si usa un account di archiviazione di Azure per l'archivio dati del Pool di Azure batch, scegliere l'opzione di ridondanza appropriata (con ridondanza locale, ZRS, GRS, RA-GRS). Se si usa Azure Key Vault per qualsiasi parte della distribuzione Azure Batch, assicurarsi che venga eseguito il backup delle chiavi.

- [Come configurare la ridondanza di archiviazione per gli account di archiviazione di Azure](../storage/common/storage-redundancy.md)

- [Come eseguire il backup di chiavi di Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: se si gestiscono chiavi personalizzate per gli account di archiviazione di Azure o qualsiasi altra risorsa correlata all'implementazione di Azure batch, testare periodicamente il ripristino delle chiavi di cui è stato eseguito il backup.

- [Come eseguire il backup di chiavi di Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [Come ripristinare una chiave gestita dal cliente con PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: se Azure Key Vault viene usato per mantenere le chiavi relative agli account di archiviazione del pool Azure batch, abilitare Soft-Delete in Azure Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.

- [Come abilitare l'eliminazione temporanea in Azure Key Vault](/azure/key-vault/key-vault-soft-delete-powershell)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: assicurarsi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono i ruoli del personale, nonché le fasi di gestione/gestione degli eventi imprevisti.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Linee guida**: il Centro sicurezza assegna un livello di gravità agli avvisi, in modo da consentire la priorità dell'ordine in cui si partecipa a ogni avviso, in modo che, quando una risorsa viene compromessa, è possibile accedervi immediatamente. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Linee guida**: le informazioni di contatto per gli eventi imprevisti di sicurezza verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati da parte di utenti non autorizzati o non autorizzati.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: [attenersi alle regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

Sono disponibili altre informazioni sulla strategia e sull'esecuzione di test di penetrazione del red teaming e della penetrazione dei siti in tempo reale sull'infrastruttura, i servizi e le applicazioni cloud gestiti da Microsoft, di seguito: 

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
