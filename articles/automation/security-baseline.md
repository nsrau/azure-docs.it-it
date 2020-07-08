---
title: Baseline della sicurezza di Azure per automazione di Azure
description: Baseline della sicurezza di Azure per l'automazione
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 718be8599db5b30aa6c98bf4dfbd3663250b9d9c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255812"
---
# <a name="azure-security-baseline-for-automation"></a>Baseline della sicurezza di Azure per l'automazione

La linea di base di sicurezza di Azure per l'automazione contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere la [Panoramica delle baseline di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: l'account di automazione di Azure non supporta ancora il collegamento privato di Azure per limitare l'accesso al servizio tramite endpoint privati. Manuali operativi che eseguono l'autenticazione e l'esecuzione su risorse in Azure vengono eseguite in una sandbox di Azure e sfruttano le risorse back-end condivise, che Microsoft è responsabile dell'isolamento reciproco. la loro rete è senza restrizioni e può accedere alle risorse pubbliche. Automazione di Azure attualmente non dispone di integrazione di rete virtuale per la rete privata oltre al supporto per i ruoli di lavoro ibridi per Runbook. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Per ottenere un ulteriore isolamento per la manuali operativi, è possibile usare i ruoli di lavoro ibridi per Runbook in esecuzione in macchine virtuali di Azure. Quando si crea una macchina virtuale di Azure, è necessario creare una rete virtuale (VNet) o usare un VNet esistente e configurare le VM con una subnet. Assicurarsi che tutte le subnet distribuite dispongano di un gruppo di sicurezza di rete applicato con controlli di accesso alla rete specifici per le porte e le origini attendibili. Per i requisiti specifici del servizio, fare riferimento alle raccomandazioni sulla sicurezza per il servizio specifico.

In alternativa, se si ha un requisito specifico, è possibile usare anche il firewall di Azure per soddisfarlo.

* [Reti virtuali e macchine virtuali in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/network-overview)

* [Come creare una rete virtuale](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Come distribuire e configurare il firewall di Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Ambiente di esecuzione dei runbook](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-execution-environment)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

**Linee guida**: automazione di Azure attualmente non dispone dell'integrazione della rete virtuale per la rete privata oltre al supporto per i ruoli di lavoro ibridi per Runbook. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Se si usano i ruoli di lavoro ibridi per Runbook supportati da macchine virtuali di Azure, assicurarsi che la subnet contenente i ruoli di lavoro sia abilitata con un gruppo di sicurezza di rete (NSG) e configurare i log di flusso per l'invio di log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi di NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Sebbene le regole di NSG e le route definite dall'utente non si applichino all'endpoint privato, i log di flusso e le informazioni di monitoraggio di NSG per le connessioni in uscita sono ancora supportati e possono essere usati.

* [Come abilitare i log dei flussi NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Come abilitare e usare Analisi del traffico](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: automazione di Azure attualmente non dispone di integrazione di rete virtuale per la rete privata oltre al supporto per i ruoli di lavoro ibridi per Runbook. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Se si usano i ruoli di lavoro ibridi per Runbook supportati da macchine virtuali di Azure, abilitare la protezione standard DDoS (Distributed Denial of Service) nelle reti virtuali che ospitano i ruoli di lavoro ibridi per Runbook per proteggersi da attacchi DDoS. Con l'Intelligence per le minacce integrata nel centro sicurezza di Azure, è possibile negare le comunicazioni con indirizzi IP dannosi noti. Configurare il firewall di Azure in ogni segmento di rete virtuale, con l'Intelligence per le minacce abilitata, e configurare in modo da **avvisare e negare** il traffico di rete dannoso.

È possibile usare l'accesso just-in-time alla rete del Centro sicurezza di Azure per limitare l'esposizione delle macchine virtuali Windows agli indirizzi IP approvati per un periodo di tempo limitato. Usare anche le raccomandazioni per la protezione avanzata della rete adattiva del Centro sicurezza di Azure per le configurazioni NSG per limitare le porte e gli indirizzi IP di origine basati sul traffico effettivo e sull'Intelligence per le minacce

* [Come configurare la protezione DDoS](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Come distribuire il firewall di Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

* [Informazioni sul centro sicurezza di Azure Adaptive Network hardening](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

* [Informazioni sul controllo di accesso di rete just-in-Time del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: automazione di Azure attualmente non dispone di integrazione della rete virtuale per la rete privata oltre al supporto per i ruoli di lavoro ibridi per Runbook, questo controllo non è applicabile se si usa il servizio predefinito senza ruoli di lavoro ibridi.

Se si usano i ruoli di lavoro ibridi per Runbook supportati da macchine virtuali di Azure, è possibile registrare i log del flusso di NSG in un account di archiviazione per generare record di flusso per le macchine virtuali di Azure che fungono da Runbook Worker. Quando si analizza un'attività anomala, è possibile abilitare Network Watcher acquisizione pacchetti in modo che il traffico di rete possa essere esaminato per attività insolite e impreviste.

* [Come abilitare i log dei flussi NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Come abilitare Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida**: automazione di Azure attualmente non dispone di integrazione di rete virtuale per la rete privata oltre al supporto per i ruoli di lavoro ibridi per Runbook. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Se si usano i ruoli di lavoro ibridi per Runbook ospitati in macchine virtuali di Azure, è possibile combinare le acquisizioni di pacchetti fornite da Network Watcher e gli strumenti di ID open source per eseguire il rilevamento delle intrusioni di rete per un'ampia gamma di minacce per i computer di lavoro. Inoltre, è possibile distribuire il firewall di Azure ai segmenti della rete virtuale in base alle esigenze, con l'Intelligence per le minacce abilitata e configurata per "avviso e negazione" per il traffico di rete dannoso.

* [Eseguire il rilevamento di intrusioni di rete con Network Watcher e strumenti open source](https://docs.microsoft.com/azure/network-watcher/network-watcher-intrusion-detection-open-source-tools)

* [Come distribuire il firewall di Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Come configurare gli avvisi con il firewall di Azure](https://docs.microsoft.com/azure/firewall/threat-intel)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure configurato in Azure, che richiedono l'accesso alle risorse di automazione. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, GuestAndHybridManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

* [Comprendere e usare i tag di servizio](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete usate da automazione di Azure con criteri di Azure.

È anche possibile usare i progetti di Azure per semplificare le distribuzioni su larga scala di Azure tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controlli RBAC e criteri, in una singola definizione di progetto. È possibile applicare il progetto a nuove sottoscrizioni e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Esempi di criteri di Azure per la rete](https://docs.microsoft.com/azure/governance/policy/samples/#network)

* [Come creare un progetto di Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni**: usare i tag per gruppi e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le regole dei singoli gruppi di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Come creare una rete virtuale](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse e rilevare le modifiche alle risorse di rete. Creare avvisi in monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse critiche.

* [Come visualizzare e recuperare gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Come creare avvisi in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: Registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Linee guida**: Microsoft gestisce le origini temporali per le risorse di Azure. Tuttavia, è possibile gestire le impostazioni di sincronizzazione dell'ora per tutti i ruoli di lavoro ibridi per Runbook in esecuzione in macchine virtuali Windows.

* [Come configurare la sincronizzazione dell'ora per le risorse di calcolo di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni**: inviare i dati di log ai log di monitoraggio di Azure per aggregare i dati di sicurezza generati dalle risorse di automazione di Azure. In monitoraggio di Azure usare le query di log per cercare ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archiviazione. Automazione di Azure può inviare lo stato del processo Runbook, i flussi di processo, i dati di configurazione dello stato di automazione, la gestione degli aggiornamenti e Log Analytics il rilevamento delle modifiche o i log di inventario nell'area di lavoro Queste informazioni sono visibili dall'API dei log di monitoraggio portale di Azure, Azure PowerShell e Azure, che consente di eseguire semplici indagini.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

* [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Inoltrare i dati dei processi di Automazione di Azure ai log di Monitoraggio di Azure](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)

* [Integrare DSC con i log di monitoraggio di Azure](https://docs.microsoft.com/azure/automation/automation-dsc-diagnostics)

* [Aree supportate per l'area di lavoro Log Analytics collegata](https://docs.microsoft.com/azure/automation/how-to/region-mappings)

* [Log di query Gestione aggiornamenti](https://docs.microsoft.com/azure/automation/automation-update-management-query-logs)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare monitoraggio di Azure per l'accesso ai log di controllo e attività che includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili.

* [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Visualizzare e recuperare gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Linee guida**: quando si usa automazione di Azure con i ruoli di lavoro Runbook multi-tenant, questo controllo non è applicabile e la piattaforma gestisce le macchine virtuali sottostanti.

Quando si usa la funzionalità Hybrid Runbook Worker, il Centro sicurezza di Azure fornisce il monitoraggio del registro eventi di sicurezza per le macchine virtuali Windows. Se l'organizzazione desidera conservare i dati del registro eventi di sicurezza, può essere archiviata in un livello di raccolta dati, a questo punto è possibile eseguire query in Log Analytics. Sono disponibili livelli diversi: minimo, comune e tutti, descritti in dettaglio nel collegamento seguente.

* [Configurare il livello di raccolta dati nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: in monitoraggio di Azure impostare il periodo di conservazione dell'area di lavoro log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione/memorizzazione a lungo termine.

* [Modificare il periodo di conservazione dei dati in Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [Dettagli sulla conservazione dei dati per gli account di automazione](https://docs.microsoft.com/azure/automation/automation-managing-data#data-retention)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida**: analizzare e monitorare i log per un comportamento anomalo ed esaminare periodicamente i risultati. Usare le query del log di monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

* [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Informazioni sulle query di log in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Come eseguire query personalizzate in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida**: usare il Centro sicurezza di Azure con monitoraggio di Azure per il monitoraggio e l'invio di avvisi su attività anomale rilevate in registri di sicurezza ed eventi.

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure.

* [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Come gestire gli avvisi nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

* [Come inviare un avviso sui dati del log di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida**: quando si usa automazione di Azure con i ruoli di lavoro Runbook multi-tenant, questo controllo non è applicabile e la piattaforma gestisce le macchine virtuali sottostanti.

Tuttavia, quando si usa la funzionalità Hybrid Runbook Worker, è possibile usare Microsoft anti-malware per servizi cloud e macchine virtuali di Azure. Configurare le macchine virtuali per registrare gli eventi in un account di archiviazione di Azure. Configurare un'area di lavoro di Log Analytics per inserire gli eventi dagli account di archiviazione e creare gli avvisi laddove appropriato. Seguire le raccomandazioni nel centro sicurezza di Azure: "calcolo di &amp; app".

* [Come configurare Microsoft anti-malware per servizi cloud e macchine virtuali](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Come abilitare il monitoraggio a livello di Guest per le macchine virtuali](https://docs.microsoft.com/azure/cost-management/azure-vm-extended-metrics)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida**: implementare una soluzione di terze parti da Azure Marketplace per la soluzione di registrazione DNS in base alle esigenze dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: quando si usa automazione di Azure con i ruoli di lavoro Runbook multi-tenant, questo controllo non è applicabile e la piattaforma gestisce le macchine virtuali sottostanti.

Tuttavia, quando si usa la funzionalità Hybrid Runbook Worker, il Centro sicurezza di Azure fornisce il monitoraggio del registro eventi di sicurezza per le macchine virtuali di Azure. Il Centro sicurezza effettua il provisioning dell'agente di Log Analytics in tutte le VM di Azure supportate e in quelle nuove che vengono create se è abilitato il provisioning automatico. In alternativa, è possibile installare l'agente manualmente. L'agente Abilita l'evento di creazione del processo 4688 e il campo CommandLine all'interno dell'evento 4688. I nuovi processi creati nella macchina virtuale vengono registrati dal registro eventi e monitorati dai servizi di rilevamento del Centro sicurezza.

* [Raccolta dati nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida**: usare Azure Active Directory ruoli di amministratore predefiniti che possono essere assegnati in modo esplicito e possono essere sottoposti a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi. Ogni volta che si usano account RunAs dell'account di automazione per la manuali operativi, verificare che anche queste entità servizio siano registrate nell'inventario perché spesso hanno autorizzazioni elevate. Eliminare gli account RunAs non usati per ridurre al minimo la superficie di attacco esposta.

* [Come ottenere un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Eliminare un account RunAs o un account RunAs classico](https://docs.microsoft.com/azure/automation/manage-runas-account#delete-a-run-as-or-classic-run-as-account)

* [Gestire un account RunAs di Automazione di Azure](https://docs.microsoft.com/azure/automation/manage-runas-account)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: l'account di automazione di Azure non ha il concetto di password predefinite. I clienti sono responsabili per le applicazioni di terze parti e i servizi del Marketplace che possono usare password predefinite eseguite sul servizio o sui ruoli di lavoro ibridi per Runbook.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi. Ogni volta che si usano account RunAs dell'account di automazione per la manuali operativi, verificare che anche queste entità servizio siano registrate nell'inventario perché spesso hanno autorizzazioni elevate. Definire l'ambito di queste identità con le autorizzazioni con privilegi minimi necessari per consentire al manuali operativi di eseguire correttamente il processo automatizzato. Eliminare gli account RunAs non usati per ridurre al minimo la superficie di attacco esposta.

È anche possibile abilitare un accesso just-in-time/just-enough usando Azure AD Privileged Identity Management ruoli con privilegi per i servizi Microsoft e Azure Resource Manager.

* [Altre informazioni su Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

* [Eliminare un account RunAs o un account RunAs classico](https://docs.microsoft.com/azure/automation/manage-runas-account#delete-a-run-as-or-classic-run-as-account)

* [Gestire un account RunAs di Automazione di Azure](https://docs.microsoft.com/azure/automation/manage-runas-account)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida**: laddove possibile, usare SSO con Azure Active Directory anziché configurare singole credenziali autonome per servizio. Usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

* [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

* [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Usare Azure AD per l'autenticazione in Azure](https://docs.microsoft.com/azure/automation/automation-use-azure-ad)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più fattori di Azure ad e seguire le indicazioni relative alla gestione delle identità e degli accessi nel centro sicurezza di Azure.

* [Come abilitare MFA in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-dedicated-machines-for-all-administrative-tasks"></a>3,6: utilizzare computer dedicati per tutte le attività amministrative

**Linee guida**: usare paw con l'autenticazione a più fattori configurata per accedere e configurare le risorse dell'account di automazione di Azure negli ambienti di produzione.

* [Informazioni sulle workstation con accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Come abilitare MFA in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida**: usare Azure ad rilevamenti dei rischi per visualizzare gli avvisi e i report sul comportamento utente rischioso. Facoltativamente, il cliente può inviare avvisi di rilevamento dei rischi del Centro sicurezza di Azure a monitoraggio di Azure e configurare avvisi/notifiche personalizzati usando i gruppi di azioni.

* [Informazioni sui rilevamenti del rischio del Centro sicurezza di Azure (attività sospetta)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

* [Come integrare i log attività di Azure in Monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Come configurare gruppi di azioni per avvisi e notifiche personalizzati](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Linee guida**: è consigliabile usare i percorsi denominati di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

* [Come configurare le località denominate in Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure ad come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente. Se si usano i ruoli di lavoro ibridi per Runbook, è possibile sfruttare le identità gestite anziché gli account RunAs per abilitare autorizzazioni sicure più semplici.

* [Come creare e configurare un'istanza di Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)

* [Usare l'autenticazione runbook con le identità gestite](https://docs.microsoft.com/azure/automation/automation-hrw-run-runbooks#runbook-auth-managed-identities)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure AD mette a disposizione i log necessari per individuare gli account obsoleti. Usare anche le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruoli. L'accesso utente può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo. Ogni volta che si usano account RunAs dell'account di automazione per manuali operativi, assicurarsi che anche queste entità servizio vengano registrate nell'inventario perché spesso hanno autorizzazioni elevate. Eliminare gli account RunAs non usati per ridurre al minimo la superficie di attacco esposta.

* [Informazioni sulla creazione di report Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Come usare le verifiche di accesso alle identità di Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Eliminare un account RunAs o un account RunAs classico](https://docs.microsoft.com/azure/automation/manage-runas-account#delete-a-run-as-or-classic-run-as-account)

* [Gestire un account RunAs di Automazione di Azure](https://docs.microsoft.com/azure/automation/manage-runas-account)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Indicazioni**: È possibile accedere alle origini dei log degli eventi di rischio e controllo e delle attività di accesso di Azure AD, che consentono di integrarsi con qualsiasi strumento di monitoraggio/SIEM.

È possibile semplificare questo processo creando impostazioni di diagnostica per gli account utente di Azure Active Directory e inviando i log di controllo e di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro di Log Analytics.

* [Come integrare i log attività di Azure in Monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Linee guida**: usare Azure ad funzionalità di protezione delle identità e dei rischi per configurare risposte automatiche per le azioni sospette rilevate correlate alle identità utente per la risorsa di rete. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.

* [Come visualizzare gli accessi a rischio per Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Come configurare e abilitare i criteri di rischio di Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: per gli account di automazione di Azure, il supporto tecnico Microsoft può accedere ai metadati delle risorse della piattaforma durante un caso di supporto aperto senza utilizzare un altro strumento.

Tuttavia, quando si usano i ruoli di lavoro ibridi per Runbook supportati da macchine virtuali di Azure e le terze parti devono accedere ai dati dei clienti (ad esempio durante una richiesta di supporto), usare Customer Lockbox (anteprima) per le macchine virtuali di Azure per rivedere e approvare o rifiutare le richieste di accesso ai dati del cliente.

* [Informazioni Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per semplificare il monitoraggio delle risorse di automazione di Azure che archiviano o elaborano informazioni riservate.

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Isolare gli ambienti usando risorse separate per gli account di automazione. Le risorse come i ruoli di lavoro ibridi per Runbook devono essere separate da rete virtuale/subnet, contrassegnate in modo appropriato e protette in un gruppo di sicurezza di rete (NSG) o in un firewall di Azure. Per le macchine virtuali che archiviano o elaborano dati sensibili, implementare i criteri e le procedure per disabilitarli quando non sono in uso.

* [Come creare sottoscrizioni di Azure aggiuntive](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Come creare gruppi di gestione](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Come creare una rete virtuale](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Come distribuire il firewall di Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Come configurare avvisi o avvisi e negare con il firewall di Azure](https://docs.microsoft.com/azure/firewall/threat-intel)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: quando si usa la funzionalità Hybrid Runbook Worker, è consigliabile usare una soluzione di terze parti da Azure Marketplace sui perimetri di rete che monitora il trasferimento non autorizzato di informazioni riservate e blocca tali trasferimenti, mentre invia avvisi ai professionisti della sicurezza delle informazioni.

Per la piattaforma sottostante gestita da Microsoft, tutti i contenuti dei clienti vengono considerati sensibili e protetti dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

* [Informazioni sulla protezione dei dati dei clienti in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: crittografare tutte le informazioni riservate in transito. Assicurarsi che tutti i client che si connettono alle risorse di Azure in reti virtuali di Azure siano in grado di negoziare TLS 1,2 o versione successiva. Automazione di Azure supporta completamente e impone il protocollo TLS (Transport Layer) 1,2 e tutte le chiamate client o versioni successive per tutti gli endpoint HTPS esterni (tramite webhook, nodi DSC, ruolo di lavoro ibrido per Runbook).

Seguire le raccomandazioni del Centro sicurezza di Azure per la crittografia dei dati inattivi e la crittografia in transito, ove applicabile.

* [Informazioni sulla crittografia in transito con Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

* [Applicazione TLS 1,2 di automazione di Azure](https://azure.microsoft.com/updates/azure-automation-tls12-enforcement/)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: usare uno strumento di individuazione attiva di terze parti per identificare tutte le informazioni riservate archiviate, elaborate o trasmesse dai sistemi tecnologici dell'organizzazione, inclusi quelli presenti in loco o presso un provider di servizi remoti e aggiornare l'inventario delle informazioni riservate dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Linee guida**: usare Azure ad RBAC per controllare l'accesso alle risorse di automazione di Azure usando le definizioni di ruolo predefinite, assegnare l'accesso per gli utenti che accedono alle risorse di automazione in base a un modello di accesso con privilegi minimi o "just-enough". Quando si usano i ruoli di lavoro ibridi per Runbook, è possibile sfruttare le identità gestite per le macchine virtuali per evitare l'uso di entità servizio, quando si usano sia i ruoli di lavoro ibridi Runbook che multi-tenant, assicurarsi di applicare le autorizzazioni di controllo degli accessi in base al ruolo corrette per l'identità dei dipendenti Runbook.

* [Come configurare RBAC in Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

* [Autorizzazioni runbook per un ruolo di lavoro ibrido per runbook](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker#runbook-permissions-for-a-hybrid-runbook-worker)

* [Gestire le autorizzazioni e la sicurezza dei ruoli](https://docs.microsoft.com/azure/automation/automation-role-based-access-control)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: automazione di Azure attualmente non espone le macchine virtuali del ruolo di lavoro Runbook multi-tenant sottostante e questo viene gestito dalla piattaforma. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Se si usano i ruoli di lavoro ibridi per Runbook supportati da macchine virtuali di Azure, è necessario usare una soluzione di prevenzione della perdita dei dati basata su host di terze parti per applicare i controlli di accesso alle macchine virtuali del ruolo di lavoro ibrido per Runbook ospitato.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: usare chiavi gestite dal cliente con automazione di Azure. Automazione di Azure supporta l'uso di chiavi gestite dal cliente per crittografare tutti gli asset protetti usati, ad esempio credenziali, certificati, connessioni e variabili crittografate. Usare le variabili crittografate con la manuali operativi per tutte le richieste di ricerca variabili persistenti per evitare un'esposizione imprevista.

Quando si usano i ruoli di lavoro ibridi per Runbook, i dischi virtuali delle macchine virtuali vengono crittografati a riposo usando la crittografia lato server o la crittografia dischi di Azure (ADE). Crittografia dischi di Azure sfrutta la funzionalità BitLocker di Windows per crittografare i dischi gestiti con chiavi gestite dal cliente all'interno della macchina virtuale guest. La crittografia lato server con chiavi gestite dal cliente migliora la funzionalità Crittografia dischi di Azure poiché consente di usare qualsiasi tipo e immagine di sistema operativo per le macchine virtuali crittografando i dati nel servizio di archiviazione.

* [Crittografia lato server di Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption)

* [Crittografia dischi di Azure per macchine virtuali Windows](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-overview)

* [Uso delle chiavi gestite dal cliente per un account di Automazione](https://docs.microsoft.com/azure/automation/automation-secure-asset-encryption#use-of-customer-managed-keys-for-an-automation-account)

* [Variabili gestite in automazione di Azure](https://docs.microsoft.com/azure/automation/shared-resources/variables)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle risorse di Azure critiche, ad esempio i componenti di rete, gli account di automazione di Azure e manuali operativi.

* [Registrazione diagnostica per un gruppo di sicurezza di rete](https://docs.microsoft.com/azure/private-link/private-link-overview#logging-and-monitoring)

* [Come creare avvisi per gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [Controllo di sicurezza: gestione della vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni**: seguire le raccomandazioni del Centro sicurezza di Azure per l'esecuzione di valutazioni delle vulnerabilità nelle risorse di Azure

* [Raccomandazioni di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

* [Riferimento per le raccomandazioni del Centro sicurezza](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Linee guida**: automazione di Azure attualmente non espone le macchine virtuali del ruolo di lavoro Runbook multi-tenant sottostante e questo viene gestito dalla piattaforma. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Se si usano i ruoli di lavoro ibridi per Runbook supportati da macchine virtuali di Azure, usare Gestione aggiornamenti di Azure per gestire gli aggiornamenti e le patch per le macchine virtuali. Gestione aggiornamenti si basa sul repository di aggiornamenti configurato localmente per applicare patch ai sistemi Windows supportati. Strumenti come System Center Updates Publisher (Updates Publisher) consentono di pubblicare aggiornamenti personalizzati in Windows Server Update Services (WSUS). Questo scenario consente Gestione aggiornamenti di applicare patch ai computer che usano Configuration Manager come repository di aggiornamento con software di terze parti.

* [Gestione aggiornamenti in Azure](https://docs.microsoft.com/azure/automation/automation-update-management)

* [Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-update-management)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: distribuire la soluzione di gestione delle patch automatizzata per i titoli software di terze parti

**Linee guida**: automazione di Azure attualmente non espone le macchine virtuali del ruolo di lavoro Runbook multi-tenant sottostante e questo viene gestito dalla piattaforma. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Se si usano i ruoli di lavoro ibridi per Runbook supportati da macchine virtuali di Azure, è possibile usare Gestione aggiornamenti di Azure per gestire gli aggiornamenti e le patch per le macchine virtuali. Gestione aggiornamenti si basa sul repository di aggiornamenti configurato localmente per applicare patch ai sistemi Windows supportati. Strumenti come System Center Updates Publisher (Updates Publisher) consentono di pubblicare aggiornamenti personalizzati in Windows Server Update Services (WSUS). Questo scenario consente Gestione aggiornamenti di applicare patch ai computer che usano Configuration Manager come repository di aggiornamento con software di terze parti.

* [Soluzione Gestione aggiornamenti in Azure](https://docs.microsoft.com/azure/automation/automation-update-management)

* [Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-update-management)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee guida**: esportare i risultati dell'analisi a intervalli coerenti e confrontare i risultati per verificare che le vulnerabilità siano state corrette. Quando si usa la raccomandazione sulla gestione delle vulnerabilità suggerita dal centro sicurezza di Azure, il cliente può passare al portale della soluzione selezionata per visualizzare i dati cronologici dell'analisi.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Linee guida**: usare le classificazioni di rischio predefinite (Punteggio sicuro) fornite dal centro sicurezza di Azure per definire la priorità della correzione delle vulnerabilità individuate.

* [Informazioni sul punteggio sicuro del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse di automazione di Azure all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

* [Come creare query con Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Come visualizzare le sottoscrizioni di Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Informazioni sul controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Linee guida**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle risorse di automazione di Azure. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione. Eliminare gli account RunAs non usati per ridurre al minimo la superficie di attacco esposta.

* [Come creare sottoscrizioni di Azure aggiuntive](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Come creare gruppi di gestione](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Eliminare un account RunAs o un account RunAs classico](https://docs.microsoft.com/azure/automation/manage-runas-account#delete-a-run-as-or-classic-run-as-account)

* [Gestire un account RunAs di Automazione di Azure](https://docs.microsoft.com/azure/automation/manage-runas-account)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definire e gestire un inventario delle risorse di Azure approvate

**Linee guida**: è necessario creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare anche il grafico delle risorse di Azure per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Questo può essere utile in ambienti con sicurezza elevata, ad esempio quelli con account di archiviazione.

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Come creare query con Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Esempi di criteri di Azure predefiniti per automazione di Azure](https://docs.microsoft.com/azure/automation/policy-samples)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: l'offerta di automazione di Azure attualmente non espone le macchine virtuali del ruolo di lavoro Runbook multi-tenant sottostante e questa operazione viene gestita dalla piattaforma. Questo controllo non è applicabile se si usa il servizio predefinito senza ruoli di lavoro ibridi. Tuttavia, è possibile installare, rimuovere e gestire i moduli di PowerShell o Python a cui manuali operativi può accedere tramite il portale o i cmdlet. Il modulo non approvato o precedente deve essere rimosso o aggiornato per manuali operativi.

Se si usano i ruoli di lavoro ibridi per Runbook supportati da macchine virtuali di Azure, automazione di Azure fornisce il controllo completo durante la distribuzione, le operazioni e la rimozione delle autorizzazioni dei carichi di lavoro e delle risorse. Sfrutta l'inventario delle macchine virtuali di Azure per automatizzare la raccolta di informazioni su tutto il software nelle macchine virtuali. Il nome del software, la versione, il server di pubblicazione e l'ora di aggiornamento sono disponibili dal portale di Azure. Per ottenere l'accesso alla data di installazione e ad altre informazioni, il cliente deve abilitare la diagnostica a livello di Guest e portare i registri eventi di Windows in un'area di lavoro Log Analytics.

* [Introduzione ad Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro)

* [Come abilitare l'inventario delle macchine virtuali di Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

* [Gestire il modulo in automazione di Azure](https://docs.microsoft.com/azure/automation/shared-resources/modules)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni**: il cliente può impedire la creazione o l'utilizzo di risorse con criteri di Azure come richiesto dalle linee guida aziendali del cliente. È possibile implementare un processo personalizzato per la rimozione di risorse non autorizzate. All'interno dell'offerta di automazione di Azure è possibile installare, rimuovere e gestire i moduli di PowerShell o Python a cui manuali operativi può accedere tramite il portale o i cmdlet. Il modulo non approvato o precedente deve essere rimosso o aggiornato per manuali operativi.

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Gestire il modulo in automazione di Azure](https://docs.microsoft.com/azure/automation/shared-resources/modules)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Linee guida**: quando si usa la funzionalità Hybrid Runbook Worker, è possibile usare i controlli delle applicazioni adattivi del Centro sicurezza di Azure per assicurarsi che venga eseguito solo il software autorizzato e che l'esecuzione di tutto il software non autorizzato venga bloccata in macchine virtuali di Azure.

* [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Come negare un tipo di risorsa specifico con Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

**Linee guida**: quando si usa la funzionalità Hybrid Runbook Worker, è possibile usare la funzionalità di controllo delle applicazioni adattive del Centro sicurezza di Azure con le macchine virtuali di lavoro ibrido.

Il controllo delle applicazioni adattivi è una soluzione end-to-end intelligente e automatizzata del Centro sicurezza di Azure che consente di controllare quali applicazioni possono essere eseguite in computer Azure e non Azure (Windows e Linux). Implementare una soluzione di terze parti se non soddisfa i requisiti dell'organizzazione.

* [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee guida**: usare i criteri di accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "blocca l'accesso" per l'app "gestione Microsoft Azure" da percorsi o dispositivi non protetti o non approvati.

* [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsibilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni**: quando si usa la funzionalità Hybrid Runbook Worker e a seconda del tipo di script, è possibile usare configurazioni specifiche del sistema operativo o risorse di terze parti per limitare la capacità degli utenti di eseguire script nelle risorse di calcolo di Azure. È anche possibile usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure per assicurarsi che venga eseguito solo il software autorizzato e che l'esecuzione di tutto il software non autorizzato venga bloccata in macchine virtuali di Azure.

* [Come controllare l'esecuzione di script di PowerShell negli ambienti Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: le applicazioni ad alto rischio distribuite nell'ambiente Azure possono essere isolate usando contenitori di rete e di risorse separati usando costrutti come reti virtuali, subnet, sottoscrizioni, gruppi di gestione e possono essere sufficientemente protetti con un firewall di Azure, un Web Application Firewall (WAF) o un gruppo di sicurezza di rete (NSG).

* [Reti virtuali e macchine virtuali in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/network-overview)

* [Panoramica del firewall di Azure](https://docs.microsoft.com/azure/firewall/overview)

* [Panoramica di Web Application Firewall di Azure](https://docs.microsoft.com/azure/web-application-firewall/overview)

* [Gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview)

* [Panoramica di rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

* [Organizzare le risorse con i gruppi di gestione di Azure ](https://docs.microsoft.com/azure/governance/management-groups/overview)

* [Guida alle decisioni relative alle sottoscrizioni](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Controllo di sicurezza: configurazione sicura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione di automazione di Azure e le risorse correlate. È anche possibile usare le definizioni di criteri di Azure predefinite.

Inoltre, Azure Resource Manager è in grado di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per garantire che le configurazioni soddisfino o superino i requisiti di sicurezza per l'organizzazione.

È anche possibile usare le raccomandazioni del Centro sicurezza di Azure come linea di base di configurazione sicura per le risorse di Azure.

* [Come visualizzare gli alias di Criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Esercitazione: Creare e gestire i criteri per applicare la conformità](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Esempi di criteri di Azure predefiniti per automazione di Azure](https://docs.microsoft.com/azure/automation/policy-samples)

* [Esportazione di una singola e più risorse in un modello in portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

* [Raccomandazioni sulla sicurezza: una guida di riferimento](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Linee guida**: automazione di Azure attualmente non espone le macchine virtuali o il sistema operativo Runbook multi-tenant sottostante. Questa operazione viene gestita dalla piattaforma. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Quando si usa la funzionalità Hybrid Runbook Worker, usare la raccomandazione del Centro sicurezza di Azure [correggere le vulnerabilità nelle configurazioni di sicurezza nelle macchine virtuali] per mantenere le configurazioni di sicurezza nelle macchine virtuali.

* [Come monitorare le raccomandazioni del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

* [Come correggere le raccomandazioni del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare modelli di Azure Resource Manager e criteri di Azure per configurare in modo sicuro le risorse di Azure associate ad automazione di Azure. Azure Resource Manager modelli sono file basati su JSON usati per distribuire le risorse di Azure. tutti i modelli personalizzati dovranno essere archiviati e gestiti in modo sicuro in un repository di codice. Usare la funzionalità di integrazione del controllo del codice sorgente per tenere aggiornati gli manuali operativi nell'account di automazione con gli script nel repository del controllo del codice sorgente. Usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure tra le risorse di Azure.

* [Usare l'integrazione del controllo del codice sorgente](https://docs.microsoft.com/azure/automation/source-control-integration)

* [Informazioni sulla creazione di modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/quickstart-create-templates-use-the-portal)

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Informazioni sugli effetti di Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

* [Distribuire un account di automazione usando un modello di Azure Resource Manager](https://docs.microsoft.com/azure/automation/automation-create-account-template#deploy-the-template)

* [Esempi di criteri di Azure predefiniti per automazione di Azure](https://docs.microsoft.com/azure/automation/policy-samples)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Linee guida**: automazione di Azure attualmente non espone le macchine virtuali o il sistema operativo Runbook multi-tenant sottostante e questo viene gestito dalla piattaforma. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Quando si usa la funzionalità Hybrid Runbook Worker, sono disponibili diverse opzioni per la gestione di una configurazione sicura per le macchine virtuali di Azure per la distribuzione:

- Modelli di Azure Resource Manager: si tratta di file basati su JSON usati per distribuire una macchina virtuale dalla portale di Azure e deve essere mantenuto il modello personalizzato. Microsoft esegue la manutenzione sui modelli di base.
- Disco rigido virtuale (VHD) personalizzato: in alcuni casi potrebbe essere necessario disporre di file VHD personalizzati utilizzati, ad esempio quando si gestiscono ambienti complessi che non possono essere gestiti con altri mezzi.
- Configurazione dello stato di automazione di Azure: una volta distribuito il sistema operativo di base, questo può essere usato per un controllo più granulare delle impostazioni e applicato tramite il Framework di automazione.

Per la maggior parte degli scenari, i modelli di VM di base Microsoft combinati con la configurazione dello stato di automazione di Azure possono contribuire alla riunione e alla gestione dei requisiti di sicurezza.

* [Informazioni su come scaricare il modello di macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

* [Informazioni sulla creazione di modelli ARM](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

* [Come caricare un disco rigido virtuale di una macchina virtuale personalizzata in Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio criteri personalizzati di Azure, modelli di Azure Resource Manager e script di configurazione dello stato desiderato. Per accedere alle risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory se integrati con Azure DevOps oppure Active Directory se integrati con TFS. Usare la funzionalità di integrazione del controllo del codice sorgente per tenere aggiornati gli manuali operativi nell'account di automazione con gli script nel repository del controllo del codice sorgente.

* [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

* [Usare l'integrazione del controllo del codice sorgente](https://docs.microsoft.com/azure/automation/source-control-integration)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Linee guida**: automazione di Azure attualmente non espone le macchine virtuali o il sistema operativo Runbook multi-tenant sottostante e questo viene gestito dalla piattaforma. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Quando si usa la funzionalità Hybrid Runbook Worker, assicurarsi di limitare correttamente l'accesso all'immagine del sistema operativo personalizzata presente nell'account di archiviazione, in modo che solo gli utenti autorizzati possano accedere all'immagine.

* [Informazioni sul RBAC in Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

* [Come configurare RBAC in Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di Azure usando i criteri di Azure. Usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione di rete delle risorse di Azure. È anche possibile usare le definizioni di criteri predefinite correlate alle risorse specifiche.

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Come usare gli alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

* [Esempi di criteri di Azure predefiniti per automazione di Azure](https://docs.microsoft.com/azure/automation/policy-samples)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Linee guida**: automazione di Azure attualmente non espone le macchine virtuali o il sistema operativo Runbook multi-tenant sottostante e questo viene gestito dalla piattaforma. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Quando si usa la funzionalità Hybrid Runbook Worker, usare la configurazione dello stato di automazione di Azure nei Runbook Worker, un servizio di gestione della configurazione per i nodi DSC (Desired state Configuration) in qualsiasi data center locale o cloud. Consente la scalabilità tra migliaia di computer in modo rapido e facile da una posizione centrale e sicura. È possibile caricare facilmente i computer, assegnare agli stessi configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni computer con lo stato desiderato specificato.

* [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida**: usare i criteri di Azure per inviare avvisi e controllare le configurazioni delle risorse di Azure. i criteri possono essere usati per rilevare determinate risorse non configurate con un endpoint privato.

Quando si usa la funzionalità Hybrid Runbook Worker, sfruttare il Centro sicurezza di Azure per eseguire analisi di base per le macchine virtuali di Azure. Altri metodi per la configurazione automatica includono la configurazione dello stato di automazione di Azure.

* [Come correggere le raccomandazioni nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

* [Introduzione a Configurazione stato di Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-dsc-getting-started)

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Esempi di criteri di Azure predefiniti per automazione di Azure](https://docs.microsoft.com/azure/automation/policy-samples)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: l'offerta di automazione di Azure attualmente non espone le macchine virtuali o il sistema operativo Runbook multi-tenant sottostante e questo viene gestito dalla piattaforma. Questo controllo non è applicabile se si usa il servizio predefinito senza ruoli di lavoro ibridi.

Quando si usa la funzionalità di ruolo di lavoro ibrido per Runbook, usare la configurazione dello stato di automazione di Azure per i Runbook Worker, un servizio di gestione della configurazione per i nodi DSC (Desired state Configuration) in qualsiasi data center locale o cloud. Consente la scalabilità tra migliaia di computer in modo rapido e facile da una posizione centrale e sicura. È possibile caricare facilmente i computer, assegnare agli stessi configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni computer con lo stato desiderato specificato.

* [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud.

* [Uso delle chiavi gestite dal cliente per un account di Automazione](https://docs.microsoft.com/azure/automation/automation-secure-asset-encryption#use-of-customer-managed-keys-for-an-automation-account)

* [Usare l'autenticazione runbook con le identità gestite](https://docs.microsoft.com/azure/automation/automation-hrw-run-runbooks#runbook-auth-managed-identities)

* [Come creare una Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

* [Come fornire l'autenticazione Key Vault con un'identità gestita](https://docs.microsoft.com/azure/key-vault/managed-identity)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure ad. Le identità gestite consentono di eseguire l'autenticazione per qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire credenziali nel codice.

* [Come configurare le identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

* [Uso delle chiavi gestite dal cliente per un account di Automazione](https://docs.microsoft.com/azure/automation/automation-secure-asset-encryption#use-of-customer-managed-keys-for-an-automation-account)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

* [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.htm)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Controllo di sicurezza: difesa da malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: usare software antimalware gestito in modo centralizzato

**Linee guida**: l'offerta di automazione di Azure attualmente non espone le macchine virtuali o il sistema operativo Runbook multi-tenant sottostante e questo viene gestito dalla piattaforma. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Quando si usa la funzionalità Hybrid Runbook Worker, usare Microsoft anti-malware per le macchine virtuali Windows di Azure per monitorare e difendere continuamente le risorse di Runbook Worker.

* [Come configurare Microsoft anti-malware per servizi cloud e macchine virtuali](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: non applicabile; Automazione di Azure come servizio non archivia i file. Microsoft antimalware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio automazione di Azure, ma non viene eseguito sui contenuti.

* [Informazioni su Microsoft antimalware per servizi cloud e macchine virtuali di Azure](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: verificare che le firme e il software antimalware siano aggiornati

**Linee guida**: automazione di Azure attualmente non espone le macchine virtuali o il sistema operativo Runbook multi-tenant sottostante e questo viene gestito dalla piattaforma. Questo controllo non è applicabile se si usa il servizio predefinito senza i ruoli di lavoro ibridi per Runbook.

Quando si usa la funzionalità Hybrid Runbook Worker, usare Microsoft antimalware per Azure per installare automaticamente gli aggiornamenti più recenti della firma, della piattaforma e del motore per impostazione predefinita nel runbook Worker. Seguire le raccomandazioni nel centro sicurezza di Azure: " &amp; app di calcolo" per assicurarsi che tutti gli endpoint siano aggiornati con le firme più recenti. Il sistema operativo Windows può essere ulteriormente protetto con sicurezza aggiuntiva per limitare il rischio di attacchi basati su virus o malware con il servizio Microsoft Defender Advanced Threat Protection che si integra con il Centro sicurezza di Azure.

* [Come distribuire Microsoft antimalware per servizi cloud e macchine virtuali di Azure](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [Controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Linee guida**: usare Azure Resource Manager per distribuire gli account di automazione di Azure e le risorse correlate. Azure Resource Manager offre la possibilità di esportare i modelli che possono essere usati come backup per ripristinare gli account di automazione di Azure e le risorse correlate. Usare automazione di Azure per chiamare l'API di esportazione del modello di Azure Resource Manager a intervalli regolari.

Usare la funzionalità di integrazione del controllo del codice sorgente per tenere aggiornati gli manuali operativi nell'account di automazione con gli script nel repository del controllo del codice sorgente.

* [Panoramica di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)

* [Riferimento al modello di Azure Resource Manager per le risorse di automazione di Azure](https://docs.microsoft.com/azure/templates/microsoft.automation/allversions)

* [Creare un account di Automazione usando un modello di Azure Resource Manager](https://docs.microsoft.com/azure/automation/automation-create-account-template)

* [Esportazione di una singola e più risorse in un modello in portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

* [Gruppi di risorse-Esporta modello](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate)

* [Introduzione ad automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro)

* [Come eseguire il backup di chiavi di Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Uso delle chiavi gestite dal cliente per un account di Automazione](https://docs.microsoft.com/azure/automation/automation-secure-asset-encryption#use-of-customer-managed-keys-for-an-automation-account)

* [Usare l'integrazione del controllo del codice sorgente](https://docs.microsoft.com/azure/automation/source-control-integration)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: usare Azure Resource Manager per distribuire gli account di automazione di Azure e le risorse correlate. Azure Resource Manager offre la possibilità di esportare i modelli che possono essere usati come backup per ripristinare gli account di automazione di Azure e le risorse correlate. Usare automazione di Azure per chiamare l'API di esportazione del modello di Azure Resource Manager a intervalli regolari. Eseguire il backup delle chiavi gestite dal cliente all'interno Azure Key Vault. È possibile esportare i manuali operativi in file di script usando portale di Azure o PowerShell.

* [Panoramica di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)

* [Riferimento al modello di Azure Resource Manager per le risorse di automazione di Azure](https://docs.microsoft.com/azure/templates/microsoft.automation/allversions)

* [Creare un account di Automazione usando un modello di Azure Resource Manager](https://docs.microsoft.com/azure/automation/automation-create-account-template)

* [Esportazione di una singola e più risorse in un modello in portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

* [Gruppi di risorse-Esporta modello](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate)

* [Introduzione ad automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro)

* [Come eseguire il backup di chiavi di Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Uso delle chiavi gestite dal cliente per un account di Automazione](https://docs.microsoft.com/azure/automation/automation-secure-asset-encryption#use-of-customer-managed-keys-for-an-automation-account)

* [Backup dei dati di Azure per gli account di automazione](https://docs.microsoft.com/azure/automation/automation-managing-data#data-backup)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: garantire la possibilità di eseguire periodicamente la distribuzione di Azure Resource Manager modelli a intervalli regolari con una sottoscrizione isolata, se necessario. Testare il ripristino delle chiavi gestite dal cliente sottoposte a backup.

* [Distribuire le risorse con i modelli ARM e portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal)

* [Come ripristinare le chiavi di Key Vault in Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Uso delle chiavi gestite dal cliente per un account di Automazione](https://docs.microsoft.com/azure/automation/automation-secure-asset-encryption#use-of-customer-managed-keys-for-an-automation-account)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: usare Azure DevOps per archiviare e gestire in modo sicuro il codice come modelli Azure Resource Manager. Per proteggere le risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory se integrati con Azure DevOps oppure Active Directory se integrati con TFS.

Usare la funzionalità di integrazione del controllo del codice sorgente per tenere aggiornati gli manuali operativi nell'account di automazione con gli script nel repository del controllo del codice sorgente.

* [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

* [Usare l'integrazione del controllo del codice sorgente](https://docs.microsoft.com/azure/automation/source-control-integration)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

* [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione, tramite i tag e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure, in particolare quelle che elaborano i dati sensibili. È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

* [Avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [Usare tag per organizzare le risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: a intervalli regolari, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi, per contribuire a proteggere le risorse di Azure. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

* [Pubblicazione del NIST - Guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

* [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per contribuire a individuare i rischi per le risorse di Azure. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

* [Come configurare l'esportazione continua](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Come trasmettere gli avvisi in Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nei consigli per proteggere le risorse di Azure.

* [Come configurare l'automazione del flusso di lavoro e App per la logica](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Controllo di sicurezza: test di penetrazione ed esercizi Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: seguire le regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.

* [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
