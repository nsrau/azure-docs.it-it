---
title: Baseline della sicurezza di Azure per servizi cloud di Azure
description: La linea di base di sicurezza dei servizi cloud di Azure fornisce linee guida e risorse per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: cloud-services
ms.topic: conceptual
ms.date: 11/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7deee88210acf700916961be7c4ccaf9477accf8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353443"
---
# <a name="azure-security-baseline-for-azure-cloud-services"></a>Baseline della sicurezza di Azure per servizi cloud di Azure

Questa linea di base di sicurezza applica le indicazioni della [versione 1,0 del benchmark di sicurezza di Azure](../security/benchmarks/overview-v1.md) a servizi cloud di Microsoft Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili ai servizi cloud. I **controlli** non applicabili ai servizi cloud sono stati esclusi.

 
Per informazioni sul mapping completo dei servizi cloud al benchmark di sicurezza di Azure, vedere il file di mapping di base per la [sicurezza dei servizi cloud](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: creare una rete virtuale di Azure classica con subnet pubbliche e private separate per applicare l'isolamento in base a porte e intervalli IP attendibili. Queste reti virtuali e subnet devono essere risorse basate sulla rete virtuale classica (distribuzione classica) e non le risorse di Azure Resource Manager correnti.  

Consentire o negare il traffico usando un gruppo di sicurezza di rete che contiene le regole di controllo di accesso in base alla direzione del traffico, al protocollo, all'indirizzo di origine e alla porta e all'indirizzo e alla porta di destinazione Le regole di un gruppo di sicurezza di rete possono essere modificate in qualsiasi momento e le modifiche vengono applicate a tutte le istanze associate.

Non è possibile inserire Servizi cloud di Microsoft Azure (classico) in Azure Resource Manager reti virtuali. Tuttavia, le reti virtuali basate su Gestione risorse e le reti virtuali basate sulla distribuzione classica possono essere connesse tramite peering. 

- [Panoramica dei gruppi di sicurezza di rete](../virtual-network/network-security-groups-overview.md)

- [Peering reti virtuali](./cloud-services-connectivity-and-networking-faq.md?amp;preserve-view=#how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

**Indicazioni**: documentare la configurazione dei servizi cloud di Azure e monitorarla per le modifiche. Utilizzare il file di configurazione del servizio per specificare il numero di istanze del ruolo da distribuire per ogni ruolo nel servizio, i valori delle impostazioni di configurazione e le identificazioni personali per i certificati associati a un ruolo. 

Se il servizio fa parte di una rete virtuale, le informazioni di configurazione per la rete devono essere specificate nel file di configurazione del servizio, oltre che nel file di configurazione della rete virtuale. L'estensione predefinita per il file di configurazione del servizio è cscfg. Si noti che i criteri di Azure non sono supportati per le distribuzioni classiche per l'imposizione della configurazione.

Impostare i valori di configurazione di un servizio cloud nel file di configurazione del servizio (con estensione cscfg) e nella definizione in un file di definizione del servizio (con estensione csdef). Utilizzare il file di definizione del servizio per definire il modello del servizio per un'applicazione. Definire i ruoli disponibili per un servizio cloud e specificare anche gli endpoint di servizio. Registrare la configurazione per i servizi cloud di Azure con il file di configurazione del servizio. Qualsiasi riconfigurazione può essere eseguita tramite il file ServiceConfig. cscfg. 

Monitorare la definizione facoltativa del servizio Elemento NetworkTrafficRules che limita i ruoli che possono comunicare con gli endpoint interni specificati. Configurare il nodo NetworkTrafficRules, un elemento facoltativo nel file di definizione del servizio, per specificare la modalità di comunicazione tra i ruoli. Posizionare i limiti sui ruoli che possono accedere agli endpoint interni del ruolo specifico.  Si noti che la definizione del servizio non può essere modificata. 

Abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log a un account di archiviazione di Azure per il controllo. Inviare i log di flusso a un'area di lavoro di Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sui modelli di traffico nel tenant di Azure. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete, identificare le aree sensibili e le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete non configurate.

- [Azure Resource Manager rispetto alla distribuzione classica: informazioni sui modelli di distribuzione e sullo stato delle risorse](../azure-resource-manager/management/deployment-models.md)

- [File di configurazione dei servizi cloud](schema-cscfg-file.md)

- [Elenco dei servizi supportati da criteri di Azure](/cli/azure/azure-services-the-azure-cli-can-manage?amp;preserve-view=)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee guida**: Microsoft usa il protocollo Transport Layer Security (TLS) v 1.2 per proteggere i dati durante il viaggio tra i servizi cloud e i clienti di Azure. I data center Microsoft negoziano una connessione TLS con i sistemi client che si connettono ai servizi di Azure. Il protocollo TLS offre autenticazione avanzata, riservatezza dei messaggi e integrità (abilitando il rilevamento di manomissioni, intercettazioni e falsificazioni di messaggi), interoperabilità, flessibilità degli algoritmi e facilità di distribuzione e di utilizzo.

- [Nozioni fondamentali sulla crittografia](../security/fundamentals/encryption-overview.md)

- [Configurare i certificati TLS/SSL](cloud-services-configure-ssl-certificate-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni**: il cloud di Azure implementa una sicurezza di rete multistrato per proteggere i servizi della piattaforma da attacchi di tipo Denial of Service (DDoS) distribuiti. La protezione DDoS di Azure fa parte del processo di monitoraggio continuo del cloud di Azure, che viene costantemente migliorato tramite test di penetrazione. Questa protezione DDoS è progettata per supportare non solo gli attacchi dall'esterno, ma anche da altri tenant di Azure. 

Esistono diversi modi per bloccare o negare la comunicazione oltre alla protezione a livello di piattaforma nei servizi cloud di Azure. Si tratta di: 

-  Creare un'attività di avvio per bloccare selettivamente alcuni indirizzi IP specifici
-  Limitare l'accesso a un ruolo Web di Azure a un set di indirizzi IP specificati modificando il file di web.config IIS

Impedire il traffico in ingresso all'URL predefinito o al nome dei servizi cloud, ad esempio *. cloudapp.NET. Impostare l'intestazione host su un nome DNS personalizzato, in configurazione dell'associazione del sito nel file di definizione dei servizi cloud (*. csdef).

Configurare una regola di applicazione Deny per assegnazioni di amministratori di sottoscrizione classiche. Per impostazione predefinita, dopo aver definito un endpoint interno, la comunicazione può avvenire tra qualsiasi ruolo e l’endpoint interno di un ruolo senza restrizioni. Per limitare la comunicazione, è necessario aggiungere un elemento NetworkTrafficRules all'elemento ServiceDefinition nel file di definizione del servizio.

- [Come è possibile bloccare/disabilitare il traffico in ingresso per l'URL predefinito del servizio cloud](./cloud-services-connectivity-and-networking-faq.md?amp;preserve-view=#how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service)

- [Protezione DDOS di Azure](./cloud-services-connectivity-and-networking-faq.md?amp;preserve-view=#how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service)

- [Bloccare un indirizzo IP specifico](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Indicazioni**: usare Azure Network Watcher, monitoraggio delle prestazioni di rete, diagnostica e servizio di analisi, che consente il monitoraggio delle reti di Azure. L'estensione macchina virtuale agente Network Watcher è un requisito per l'acquisizione del traffico di rete su richiesta e altre funzionalità avanzate nelle macchine virtuali di Azure. Installare l'estensione della macchina virtuale Network Watcher Agent e attivare i log di flusso dei gruppi di sicurezza di rete.

Configurare la registrazione del flusso in un gruppo di sicurezza di rete. Vedere i dettagli su come distribuire l'estensione della macchina virtuale Network Watcher a una macchina virtuale esistente distribuita tramite il modello di distribuzione classica.

- [Configurare la registrazione del flusso in un gruppo di sicurezza di rete](../virtual-machines/extensions/network-watcher-linux.md)

- [Per altre informazioni sulla configurazione dei log di flusso, visitare](/cli/azure/azure-services-the-azure-cli-can-manage?amp;preserve-view=)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida**: i servizi cloud di Azure non hanno ID predefiniti o funzionalità IP. I clienti possono selezionare e distribuire una soluzione per gli indirizzi IP o gli ID basati sulla rete supplementare da Azure Marketplace in base ai requisiti aziendali. Quando si usano soluzioni di terze parti, assicurarsi di testare accuratamente gli ID selezionati o la soluzione IPS con i servizi cloud di Azure per assicurare il funzionamento e le funzionalità appropriate.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee guida**: i certificati di servizio, collegati ai servizi cloud di Azure, consentono la comunicazione sicura da e verso il servizio. Questi certificati sono definiti nella definizione dei servizi e vengono distribuiti automaticamente alla macchina virtuale in cui è in esecuzione un'istanza di un ruolo Web. Per un ruolo Web, ad esempio, è possibile usare un certificato di servizio in grado di autenticare un endpoint HTTPS esposto. 

Per aggiornare il certificato, è sufficiente caricare un nuovo certificato e modificare il valore di identificazione personale nel file di configurazione del servizio.

Usare il protocollo TLS 1,2, il metodo più comunemente usato per proteggere i dati per garantire la riservatezza e la protezione dell'integrità. 

In genere, per proteggere le applicazioni Web e proteggerle da attacchi come OWASP Top 10, è possibile distribuire un'applicazione Web di Azure abilitata per il firewall applicazione Azure gateway per la protezione delle applicazioni Web. 

- [Certificati di servizio](cloud-services-certs-create.md)

- [Configurazione di TLS per un'applicazione in Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Come distribuire il gateway applicazione](../application-gateway/quick-create-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: rafforzare la configurazione dei servizi cloud di Azure e monitorarne le modifiche. Il file di configurazione del servizio specifica il numero di istanze del ruolo da distribuire per ogni ruolo nel servizio, i valori delle impostazioni di configurazione e le identificazioni personali per i certificati associati a un ruolo. 

Se il servizio fa parte di una rete virtuale, le informazioni di configurazione per la rete devono essere specificate nel file di configurazione del servizio, così come nel file di configurazione della rete virtuale. L'estensione predefinita per il file di configurazione del servizio è cscfg.

Si noti che i criteri di Azure non sono supportati con servizi cloud di Azure per l'applicazione della configurazione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida**: i gruppi di sicurezza di rete di Azure possono essere usati per filtrare il traffico di rete da e verso le risorse di Azure in una rete virtuale di Azure. Un gruppo di sicurezza di rete contiene regole di sicurezza che consentono o negano il traffico di rete in ingresso verso, o, il traffico di rete in uscita da, diversi tipi di risorse di Azure. Per ogni regola è possibile specificare l'origine e la destinazione, la porta e il protocollo.

Usare il campo "Description" per le singole regole del gruppo di sicurezza di rete all'interno dei servizi cloud di Azure per documentare le regole che consentono il traffico verso o da una rete.

- [Come filtrare il traffico di rete con le regole del gruppo di sicurezza di rete](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni**: usare il monitoraggio degli endpoint predefinito di gestione traffico di Azure e le funzionalità di failover automatico degli endpoint. Consentono di distribuire applicazioni a disponibilità elevata, resilienti agli errori dell'endpoint e dell'area di Azure. Per configurare il monitoraggio degli endpoint, è necessario specificare determinate impostazioni nel profilo di gestione traffico.

Raccogliere informazioni dettagliate dal log attività, un log di piattaforma in Azure, negli eventi a livello di sottoscrizione. Include informazioni quali la modifica di una risorsa o l'avvio di una macchina virtuale. Visualizzare il log attività nel portale di Azure o recuperare le voci con PowerShell e l'interfaccia della riga di comando. 

Creare un'impostazione di diagnostica per inviare il log attività a monitoraggio di Azure, Hub eventi di Azure da inoltrare all'esterno di Azure o ad archiviazione di Azure per l'archiviazione. Configurare monitoraggio di Azure per gli avvisi di notifica quando vengono modificate le risorse critiche nei servizi cloud di Azure. 

- [Log attività di Azure](../azure-monitor/platform/activity-log.md)

- [Creare, visualizzare e gestire gli avvisi del log attività usando Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md)

- [Monitoraggio di gestione traffico](../traffic-manager/traffic-manager-monitoring.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Linee guida**: Microsoft gestisce le origini temporali per le risorse di Azure per i servizi cloud di Azure. È possibile che i clienti debbano creare una regola di rete per consentire l'accesso a un server di tempo usato nel proprio ambiente, sulla porta 123 con il protocollo UDP.

- [Accesso al server NTP](../firewall/protect-windows-virtual-desktop.md#additional-considerations)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni**: usare il servizio cloud per lo streaming di dati a livello di codice con hub eventi di Azure. Integra e invia tutti questi dati a Sentinel di Azure per monitorare e controllare i log o usare SIEM di terze parti. Per la gestione dei log di sicurezza centrale, configurare l'esportazione continua dei dati del Centro sicurezza di Azure scelti nell'hub eventi di Azure e configurare il connettore appropriato per SIEM. Ecco alcune opzioni per Azure Sentinel, inclusi gli strumenti di terze parti:

- Azure Sentinel: usare il connettore dati avvisi del Centro sicurezza nativo
- Splunk-usare il componente aggiuntivo monitoraggio di Azure per Splunk
- IBM QRadar: usare un'origine di log configurata manualmente
- ArcSight: USA SmartConnector

Vedere la documentazione di Sentinel di Azure per altre informazioni sui connettori disponibili con Sentinel di Azure. 

- [Connettere le origini dati](../sentinel/connect-data-sources.md)

- [Eseguire l'integrazione con un SIEM](../security-center/continuous-export.md)

- [Archiviare i dati di diagnostica](diagnostics-extension-to-storage.md)

- [Configurazione dell'integrazione SIEM tramite hub eventi di Azure](../security-center/continuous-export.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni**: configurare Visual Studio per la configurazione di diagnostica di Azure per la risoluzione dei problemi relativi ai servizi cloud di Azure che acquisisce i dati di sistema e di registrazione nelle macchine virtuali, incluse le istanze di macchine virtuali che eseguono i servizi cloud di Azure. I dati di diagnostica vengono trasferiti in un account di archiviazione di propria scelta. Abilitare la diagnostica nei progetti di servizi cloud di Azure prima della distribuzione.

 
Visualizzare la cronologia delle modifiche per alcuni eventi nel log attività in monitoraggio di Azure. Controllare le modifiche apportate durante un periodo di tempo dell'evento. Scegliere un evento dal log attività per un controllo più approfondito con la scheda cronologia modifiche (anteprima). Inviare i dati di diagnostica a Application Insights quando si pubblicano i servizi cloud di Azure da Visual Studio. Creare la risorsa di Azure Application Insights in quel momento o inviare i dati a una risorsa di Azure esistente. 

I servizi cloud di Azure possono essere monitorati da Application Insights per disponibilità, prestazioni, errori e utilizzo. È possibile aggiungere grafici personalizzati ad Application Insights che mostrino i dati più importanti. I dati dell'istanza del ruolo possono essere raccolti usando il Application Insights SDK nel progetto di servizi cloud di Azure. 

- [Abilitare la diagnostica in Visual Studio prima della distribuzione](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines?amp;preserve-view=#to-turn-on-diagnostics-in-visual-studio-before-deployment)

- [Visualizzare la cronologia modifiche](../azure-monitor/platform/activity-log.md#view-change-history)

- [Application Insights per il servizio cloud di Azure (versione classica)](../azure-monitor/app/cloudservices.md)

- [Configurare la diagnostica per il servizio cloud di Azure (versione classica) e le macchine virtuali](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines?amp;preserve-view=true&toc=%2fazure%2fcloud-services%2ftoc.json)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: è possibile usare il monitoraggio avanzato con servizi cloud di Azure, che consente di campionare le metriche aggiuntive e raccoglierle a intervalli di 5 minuti, 1 ora e 12 ore. I dati aggregati vengono archiviati nell'account di archiviazione, nelle tabelle di e vengono eliminati dopo 10 giorni. Tuttavia, l'account di archiviazione usato è configurato in base al ruolo ed è possibile usare account di archiviazione diversi per ruoli diversi. Questo è configurato con una stringa di connessione nei file con estensione .csdef e .cscfg.

Si noti che il monitoraggio avanzato comporta l'uso dell'estensione Diagnostica di Azure (Application Insights SDK è facoltativo) sul ruolo che si vuole monitorare. L'estensione di diagnostica usa un file di configurazione (per ruolo) denominato diagnostics.wadcfgx per configurare le metriche di diagnostica monitorate. L'estensione Diagnostica di Azure raccoglie e archivia i dati in un account di archiviazione di Azure. Queste impostazioni vengono configurate nei file .wadcfgx, .csdef e .cscfg.

- [Presentazione del monitoraggio del servizio cloud](cloud-services-how-to-monitor.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida**: sono disponibili le modalità di monitoraggio di base o avanzata per i servizi cloud di Azure. Servizi cloud di Azure raccoglie automaticamente i dati di monitoraggio di base (percentuale CPU, in uscita/uscita rete e lettura/scrittura disco) da una macchina virtuale host. Visualizzare i dati di monitoraggio raccolti nelle pagine Panoramica e metriche di un servizio cloud nella portale di Azure. 

Abilitare la diagnostica nei servizi cloud di Azure per raccogliere dati di diagnostica come log applicazioni, contatori delle prestazioni e altro ancora, usando l'estensione Diagnostica di Azure. Abilitare o aggiornare la configurazione di diagnostica in un servizio cloud già in esecuzione con Set-AzureServiceDiagnosticsExtension cmdlet o distribuire automaticamente un servizio cloud con l'estensione di diagnostica. Facoltativamente, installare il Application Insights SDK. Inviare i contatori delle prestazioni a monitoraggio di Azure.

L'estensione Diagnostica di Azure raccoglie e archivia i dati in un account di archiviazione di Azure. Trasferire i dati di diagnostica al Emulatore di archiviazione di Microsoft Azure o all'archiviazione di Azure perché non vengono archiviati in modo permanente. Una volta nella risorsa di archiviazione, è possibile visualizzarla con uno dei diversi strumenti disponibili, ad esempio Esplora server in Visual Studio, Microsoft Azure Storage Explorer Management Studio Azure. Configurare le metriche di diagnostica da monitorare con un file di configurazione (per ruolo) denominato Diagnostics. wadcfgx nell'estensione di diagnostica. 

- [Presentazione del monitoraggio del servizio cloud](cloud-services-how-to-monitor.md)

- [Come abilitare la diagnostica in un ruolo di lavoro-integrare con una SIEM](../security-center/continuous-export.md)

- [Abilitare la diagnostica nei servizi cloud di Azure con PowerShell](cloud-services-diagnostics-powershell.md)

- [Archiviare e visualizzare i dati di diagnostica nell'account di archiviazione Azure](diagnostics-extension-to-storage.md?&amp;preserve-view=true)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida**: è possibile monitorare i dati di log di servizi cloud di Azure con l'integrazione con Sentinel di Azure o con Siem di terze parti, abilitando avvisi per attività anomale.

- [Eseguire l'integrazione con un SIEM](../security-center/continuous-export.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida**: Microsoft antimalware per Azure protegge i servizi cloud e le macchine virtuali di Azure. Si ha la possibilità di distribuire soluzioni di sicurezza di terze parti, ad esempio le barriere antincendio per le applicazioni Web, i firewall di rete, antimalware, sistemi di rilevamento e prevenzione delle intrusioni (ID o indirizzi IP) e altro ancora.

- [Quali sono le funzionalità e le funzionalità fornite da Azure Basic IPS/IDS e DDOS](./cloud-services-configuration-and-management-faq.md?amp;preserve-view=#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni**: Microsoft consiglia di gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). I servizi cloud di Azure, tuttavia, non supportano il modello di controllo degli accessi in base al ruolo di Azure, perché non è un servizio basato su Azure Resource Manager ed è necessario usare una sottoscrizione classica

Per impostazione predefinita, l'amministratore dell'account, l'amministratore del servizio e il Co-Administrator sono i tre ruoli di amministratore della sottoscrizione classica in Azure. 

I ruoli di amministratore sottoscrizione classica hanno accesso completo alla sottoscrizione di Azure e possono gestire le risorse con il portale di Azure, con le API di Azure Resource Manager e con le API del modello di distribuzione classica. L'account usato per iscriversi a Azure viene impostato automaticamente come amministratore account e amministratore del servizio. È possibile aggiungere ulteriori Co-Administrators in un secondo momento. 

L'amministratore del servizio e il Co-Administrators hanno accesso equivalente agli utenti a cui è stato assegnato il ruolo di proprietario (un ruolo di Azure) nell'ambito della sottoscrizione. Gestire Co-Administrators o visualizzare l'amministratore del servizio usando la scheda amministratori classici della portale di Azure. 

Elencare le assegnazioni di ruolo per l'amministratore e i coamministratori del servizio classico con PowerShell con il comando:

Get-AzRoleAssignment-IncludeClassicAdministrators

Esaminare le differenze tra i ruoli amministrativi delle sottoscrizioni classiche. 

- [Differenze tra tre ruoli amministrativi di sottoscrizione classici](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: è consigliabile creare procedure operative standard per l'uso di account amministrativi dedicati, in base ai ruoli disponibili e alle autorizzazioni necessarie per il funzionamento e la gestione delle risorse di servizi cloud di Azure.

- [Differenze tra i ruoli amministrativi della sottoscrizione classica](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida**: evitare di gestire identità separate per le applicazioni in esecuzione in servizi cloud di Azure. Implementare Single Sign-On per evitare che gli utenti possano gestire più identità e credenziali.

- [Informazioni su Single Sign-On (SSO)](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida**: è consigliabile usare una workstation protetta gestita da Azure (nota anche come workstation con privilegi di accesso con privilegi) per le attività amministrative, che richiedono privilegi elevati.

- [Informazioni sulle workstation sicure gestite da Azure](../active-directory/devices/concept-azure-managed-workstation.md)

- [Come abilitare l'autenticazione a più fattori Azure AD](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Linee guida**: usare le API REST del servizio cloud di Azure per eseguire l'inventario delle risorse del servizio cloud di Azure per informazioni riservate. Eseguire il polling delle risorse del servizio cloud distribuite per ottenere la configurazione e le risorse. pkg.

 Ad esempio, alcune API sono elencate di seguito:

- Ottenere la distribuzione: l'operazione Get Deployment restituisce le informazioni di configurazione, lo stato e le proprietà di sistema per una distribuzione.
- Get Package: l'operazione get package recupera un pacchetto del servizio cloud per una distribuzione e archivia i file del pacchetto nell'archivio BLOB Microsoft Azure
- Ottenere le proprietà del servizio cloud: l'operazione Get Cloud Service Properties recupera le proprietà per il servizio cloud specificato

Vedere la documentazione sulle API REST del servizio cloud di Azure e creare un processo per la protezione dei dati sensibili, in base ai requisiti dell'organizzazione.

- [Ottenere la distribuzione](/rest/api/compute/cloudservices/rest-get-deployment)

- [Recuperare le proprietà del servizio cloud](/rest/api/compute/cloudservices/rest-get-cloud-service-properties)

- [Ottenere il pacchetto](/rest/api/compute/cloudservices/rest-get-package)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare l'isolamento usando sottoscrizioni e gruppi di gestione distinti per singoli domini di sicurezza, ad esempio il tipo di ambiente e il livello di sensibilità dei dati per i servizi cloud di Azure

È anche possibile modificare "permissionLevel" nell'elemento di certificato del servizio cloud di Azure per specificare le autorizzazioni di accesso concesse ai processi del ruolo. Se si desidera che solo i processi con privilegi elevati siano in grado di accedere alla chiave privata, specificare l'autorizzazione con privilegi elevati. l'autorizzazione limitedOrElevated consente a tutti i processi di ruolo di accedere alla chiave privata. I valori possibili sono limitedOrElevated o con privilegi elevati. Il valore predefinito è limitedOrElevated.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Schema WebRole](schema-csdef-webrole.md#Certificate)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: è consigliabile usare una soluzione di terze parti da Azure Marketplace in perimetri di rete per monitorare il trasferimento non autorizzato di informazioni riservate e bloccare tali trasferimenti mentre si inviano avvisi ai professionisti della sicurezza delle informazioni.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: configurare TLS V2 per i servizi cloud di Azure. Usare il portale di Azure per aggiungere il certificato alla distribuzione di servizi cloud di Azure di gestione temporanea e aggiungere le informazioni del certificato ai file CSDEF e CSCFG dei servizi. Ricreare il pacchetto dell'applicazione e aggiornare la distribuzione di gestione temporanea per l'uso del nuovo pacchetto. 

Usare i certificati di servizio in Azure collegati ai servizi cloud di Azure per consentire la comunicazione sicura da e verso il servizio. Fornire un certificato in grado di autenticare un endpoint HTTPS esposto. Definire i certificati di servizio nella definizione del servizio del servizio cloud e distribuirli automaticamente nella macchina virtuale, eseguendo un'istanza del ruolo.

Eseguire l'autenticazione con l'API di gestione con certificati di gestione) i certificati di gestione consentono di eseguire l'autenticazione con il modello di distribuzione classica. Numerosi programmi e strumenti (ad esempio Visual Studio o Azure SDK) usano questi certificati per automatizzare la configurazione e la distribuzione di vari servizi di Azure. 

Per informazioni di riferimento aggiuntive, il API Gestione dei servizi di Azure fornisce l'accesso a livello di codice alle funzionalità di gestione dei servizi disponibili tramite l'portale di Azure. Azure SDK per Python può essere usato per gestire i servizi cloud di Azure e gli account di archiviazione di Azure. Azure SDK per Python include il API Gestione dei servizi, un'API REST. Tutte le operazioni dell'API vengono eseguite su TLS e autenticate reciprocamente usando i certificati X. 509 v3. Il servizio di gestione è accessibile da un servizio in esecuzione in Azure. È anche possibile accedervi direttamente tramite Internet da qualsiasi applicazione in grado di inviare una richiesta HTTPS e ricevere una risposta HTTPS.

- [Configurare TLS per un'applicazione in Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Usare la gestione dei servizi da Python](cloud-services-python-how-to-use-service-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: è consigliabile usare uno strumento di individuazione attiva di terze parti per identificare tutte le informazioni riservate archiviate, elaborate o trasmesse dai sistemi tecnologici dell'organizzazione, incluse quelle situate in loco o presso un provider di servizi remoti, quindi aggiornare l'inventario delle informazioni riservate dell'organizzazione.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: non applicabile al servizio cloud (versione classica). Non impone la prevenzione della perdita dei dati.

È consigliabile implementare uno strumento di terze parti, ad esempio una soluzione di prevenzione della perdita dei dati basata su host automatizzata, per applicare i controlli di accesso sui dati anche quando i dati vengono copiati fuori da un sistema.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e mantiene una suite di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: Servizi cloud di Azure non supporta la crittografia inattiva. Questo perché i servizi cloud di Azure sono progettati per essere senza stato. I servizi cloud di Azure supportano l'archiviazione esterna, ad esempio archiviazione di Azure, che per impostazione predefinita è crittografata per impostazione predefinita.  

I dati dell'applicazione archiviati nei dischi temporanei non vengono crittografati. Il cliente è responsabile della gestione e della crittografia dei dati, se necessario.  

- [Informazioni sulla crittografia dei dati inattivi in Azure](../security/fundamentals/encryption-atrest.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: è possibile usare gli avvisi delle metriche classici in monitoraggio di Azure per ricevere una notifica quando una delle metriche applicate a risorse critiche supera una soglia. Gli avvisi delle metriche classici sono una funzionalità meno recente che consente di inviare avvisi solo su metriche non dimensionali. È disponibile una funzionalità più recente esistenti denominata Avvisi delle metriche con funzionalità migliorate rispetto agli avvisi delle metriche classici. 

Inoltre, Application Insights possibile monitorare le app di servizi cloud di Azure per disponibilità, prestazioni, errori e utilizzo. Questa operazione USA i dati combinati di Application Insights SDK con i dati Diagnostica di Azure dai servizi cloud di Azure.

- [Creare, visualizzare e gestire gli avvisi delle metriche classici con monitoraggio di Azure](../azure-monitor/platform/alerts-classic-portal.md)

- [Panoramica degli avvisi delle metriche](../azure-monitor/platform/alerts-metric-overview.md) 

- [Application Insights per il servizio cloud di Azure (versione classica)](../azure-monitor/app/cloudservices.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Linee guida**: si noti che queste informazioni sono correlate al sistema operativo guest di Azure per i ruoli di lavoro e Web di servizi cloud di Azure con piattaforma distribuita come servizio (PaaS). Non si applica tuttavia alle macchine virtuali con infrastruttura distribuita come servizio (IaaS).

Per impostazione predefinita, Azure aggiorna periodicamente il sistema operativo guest all'ultima immagine supportata all'interno della famiglia di sistemi operativi specificata nella configurazione del servizio (con estensione cscfg), ad esempio, Windows Server 2016.

Quando un cliente sceglie una versione specifica del sistema operativo per la distribuzione dei servizi cloud di Azure, Disabilita gli aggiornamenti automatici del sistema operativo e applica l'applicazione di patch alla propria responsabilità. Il cliente deve assicurarsi che le istanze del ruolo ricevano gli aggiornamenti o possano esporre l'applicazione a vulnerabilità di sicurezza.

- [Sistema operativo guest di Azure](cloud-services-guestos-msrc-releases.md)

- [Criteri relativi al supporto e al ritiro del sistema operativo guest di Azure](cloud-services-guestos-retirement-policy.md)

- [Come configurare il servizio cloud (versione classica)](cloud-services-how-to-configure-portal.md)

- [Gestire la versione del sistema operativo guest](cloud-services-how-to-configure-portal.md#manage-guest-os-version)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: distribuire una soluzione di gestione delle patch automatizzata per i titoli software di terze parti

**Linee guida**: usare una soluzione di gestione delle patch di terze parti. I clienti che usano già Configuration Manager nel proprio ambiente possono anche usare System Center Updates Publisher, consentendo loro di pubblicare aggiornamenti personalizzati in Windows Server Update Services. 

Questo consente Gestione aggiornamenti di applicare patch ai computer che usano Configuration Manager come repository di aggiornamento con software di terze parti.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: è consigliabile per un cliente comprendere l'ambito dei rischi da un attacco DDoS su base continuativa. 

Si consiglia di considerare questi scenari:

- Quali nuove risorse di Azure pubblicamente disponibili hanno bisogno di protezione?
- È presente un singolo punto di guasto nel servizio?
- Come possono essere isolati i servizi per limitare l'impatto di un attacco mantenendo comunque i servizi disponibili per i clienti validi?
- Esistono reti virtuali in cui Protezione DDoS Standard dovrebbe essere abilitata, ma non lo è?
- I miei servizi sono attivi/non attivi con il failover in più regioni?

Documentazione di supporto:

- [Valutazione del rischio delle risorse di Azure.](../security/fundamentals/ddos-best-practices.md#risk-evaluation-of-your-azure-resources)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: non applicabile ai servizi cloud di Azure. Questa raccomandazione è applicabile alle risorse di calcolo IaaS.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: è consigliabile riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definire e gestire un inventario delle risorse di Azure approvate

**Linee guida**: il cliente deve definire le risorse di Azure approvate e il software approvato per le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare la funzionalità Adaptive Application Control, disponibile nel centro sicurezza di Azure. Si tratta di una soluzione end-to-end intelligente e automatizzata dal centro sicurezza che consente di controllare quali applicazioni possono essere eseguite nei computer Windows e Linux, Azure e non Azure. Consente inoltre di proteggere i computer da malware. 

Questa funzionalità è disponibile sia per le finestre di Azure che per quelle non Azure (tutte le versioni, classiche o Azure Resource Manager) e i computer Linux.

Il Centro sicurezza USA Machine Learning per analizzare le applicazioni in esecuzione nei computer e crea un elenco Consenti da questa intelligence. Questa funzionalità semplifica notevolmente il processo di configurazione e gestione dei criteri dell'elenco Consenti applicazioni, consentendo di:
- Bloccare i tentativi di esecuzione di applicazioni dannose, inclusi quelli che potrebbero altrimenti non venire rilevati dalle soluzioni antimalware oppure inviare un avviso per tali tentativi.

- Rispettare i criteri di sicurezza dell'organizzazione che impongono l'uso solo di software concesso in licenza.
- Evitare l'uso di software indesiderato nel proprio ambiente.
- Evitare l'esecuzione di app obsolete e non supportate.
- Impedire l'uso di strumenti software specifici non consentiti nell'organizzazione.
- Consentire al personale IT di controllare l'accesso ai dati sensibili tramite l'utilizzo delle app.

Per altri dettagli, vedere i collegamenti a cui si fa riferimento.

- [Controlli delle applicazioni adattivi](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: usare la funzionalità Adaptive Application Control, disponibile nel centro sicurezza di Azure. Si tratta di una soluzione end-to-end intelligente e automatizzata dal centro sicurezza che consente di controllare quali applicazioni possono essere eseguite nei computer Windows e Linux, Azure e non Azure. Consente inoltre di proteggere i computer da malware. 

Questa funzionalità è disponibile sia per le finestre di Azure che per quelle non Azure (tutte le versioni, classiche o Azure Resource Manager) e i computer Linux.

Il Centro sicurezza USA Machine Learning per analizzare le applicazioni in esecuzione nei computer e crea un elenco Consenti da questa intelligence. Questa funzionalità semplifica notevolmente il processo di configurazione e gestione dei criteri dell'elenco Consenti applicazioni, consentendo di:

- Bloccare i tentativi di esecuzione di applicazioni dannose, inclusi quelli che potrebbero altrimenti non venire rilevati dalle soluzioni antimalware oppure inviare un avviso per tali tentativi.

- Rispettare i criteri di sicurezza dell'organizzazione che impongono l'uso solo di software concesso in licenza.

- Evitare l'uso di software indesiderato nel proprio ambiente.

- Evitare l'esecuzione di app obsolete e non supportate.

- Impedire l'uso di strumenti software specifici non consentiti nell'organizzazione.

- Consentire al personale IT di controllare l'accesso ai dati sensibili tramite l'utilizzo delle app.

Per altri dettagli, vedere i collegamenti a cui si fa riferimento.

- [Controlli delle applicazioni adattivi](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: usare la funzionalità Adaptive Application Control, disponibile nel centro sicurezza di Azure. Si tratta di una soluzione end-to-end intelligente e automatizzata dal centro sicurezza che consente di controllare quali applicazioni possono essere eseguite nei computer Windows e Linux, Azure e non Azure. Consente inoltre di proteggere i computer da malware. 

Questa funzionalità è disponibile sia per le finestre di Azure che per quelle non Azure (tutte le versioni, classiche o Azure Resource Manager) e i computer Linux.

Il Centro sicurezza USA Machine Learning per analizzare le applicazioni in esecuzione nei computer e crea un elenco Consenti da questa intelligence. Questa funzionalità semplifica notevolmente il processo di configurazione e gestione dei criteri dell'elenco Consenti applicazioni, consentendo di:

- Bloccare i tentativi di esecuzione di applicazioni dannose, inclusi quelli che potrebbero altrimenti non venire rilevati dalle soluzioni antimalware oppure inviare un avviso per tali tentativi.

- Rispettare i criteri di sicurezza dell'organizzazione che impongono l'uso solo di software concesso in licenza.

- Evitare l'uso di software indesiderato nel proprio ambiente.

- Evitare l'esecuzione di app obsolete e non supportate.

- Impedire l'uso di strumenti software specifici non consentiti nell'organizzazione.

- Consentire al personale IT di controllare l'accesso ai dati sensibili tramite l'utilizzo delle app.

Per altri dettagli, vedere i collegamenti a cui si fa riferimento.

- [Controlli delle applicazioni adattivi](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Linee guida**: usare la funzionalità Adaptive Application Control, disponibile nel centro sicurezza di Azure. Si tratta di una soluzione end-to-end intelligente e automatizzata dal centro sicurezza che consente di controllare quali applicazioni possono essere eseguite nei computer Windows e Linux, Azure e non Azure. Consente inoltre di proteggere i computer da malware. 

Questa funzionalità è disponibile sia per le finestre di Azure che per quelle non Azure (tutte le versioni, classiche o Azure Resource Manager) e i computer Linux.

Il Centro sicurezza USA Machine Learning per analizzare le applicazioni in esecuzione nei computer e crea un elenco Consenti da questa intelligence. Questa funzionalità semplifica notevolmente il processo di configurazione e gestione dei criteri dell'elenco Consenti applicazioni, consentendo di:

- Bloccare i tentativi di esecuzione di applicazioni dannose, inclusi quelli che potrebbero altrimenti non venire rilevati dalle soluzioni antimalware oppure inviare un avviso per tali tentativi.

- Rispettare i criteri di sicurezza dell'organizzazione che impongono l'uso solo di software concesso in licenza.

- Evitare l'uso di software indesiderato nel proprio ambiente.

- Evitare l'esecuzione di app obsolete e non supportate.

- Impedire l'uso di strumenti software specifici non consentiti nell'organizzazione.

- Consentire al personale IT di controllare l'accesso ai dati sensibili tramite l'utilizzo delle app.

Per altri dettagli, vedere i collegamenti a cui si fa riferimento.

- [Controlli delle applicazioni adattivi](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

**Linee guida**: usare la funzionalità Adaptive Application Control, disponibile nel centro sicurezza di Azure. Si tratta di una soluzione end-to-end intelligente e automatizzata dal centro sicurezza che consente di controllare quali applicazioni possono essere eseguite nei computer Windows e Linux, Azure e non Azure. Consente inoltre di proteggere i computer da malware. 

Questa funzionalità è disponibile sia per le finestre di Azure che per quelle non Azure (tutte le versioni, classiche o Azure Resource Manager) e i computer Linux.

Il Centro sicurezza USA Machine Learning per analizzare le applicazioni in esecuzione nei computer e crea un elenco Consenti da questa intelligence. Questa funzionalità semplifica notevolmente il processo di configurazione e gestione dei criteri dell'elenco Consenti applicazioni, consentendo di:
- Bloccare i tentativi di esecuzione di applicazioni dannose, inclusi quelli che potrebbero altrimenti non venire rilevati dalle soluzioni antimalware oppure inviare un avviso per tali tentativi.

- Rispettare i criteri di sicurezza dell'organizzazione che impongono l'uso solo di software concesso in licenza.
- Evitare l'uso di software indesiderato nel proprio ambiente.
- Evitare l'esecuzione di app obsolete e non supportate.
- Impedire l'uso di strumenti software specifici non consentiti nell'organizzazione.
- Consentire al personale IT di controllare l'accesso ai dati sensibili tramite l'utilizzo delle app.

Per altri dettagli, vedere i collegamenti a cui si fa riferimento.

- [Controlli delle applicazioni adattivi](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Linee guida**: usare la funzionalità Adaptive Application Control, disponibile nel centro sicurezza di Azure. Si tratta di una soluzione end-to-end intelligente e automatizzata dal centro sicurezza che consente di controllare quali applicazioni possono essere eseguite nei computer Windows e Linux, Azure e non Azure. Consente inoltre di proteggere i computer da malware. 

Questa funzionalità è disponibile sia per le finestre di Azure che per quelle non Azure (tutte le versioni, classiche o Azure Resource Manager) e i computer Linux.

Il Centro sicurezza USA Machine Learning per analizzare le applicazioni in esecuzione nei computer e crea un elenco Consenti da questa intelligence. Questa funzionalità semplifica notevolmente il processo di configurazione e gestione dei criteri dell'elenco Consenti applicazioni, consentendo di:

- Bloccare i tentativi di esecuzione di applicazioni dannose, inclusi quelli che potrebbero altrimenti non venire rilevati dalle soluzioni antimalware oppure inviare un avviso per tali tentativi.

- Rispettare i criteri di sicurezza dell'organizzazione che impongono l'uso solo di software concesso in licenza.

- Evitare l'uso di software indesiderato nel proprio ambiente.

- Evitare l'esecuzione di app obsolete e non supportate.

- Impedire l'uso di strumenti software specifici non consentiti nell'organizzazione.

- Consentire al personale IT di controllare l'accesso ai dati sensibili tramite l'utilizzo delle app.

Per altri dettagli, vedere i collegamenti a cui si fa riferimento.

- [Controlli delle applicazioni adattivi](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: per applicazioni sensibili o ad alto rischio con servizi cloud di Azure, implementare sottoscrizioni separate o gruppi di gestione per garantire l'isolamento.

Usare un gruppo di sicurezza di rete, creare una regola di sicurezza in ingresso, scegliere un servizio come http, scegliere anche una porta personalizzata, assegnarle una priorità e un nome. La priorità influiscono sull'ordine in cui vengono applicate le regole, minore è il valore numerico, ovvero l'applicazione della regola. Sarà necessario associare il gruppo di sicurezza di rete a una subnet o a un'interfaccia di rete specifica per isolare o segmentare il traffico di rete in base alle esigenze aziendali.

Per altri dettagli, vedere i collegamenti a cui si fa riferimento.

- [Esercitazione: filtrare il traffico di rete con un gruppo di sicurezza di rete usando il portale di Azure](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Indicazioni**: usare le raccomandazioni del Centro sicurezza di Azure come linea di base di configurazione sicura per le risorse di servizi cloud di Azure. 

Nella portale di Azure scegliere Centro sicurezza, quindi &amp; app di calcolo e servizi cloud di Azure per visualizzare le raccomandazioni applicabili alle risorse del servizio.

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: non applicabile ai servizi cloud di Azure. Si basa sul modello di distribuzione classica. È consigliabile usare una soluzione di terze parti per mantenere sicure le configurazioni delle risorse di Azure

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: il file di configurazione del servizio cloud di Azure archivia gli attributi operativi per una risorsa. È possibile archiviare una copia dei file di configurazione in un account di archiviazione protetto.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: non applicabile ai servizi cloud di Azure. Si basa sul modello di distribuzione classica e non può essere gestito da Azure Resource Manager strumenti di configurazione basati sulla distribuzione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Linee guida**: non applicabile ai servizi cloud di Azure. Questa raccomandazione è applicabile alle risorse di calcolo basate su infrastruttura distribuita come servizio (IaaS).

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida**: usare il Centro sicurezza di Azure per eseguire analisi di base per le risorse di Azure.  

- [Come correggere le raccomandazioni nel centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: nel centro sicurezza di Azure scegliere &amp; funzionalità per le app di calcolo e seguire le indicazioni per le macchine virtuali, i server e i contenitori.

- [Informazioni sulle raccomandazioni per i contenitori nel Centro sicurezza di Azure](../security-center/container-security.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: i servizi cloud di Azure si basano su un modello di distribuzione classica e non si integrano con Azure Key Vault.

È possibile proteggere i segreti, ad esempio le credenziali usate nei servizi cloud di Azure, in modo da non dover digitare ogni volta una password. Per iniziare, specificare una password in testo normale, convertirla in una stringa sicura usando ConvertTo-SecureString, comando di PowerShell. Convertire quindi questa stringa sicura in una stringa standard crittografata con ConvertFrom-SecureString.  È ora possibile salvare la stringa standard crittografata in un file usando Set-Content.

Inoltre, è consigliabile archiviare le chiavi private per i certificati utilizzati nei servizi cloud di Azure in una risorsa di archiviazione protetta.

- [Configurare Remote Desktop da PowerShell](cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: proteggere i segreti, ad esempio le credenziali usate nei servizi cloud di Azure, in modo che non sia necessario digitare una password ogni volta. 
 

Per iniziare, specificare una password in testo normale, impostarla su una stringa sicura usando ConvertTo-SecureString, comando di PowerShell. Convertire quindi questa stringa sicura in una stringa standard crittografata con ConvertFrom-SecureString. Salvare ora la stringa standard crittografata in un file usando Set-Content comando.

Archiviare le chiavi private per i certificati usati nei servizi cloud di Azure in un percorso di archiviazione protetto.

- [Configurare Remote Desktop da PowerShell](cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: usare il software antimalware gestito centralmente

**Linee guida**: Microsoft antimalware per Azure è disponibile per i servizi cloud e le macchine virtuali di Azure. Si tratta di una protezione in tempo reale gratuita che consente di identificare e rimuovere virus, spyware e altro software dannoso. e genera avvisi quando un software dannoso o indesiderato tenta l'installazione o l'esecuzione nei sistemi di Azure. 

Usare il cmdlet antimalware basato su PowerShell per ottenere la configurazione antimalware con "Get-Azureserviceantimalwareconfig per".

Abilitare l'estensione antimalware con uno script di PowerShell nell'attività di avvio in servizi cloud di Azure.

Scegli la funzionalità di controllo delle applicazioni adattivo nel centro sicurezza di Azure, una soluzione end-to-end intelligente e automatizzata. Consente di proteggere i computer da malware e di bloccare o avvisare i tentativi di esecuzione di applicazioni dannose, incluse quelle che altrimenti potrebbero essere perse dalle soluzioni antimalware.

- [Come è possibile aggiungere un'estensione antimalware per i servizi cloud di Azure in modo automatico](./cloud-services-configuration-and-management-faq.md?amp;preserve-view=#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)

- [Scenari di distribuzione di Antimalware](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)

- [Controlli delle applicazioni adattivi](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Linee guida**: il Centro sicurezza di Azure assegna una gravità a ogni avviso per facilitare la priorità degli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso. 

Contrassegnare chiaramente le sottoscrizioni, ad esempio produzione e non di produzione, e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze. 

- [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza per trasmettere gli avvisi ad Azure Sentinel. 

- [Come configurare l'esportazione continua](../security-center/continuous-export.md) 

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Indicazioni**: seguire le regole Microsoft per assicurarsi che i propri test di penetrazione non violino i criteri Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- [È possibile trovare altre informazioni sulla strategia Microsoft e sull'esecuzione dei test di penetrazione Red Teaming sull'infrastruttura cloud gestita da Microsoft, sui servizi e sulle applicazioni qui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)