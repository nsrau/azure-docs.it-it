---
title: Baseline della sicurezza di Azure per servizi cognitivi
description: Baseline della sicurezza di Azure per servizi cognitivi
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2f3849e052cfc73d3e6e0bafd8b87b088a08266d
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363851"
---
# <a name="azure-security-baseline-for-cognitive-services"></a>Baseline della sicurezza di Azure per servizi cognitivi

La linea di base di sicurezza di Azure per servizi cognitivi contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](../security/benchmarks/overview.md), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere la [Panoramica delle baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Controllo di sicurezza: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida** : Servizi cognitivi di Azure fornisce un modello di sicurezza a più livelli. Questo modello consente di proteggere gli account di Servizi cognitivi in un subset specifico di reti. Quando le regole di rete sono configurate, solo le applicazioni che richiedono dati sul set di reti specificato possono accedere all'account. È possibile limitare l'accesso alle risorse con il filtro delle richieste, consentendo solo le richieste che hanno origine da indirizzi IP, intervalli IP o da un elenco di subnet in reti virtuali di Azure specificati.

Il supporto per la rete virtuale e l'endpoint di servizio per servizi cognitivi è limitato a un set specifico di aree.

* [Come configurare le reti virtuali dei servizi cognitivi di Azure](./cognitive-services-virtual-networks.md?tabs=portal)

* [Panoramica delle reti virtuali di Azure](../virtual-network/virtual-networks-overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

**Linee guida** : quando le macchine virtuali vengono distribuite nella stessa rete virtuale del contenitore dei servizi cognitivi di Azure, è possibile usare i gruppi di sicurezza di rete (NSG) per ridurre il rischio di exfiltration dei dati. Abilitare i log di flusso NSG e inviare i log a un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

* [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee guida** : se si usano servizi cognitivi all'interno di un contenitore, è possibile aumentare la distribuzione del contenitore con una soluzione di firewall per applicazioni Web in primo piano che filtra il traffico dannoso e supporta la crittografia TLS end-to-end, mantenendo l'endpoint contenitore privato e sicuro.

Tenere presente che i contenitori di servizi cognitivi sono tenuti a inviare le informazioni di controllo per finalità di fatturazione. L'unica eccezione è che i contenitori non in linea seguono una metodologia di fatturazione diversa. Se non è possibile consentire l'elenco di diversi canali di rete su cui si basano i contenitori di servizi cognitivi, il contenitore non funzionerà. L'host deve consentire la porta elenco 443 e i domini seguenti:
- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Si noti inoltre che è necessario disabilitare l'ispezione approfondita dei pacchetti per la soluzione firewall nei canali protetti creati dai contenitori di servizi cognitivi nei server Microsoft. In caso contrario, il contenitore non funzionerà correttamente.

* [Informazioni sulla sicurezza del contenitore dei servizi cognitivi di Azure](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida** : quando le macchine virtuali vengono distribuite nella stessa rete virtuale del contenitore dei servizi cognitivi di Azure, definire e implementare configurazioni di sicurezza standard per le risorse di rete correlate con criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. CognitiveServices" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete della cache di Azure per le istanze di Redis. È anche possibile usare le definizioni di criteri predefinite, ad esempio:
- Lo standard di protezione DDoS deve essere abilitato

È anche possibile usare i progetti di Azure per semplificare le distribuzioni su larga scala di Azure tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controllo degli accessi in base al ruolo di Azure (RBAC di Azure) e criteri, in una singola definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

Se si usano servizi cognitivi all'interno di un contenitore, è possibile aumentare la distribuzione del contenitore con una soluzione di firewall per applicazioni Web front-end che filtra il traffico dannoso e supporta la crittografia TLS end-to-end, mantenendo l'endpoint contenitore privato e sicuro.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

* [Informazioni sulla sicurezza del contenitore dei servizi cognitivi di Azure](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida** : quando le macchine virtuali vengono distribuite nella stessa rete virtuale del contenitore dei servizi cognitivi di Azure, è possibile usare i gruppi di sicurezza di rete (NSG) per ridurre il rischio di exfiltration dei dati. Abilitare i log di flusso NSG e inviare i log a un account di archiviazione di Azure per il controllo del traffico. È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

* [Come abilitare i log dei flussi NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida** : se si usano servizi cognitivi all'interno di un contenitore, è possibile aumentare la distribuzione del contenitore con una soluzione di firewall per applicazioni Web in primo piano che filtra il traffico dannoso e supporta la crittografia TLS end-to-end, mantenendo l'endpoint contenitore privato e sicuro. È possibile selezionare un'offerta da Azure Marketplace che supporta la funzionalità di ID/IP con la possibilità di disabilitare l'ispezione del payload.

Tenere presente che i contenitori di servizi cognitivi sono tenuti a inviare le informazioni di controllo per finalità di fatturazione. L'unica eccezione è che i contenitori non in linea seguono una metodologia di fatturazione diversa. Se non è possibile consentire l'elenco di diversi canali di rete su cui si basano i contenitori di servizi cognitivi, il contenitore non funzionerà. L'host deve consentire la porta elenco 443 e i domini seguenti:
- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Si noti inoltre che è necessario disabilitare l'ispezione approfondita dei pacchetti per la soluzione firewall nei canali protetti creati dai contenitori di servizi cognitivi nei server Microsoft. In caso contrario, il contenitore non funzionerà correttamente.

* [Informazioni sulla sicurezza del contenitore dei servizi cognitivi di Azure](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee guida** : se si usano servizi cognitivi all'interno di un contenitore, è possibile aumentare la distribuzione del contenitore con una soluzione di firewall per applicazioni Web in primo piano che filtra il traffico dannoso e supporta la crittografia TLS end-to-end, mantenendo l'endpoint contenitore privato e sicuro.

Tenere presente che i contenitori di servizi cognitivi sono tenuti a inviare le informazioni di controllo per finalità di fatturazione. L'unica eccezione è che i contenitori non in linea seguono una metodologia di fatturazione diversa. Se non è possibile consentire l'elenco di diversi canali di rete su cui si basano i contenitori di servizi cognitivi, il contenitore non funzionerà. L'host deve consentire la porta elenco 443 e i domini seguenti:
- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Si noti inoltre che è necessario disabilitare l'ispezione approfondita dei pacchetti per la soluzione firewall nei canali protetti creati dai contenitori di servizi cognitivi nei server Microsoft. In caso contrario, il contenitore non funzionerà correttamente.

* [Informazioni sulla sicurezza del contenitore dei servizi cognitivi di Azure](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida** : usare i tag del servizio rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete (NSG) o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

È anche possibile usare i gruppi di sicurezza delle applicazioni (ASG) per semplificare la configurazione della sicurezza complessa. Gruppi consente di configurare la sicurezza di rete come un'estensione naturale della struttura di un'applicazione, consentendo di raggruppare le macchine virtuali e definire i criteri di sicurezza di rete in base a tali gruppi.

* [Tag del servizio di rete virtuale](../virtual-network/service-tags-overview.md)

* [Gruppi di sicurezza delle applicazioni](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida** : definire e implementare configurazioni di sicurezza standard per le risorse di rete correlate al contenitore dei servizi cognitivi di Azure con criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. CognitiveServices" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete della cache di Azure per le istanze di Redis.

È anche possibile usare i progetti di Azure per semplificare le distribuzioni su larga scala di Azure tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controllo degli accessi in base al ruolo di Azure (RBAC di Azure) e criteri, in una singola definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni** : usare i tag per le risorse di rete associate al contenitore dei servizi cognitivi di Azure per organizzarle in modo logico in una tassonomia.

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida** : usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate al contenitore dei servizi cognitivi di Azure. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

* [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: Registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Linee guida** : Microsoft gestisce l'origine dell'ora usata per le risorse di Azure, ad esempio servizi cognitivi di Azure per i timestamp nei log.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni** : abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro di Log Analytics, all'hub eventi di Azure o all'account di archiviazione di Azure per essere archiviate. I log attività forniscono informazioni approfondite sulle operazioni eseguite nel contenitore dei servizi cognitivi di Azure a livello di piano di controllo. Usando i dati del log attività di Azure, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita a livello di piano di controllo per la cache di Azure per le istanze di Redis.

* [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/activity-log.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni** : per la registrazione di controllo del piano di controllo, abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro log Analytics, a hub eventi di Azure o all'account di archiviazione di Azure per l'archiviazione Usando i dati del log attività di Azure, è possibile acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita al livello del piano di controllo per le risorse di Azure.

Inoltre, servizi cognitivi di Azure invia eventi di diagnostica che possono essere raccolti e usati a scopo di analisi, avvisi e report. È possibile configurare le impostazioni di diagnostica per un contenitore di servizi cognitivi tramite il portale di Azure. È possibile inviare uno o più eventi di diagnostica a un account di archiviazione, a un hub eventi o a un'area di lavoro Log Analytics.

* [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/activity-log.md)

* [Uso delle impostazioni di diagnostica per servizi cognitivi di Azure](./diagnostic-logging.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Indicazioni** : In monitoraggio di Azure, impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine o l'archivio.

* [Come impostare i parametri di conservazione dei log per le aree di lavoro Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida** : abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro log Analytics. Questi log forniscono dati avanzati e frequenti sul funzionamento di una risorsa che vengono usati per l'identificazione e il debug dei problemi. È possibile eseguire query Log Analytics per cercare termini, identificare tendenze, analizzare modelli e fornire molte altre informazioni basate sui dati del log attività raccolti per servizi cognitivi di Azure.

* [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/activity-log.md)

* [Come raccogliere e analizzare i log attività di Azure nell'area di lavoro Log Analytics in monitoraggio di Azure](../azure-monitor/platform/activity-log.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida** : è possibile generare avvisi sulle metriche supportate in Servizi cognitivi di Azure passando alla &amp; sezione relativa alle metriche degli avvisi in monitoraggio di Azure.

Configurare le impostazioni di diagnostica per il contenitore di servizi cognitivi e inviare i log a un'area di lavoro Log Analytics. All'interno dell'area di lavoro di Log Analytics, configurare gli avvisi in modo che vengano visualizzati quando viene soddisfatto un set predefinito di condizioni. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti.

* [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Creare, visualizzare e gestire gli avvisi del log tramite Monitoraggio di Azure](../azure-monitor/platform/alerts-log.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida** : non applicabile; I servizi cognitivi di Azure non elaborano o producono log correlati all'anti-malware.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida** : non applicabile; Servizi cognitivi di Azure non elabora o produce log correlati a DNS.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida** : Azure Active Directory (ad) include ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

* [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Indicazioni** : l'accesso del piano di controllo ai servizi cognitivi di Azure viene controllato tramite Azure Active Directory (ad). Azure AD non è il concetto di password predefinite.

L'accesso al piano dati ai servizi cognitivi di Azure viene controllato tramite chiavi di accesso. Queste chiavi vengono usate dai client che si connettono alla cache e possono essere rigenerate in qualsiasi momento.

Non è consigliabile creare password predefinite nell'applicazione. È invece possibile archiviare le password in Azure Key Vault e quindi usare Azure Active Directory per recuperarle.

* [Come rigenerare la cache di Azure per le chiavi di accesso Redis](../azure-cache-for-redis/cache-configure.md#settings)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni** : creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

Inoltre, per tenere traccia degli account amministrativi dedicati, è possibile usare le raccomandazioni del Centro sicurezza di Azure o dei criteri predefiniti di Azure, ad esempio:
- Alla sottoscrizione deve essere assegnato più di un proprietario
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

* [Come usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso (Anteprima)](../security-center/security-center-identity-access.md)

* [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida** : Servizi cognitivi di Azure usa le chiavi di accesso per autenticare gli utenti e non supporta il Single Sign-on (SSO) a livello di piano dati. L'accesso al piano di controllo per servizi cognitivi di Azure è disponibile tramite l'API REST e supporta SSO. Per l'autenticazione, impostare l'intestazione di autorizzazione per le richieste su un token JSON Web che è possibile ottenere da Azure Active Directory.

* [Informazioni sull'API REST dei servizi cognitivi di Azure](/rest/api/cognitiveservices/)

* [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Indicazioni** : abilitare Azure Active Directory (AD) Multi-Factor Authentication (MFA) e seguire le raccomandazioni sulla gestione delle identità e degli accessi nel Centro sicurezza di Azure.

* [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni** : usare workstation con accesso con privilegi insieme a Multi-Factor Authentication (MFA) configurato per l'accesso e la configurazione delle risorse di Azure.

* [Informazioni sulle workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Indicazioni** : usare Azure Active Directory (AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando nell'ambiente si verifica un'attività sospetta o non sicura.

Inoltre, usare i rilevamenti di rischi di Azure AD per visualizzare gli avvisi e i report sul comportamento utente rischioso.

* [Come distribuire Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Informazioni sui rilevamenti di rischi di Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Linee guida** : configurare i percorsi denominati nell'accesso condizionale di Azure Active Directory (ad) per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o di paesi/aree geografiche.

* [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Indicazioni** : usare Azure Active Directory (AD) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente. Se il caso d'uso supporta l'autenticazione di Active Directory, usare Azure AD per autenticare le richieste all'API servizi cognitivi.

Attualmente, solo il API Visione artificiale, API Viso, API Analisi del testo, il lettore immersivo, il riconoscitore del modulo, il rilevatore di anomalie e tutti i servizi Bing tranne Ricerca personalizzata Bing supportano l'autenticazione con Azure AD.

* [Come autenticare le richieste ai servizi cognitivi](./authentication.md#authenticate-with-azure-active-directory)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni** : Azure AD mette a disposizione i log necessari per individuare gli account obsoleti. Inoltre, il cliente può usare le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso dell'utente può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo.

Il cliente deve mantenere l'inventario degli account utente di gestione API, risolvendo l'accesso in base alle esigenze. In Gestione API gli sviluppatori sono gli utenti delle API esposte con Gestione API. Per impostazione predefinita, i nuovi account sviluppatore creati sono attivi e associati al gruppo Sviluppatori. Gli account sviluppatore con stato attivo possono essere usati per accedere a tutte le API per cui dispongono di sottoscrizioni.

* [Come gestire gli account utente in Gestione API di Azure](../api-management/api-management-howto-create-or-invite-developers.md)

* [Come ottenere un elenco di utenti di Gestione API](/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida** : è possibile accedere alle origini del registro eventi di accesso, controllo e rischio di Azure Active Directory (ad), che consentono di eseguire l'integrazione con Azure Sentinel o con Siem di terze parti.

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi del log desiderati all'interno Log Analytics.

* [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Come caricare dati in Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Linee guida** : per la deviazione del comportamento dell'account di accesso nel piano di controllo, usare le funzionalità di protezione delle identità Azure Active Directory (ad) e rilevamento dei rischi per configurare risposte automatiche per rilevare azioni sospette correlate alle identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.

* [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida** : non ancora disponibili; Customer Lockbox non è ancora supportato per servizi cognitivi di Azure.

* [Elenco dei servizi Customer Lockbox supportati](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : attualmente non disponibile

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni** : usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni** : implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Le risorse devono essere separate da VNet/subnet, contrassegnate in modo appropriato e protette da un NSG o da un firewall di Azure. Le risorse che archiviano o elaborano dati sensibili devono essere sufficientemente isolate. Per le macchine virtuali che archiviano o elaborano dati sensibili, implementare i criteri e le procedure per disabilitarli quando non sono in uso.

* [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

* [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

* [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

* [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

* [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Come configurare avvisi o avvisi e negare con il firewall di Azure](../firewall/threat-intel.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida** : non ancora disponibili; le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per servizi cognitivi di Azure.

Microsoft gestisce l'infrastruttura sottostante per servizi cognitivi di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

* [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : attualmente non disponibile

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Crittografare tutte le informazioni riservate in transito

**Linee guida** : tutti gli endpoint di servizi cognitivi esposti tramite http applicano TLS 1,2. Con un protocollo di sicurezza applicato, i consumer che tentano di chiamare un endpoint di servizi cognitivi devono rispettare queste linee guida:
- Il sistema operativo client deve supportare TLS 1,2.
- Il linguaggio e la piattaforma usati per eseguire la chiamata HTTP devono specificare TLS 1,2 come parte della richiesta. (A seconda del linguaggio e della piattaforma, la specifica di TLS viene eseguita in modo implicito o esplicito).

* [Informazioni su Transport Layer Security per servizi cognitivi di Azure](./cognitive-services-security.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsibilità** : Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida** : identificazione dei dati, classificazione e funzionalità di prevenzione della perdita non ancora disponibili per servizi cognitivi di Azure. Istanze di tag che contengono informazioni riservate come tali e implementano una soluzione di terze parti, se necessario ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

* [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Indicazioni** : usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso al piano di controllo dei servizi cognitivi di Azure, ad esempio portale di Azure.

* [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per servizi cognitivi di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

* [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografare le informazioni riservate inattive

**Indicazioni** : la crittografia dei servizi cognitivi per i servizi cognitivi dipende dal servizio specifico usato. Nella maggior parte dei casi, i dati vengono crittografati e decrittografati usando la crittografia AES a 256 bit conforme a FIPS 140-2. La crittografia e la decrittografia sono trasparenti, ovvero la crittografia e l'accesso vengono gestite per l'utente. I dati sono protetti per impostazione predefinita e non è necessario modificare il codice o le applicazioni per sfruttare i vantaggi della crittografia.

È inoltre possibile utilizzare Azure Key Vault per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi.

* [Elenco di servizi che crittografano le informazioni inattive](./encryption/cognitive-services-encryption-keys-portal.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida** : usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle istanze di produzione dei servizi cognitivi di Azure e altre risorse critiche o correlate.

* [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [Controllo di sicurezza: gestione della vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee guida** : Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano servizi cognitivi di Azure.

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: distribuire la soluzione di gestione delle patch automatizzata per i titoli software di terze parti

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Linee guida** : Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano servizi cognitivi di Azure.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Microsoft

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Indicazioni** : usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) disponibili nelle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

* [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni** : applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia.

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni** : usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia della cache di Azure per le istanze di redis e le risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Usare inoltre i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

* [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

* [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

* [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definire e gestire un inventario delle risorse di Azure approvate

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo e Azure in generale.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare le risorse di Azure non approvate

**Indicazioni** : usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare anche Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo e Azure in generale.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni** : usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/index.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni** : usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

* [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni** : non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Controllo di sicurezza: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida** : definire e implementare configurazioni di sicurezza standard per il contenitore dei servizi cognitivi di Azure con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. CognitiveServices" per creare criteri personalizzati per controllare o applicare la configurazione della cache di Azure per le istanze di Redis.

* [Come visualizzare gli alias di Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Indicazioni** : non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni** : usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

* [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni** : non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida** : se si usano definizioni di criteri di Azure personalizzate o modelli Azure Resource Manager per i contenitori dei servizi cognitivi di Azure e le risorse correlate, usare Azure Repos per archiviare e gestire in modo sicuro il codice.

* [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentazione di Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida** : usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. cache" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida** : usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. CognitiveServices" per creare definizioni di criteri di Azure personalizzate per l'avviso, il controllo e l'applicazione delle configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [Deploy if not exist] per applicare automaticamente le configurazioni per la cache di Azure per le istanze di redis e le risorse correlate.

* [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida** : per le macchine virtuali o le applicazioni Web di Azure in esecuzione in app Azure servizio usato per accedere all'API servizi cognitivi di Azure, usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione delle chiavi di servizi cognitivi di Azure. Verificare Key Vault l'eliminazione temporanea sia abilitata.

* [Come eseguire l'integrazione con le identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Come creare una Key Vault](../key-vault/secrets/quick-create-portal.md)

* [Come eseguire l'autenticazione a Key Vault](../key-vault/general/authentication.md)

* [Come assegnare un criterio di accesso Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida** : per le macchine virtuali o le applicazioni Web di Azure in esecuzione in app Azure servizio usato per accedere all'API servizi cognitivi di Azure, usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione delle chiavi di servizi cognitivi di Azure. Assicurarsi che l'eliminazione temporanea di Azure Key Vault sia abilitata.

Usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Azure Key Vault, senza credenziali nel codice.

* [Come configurare le identità gestite](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Come eseguire l'integrazione con le identità gestite di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni** : implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

* [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Controllo di sicurezza: difesa da malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: usare software antimalware gestito in modo centralizzato

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio servizi cognitivi di Azure, ma non viene eseguito sui contenuti dei clienti.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida** : Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, cache di Azure per Redis), ma non viene eseguito sui contenuti dei clienti.

Eseguire l'analisi preliminare del contenuto da caricare in risorse di Azure non di calcolo, ad esempio Servizio app, Data Lake Storage, Archiviazione BLOB, Database di Azure per PostgreSQL e così via. Microsoft non è in grado di accedere ai dati in questi casi.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: verificare che le firme e il software antimalware siano aggiornati

**Indicazioni** : non applicabile; questa raccomandazione riguarda le risorse di calcolo.

Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio servizi cognitivi di Azure, ma non viene eseguito sui contenuti dei clienti.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [Controllo di sicurezza: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Linee guida** : i dati nell'account di archiviazione Microsoft Azure vengono sempre replicati automaticamente per assicurare durabilità e disponibilità elevata. Archiviazione di Azure copia i dati in modo che siano protetti da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni della rete o dell'alimentazione e forti calamità naturali. È possibile scegliere di replicare i dati nello stesso data center, tra data center di zona nella stessa area o tra aree separate geograficamente.

È anche possibile usare la funzionalità di gestione del ciclo di vita per eseguire il backup dei dati nel livello archivio. Inoltre, abilitare l'eliminazione temporanea per i backup archiviati nell'account di archiviazione.

* [Informazioni sulla ridondanza di archiviazione di Azure e sui contratti di Service-Level](../storage/common/storage-redundancy.md)

* [Gestire il ciclo di vita di Archiviazione BLOB di Azure](../storage/blobs/storage-lifecycle-management-concepts.md)

* [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida** : usare Azure Resource Manager per distribuire servizi cognitivi e risorse correlate. Azure Resource Manager offre la possibilità di esportare modelli, che consentono di ridistribuire la soluzione nel corso del ciclo di vita di sviluppo e di avere la certezza che le risorse vengano distribuite in uno stato coerente. Usare automazione di Azure per chiamare l'API di esportazione del modello di Azure Resource Manager a intervalli regolari. Eseguire il backup delle chiavi precondivise all'interno Azure Key Vault.

* [Panoramica di Azure Resource Manager](../azure-resource-manager/management/overview.md)

* [Come creare una risorsa di servizi cognitivi usando un modello di Azure Resource Manager](./create-account-resource-manager-template.md?tabs=portal)

* [Esportazione di una singola e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

* [Gruppi di risorse-Esporta modello](/rest/api/resources/resourcegroups/exporttemplate)

* [Introduzione ad automazione di Azure](../automation/automation-intro.md)

* [Come eseguire il backup di chiavi di Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida** : garantire la possibilità di eseguire periodicamente la distribuzione di Azure Resource Manager modelli a intervalli regolari con una sottoscrizione isolata, se necessario. Testare il ripristino delle chiavi precondivise di cui è stato eseguito il backup.

* [Distribuire le risorse con i modelli ARM e portale di Azure](../azure-resource-manager/templates/deploy-portal.md)

* [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida** : usare Azure DevOps per archiviare e gestire in modo sicuro i modelli di Azure Resource Manager. Per proteggere le risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps oppure Active Directory se integrato con TFS.  Usare il controllo degli accessi in base al ruolo di Azure per proteggere le chiavi gestite dal cliente. Abilitare Soft-Delete ed eliminare la protezione in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose. 

* [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

* [Come abilitare Soft-Delete ed eliminare la protezione in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni** : creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

* [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

* [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [È anche possibile sfruttare la guida alla gestione degli eventi imprevisti di sicurezza del NIST per semplificare la creazione del piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni** : il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione) e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure.

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni** : con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

* [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni** : le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

* [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni** : esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi in Sentinel.

* [Come configurare l'esportazione continua](../security-center/continuous-export.md)

* [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni** : usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

* [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Controllo di sicurezza: test di penetrazione ed esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Indicazioni** : * [seguire le regole di engagement Microsoft per assicurarsi che i propri test di penetrazione non violino i criteri Microsoft all'indirizzo](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [È possibile trovare altre informazioni sulla strategia Microsoft e sull'esecuzione dei test di penetrazione Red Teaming sull'infrastruttura cloud gestita da Microsoft, sui servizi e sulle applicazioni qui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)