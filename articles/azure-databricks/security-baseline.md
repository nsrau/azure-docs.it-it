---
title: Baseline di sicurezza di Azure per Azure Databricks
description: Baseline di sicurezza di Azure per Azure Databricks
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 23057723d46ed5d9203741ab2eb1a15edb5bd510
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681701"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>Baseline di sicurezza di Azure per Azure Databricks

La baseline di sicurezza di Azure per Azure Databricks contiene raccomandazioni utili per migliorare il comportamento della distribuzione in termini di sicurezza.

La baseline per questo servizio è tratta da [Azure Security Benchmark, versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere la [Panoramica delle baseline di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Controllo di sicurezza: Sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteggere le risorse usando gruppi di sicurezza di rete o Firewall di Azure nella rete virtuale

**Indicazioni**: Distribuire Azure Databricks nella rete virtuale di Azure. La distribuzione predefinita di Azure Databricks è un servizio completamente gestito in Azure: tutte le risorse del piano dati, inclusa una rete virtuale a cui verranno associati tutti i cluster, vengono distribuite in un gruppo di risorse bloccato. Se è necessaria la personalizzazione della rete, è possibile tuttavia distribuire le risorse del piano dati di Azure Databricks nella propria rete virtuale (aggiunta della rete virtuale), in modo da poter implementare configurazioni di rete personalizzate. Al proprio gruppo di sicurezza di rete con regole personalizzate è possibile applicare restrizioni specifiche del traffico in uscita.

È inoltre possibile configurare regole del gruppo di sicurezza di rete per definire specifiche restrizioni del traffico in uscita sulla subnet in cui è distribuita l'istanza di Azure Databricks. Per un'area di lavoro inserita in una rete virtuale può essere configurato infine Firewall di Azure.

* [Come distribuire Azure Databricks in una rete virtuale](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Come gestire i gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Indicazioni**: distribuire Azure Databricks nella rete virtuale di Azure. Un gruppo di sicurezza di rete non viene creato automaticamente, ma per creare un gruppo di sicurezza di base è necessario usare le regole predefinite di Azure. Quando si distribuisce un'area di lavoro, vengono aggiunte anche le regole previste da Databricks. In alternativa, è possibile iniziare con un gruppo di sicurezza di rete vuoto; le regole appropriate verranno aggiunte automaticamente. Abilitare i log dei flussi dei gruppi di sicurezza di rete e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

* [Come distribuire Azure Databricks in una rete virtuale](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Come abilitare i log dei flussi NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Come abilitare e usare Analisi del traffico](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Come abilitare Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: Proteggere le applicazioni Web critiche

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni**: Abilitare la protezione DDoS Standard di Azure nelle reti virtuali di Azure associate alle istanze di Azure Databricks per la protezione da attacchi Denial of Service distribuiti. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP pubblici notoriamente dannosi o non usati.

* [Gestire Protezione DDoS di Azure Standard nel portale di Azure](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Informazioni sulla protezione dalle minacce per il livello di rete di Azure nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Registrare i pacchetti di rete e i log dei flussi

**Indicazioni**: distribuire Azure Databricks nella rete virtuale di Azure. Un gruppo di sicurezza di rete non viene creato automaticamente, ma per creare un gruppo di sicurezza di base è necessario usare le regole predefinite di Azure. Quando si distribuisce un'area di lavoro, vengono aggiunte anche le regole previste da Databricks. Abilitare i log dei flussi NSG e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

* [Come distribuire Azure Databricks in una rete virtuale](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Come abilitare i log dei flussi NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Come abilitare e usare Analisi del traffico](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Come abilitare Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento intrusioni/prevenzione intrusioni (IDS/IPS) basati sulla rete

**Indicazioni**: Implementare un'appliance virtuale di rete con supporto per IDP/IPS da Azure Marketplace per creare un'area di lavoro virtuale integrata in rete in cui tutti i cluster di Azure Databricks abbiano un solo indirizzo IP in uscita. Il singolo indirizzo IP può essere usato come livello di sicurezza aggiuntivo con altri servizi e applicazioni di Azure che consentono l'accesso in base a indirizzi IP specifici. È possibile usare un firewall di Azure o altri strumenti di terze parti, come un'appliance virtuale di Azure, per gestire il traffico in ingresso e in uscita.

Se il rilevamento e/o la prevenzione di intrusioni in base all'ispezione del payload non costituisce un requisito, è possibile usare Firewall di Azure con intelligence sulle minacce. I filtri basati sull'intelligence sulle minacce del Firewall di Azure possono creare avvisi e rifiutare il traffico da o verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

* [Come assegnare un singolo indirizzo IP pubblico per le aree di lavoro inserite in una rete virtuale usando il Firewall di Azure](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip)

* [Procedura: Creare un messaggio di posta elettronica](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gestire il traffico verso le applicazioni Web

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni**: usare i tag di servizio per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete collegati alle subnet a cui è associata l'istanza di Azure Databricks. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che aggiorna automaticamente il tag in caso di modifica degli indirizzi. I tag di servizio non sono supportati con aree di lavoro non inserite in reti virtuali.

* [Informazioni sui tag di servizio](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Indicazioni**: definire e implementare configurazioni di sicurezza di rete per le istanze di Azure Databricks con Criteri di Azure. È possibile usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.Databricks" per specificare definizioni di criteri personalizzate. I criteri non verranno applicati alle risorse presenti all'interno del gruppo di risorse gestite.

È inoltre possibile usare Azure Blueprints per semplificare le distribuzioni di Azure su larga scala tramite la creazione di pacchetti di artefatti ambientali fondamentali, quali i modelli di Azure Resource Manager, il controllo degli accessi in base al ruolo e i criteri in un'unica definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Informazioni sugli alias e sulla struttura delle definizioni di Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [Come creare un progetto di Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: Documentare le regole di configurazione del traffico

**Indicazioni**: usare i tag per i gruppi di sicurezza di rete e altre risorse correlate alla sicurezza di rete e al flusso del traffico inerenti all'istanza di Azure Databricks. Per le regole dei singoli gruppi di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze di Azure Databricks. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

* [Come visualizzare e recuperare gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Come creare avvisi in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: Registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Usare origini di sincronizzazione ora approvate

**Indicazioni**: Microsoft gestisce le origini temporali per le risorse di Azure, ma è possibile gestire autonomamente le impostazioni di sincronizzazione dell'ora per le risorse di calcolo. Poiché Azure Databricks è un servizio PaaS, non è possibile invece accedere direttamente alle macchine virtuali in un cluster Azure Databricks e non è possibile configurare le impostazioni di sincronizzazione dell'ora.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurare la gestione dei log di sicurezza centralizzata

**Indicazioni**: inserire i log tramite Monitoraggio di Azure per aggregare i dati di sicurezza generati da Azure Databricks. Da Monitoraggio di Azure è possibile eseguire una query nell'area di lavoro di Log Analytics configurata per ricevere i log di diagnostica e di Databricks. Usare gli account di archiviazione di Azure per archiviare log a lungo termine o di archiviazione o hub eventi per l'esportazione di dati in altri sistemi. In alternativa, è possibile abilitare e caricare i dati in Azure Sentinel o in una integrazione di Informazioni di sicurezza e gestione degli eventi (SIEM) di terze parti.

Nota: I log di diagnostica di Azure Databricks richiedono il piano Azure Databricks Premium

* [Come configurare le impostazioni di diagnostica](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni**: abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro di Log Analytics, all'hub eventi di Azure o all'account di archiviazione di Azure per essere archiviate. Usando i dati del log attività di Azure, è possibile acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita al livello del piano di controllo per le risorse di Azure.

Per la registrazione di controllo, Azure Databricks offre log di diagnostica end-to-end per le attività eseguite dagli utenti di Azure Databricks, offrendo all'azienda la possibilità di monitorare i modelli di utilizzo di Azure Databricks, estremamente dettagliati.

Nota: I log di diagnostica di Azure Databricks richiedono il piano Azure Databricks Premium

* [Come abilitare le impostazioni di diagnostica per il log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Come abilitare le impostazioni di diagnostica per Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Raccogliere i log di sicurezza dai sistemi operativi

**Indicazioni**: Azure Databricks offre log di diagnostica end-to-end per le attività eseguite dagli utenti di Azure Databricks, offrendo all'azienda la possibilità di monitorare i modelli di utilizzo di Azure Databricks, estremamente dettagliati.

Nota: I log di diagnostica di Azure Databricks richiedono il piano Azure Databricks Premium. La registrazione di sicurezza del sistema operativo non è disponibile.

* [Come abilitare le impostazioni di diagnostica per il log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Come abilitare le impostazioni di diagnostica per Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni**: abilitare le impostazioni di diagnostica per Azure Databricks. Se si sceglie di archiviare i log in un'area di lavoro di Log Analytics, impostare il periodo di conservazione dell'area di lavoro di Log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine o l'archivio. Le attività correlate alla sicurezza vengono registrate nei log di controllo di Databricks.

Nota: I log di diagnostica di Azure Databricks richiedono il piano Azure Databricks Premium

* [Come abilitare le impostazioni di diagnostica per Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Come impostare i parametri di conservazione dei log per le aree di lavoro Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i log

**Indicazioni**: abilitare le impostazioni di diagnostica per Azure Databricks e inviare i log a un'area di lavoro di Log Analytics. Usare l'area di lavoro di Log Analytics per analizzare e monitorare i log di Azure Databricks per indagare su un comportamento anomalo ed esaminare regolarmente i risultati.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o nelle informazioni di sicurezza e gestione degli eventi di terze parti.

Nota: I log di diagnostica di Azure Databricks richiedono il piano Azure Databricks Premium

* [Come abilitare le impostazioni di diagnostica per Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Come eseguire query sui log di Azure Databricks inviati all'area di lavoro di Log Analytics](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Abilitare gli avvisi per le attività anomale

**Indicazioni**: configurare le impostazioni di diagnostica per l'istanza di Azure Databricks in uso e inviare i log a un'area di lavoro di Log Analytics. All'interno dell'area di lavoro di Log Analytics, configurare gli avvisi in modo che vengano visualizzati quando viene soddisfatto un set predefinito di condizioni.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o nelle informazioni di sicurezza e gestione degli eventi di terze parti.

Nota: I log di diagnostica di Azure Databricks richiedono il piano Azure Databricks Premium

* [Come inviare log di Azure Databricks all'area di lavoro di Log Analytics](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery)

* [Come configurare gli avvisi nell'area di lavoro di Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizzare la registrazione antimalware

**Indicazioni**: Non applicabile.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: Abilitare la registrazione delle query DNS

**Indicazioni**: Non applicabile; Azure Databricks non elabora o produce log correlati a DNS accessibili dagli utenti.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: Abilitare la registrazione di controllo da riga di comando

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Gestire un inventario degli account amministrativi

**Indicazioni**: È possibile usare le API SCIM di Azure Databricks per gestire gli utenti in un'area di lavoro di Azure Databricks e concedere privilegi amministrativi agli utenti designati. È possibile gestirli anche tramite l'interfaccia utente di Azure Databricks.

* [Come usare le API SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Come aggiungere e rimuovere utenti in Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Modificare le password predefinite, ove applicabile

**Indicazioni**: Azure Databricks usa Azure Active Directory (AD) per fornire l'accesso al portale di Azure e alla console di amministrazione di Azure Databricks. Azure AD non applica il concetto di password predefinite, ma è responsabilità dell'utente modificare o non consentire eventuali password predefinite di applicazioni personalizzate o di terze parti.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usare account amministrativi dedicati

**Indicazioni**: è possibile usare le API SCIM di Azure Databricks per aggiungere utenti con privilegi amministrativi in Azure Databricks. È possibile gestirli anche tramite l'interfaccia utente di Azure Databricks.

* [Come usare le API SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Come aggiungere e rimuovere utenti in Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Usare Single Sign-On (SSO) con Azure Active Directory

**Indicazioni**: Azure Databricks viene automaticamente configurato per ricorrere all'accesso Single Sign-On di Azure Active Directory per autenticare gli utenti. Gli utenti esterni all'organizzazione devono completare il processo di invito ed essere aggiunti al tenant di Active Directory prima di poter accedere ad Azure Databricks tramite Single Sign-On. È possibile implementare SCIM per automatizzare il provisioning e il deprovisioning degli utenti dalle aree di lavoro.

* [Come usare le API SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Informazioni sull'accesso Single Sign-On per Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Come invitare utenti esterni nel proprio tenant di Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory.

**Indicazioni**: abilitare Azure AD MFA e seguire le raccomandazioni sulla gestione delle identità e degli accessi nel Centro sicurezza di Azure.

* [Come abilitare MFA in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni**: usare workstation con accesso con privilegi insieme a Multi-Factor Authentication (MFA) configurato per l'accesso e la configurazione delle risorse di Azure.

* [Informazioni sulle workstation con accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Come abilitare MFA in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registrare e inviare avvisi per le attività sospette dagli account amministrativi

**Indicazioni**: usare i report di sicurezza di Azure Active Directory per la generazione di log e avvisi quando nell'ambiente si verifica un'attività sospetta o non sicura. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso. È possibile usufruire anche dei log di diagnostica di Azure Databricks.

* [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare le località denominate di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

* [Come configurare località denominate in Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Indicazioni**: Azure Databricks viene automaticamente configurato per ricorrere all'accesso Single Sign-On di Azure Active Directory per autenticare gli utenti. Gli utenti esterni all'organizzazione devono completare il processo di invito ed essere aggiunti al tenant di Active Directory prima di poter accedere ad Azure Databricks tramite Single Sign-On.

* [Informazioni sull'accesso Single Sign-On per Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Come invitare utenti esterni nel proprio tenant di Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure AD mette a disposizione i log necessari per individuare gli account obsoleti. Usare anche le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruoli. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo. Per verificare l'accesso degli utenti, è anche possibile implementare le API SCIM e i log di diagnostica di Azure Databricks. Per verificare l'accesso degli utenti, è anche possibile usare le API SCIM e i log di diagnostica di Azure Databricks.

È possibile infine verificare e gestire l'accesso degli utenti dalla console di amministrazione di Azure Databricks.

* [Creare report di Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Come usare le verifiche di accesso alle identità di Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Come gestire l'accesso degli utenti dalla consolo di amministrazione di Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorare i tentativi di accesso agli account disattivati

**Indicazioni**: È possibile accedere alle origini dei log degli eventi di rischio e controllo e delle attività di accesso di Azure AD, che consentono di integrarsi con qualsiasi strumento di monitoraggio/SIEM. Per verificare le attività di accesso degli utenti, è anche possibile usare i log di diagnostica di Azure Databricks.

È possibile semplificare questo processo creando impostazioni di diagnostica per gli account utente di Azure Active Directory e inviando i log di controllo e di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro di Log Analytics.

* [Come usare le API SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Come integrare i log attività di Azure in Monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Avvisare in caso di deviazione dal comportamento di accesso dell'account

**Indicazioni**: Usare le funzionalità di rilevamento dei rischi e protezione delle identità di Azure Active Directory per configurare risposte automatiche ad azioni sospette correlate a identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita. Per verificare le attività di accesso degli utenti, è anche possibile usare i log di diagnostica di Azure Databricks.

* [Come visualizzare gli accessi a rischio per Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Come configurare e abilitare i criteri di rischio di Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Indicazioni**: Quando vengono aperti i ticket di supporto, i tecnici del servizio clienti e del supporto tecnico chiederanno il consenso per l'accesso ai dati rilevanti dei clienti.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: attualmente non disponibile

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Gestire un inventario delle informazioni riservate

**Indicazioni**: Usare i tag per rilevare più facilmente le istanze di Azure Databricks che elaborano informazioni riservate.

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. La distribuzione predefinita di Azure Databricks è un servizio completamente gestito distribuito nella rispettiva rete virtuale. Se tuttavia è necessaria la personalizzazione della rete, è possibile distribuire Azure Databricks nella propria rete virtuale. È consigliabile creare aree di lavoro di Azure Databricks distinte per diversi team o reparti aziendali.

* [Come distribuire Azure Databricks in una rete virtuale](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Come creare sottoscrizioni di Azure aggiuntive](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Come creare gruppi di gestione](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Come creare una rete virtuale](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorare e bloccare il trasferimento non autorizzato di informazioni riservate.

**Indicazioni**: seguire l'architettura di protezione dalle esfiltrazioni di Databricks per ridurre il rischio di esfiltrazione dei dati.

* [Protezione dell'esfiltrazione di dati con Azure Databricks](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

Microsoft gestisce l'infrastruttura sottostante per Azure Databricks e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

* [Informazioni sulla protezione dei dati dei clienti in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: attualmente non disponibile

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Crittografare tutte le informazioni riservate in transito

**Indicazioni**: per impostazione predefinita, Microsoft negozia TLS 1.2 quando si amministra l'istanza di Azure Databricks tramite la console del portale di Azure o di Azure Databricks. L'app Web Databricks supporta TLS 1.3.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni**: attualmente non disponibili; le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per Azure Databricks. Contrassegnare le istanze di Azure Databricks e le risorse correlate che potrebbero elaborare informazioni riservate come tali e implementare una soluzione di terze parti, se necessario ai fini della conformità.

La piattaforma Databricks è di sola elaborazione e tutti i dati vengono archiviati in altri servizi dati di Azure. Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

* [Informazioni sulla protezione dei dati dei clienti in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: attualmente non disponibile

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Indicazioni**: In Azure Databricks è possibile usare gli elenchi di controllo di accesso (ACL) per configurare l'autorizzazione per accedere a tabelle dati, cluster, pool, processi e oggetti dell'area di lavoro, ad esempio notebook, esperimenti e cartelle. Tutti gli utenti amministratore possono gestire gli elenchi di controllo di accesso, così come gli utenti a cui sono state concesse autorizzazioni delegate per gestire tali elenchi. È possibile usare il controllo degli accessi in base ruolo di Azure per impostare le autorizzazioni nell'area di lavoro di Azure Databricks.

Nota: il controllo di accesso a tabelle, cluster, pool, processi e area di lavoro è disponibile solo nel piano di Azure Databricks Premium

* [Gestire il controllo di accesso in Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per Azure Databricks e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

* [Informazioni sulla protezione dei dati dei clienti in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografare le informazioni riservate inattive

**Indicazioni**: Un'area di lavoro di Azure Databricks è costituita da un piano di gestione ospitato in una rete virtuale gestita da Azure Databricks e da un piano dati distribuito in una rete virtuale gestita dal cliente. Il piano di controllo archivia tutti i notebook degli utenti e i relativi risultati in un database. Per impostazione predefinita, tutti i notebook e i risultati vengono crittografati a riposo con una chiave di crittografia diversa.

Il file system di Databricks (DBFS) è un file system distribuito montato in un'area di lavoro di Azure Databricks e disponibile nei cluster Azure Databricks. Il file system di Databricks viene implementato come account di archiviazione nel gruppo di risorse gestite dell'area di lavoro di Azure Databricks. Per impostazione predefinita, l'account di archiviazione viene crittografato con chiavi gestite da Microsoft. I dati vengono archiviati nei servizi dati di Azure di cui si è proprietari ed è possibile scegliere di crittografarli. Non è consigliabile usare il file system di Databricks per archiviare i dati di produzione

Nota: queste funzionalità non sono disponibili per tutte le sottoscrizioni di Azure Databricks. Per richiederne l'accesso, contattare il rappresentante dell'account Microsoft o Databricks.

* [Come abilitare chiavi gestite dal cliente per notebook Azure Databricks](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [Come abilitare chiavi gestite dal cliente per il file system di Azure Databricks](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Indicazioni**: usare Monitoraggio di Azure con il log attività di Azure per creare un avviso ogni volta che vengono apportate modifiche alle aree di lavoro critiche di Azure Databricks.

* [Come creare avvisi per gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [Controllo di sicurezza: Gestione della vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni**: Implementare una soluzione di gestione delle vulnerabilità di terze parti.

Se si dispone di una sottoscrizione a una piattaforma di gestione delle vulnerabilità, è possibile usare gli script di inizializzazione di Azure Databricks per installare agenti di valutazione delle vulnerabilità nei nodi del cluster di Azure Databricks e gestire i nodi tramite il rispettivo portale. Ogni soluzione di terze parti funziona in modo diverso.

* [Come installare l'agente Rapid7 manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Come installare l'agente Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Script di inizializzazione di Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Indicazioni**: Microsoft gestisce le immagini di base dei nodi del cluster di Azure Databricks, ma l'utente è responsabile di applicare le patch ai nodi del cluster. Per aggiungere un aggiornamento di manutenzione a un cluster in esecuzione esistente, è necessario riavviare il cluster.

* [Informazioni sugli aggiornamenti di manutenzione di runtime per Azure Databricks](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuire una soluzione di gestione delle patch automatizzata per il software di terze parti

**Indicazioni**: Microsoft gestisce le immagini di base dei nodi del cluster di Azure Databricks, ma l'utente è responsabile di applicare le patch a eventuali applicazioni di terze parti installate.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Confrontare le analisi di vulnerabilità back-to-back

**Indicazioni**: implementare una soluzione di gestione delle vulnerabilità di terze parti che abbia la possibilità di confrontare le analisi di vulnerabilità nel tempo. Se si dispone di una sottoscrizione di gestione delle vulnerabilità, è possibile usare il portale del fornitore per visualizzare e confrontare le analisi delle vulnerabilità back-to-back. Ogni soluzione di terze parti funziona in modo diverso.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate.

**Indicazioni**: usare un programma comune di assegnazione dei punteggi di rischio, ad esempio un sistema comune di valutazione delle vulnerabilità, oppure le valutazioni di rischio predefinite fornite dallo strumento di analisi di terze parti.

**Monitoraggio del Centro sicurezza di Azure**: N/D

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Usare l'individuazione di asset in Azure

**Indicazioni**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) disponibili nelle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

* [Come creare query con Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Come visualizzare le sottoscrizioni di Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Informazioni sul controllo degli accessi in base al ruolo Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: Gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle risorse di Azure. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Usare inoltre Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

* [Come creare sottoscrizioni di Azure aggiuntive](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Come creare gruppi di gestione](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Mantenere un inventario delle risorse di Azure approvate e dei titoli software.

**Indicazioni**: Definire il software e le risorse di Azure approvate per le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorare la presenza di risorse di Azure non approvate

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Come creare query con Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorare le applicazioni software non approvate nelle risorse di calcolo

**Indicazioni**: non applicabile; Azure Databricks è un servizio PaaS e, pertanto, non è possibile accedere direttamente alle macchine virtuali in un cluster di Azure Databricks.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via), incluse le istanze di Azure Databricks, disponibili nelle sottoscrizioni. Se individuate, rimuovere eventuali risorse di Azure non approvate. Per nodi del cluster di Azure Databricks, implementare una soluzione di terze parti per rimuovere o segnalare software non approvato.

* [Come creare query con Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: Usare solo applicazioni approvate

**Indicazioni**: non applicabile; Azure Databricks è un servizio PaaS e, pertanto, non è possibile accedere direttamente alle macchine virtuali in un cluster di Azure Databricks.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: Usare solo servizi di Azure approvati

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Come negare un tipo di risorsa specifico con Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="610-implement-approved-application-list"></a>6.10: Implementare l'elenco di applicazioni approvate

**Indicazioni**: non applicabile; Azure Databricks è un servizio PaaS e, pertanto, non è possibile accedere direttamente alle macchine virtuali in un cluster di Azure Databricks.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Limitare la capacità degli utenti di interagire con Azure Resource Manager tramite script</div>

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

* [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni**: non applicabile; non è applicabile ad Azure Databricks perché non è necessario che gli utenti (non amministratori) del cluster accedano ai singoli nodi per eseguire i processi. Per impostazione predefinita, l'amministratore del cluster ha accesso alla radice a tutti i nodi del cluster.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni**: Non applicabile; il benchmark è destinato al servizio App di Azure o alle risorse di calcolo che ospitano applicazioni Web.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Controllo di sicurezza: configurazione sicura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Indicazioni**: definire e implementare configurazioni di sicurezza standard per le istanze di Azure Databricks con Criteri di Azure. Usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.Databricks" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze di Azure Databricks. Eventuali criteri applicati non funzionano nel gruppo delle risorse gestite di Databricks.

* [Come visualizzare gli alias di Criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Definire configurazioni sicure del sistema operativo

**Indicazioni**: non applicabile; Azure Databricks è un servizio PaaS e, pertanto, non è possibile accedere direttamente alle macchine virtuali in un cluster di Azure Databricks.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: definire e implementare configurazioni di sicurezza standard per le istanze di Azure Databricks con Criteri di Azure. Usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.Databricks" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze di Azure Databricks. usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Informazioni sugli effetti di Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni**: le immagini del sistema operativo di Azure Databricks sono gestite da Microsoft. L'utente è responsabile di implementare la configurazione dello stato a livello di sistema operativo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni**: Se si usano definizioni di criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire il codice in modo sicuro.

* [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentazione di Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Archiviare in modo sicuro immagini personalizzate del sistema operativo

**Indicazioni**: se si usano immagini personalizzate per i nodi del cluster di Azure Databricks, eseguire il push dell'immagine di base personalizzata in un registro Docker come il registro Azure Container.

* [Come personalizzare i contenitori con Databricks Container Services](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [Informazioni sul registro Azure Container](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuire strumenti di gestione della configurazione di sistema

**Indicazioni**: definire e implementare configurazioni di sicurezza standard per le istanze di Azure Databricks con Criteri di Azure. Usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.Databricks" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze di Azure Databricks.

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Come visualizzare gli alias di Criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Indicazioni**: non applicabile; Azure Databricks è un servizio PaaS e, pertanto, non è possibile accedere direttamente alle macchine virtuali in un cluster di Azure Databricks.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

**Indicazioni**: definire e implementare configurazioni di sicurezza standard per le istanze di Azure Databricks con Criteri di Azure. Usare gli alias di Criteri di Azure nello spazio dei nomi "Microsoft.Databricks" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze di Azure Databricks.

* [Come visualizzare gli alias di Criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Come configurare e gestire Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Indicazioni**: Implementare una soluzione di terze parti per monitorare lo stato dei sistemi operativi dei nodi del cluster di Azure Databricks.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gestire i segreti di Azure in modo sicuro

**Indicazioni**: usare Azure Key Vault con un ambito dei segreti di Azure Databricks per gestire e usare i segreti in modo sicuro.

* [Come usare Azure Key Vault con Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gestire le identità in modo sicuro e automatico

**Indicazioni**: attualmente non disponibile; le identità gestite non sono attualmente disponibili per Azure Databricks

* [Servizi che supportano le identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: attualmente non disponibile

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Questo strumento incoraggerà inoltre a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

* [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Controllo di sicurezza: difesa da malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Usare software antimalware gestito in modo centralizzato

**Indicazioni**: Se si dispone di una sottoscrizione a una piattaforma di gestione delle vulnerabilità, è possibile usare gli script di inizializzazione di Azure Databricks per installare agenti di valutazione delle vulnerabilità nei nodi del cluster di Azure Databricks e gestire i nodi tramite il rispettivo portale. Ogni soluzione di terze parti funziona in modo diverso.

* [Come installare l'agente Rapid7 manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Come installare l'agente Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Script di inizializzazione di Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Indicazioni**: il software antimalware Microsoft è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio Servizio app di Azure, ma non viene eseguito sui contenuti del cliente.

Eseguire l'analisi preliminare di tutti i file caricati nei nodi del cluster di Azure Databricks o nelle risorse correlate.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: Verificare che le firme e il software antimalware siano aggiornati

**Indicazioni**: Se si dispone di una sottoscrizione a una piattaforma di gestione delle vulnerabilità, è possibile usare gli script di inizializzazione di Azure Databricks per installare agenti di valutazione delle vulnerabilità nei nodi del cluster di Azure Databricks e gestire i nodi tramite il rispettivo portale. Ogni soluzione di terze parti funziona in modo diverso.

* [Come installare l'agente Rapid7 manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Come installare l'agente Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Script di inizializzazione di Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [Controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantire l'esecuzione regolare di backup automatizzati

**Indicazioni**: per le origini dati di Azure Databricks, assicurarsi di aver configurato un livello di ridondanza dei dati appropriato per il caso d'uso. Se, ad esempio, si usa un account di archiviazione di Azure per l'archivio dati di Azure Databricks, scegliere l'opzione di ridondanza appropriata (archiviazione con ridondanza locale, con ridondanza della zona, con ridondanza geografica, con ridondanza geografica e accesso in lettura).

* [Origini dati per Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Ripristino di emergenza a livello di area per cluster di Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Eseguire backup completi del sistema e il backup di tutte le chiavi gestite dal cliente

**Indicazioni**: eseguire il backup in Azure Key Vault di tutte le chiavi gestite dal cliente correlate alle implementazioni di Azure Databricks. È anche possibile usare l'API REST e l'interfaccia della riga di comando per creare un backup giornaliero delle configurazioni di Databricks. È anche possibile usare l'API REST e l'interfaccia della riga di comando per creare un backup giornaliero delle configurazioni di Databricks.

* [Come eseguire il backup di chiavi di Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni**: testare il ripristino delle chiavi gestite dal cliente di cui è stato eseguito il backup correlate alle implementazioni di Azure Databricks.

* [Come ripristinare le chiavi di Key Vault in Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantire la protezione dei backup e delle chiavi gestite dal cliente

**Indicazioni**: assicurarsi che in Key Vault sia abilitata l'eliminazione temporanea per proteggere le chiavi da eliminazioni accidentali o dannose.

* [Come abilitare l'eliminazione temporanea in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

* [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [È possibile inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un proprio piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione, e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: Testare le procedure di risposta per la sicurezza

**Indicazioni**: con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

* [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

* [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Questa funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi in Sentinel.

* [Come configurare l'esportazione continua](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Come trasmettere gli avvisi in Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

* [Come configurare l'automazione del flusso di lavoro e App per la logica](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Controllo di sicurezza: test di penetrazione ed esercizi Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Eseguire con regolarità test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici in termini di sicurezza entro 60 giorni.

**Indicazioni**: * [seguire le regole di engagement Microsoft per assicurarsi che i propri test di penetrazione non violino i criteri Microsoft all'indirizzo](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [È possibile trovare altre informazioni sulla strategia Microsoft e sull'esecuzione dei test di penetrazione Red Teaming sull'infrastruttura cloud gestita da Microsoft, sui servizi e sulle applicazioni qui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
