---
title: Baseline della sicurezza di Azure per Azure Data Factory
description: Baseline della sicurezza di Azure per Azure Data Factory
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6cda99bfcb883dd42aa813d6edb922f2701ed1fe
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352202"
---
# <a name="azure-security-baseline-for-azure-data-factory"></a>Baseline della sicurezza di Azure per Azure Data Factory

La linea di base di sicurezza di Azure per Azure Data Factory contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](../security/benchmarks/overview.md), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere la [Panoramica delle baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Controllo di sicurezza: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: quando si crea un Azure-SSIS Integration Runtime (IR), è possibile aggiungerlo a una rete virtuale. Ciò consentirà Azure Data Factory di creare determinate risorse di rete, ad esempio un gruppo di sicurezza di rete (NSG) e un servizio di bilanciamento del carico. È anche possibile fornire il proprio indirizzo IP pubblico statico o Azure Data Factory crearne uno. Nel NSG creato automaticamente da Azure Data Factory la porta 3389 è aperta a tutto il traffico per impostazione predefinita. Bloccarlo per garantire che solo gli amministratori abbiano accesso.

Self-Hosted IRs può essere distribuito in un computer locale o in una macchina virtuale di Azure all'interno di una rete virtuale. Assicurarsi che la distribuzione della subnet della rete virtuale disponga di un NSG configurato per consentire solo l'accesso amministrativo. Per impostazione predefinita, Azure-SSIS IR ha disabilitato la porta 3389 in uscita in base alla regola di Windows Firewall in ogni nodo IR per la protezione. È possibile proteggere le risorse configurate per la rete virtuale associando un NSG di sicurezza di rete alla subnet e impostando regole rigide.

Se è disponibile un collegamento privato, usare endpoint privati per proteggere le risorse collegate alla pipeline di Azure Data Factory, ad esempio SQL Server di Azure. Con il collegamento privato, il traffico tra la rete virtuale e il servizio attraversa la rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica.

* [Come creare un Azure-SSIS IR](./create-azure-ssis-integration-runtime.md)

* [Come creare e configurare un runtime di integrazione self-hosted](./create-self-hosted-integration-runtime.md)

* [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

* [Come creare una NSG con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

* [Aggiungere un Azure-SSIS IR a una rete virtuale](./join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)

* [Informazioni sul collegamento privato di Azure](../private-link/private-link-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

**Linee guida**: usare il Centro sicurezza di Azure e correggere i consigli sulla protezione della rete per la rete virtuale e il gruppo di sicurezza di rete associati alla distribuzione del Integration Runtime.

Inoltre, abilitare i log di flusso del gruppo di sicurezza di rete (NSG) per la NSG che protegge la distribuzione di Integration Runtime e inviare i log a un account di archiviazione di Azure per il controllo del traffico.

È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

* [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

* [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

* [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee guida**: non applicabile; Questa raccomandazione è destinata al servizio app di Azure o alle risorse di calcolo che ospitano applicazioni Web.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare protezione DDoS standard nelle reti virtuali associate alla distribuzione Integration Runtime per la protezione da attacchi Denial of Service distribuiti. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

* [Come configurare la protezione DDoS](../ddos-protection/manage-ddos-protection.md)

* [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](../security-center/azure-defender.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: abilitare i log di flusso del gruppo di sicurezza di rete (NSG) per la NSG che protegge la distribuzione di Integration Runtime e inviare i log a un account di archiviazione di Azure per il controllo del traffico.

È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

* [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

* [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida**: se si vuole ispezionare il traffico in uscita da Azure-SSIS IR, è possibile instradare il traffico avviato da Azure-SSIS IR al dispositivo firewall locale tramite Azure ExpressRoute Force Tunneling o a un'appliance virtuale di rete da Azure Marketplace che supporta le funzionalità di ID/IP. Se il rilevamento e/o la prevenzione di intrusioni in base all'ispezione del payload non costituisce un requisito, è possibile usare Firewall di Azure con intelligence sulle minacce.

* [Aggiungere un Azure-SSIS Integration Runtime a una rete virtuale](./join-azure-ssis-integration-runtime-virtual-network.md)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Come configurare gli avvisi con il firewall di Azure](../firewall/threat-intel.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee guida**: non applicabile; Questa raccomandazione è destinata al servizio app di Azure o alle risorse di calcolo che ospitano applicazioni Web.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio rete virtuale per definire i controlli di accesso alla rete nel gruppo di sicurezza di rete (NSG) o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, DataFactoryManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico in ingresso per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

* [Comprendere e usare i tag di servizio](../virtual-network/service-tags-overview.md)

* [Informazioni sui tag di servizio specifici Azure Data Factory](./join-azure-ssis-integration-runtime-virtual-network.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le impostazioni di rete e le risorse di rete associate alle istanze di Azure Data Factory con criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. DataFactory" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle istanze del Azure Data Factory. È anche possibile usare le definizioni di criteri predefinite correlate alla rete o alle istanze di Azure Data Factory, ad esempio:
- Lo standard di protezione DDoS deve essere abilitato

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Esempi di criteri di Azure per la rete](../governance/policy/samples/built-in-policies.md#network) 

* [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida**: usare i tag per le risorse correlate alla sicurezza di rete e al flusso del traffico per le istanze di Azure Data Factory per fornire i metadati e l'organizzazione logica.

Usare una qualsiasi delle definizioni di Criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore", per garantire che tutte le risorse vengano create con tag e per notificare l'esistenza di risorse senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni su risorse basate sui rispettivi tag.

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze di Azure Data Factory. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

* [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: Registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Linee guida**: Microsoft gestisce l'origine dell'ora usata per le risorse di Azure, ad esempio Azure Data Factory per i timestamp nei log.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati da Azure Data Factory. In monitoraggio di Azure è possibile eseguire una query nell'area di lavoro Log Analytics configurata per ricevere i log attività Azure Data Factory. Usare gli account di archiviazione di Azure per archiviare log a lungo termine o di archiviazione o hub eventi per l'esportazione di dati in altri sistemi.

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure o in un evento di sicurezza e gestione degli eventi di terze parti (SIEM). È anche possibile integrare Azure Data Factory con git per sfruttare diversi vantaggi del controllo del codice sorgente, ad esempio la capacità di rilevare/controllare le modifiche e la possibilità di ripristinare le modifiche che introducono bug.

* [Come configurare le impostazioni di diagnostica](../azure-monitor/platform/diagnostic-settings.md)

* [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Controllo del codice sorgente in Azure Data Factory](./source-control.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni**: per la registrazione di controllo del piano di controllo, abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro log Analytics, a hub eventi di Azure o all'account di archiviazione di Azure per l'archiviazione Usando i dati del log attività di Azure, è possibile acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita al livello del piano di controllo per le risorse di Azure.

Usare le impostazioni di diagnostica per configurare i log di diagnostica per le risorse non di calcolo in Azure Data Factory, ad esempio le metriche e i dati di esecuzione della pipeline. Azure Data Factory archivia i dati di esecuzione della pipeline per 45 giorni. Per conservare i dati per un periodo di tempo più lungo, salvare i log di diagnostica in un account di archiviazione per il controllo o l'ispezione manuale e specificare il periodo di conservazione in giorni. È anche possibile trasmettere i log a hub eventi di Azure o inviare i log a un'area di lavoro di Log Analytics per l'analisi.

* [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/activity-log.md)

* [Informazioni sui log di diagnostica Azure Data Factory](./monitor-using-azure-monitor.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Linee guida**: se si esegue la Integration Runtime in una macchina virtuale (VM) di Azure, è possibile usare monitoraggio di Azure per raccogliere dati dalla macchina virtuale. L'installazione dell'estensione Log Analytics VM consente a monitoraggio di Azure di raccogliere i dati dalle macchine virtuali di Azure. Il Centro sicurezza di Azure può quindi fornire il monitoraggio del registro eventi di sicurezza per le macchine virtuali. Dato il volume dei dati generati dal registro eventi di sicurezza, non viene archiviato per impostazione predefinita.

Se l'organizzazione desidera conservare i dati del registro eventi di sicurezza, può essere archiviata in un livello di raccolta dati, a questo punto è possibile eseguire query in Log Analytics.

* [Come raccogliere dati da macchine virtuali di Azure in monitoraggio di Azure](../azure-monitor/learn/quick-collect-azurevm.md)

* [Abilitazione della raccolta dati nel centro sicurezza di Azure](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: abilitare le impostazioni di diagnostica per Azure Data Factory. Se si sceglie di archiviare i log in un'area di lavoro di Log Analytics, impostare il periodo di conservazione dell'area di lavoro di Log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine o l'archivio.

* [Come abilitare i log di diagnostica in Azure Data Factory](./monitor-using-azure-monitor.md)

* [Come impostare i parametri di conservazione dei log per le aree di lavoro Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida**: abilitare le impostazioni di diagnostica per Azure Data Factory e inviare i log a un'area di lavoro di log Analytics. Usare Log Analytics per analizzare e monitorare i log per un comportamento anomalo ed esaminare periodicamente i risultati. Assicurarsi di abilitare anche le impostazioni di diagnostica per gli archivi dati correlati alle distribuzioni Azure Data Factory. Per istruzioni su come abilitare le impostazioni di diagnostica, vedere la linea di base di sicurezza di ogni servizio.

Se si eseguono le Integration Runtime in una macchina virtuale (VM) di Azure, abilitare anche le impostazioni di diagnostica per la VM.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

* [Schema Log Analytics](./monitor-using-azure-monitor.md#schema-of-logs-and-events)

* [Come raccogliere dati da una macchina virtuale di Azure con monitoraggio di Azure](../azure-monitor/learn/quick-collect-azurevm.md)

* [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida**: è possibile generare avvisi sulle metriche supportate in data factory andando alla &amp; sezione relativa alle metriche degli avvisi in monitoraggio di Azure.

Configurare le impostazioni di diagnostica per Azure Data Factory e inviare i log a un'area di lavoro di Log Analytics. All'interno dell'area di lavoro di Log Analytics, configurare gli avvisi in modo che vengano visualizzati quando viene soddisfatto un set predefinito di condizioni. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

Assicurarsi inoltre di abilitare le impostazioni di diagnostica per i servizi correlati agli archivi dati. Per informazioni aggiuntive, è possibile fare riferimento alle linee di base di sicurezza di ogni servizio.

* [Avvisi in Azure Data Factory](./monitor-visually.md#alerts)

* [Pagina tutte le metriche supportate](../azure-monitor/platform/metrics-supported.md)

* [Come configurare gli avvisi nell'area di lavoro di Log Analytics](../azure-monitor/platform/alerts-log.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida**: se si esegue la Integration Runtime in una macchina virtuale di Azure, è possibile usare Microsoft antimalware per servizi cloud e macchine virtuali di Azure e configurare le macchine virtuali per registrare gli eventi in un account di archiviazione di Azure. Configurare un'area di lavoro di Log Analytics per inserire gli eventi dagli account di archiviazione e creare gli avvisi laddove appropriato. Seguire le raccomandazioni nel centro sicurezza di Azure: "calcolo di &amp; app".

* [Come configurare Microsoft antimalware per servizi cloud e macchine virtuali](../security/fundamentals/antimalware.md)

* [Come abilitare il monitoraggio a livello di Guest per le macchine virtuali](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida**: non applicabile; Azure Data Factory non elabora o produce log correlati a DNS.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: se si esegue la Integration Runtime in una macchina virtuale (VM) di Azure, è possibile abilitare la registrazione di controllo da riga di comando. Il Centro sicurezza di Azure fornisce il monitoraggio del registro eventi di sicurezza per le macchine virtuali di Azure. Il Centro sicurezza effettua il provisioning dei Microsoft Monitoring Agent in tutte le VM di Azure supportate e in quelle nuove che vengono create se il provisioning automatico è abilitato oppure è possibile installare l'agente manualmente. L'agente abilita l'evento di creazione di processi 4688 e il campo CommandLine all'interno dell'evento 4688. I nuovi processi creati nella macchina virtuale vengono registrati da EventLog e monitorati dai servizi di rilevamento del Centro sicurezza.

* [Raccolta dati nel Centro sicurezza di Azure](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida**: all'interno Azure Data Factory, assicurarsi di tenere traccia e riconciliare l'accesso utente a intervalli regolari. Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro del ruolo collaboratore o proprietario oppure un amministratore della sottoscrizione di Azure.

Inoltre, a livello di tenant, Azure Active Directory (AD) include ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi con accesso amministrativo al piano di controllo delle istanze di Azure Data Factory.

Sebbene Azure AD sia il metodo consigliato per amministrare l'accesso utente, tenere presente che se si esegue Integration Runtime in una macchina virtuale (VM) di Azure, è possibile che anche la VM disponga di account locali. Sia gli account locali che quelli di dominio devono essere rivisti e gestiti, in genere con un footprint minimo. Si consiglia inoltre di verificare che Privileged Identity Manager venga esaminato per la funzionalità Just-in-time per ridurre la disponibilità delle autorizzazioni amministrative.

* [Roles and permissions for Azure Data Factory](./concepts-roles-permissions.md) (Ruoli e autorizzazioni per Azure Data Factory)

* [Informazioni su Privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Informazioni per gli account locali](../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Indicazioni**: Azure Data Factory USA Azure Active Directory (ad) per fornire l'accesso al portale di Azure e alla console Azure Data Factory. Azure AD non applica il concetto di password predefinite, ma è responsabilità dell'utente modificare o non consentire eventuali password predefinite di applicazioni personalizzate o di terze parti.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Linee guida**: creare procedure operative standard per l'uso di account amministrativi dedicati per accedere al piano di controllo di Azure (portale di Azure) e alla console di Azure Data Factory. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure per monitorare il numero di account amministrativi all'interno Azure AD.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio:
- Alla sottoscrizione deve essere assegnato più di un proprietario
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

Se si esegue la Integration Runtime in una macchina virtuale di Azure, è anche possibile configurare gli account amministratore nelle macchine virtuali di Azure con Azure Privileged Identity Manager (PIM). Azure Privileged Identity Manager offre diverse opzioni, ad esempio l'elevazione JIT, il Multi-Factor Authentication e le opzioni di delega, in modo che le autorizzazioni siano disponibili solo per i frame temporali specifici e che l'approvazione venga richiesta da una seconda persona.

* [Informazioni sull'identità e sull'accesso del Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

* [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Informazioni su Privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Roles and permissions for Azure Data Factory](./concepts-roles-permissions.md) (Ruoli e autorizzazioni per Azure Data Factory)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Indicazioni**: usare una registrazione di app di Azure (entità servizio) per recuperare un token che l'applicazione o la funzione può usare per accedere e interagire con gli insiemi di credenziali dei servizi di ripristino.

* [Come chiamare le API REST di Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Come registrare l'applicazione client (entità servizio) con Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Informazioni sull'API dei servizi di ripristino di Azure](/rest/api/recoveryservices/)

* [Informazioni sull'API REST per Azure Data Factory](/rest/api/datafactory/)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Indicazioni**: abilitare Azure Active Directory Multi-Factor Authentication (MFA) e seguire le raccomandazioni sulla gestione delle identità e degli accessi nel Centro sicurezza di Azure.

* [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni**: usare workstation con accesso con privilegi insieme a Multi-Factor Authentication (MFA) configurato per l'accesso e la configurazione delle risorse di Azure.

* [Informazioni sulle workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida**: usare Azure Active Directory report di sicurezza per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

Se si esegue la Integration Runtime in una macchina virtuale (VM) di Azure, è possibile, inoltre, caricare la VM in Azure Sentinel. Microsoft Azure Sentinel è una soluzione di tipo SIEM (Security Information and Event Management) e SOAR (Security Orchestration, Automation and Response) scalabile e nativa del cloud. Azure Sentinel offre analisi della sicurezza intelligenti e intelligence sulle minacce per l'intera azienda, fornendo un'unica soluzione per il rilevamento degli avvisi, la visibilità delle minacce, la ricerca proattiva e la risposta alle minacce.

* [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

* [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

* [Come caricare dati in Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare le località denominate di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

* [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: un data factory può essere associato a un'identità gestita per le risorse di Azure che rappresenta il data factory specifico. È possibile usare questa identità gestita per l'autenticazione del database SQL di Azure. La factory specificata può accedere e copiare i dati dal database o nel database usando questa identità.

Se si esegue il Integration Runtime (IR) in una macchina virtuale di Azure, è possibile usare le identità gestite per eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Key Vault, senza credenziali nel codice. Il codice in esecuzione in una macchina virtuale può usare l'identità gestita per richiedere i token di accesso per i servizi che supportano l'autenticazione Azure AD.

* [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md)

* [Copiare e trasformare i dati nel database SQL di Azure usando Azure Data Factory](./connector-azure-sql-database.md)

* [Come configurare e gestire l'autenticazione di Azure Active Directory con il database SQL di Azure](../azure-sql/database/authentication-aad-configure.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Linee guida**: Azure Active Directory (ad) fornisce log che consentono di individuare gli account obsoleti. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

Se si esegue l'integrazione di runtime in una macchina virtuale di Azure, sarà necessario esaminare i gruppi di sicurezza e gli utenti locali per assicurarsi che non siano presenti account imprevisti che potrebbero compromettere il sistema.

* [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

* [Informazioni sulla creazione di report Azure AD](../active-directory/reports-monitoring/index.yml)

* [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: è possibile accedere alle origini del registro eventi di attività di accesso, controllo e rischio di Azure Active Directory (ad), che consentono di eseguire l'integrazione con qualsiasi strumento Siem/Monitoring. È possibile semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi del log desiderati all'interno Log Analytics.

Se si esegue la Integration Runtime in una macchina virtuale (VM) di Azure, caricare la VM in Azure Sentinel. Microsoft Azure Sentinel è una soluzione di tipo SIEM (Security Information and Event Management) e SOAR (Security Orchestration, Automation and Response) scalabile e nativa del cloud. Azure Sentinel offre analisi della sicurezza intelligenti e intelligence sulle minacce per l'intera azienda, fornendo un'unica soluzione per il rilevamento degli avvisi, la visibilità delle minacce, la ricerca proattiva e la risposta alle minacce.

* [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Autorizzare l'accesso alle risorse di hub eventi usando Azure Active Directory](../event-hubs/authorize-access-azure-active-directory.md)

* [Come caricare dati in Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Linee guida**: usare Azure Active Directory (ad) come sistema di autenticazione e autorizzazione centrale per le risorse di Azure Data Factory, ad esempio il database SQL di Azure o le macchine virtuali di Azure. Per la deviazione del comportamento di accesso dell'account nel piano di controllo (il portale di Azure), usare Azure AD Identity Protection e funzionalità di rilevamento dei rischi per configurare risposte automatiche per rilevare azioni sospette correlate alle identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.

* [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Configurare e gestire l'autenticazione di Azure Active Directory con SQL](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell)

* [Abilitare l'autenticazione di Azure Active Directory per Azure-SSIS Integration Runtime](./enable-aad-authentication-azure-ssis-ir.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: in scenari di supporto in cui Microsoft deve accedere ai dati dei clienti, Azure Customer Lockbox fornisce un'interfaccia che consente ai clienti di rivedere e approvare o rifiutare le richieste di accesso ai dati dei clienti. Si noti che anche se il servizio protetto di Azure non è disponibile per Azure Data Factory stesso, il servizio protetto di Azure supporta il database SQL di Azure e le macchine virtuali di Azure.

* [Informazioni Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

Usare la funzionalità di individuazione e classificazione dei dati del database SQL di Azure. Individuazione e classificazione dei dati offre funzionalità avanzate incorporate nel database SQL di Azure per l'individuazione, la classificazione, l'assegnazione di etichette per &amp; la protezione dei dati sensibili nei database.

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

* [Come usare l'individuazione e la classificazione dei dati per Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. I runtime di integrazione devono essere separati da rete virtuale (VNet)/della subnet e contrassegnati in modo appropriato.

 È anche possibile usare endpoint privati per eseguire l'isolamento rete. Un endpoint privato di Azure è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su Collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale.

* [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

* [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

* [Informazioni sul collegamento privato](../private-link/private-endpoint-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Indicazioni**: per le origini dati, ad esempio il database SQL di Azure, che archiviano o elaborano informazioni riservate per la distribuzione di Azure Data Factory, contrassegnare le risorse correlate come sensibili usando i tag.

Se è disponibile un collegamento privato, usare endpoint privati per proteggere le risorse collegate alla pipeline Azure Data Factory. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È anche possibile ridurre il rischio di exfiltration di dati configurando un set di regole in uscita rigoroso in un gruppo di sicurezza di rete (NSG) e associando tale NSG alla subnet.

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

* [Come creare una NSG con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

* [Informazioni sul collegamento privato di Azure](../private-link/private-link-overview.md)

* [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: se l'archivio dati cloud supporta HTTPS o TLS, tutti i trasferimenti di dati tra i servizi di spostamento dei dati in data factory e un archivio dati cloud sono tramite il canale sicuro HTTPS o TLS. La versione TLS usata è 1.2.

Tutte le connessioni al database SQL di Azure e ad Azure sinapsi Analytics (in precedenza SQL Data Warehouse) richiedono la crittografia (SSL/TLS) mentre i dati sono in transito da e verso il database. Quando si crea una pipeline usando JSON, aggiungere la proprietà encryption e impostarla su true nella stringa di connessione. Per Archiviazione di Azure è possibile usare HTTPS nella stringa di connessione.

* [Informazioni sulla crittografia in transito in Azure Data Factory](./data-movement-security-considerations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: se si usa Azure Data Factory per copiare e trasformare le istanze del database SQL di Azure, usare la funzionalità di individuazione e classificazione dei dati del database SQL di Azure. Individuazione e classificazione dei dati offre funzionalità avanzate incorporate nel database SQL di Azure per l'individuazione, la classificazione, l'assegnazione di etichette per &amp; la protezione dei dati sensibili nei database.

Le funzionalità di individuazione e classificazione dei dati non sono ancora disponibili per altri servizi di Azure.

* [Come usare l'individuazione e la classificazione dei dati per Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per controllare l'accesso al piano di controllo Azure Data Factory (il portale di Azure).

Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro del ruolo collaboratore o proprietario oppure un amministratore della sottoscrizione di Azure.

Per le origini dati Data Factory, ad esempio il database SQL di Azure, fare riferimento alla baseline di sicurezza per il servizio per altre informazioni sul controllo degli accessi in base al ruolo di Azure.

* [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)

* [Roles and permissions for Azure Data Factory](./concepts-roles-permissions.md) (Ruoli e autorizzazioni per Azure Data Factory)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni**: le funzionalità di identificazione dei dati, classificazione e prevenzione della perdita non sono ancora disponibili per le risorse di calcolo o di archiviazione di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

* [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: è consigliabile abilitare il meccanismo di crittografia dei dati per gli archivi dati correlati alle distribuzioni Azure Data Factory. Per ulteriori informazioni sulla crittografia dei dati inattivi, è possibile fare riferimento alle linee di base di sicurezza per il servizio.

Se si esegue la Integration Runtime in una macchina virtuale di Azure, i dischi virtuali in macchine virtuali Windows (VM) vengono crittografati a riposo usando la crittografia lato server o la crittografia dischi di Azure (ADE). Crittografia dischi di Azure usa la funzionalità BitLocker di Windows per crittografare i dischi gestiti con le chiavi gestite dal cliente all'interno della macchina virtuale guest. La crittografia lato server con chiavi gestite dal cliente migliora la funzionalità Crittografia dischi di Azure poiché consente di usare qualsiasi tipo e immagine di sistema operativo per le macchine virtuali crittografando i dati nel servizio di archiviazione.

È possibile archiviare le credenziali o i valori dei segreti in un Azure Key Vault e usarli durante l'esecuzione della pipeline per passare alle attività. È anche possibile archiviare le credenziali per gli archivi dati e i calcoli in un Azure Key Vault. Azure Data Factory recupera le credenziali durante l'esecuzione di un'attività che usa l'archivio dati o il calcolo.

* [Informazioni sulla crittografia inattiva in Azure Data Factory](./data-movement-security-considerations.md)

* [Crittografia lato server di Azure Managed Disks](../virtual-machines/disk-encryption.md)

* [Crittografia dischi di Azure per macchine virtuali Windows](../virtual-machines/windows/disk-encryption-overview.md)

* [Come usare i segreti Azure Key Vault nelle attività della pipeline](./how-to-use-azure-key-vault-secrets-pipeline-activities.md)

* [Come le credenziali in Azure Key Vault](./store-credentials-in-key-vault.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi quando si verificano modifiche per Azure Data Factory e le risorse correlate.

* [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/platform/alerts-activity-log.md)

* [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/platform/alerts-activity-log.md)

* [Registrazione di Analisi di archiviazione di Azure](../storage/common/storage-analytics-logging.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [Controllo di sicurezza: gestione della vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee guida**: se si usa il database SQL di Azure come archivio dati, abilitare la sicurezza dei dati avanzata per il database SQL di Azure e seguire le raccomandazioni del Centro sicurezza di Azure per l'esecuzione di valutazioni delle vulnerabilità sui server SQL di Azure.

Se si eseguono le Integration Runtime in una macchina virtuale (VM) di Azure, seguire le raccomandazioni del Centro sicurezza di Azure per l'esecuzione di valutazioni delle vulnerabilità nelle VM. Usare la sicurezza di Azure consigliata o una soluzione di terze parti per l'esecuzione di valutazioni delle vulnerabilità per le macchine virtuali.

* [Come eseguire valutazioni delle vulnerabilità nel database SQL di Azure](../azure-sql/database/sql-vulnerability-assessment.md)

* [Come abilitare la sicurezza avanzata dei dati](../azure-sql/database/azure-defender-for-sql.md)

* [Come implementare le raccomandazioni per la valutazione della vulnerabilità del Centro sicurezza di Azure](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Linee guida**: se si esegue la Integration Runtime in una macchina virtuale (VM) di Azure, usare la soluzione Azure Gestione aggiornamenti per gestire gli aggiornamenti e le patch per le macchine virtuali. Gestione aggiornamenti si basa sul repository di aggiornamenti configurato localmente per applicare patch ai sistemi Windows supportati. Strumenti come System Center Updates Publisher (Updates Publisher) consentono di pubblicare aggiornamenti personalizzati in Windows Server Update Services (WSUS). Questo scenario consente Gestione aggiornamenti di applicare patch ai computer che usano Configuration Manager come repository di aggiornamento con software di terze parti.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

* [Soluzione Gestione aggiornamenti in Azure](../automation/update-management/overview.md)

* [Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](../automation/update-management/manage-updates-for-vm.md)

* [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: distribuire la soluzione di gestione delle patch automatizzata per i titoli software di terze parti

**Linee guida**: se si esegue la Integration Runtime in una macchina virtuale (VM) di Azure, è possibile usare una soluzione di gestione delle patch di terze parti. È possibile usare la soluzione Gestione aggiornamenti di Azure per gestire gli aggiornamenti e le patch per le macchine virtuali. Gestione aggiornamenti si basa sul repository di aggiornamenti configurato localmente per applicare patch ai sistemi Windows supportati. Strumenti come System Center Updates Publisher (Updates Publisher) consentono di pubblicare aggiornamenti personalizzati in Windows Server Update Services (WSUS). Questo scenario consente Gestione aggiornamenti di applicare patch ai computer che usano Configuration Manager come repository di aggiornamento con software di terze parti.

* [Soluzione Gestione aggiornamenti in Azure](../automation/update-management/overview.md)

* [Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](../automation/update-management/manage-updates-for-vm.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee guida**: se si esegue la Integration Runtime in una macchina virtuale di Azure, esportare i risultati dell'analisi a intervalli coerenti e confrontare i risultati per verificare che le vulnerabilità siano state corrette. Quando si usa la raccomandazione sulla gestione delle vulnerabilità suggerita dal centro sicurezza di Azure, è possibile passare al portale della soluzione selezionata per visualizzare i dati cronologici dell'analisi.

* [Comprendere lo scanner di vulnerabilità integrato per le macchine virtuali](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Linee guida**: se si esegue la Integration Runtime in una macchina virtuale di Azure, è possibile usare lo scanner delle vulnerabilità native. Lo scanner di vulnerabilità incluso nel Centro sicurezza di Azure è basato su tecnologia Qualys. Lo scanner di Qualys è lo strumento ideale per l'identificazione in tempo reale delle vulnerabilità nelle macchine virtuali di Azure.

Quando il Centro sicurezza identifica le vulnerabilità, presenta i risultati e le informazioni correlate sotto forma di raccomandazioni. Le informazioni correlate includono la procedura di correzione, gli elenchi CVE correlati, i punteggi CVSS e altro ancora. È possibile visualizzare le vulnerabilità identificate per una o più sottoscrizioni o per una macchina virtuale specifica.

* [Scanner di vulnerabilità integrato per le macchine virtuali](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Indicazioni**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) disponibili nelle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

* [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle risorse di Azure. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Usare inoltre i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

* [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

* [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definire e gestire un inventario delle risorse di Azure approvate

**Indicazioni**: Definire il software e le risorse di Azure approvate per le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: se si esegue la Integration Runtime in una macchina virtuale (VM) di Azure, utilizzare l'inventario delle macchine virtuali di Azure per automatizzare la raccolta di informazioni su tutto il software nelle macchine virtuali. Automazione di Azure fornisce il controllo completo durante la distribuzione, l'attività e la rimozione delle autorizzazioni di carichi di lavoro e risorse.

Nota: il nome del software, la versione, il server di pubblicazione e l'ora di aggiornamento sono disponibili dal portale di Azure. Per ottenere l'accesso alla data di installazione e ad altre informazioni, il cliente deve abilitare la diagnostica a livello di Guest e portare i registri eventi di Windows in un'area di lavoro Log Analytics.

* [Introduzione ad Automazione di Azure](../automation/automation-intro.md)

* [Come abilitare l'inventario delle macchine virtuali di Azure](../automation/automation-tutorial-installed-software.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: se si esegue la Integration Runtime in una macchina virtuale di Azure, automazione di Azure fornisce il controllo completo durante la distribuzione, le operazioni e la rimozione delle autorizzazioni dei carichi di lavoro e delle risorse. È possibile utilizzare Rilevamento modifiche per identificare tutto il software installato nelle macchine virtuali. È possibile implementare un processo personalizzato o usare la configurazione dello stato di automazione di Azure per la rimozione di software non autorizzato.

* [Introduzione ad Automazione di Azure](../automation/automation-intro.md)

* [Rilevare le modifiche nell'ambiente in uso con la soluzione di rilevamento modifiche](../automation/change-tracking/overview.md)

* [Panoramica della configurazione dello stato di automazione di Azure](../automation/automation-dsc-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Linee guida**: se si esegue la Integration Runtime in una macchina virtuale (VM) di Azure, usare i controlli delle applicazioni adattivi del Centro sicurezza di Azure per assicurarsi che venga eseguito solo il software autorizzato e che l'esecuzione di tutto il software non autorizzato venga bloccata nelle macchine virtuali.

* [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/index.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

**Linee guida**: il controllo delle applicazioni adattivo è una soluzione end-to-end intelligente e automatizzata del Centro sicurezza di Azure che consente di controllare quali applicazioni possono essere eseguite nei computer Azure e non Azure (Windows e Linux). Implementare una soluzione di terze parti se non soddisfa i requisiti dell'organizzazione.

Si noti che questo vale solo se il Integration Runtime è in esecuzione in una macchina virtuale di Azure.

* [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

* [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Linee guida**: se si esegue l'integrazione di runtime in una macchina virtuale di Azure, a seconda del tipo di script, è possibile usare configurazioni specifiche del sistema operativo o risorse di terze parti per limitare la capacità degli utenti di eseguire script nelle risorse di calcolo di Azure. È anche possibile usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure per assicurarsi che venga eseguito solo il software autorizzato e che l'esecuzione di tutto il software non autorizzato venga bloccata in macchine virtuali di Azure.

* [Come controllare l'esecuzione di script di PowerShell negli ambienti Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: le applicazioni ad alto rischio distribuite nell'ambiente Azure possono essere isolate usando rete virtuale, subnet, sottoscrizioni, gruppi di gestione e così via e sufficientemente protette con un firewall di Azure, un Web Application Firewall (WAF) o un gruppo di sicurezza di rete (NSG).

* [Reti virtuali e macchine virtuali in Azure](../virtual-machines/network-overview.md)

* [Informazioni sul firewall di Azure](../firewall/overview.md)

* [Che cos'è Web Application Firewall di Azure?](../web-application-firewall/overview.md)

* [Gruppi di sicurezza di rete](../virtual-network/network-security-groups-overview.md)

* [Che cos'è la rete virtuale di Azure?](../virtual-network/virtual-networks-overview.md)

* [Organizzare le risorse con i gruppi di gestione di Azure ](../governance/management-groups/overview.md)

* [Guida alle decisioni relative alle sottoscrizioni](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Controllo di sicurezza: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per Azure Data Factory con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. DataFactory" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze del Azure Data Factory.

* [Come visualizzare gli alias di Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Linee guida**: se si esegue l'integrazione di runtime in una macchina virtuale di Azure, usare la raccomandazione del Centro sicurezza di Azure [correggere le vulnerabilità nelle configurazioni di sicurezza nelle macchine virtuali] per mantenere le configurazioni di sicurezza in tutte le risorse di calcolo.

* [Come monitorare le raccomandazioni del Centro sicurezza di Azure](../security-center/security-center-recommendations.md)

* [Come correggere le raccomandazioni del Centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

* [Informazioni sulla creazione di modelli di Azure Resource Manager](../virtual-machines/windows/ps-template.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Linee guida**: se si esegue la Integration Runtime in una macchina virtuale (VM) di Azure, si noti che sono disponibili diverse opzioni per la gestione di una configurazione sicura per le macchine virtuali per la distribuzione:
- Modelli di Azure Resource Manager: si tratta di file basati su JSON usati per distribuire una macchina virtuale dalla portale di Azure e deve essere mantenuto il modello personalizzato. Microsoft esegue la manutenzione sui modelli di base.
- Disco rigido virtuale (VHD) personalizzato: in alcuni casi potrebbe essere necessario disporre di file VHD personalizzati utilizzati, ad esempio quando si gestiscono ambienti complessi che non possono essere gestiti con altri mezzi. -Configurazione dello stato di automazione di Azure: una volta distribuito il sistema operativo di base, questo può essere usato per un controllo più granulare delle impostazioni e applicato tramite il Framework di automazione.

Per la maggior parte degli scenari, i modelli di VM di base Microsoft combinati con la configurazione dello stato desiderato di automazione di Azure possono contribuire alla riunione e alla gestione dei requisiti di sicurezza.

* [Informazioni su come scaricare il modello di macchina virtuale](../virtual-machines/windows/download-template.md)

* [Informazioni sulla creazione di modelli di Azure Resource Manager](../virtual-machines/windows/ps-template.md)

* [Come caricare un disco rigido virtuale di una macchina virtuale personalizzata in Azure](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni di criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire il codice in modo sicuro.

* [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentazione di Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Linee guida**: se si usano immagini personalizzate, usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per garantire che solo gli utenti autorizzati possano accedere alle immagini. Per le immagini contenitore, archiviarle in Azure Container Registry e sfruttare il controllo degli accessi in base al ruolo di Azure per garantire che solo gli utenti autorizzati possano accedere alle immagini.

Il ruolo Collaboratore Data Factory può essere usato per creare e gestire Data Factory, nonché per le risorse figlio al loro interno.

* [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Informazioni su RBAC di Azure per Container Registry](../container-registry/container-registry-roles.md)

* [Come configurare RBAC di Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

* [Roles and permissions for Azure Data Factory](./concepts-roles-permissions.md) (Ruoli e autorizzazioni per Azure Data Factory)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: usare le definizioni di criteri di Azure predefinite e gli alias di criteri di Azure nello spazio dei nomi "Microsoft. DataFactory" per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Linee guida**: questa raccomandazione può essere applicata se la Integration Runtime è in esecuzione in una macchina virtuale di Azure. La configurazione dello stato di automazione di Azure è un servizio di gestione della configurazione per i nodi DSC (Desired state Configuration) in qualsiasi Data Center nel cloud o in locale. Consente la scalabilità tra migliaia di computer in modo rapido e facile da una posizione centrale e sicura. È possibile caricare facilmente i computer, assegnare agli stessi configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni computer con lo stato desiderato specificato.

* [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](../automation/automation-dsc-onboarding.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida**: usare le definizioni di criteri di Azure predefinite e gli alias di criteri di Azure nello spazio dei nomi "Microsoft. DataFactory" per creare criteri personalizzati per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [Deploy if not exist] per applicare automaticamente le configurazioni per le risorse di Azure.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: questa raccomandazione può essere applicata se la Integration Runtime è in esecuzione in una macchina virtuale di Azure. La configurazione dello stato di automazione di Azure è un servizio di gestione della configurazione per i nodi DSC (Desired state Configuration) in qualsiasi Data Center nel cloud o in locale. Consente la scalabilità tra migliaia di computer in modo rapido e facile da una posizione centrale e sicura. È possibile caricare facilmente i computer, assegnare agli stessi configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni computer con lo stato desiderato specificato.

* [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](../automation/automation-dsc-onboarding.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud.

È anche possibile archiviare le credenziali o i valori dei segreti in un Azure Key Vault e usarli durante l'esecuzione della pipeline per passare alle attività. Verificare che l'eliminazione temporanea sia abilitata.

* [Come eseguire l'integrazione con le identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Come creare una Key Vault](../key-vault/secrets/quick-create-portal.md)

* [Come eseguire l'autenticazione a Key Vault](../key-vault/general/authentication.md)

* [Come assegnare un criterio di accesso Key Vault](../key-vault/general/assign-access-policy-portal.md)

* [Usare i segreti di Azure Key Vault nelle attività della pipeline](./how-to-use-azure-key-vault-secrets-pipeline-activities.md)

* [Eliminazione temporanea in Azure Key Vault](../key-vault/general/soft-delete-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: quando si crea una data factory, è possibile creare un'identità gestita insieme alla creazione della factory. L'identità gestita è un'applicazione gestita registrata per Azure Active Directory e rappresenta questo data factory specifico.

* [Identità gestita per Azure Data Factory](./data-factory-service-identity.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

* [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Controllo di sicurezza: difesa da malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: usare software antimalware gestito in modo centralizzato

**Linee guida**: se si esegue la Integration Runtime in una macchina virtuale di Azure, è possibile usare Microsoft antimalware per Azure macchine virtuali Windows per monitorare e difendere continuamente le risorse.

* [Come configurare Microsoft antimalware per servizi cloud e macchine virtuali](../security/fundamentals/antimalware.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Indicazioni**: il software antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio Servizio app di Azure, ma non viene eseguito sui contenuti del cliente.

Pre-analizza tutti i file caricati in risorse di Azure non di calcolo, ad esempio servizio app, Data Lake Storage, archiviazione BLOB e così via.

Usare il rilevamento delle minacce del Centro sicurezza di Azure per i servizi dati per rilevare il malware caricato negli account di archiviazione.

* [Informazioni su Microsoft antimalware per servizi cloud e macchine virtuali di Azure](../security/fundamentals/antimalware.md)

* [Informazioni sul rilevamento delle minacce del Centro sicurezza di Azure per i servizi dati](../security-center/azure-defender.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: verificare che le firme e il software antimalware siano aggiornati

**Indicazioni**: durante la distribuzione, Microsoft antimalware per Azure installerà automaticamente gli aggiornamenti più recenti per la firma, la piattaforma e il motore per impostazione predefinita. Seguire le raccomandazioni nel centro sicurezza di Azure: " &amp; app di calcolo" per assicurarsi che tutti gli endpoint siano aggiornati con le firme più recenti. Il sistema operativo Windows può essere ulteriormente protetto con sicurezza aggiuntiva per limitare il rischio di attacchi basati su virus o malware con il servizio Microsoft Defender Advanced Threat Protection che si integra con il Centro sicurezza di Azure.

* [Come distribuire Microsoft antimalware per servizi cloud e macchine virtuali di Azure](../security/fundamentals/antimalware.md)

* [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [Controllo di sicurezza: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Linee guida**: se si esegue la Integration Runtime in una macchina virtuale (VM) di Azure, abilitare backup di Azure e configurare la VM, nonché la frequenza e il periodo di memorizzazione desiderati per i backup automatici.

Per gli archivi dati, fare riferimento alla baseline di sicurezza del servizio per indicazioni su come eseguire backup regolari e automatici.

* [Panoramica del backup delle macchine virtuali di Azure](../backup/backup-azure-vms-introduction.md)

* [Eseguire il backup di una macchina virtuale di Azure dalle impostazioni della macchina virtuale](../backup/backup-azure-vms-first-look-arm.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: se si esegue la Integration Runtime in una macchina virtuale (VM) di Azure, abilitare backup di Azure e le macchine virtuali di Azure di destinazione, nonché la frequenza e i periodi di conservazione desiderati. Eseguire il backup delle chiavi gestite dal cliente all'interno Azure Key Vault.

Per gli archivi dati, fare riferimento alla baseline di sicurezza del servizio per indicazioni su come eseguire backup regolari e automatici.

* [Panoramica del backup delle macchine virtuali di Azure](../backup/backup-azure-vms-introduction.md)

* [Come eseguire il backup di chiavi di Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: se si esegue la Integration Runtime in una macchina virtuale di Azure, assicurarsi di eseguire periodicamente il ripristino dei dati del contenuto all'interno di backup di Azure. Se necessario, testare il ripristino del contenuto a una VLAN isolata. Testare periodicamente il ripristino delle chiavi gestite dal cliente di cui è stato eseguito il backup.

Per gli archivi dati, fare riferimento alla baseline di sicurezza del servizio per informazioni aggiuntive sulla convalida dei backup.

* [Come ripristinare i file dal backup della macchina virtuale di Azure](../backup/backup-azure-restore-files-from-vm.md)

* [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: se si esegue la Integration Runtime in una macchina virtuale (VM) di Azure e si torna alla VM con backup di Azure, la macchina virtuale viene crittografata a riposo con crittografia del servizio di archiviazione (SSE). Backup di Azure può anche eseguire il backup di macchine virtuali di Azure crittografate tramite crittografia dischi di Azure. Crittografia dischi di Azure si integra con le chiavi di crittografia di BitLocker (BEKs), che vengono protette in un insieme di credenziali delle chiavi come segreti. Crittografia dischi di Azure si integra inoltre con Azure Key Vault chiavi di crittografia della chiave (KEKs). Abilitare l'eliminazione temporanea in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.

* [Eliminazione temporanea per le macchine virtuali](../backup/backup-azure-security-feature-cloud.md)

* [Panoramica dell'eliminazione temporanea di Azure Key Vault](../key-vault/general/soft-delete-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

* [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

* [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione) e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

* [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

* [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi in Sentinel.

* [Come configurare l'esportazione continua](../security-center/continuous-export.md)

* [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

* [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Controllo di sicurezza: test di penetrazione ed esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: 

* [Segui Microsoft Rules of Engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [È possibile trovare altre informazioni sulla strategia Microsoft e sull'esecuzione dei test di penetrazione Red Teaming sull'infrastruttura cloud gestita da Microsoft, sui servizi e sulle applicazioni qui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)