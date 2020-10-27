---
title: Baseline della sicurezza di Azure per il servizio Azure Kubernetes
description: La linea di base di sicurezza del servizio Kubernetes di Azure fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 231a3eb377c32d422efc39833bf0fe00af105ff1
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546518"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Baseline della sicurezza di Azure per il servizio Azure Kubernetes

Il servizio di base di sicurezza di Azure per il servizio Azure Kubernetes contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](../security/benchmarks/overview.md), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere [Panoramica delle baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida** : per impostazione predefinita, un gruppo di sicurezza di rete e una tabella di route vengono creati automaticamente con la creazione di un cluster del servizio Microsoft Azure KUBERNETES (AKS). AKS modifica automaticamente i gruppi di sicurezza di rete per un flusso di traffico appropriato quando vengono creati servizi con servizi di bilanciamento del carico, mapping delle porte o route di ingresso. Il gruppo di sicurezza di rete viene associato automaticamente alle schede NIC virtuali nei nodi del cliente e alla tabella di route con la subnet nella rete virtuale. 

Usare i criteri di rete AKS per limitare il traffico di rete definendo le regole per il traffico in ingresso e in uscita tra i pod Linux in un cluster in base alla scelta di spazi dei nomi e selettori di etichette. L'utilizzo dei criteri di rete richiede il plug-in Azure CNI con la rete virtuale e le subnet definite e può essere abilitato solo in fase di creazione del cluster. Non possono essere distribuiti in un cluster AKS esistente.

È possibile implementare un cluster AKS privato per garantire che il traffico di rete tra il server dell'API AKS e i pool di nodi rimanga solo sulla rete privata. Il piano di controllo o il server API si trova in una sottoscrizione di Azure gestita da AKS e USA indirizzi IP interni (RFC1918), mentre il cluster o il pool di nodi del cliente si trova nella propria sottoscrizione. Il server e il cluster o il pool di nodi comunicano tra loro usando il servizio di collegamento privato di Azure nella rete virtuale del server API e un endpoint privato esposto nella subnet del cluster AKS del cliente.  In alternativa, usare un endpoint pubblico per il server API AKS, ma limitare l'accesso con la funzionalità degli intervalli IP autorizzati del server dell'API AKS. 

- [Concetti relativi alla sicurezza per le applicazioni e i cluster nel servizio Azure Kubernetes](concepts-security.md)

- [Proteggere il traffico tra i pod usando criteri di rete nel servizio Azure Kubernetes](use-network-policies.md)

- [Creare un cluster privato del servizio Azure Kubernetes](private-clusters.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

**Linee guida** : usare il Centro sicurezza e seguire le raccomandazioni per la protezione della rete per proteggere le risorse di rete usate dai cluster di Azure Kubernetes Service (AKS). 

Abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log a un account di archiviazione di Azure per il controllo. È anche possibile inviare i log di flusso a un'area di lavoro di Log Analytics e quindi usare Analisi del traffico per fornire informazioni dettagliate sui modelli di traffico nel cloud di Azure per visualizzare l'attività di rete, identificare le aree sensibili e le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete non configurate.

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

- [Come abilitare i log dei flussi di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

Materiale sussidiario: usare un firewall di applicazione Web (WAF) abilitato per il gateway applicazione Azure davanti a un cluster AKS per offrire un ulteriore livello di **sicurezza,** filtrando il traffico in ingresso per le applicazioni Web. Azure WAF usa un set di regole, fornito da Open Web Application Security Project (OWASP), per gli attacchi, ad esempio, cross site scripting o cookie poisoning per questo traffico. 

Usare un gateway API per l'autenticazione, l'autorizzazione, la limitazione, la memorizzazione nella cache, la trasformazione e il monitoraggio per le API usate nell'ambiente AKS. Un gateway API funge da porta principale per i microservizi, separa i client dai microservizi e riduce la complessità dei microservizi eliminando il carico di lavoro per la gestione dei problemi trasversali.

- [Informazioni sulle procedure consigliate per la sicurezza e la connettività di rete in AKS](operator-best-practices-network.md)

- [Controller di ingresso del gateway applicazione ](../application-gateway/ingress-controller-overview.md)

- [Usare gestione API di Azure con microservizi distribuiti nel servizio Azure Kubernetes](../api-management/api-management-kubernetes.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida** : abilitare la protezione standard Microsoft Distributed Denial of Service (DDoS) nelle reti virtuali in cui vengono distribuiti i componenti di Azure Kubernetes Service (AKS) per la protezione da attacchi DDoS.
Installare il motore dei criteri di rete e creare i criteri di rete Kubernetes per controllare il flusso del traffico tra i pod in AKS come, per impostazione predefinita, tutto il traffico è consentito tra questi pod. I criteri di rete devono essere usati solo per pod e nodi basati su Linux nel servizio Azure Kubernetes. Definire le regole che limitano la comunicazione pod per una maggiore sicurezza. 

Scegliere di consentire o negare il traffico in base a impostazioni quali etichette assegnate, spazio dei nomi o porta del traffico. I criteri di rete richiesti possono essere applicati automaticamente quando i pod vengono creati dinamicamente in un cluster AKS. 

- [Proteggere il traffico tra i pod usando criteri di rete nel servizio Azure Kubernetes](use-network-policies.md)

- [Come configurare la protezione DDoS](../virtual-network/manage-ddos-protection.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Indicazioni** : usare l'acquisizione di pacchetti Network Watcher come richiesto per l'analisi delle attività anomale. 

Network Watcher viene abilitato automaticamente nell'area della rete virtuale quando si crea o si aggiorna una rete virtuale nella sottoscrizione. È anche possibile creare nuove istanze di Network Watcher usando PowerShell, l'interfaccia della riga di comando di Azure, l'API REST o il metodo ARMClient

- [Come abilitare Network Watcher](../network-watcher/network-watcher-create.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida** : proteggere il cluster Azure Kubernetes Service (AKS) con un Gateway di applicazione Azure abilitato con un Web Application Firewall (WAF). 

Se il rilevamento delle intrusioni e/o la prevenzione basata sull'ispezione del payload o l'analisi del comportamento non è un requisito, è possibile usare un gateway applicazione Azure con WAF e configurarlo in "modalità di rilevamento" per registrare avvisi e minacce o "modalità di prevenzione" per bloccare attivamente le intrusioni e gli attacchi rilevati.

- [Informazioni sulle procedure consigliate per la protezione del cluster AKS con un WAF](operator-best-practices-network.md#secure-traffic-with-a-web-application-firewall-waf)

- [Come distribuire applicazione Azure Gateway (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee guida** : usare applicazione Azure un firewall applicazione Web (WAF) abilitato per il gateway per filtrare il traffico in ingresso in un cluster AKS. Il progetto Open Web Application Security (OWASP) fornisce un set di regole che vengono usate nella WAF di Azure per controllare gli attacchi, ad esempio lo scripting tra siti o l'avvelenamento dei cookie.

Applicare tag di nome di dominio completo (FQDN) alle applicazioni per semplificare l'impostazione delle regole dell'applicazione all'interno di un gruppo di sicurezza di rete. Dopo la configurazione delle regole di rete. Aggiungere una regola dell'applicazione usando un tag FQDN, ad esempio AzureKubernetesService, che include tutti i nomi di dominio completi necessari accessibili tramite la porta TCP 443 e la porta 80. 

- [Informazioni sulle procedure consigliate per la sicurezza e la connettività di rete in AKS](operator-best-practices-network.md)

- [Proteggere il traffico tra i pod usando criteri di rete nel servizio Azure Kubernetes](use-network-policies.md)

- [Come distribuire applicazione Azure Gateway (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida** : usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete associati alle istanze del servizio Kubernetes di Azure (AKS). I tag di servizio possono essere usati al posto di indirizzi IP specifici quando si creano regole di sicurezza per consentire o negare il traffico per il servizio corrispondente specificando il nome del tag di servizio. 

I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

Applicare un tag di Azure ai pool di nodi nel cluster AKS. Sono diversi dai tag del servizio di rete virtuale e vengono applicati a ogni nodo all'interno del pool di nodi e vengono mantenuti attraverso gli aggiornamenti. 

- [Comprendere e usare i tag di servizio](../virtual-network/service-tags-overview.md)

- [Informazioni su gruppi per AKS](support-policies.md)

- [Controllare il traffico in uscita per i nodi del cluster nel servizio Azure Kubernetes](limit-egress-traffic.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida** : definire e implementare configurazioni di sicurezza standard con criteri di Azure per le risorse di rete associate ai cluster del servizio Kubernetes di Azure (AKS). Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. servizio contenitore" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete dei cluster AKS. 

Usare anche le definizioni di criteri predefinite correlate a AKS, ad esempio:

• È necessario definire intervalli IP autorizzati nei servizi Kubernetes

• Applicare l'ingresso HTTPS nel cluster Kubernetes

• Assicurarsi che i servizi attendano solo le porte consentite nel cluster Kubernetes

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Esempi di criteri di Azure per la rete](../governance/policy/samples/built-in-policies.md#network)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni** : usare i tag per i gruppi di sicurezza di rete e altre risorse per il flusso del traffico da e verso i cluster di Azure Kubernetes Service (AKS). Usare il campo "Description" per le singole regole del gruppo di sicurezza di rete per specificare le esigenze aziendali e/o la durata e così via, per tutte le regole che consentono il traffico da e verso una rete.
Usare una qualsiasi delle definizioni correlate all'assegnazione di tag dei criteri di Azure, ad esempio "Richiedi tag e il relativo valore", che assicurano che tutte le risorse vengano create con tag e ricevano le notifiche per le risorse senza tag esistenti.

Scegliere di consentire o negare percorsi di rete specifici all'interno del cluster in base agli spazi dei nomi e ai selettori di etichette con i criteri di rete. Usare questi spazi dei nomi ed etichette come descrittori per le regole di configurazione del traffico. Usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Criteri di Azure con interfaccia della riga di comando](/cli/azure/policy?view=azure-cli-latest)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida** : usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate ai cluster di Azure Kubernetes Service (AKS). 

In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche. Qualsiasi voce dell'utente AzureContainerService nei log attività viene registrata come azione della piattaforma. 

Usare i log di monitoraggio di Azure per abilitare ed eseguire query sui log da AKS The Master Components, Kube-apiserver e Kube-Controller-Manager. Creare e gestire i nodi che eseguono kubelet con il runtime del contenitore e distribuire le applicazioni tramite il server API Kubernetes gestito. 

- [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md)

- [Abilitare e controllare i log del nodo master di Kubernetes nel servizio Azure Kubernetes](view-master-logs.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Linee guida** : i nodi del servizio Azure KUBERNETES (AKS) usano NTP.Ubuntu.com per la sincronizzazione dell'ora, insieme alla porta UDP 123 e al protocollo NTP (Network Time Protocol). 

Se si usano server DNS personalizzati, verificare che i server NTP siano accessibili dai nodi del cluster. 

- [Informazioni sui requisiti di dominio e porta NTP per i nodi del cluster AKS](limit-egress-traffic.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida** : abilitare i log di controllo dai componenti Master di Azure Kubernetes Services (AKS), Kube-apiserver e Kube-Controller-Manager, che vengono forniti come servizio gestito. 

• Kube-auditaksService: nome visualizzato nel log di controllo per l'operazione del piano di controllo (da hcpService) 

• MasterClient: il nome visualizzato nel log di controllo per MasterClientCertificate, il certificato ottenuto da AZ AKS Get-credentials 

• nodeclient: nome visualizzato per ClientCertificate, usato dai nodi di Agent

Abilitare anche altri log di controllo, ad esempio Kube-audit. 

Esportare questi log in Log Analytics o in un'altra piattaforma di archiviazione. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione.

Abilitare e caricare questi dati in Sentinel di Azure o in una SIEM di terze parti in base ai requisiti aziendali dell'organizzazione.

- [Esaminare lo schema del log, inclusi i ruoli del log qui](view-master-logs.md)

- [Informazioni sul monitoraggio di Azure per i contenitori](../azure-monitor/insights/container-insights-overview.md)

- [Come abilitare monitoraggio di Azure per i contenitori](../azure-monitor/insights/container-insights-onboard.md)

- [Abilitare e controllare i log del nodo master di Kubernetes nel servizio Azure Kubernetes](view-master-logs.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida** : usare i log attività per monitorare le azioni sulle risorse del servizio Azure KUBERNETES (AKS) per visualizzare tutte le attività e il relativo stato. Determinare quali operazioni sono state eseguite sulle risorse nella sottoscrizione con i log attività: chi ha avviato l'operazione

quando si è verificata l'operazione;

lo stato dell'operazione;

i valori delle altre proprietà che potrebbero essere utili per esaminare l'operazione.

Recuperare le informazioni dal log attività tramite Azure PowerShell, l'interfaccia della riga di comando di Azure (CLI), l'API REST di Azure o l'portale di Azure. 

Abilitare i log di controllo sui componenti Master AKS, ad esempio: 

• Kube-auditaksService: nome visualizzato nel log di controllo per l'operazione del piano di controllo (da hcpService) 

• MasterClient: il nome visualizzato nel log di controllo per MasterClientCertificate, il certificato ottenuto da AZ AKS Get-credentials 

• nodeclient: nome visualizzato per ClientCertificate, usato dai nodi di Agent

Attivare anche altri log di controllo, ad esempio Kube-audit. 

- [Come abilitare ed esaminare i log del nodo master di Kubernetes in AKS](view-master-logs.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Linee guida** : abilitare l'installazione automatica degli agenti di log Analytics per la raccolta dei dati dai nodi del cluster AKS. Inoltre, attivare il provisioning automatico dell'agente di monitoraggio di Azure Log Analytics dal centro sicurezza di Azure, come per impostazione predefinita, il provisioning automatico è disattivato. L'agente può essere installato anche manualmente. Con il provisioning automatico in, il Centro sicurezza distribuisce l'agente di Log Analytics in tutte le VM di Azure supportate e in quelle nuove che vengono create. Il Centro sicurezza raccoglie i dati da macchine virtuali (VM) di Azure, set di scalabilità di macchine virtuali e contenitori IaaS, ad esempio nodi del cluster Kubernetes, per monitorare le vulnerabilità e le minacce alla sicurezza. I dati vengono raccolti usando l'agente di Log Analytics di Azure, che legge diverse configurazioni e registri eventi relativi alla sicurezza dal computer e copia i dati nell'area di lavoro per l'analisi. 

La raccolta dei dati è necessaria per fornire visibilità sugli aggiornamenti mancanti, sulle impostazioni di sicurezza del sistema operativo, sullo stato di Endpoint Protection e sul rilevamento delle minacce e sull'integrità.

- [Come abilitare il provisioning automatico dell'agente di Log Analytics](../security-center/security-center-enable-data-collection.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Condiviso

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida** : caricare le istanze del servizio Kubernetes di Azure (AKS) in monitoraggio di Azure e impostare il periodo di conservazione dell'area di lavoro di Azure log Analytics corrispondente in base ai requisiti di conformità dell'organizzazione. 

- [Come impostare i parametri di conservazione dei log per le aree di lavoro Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida** : caricare le istanze del servizio Kubernetes di Azure (AKS) in monitoraggio di Azure e configurare le impostazioni di diagnostica per il cluster. 

Usare l'area di lavoro Log Analytics di monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log. I log di monitoraggio di Azure sono abilitati e gestiti nella portale di Azure o tramite l'interfaccia della riga di comando e funzionano con i cluster AKS con controllo degli accessi in base al ruolo di Azure (RBAC di Azure) e non RBAC abilitati.

Per la risoluzione dei problemi relativi all'applicazione e ai servizi, visualizzare i log generati dai componenti Master AKS (KUBE-apiserver e Kube-ControllerManager). Abilitare e caricare i dati in Sentinel di Azure o in un sistema SIEM di terze parti per la gestione e il monitoraggio centralizzati dei log.

- [Come abilitare ed esaminare i log del nodo master di Kubernetes in AKS](view-master-logs.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Indicazioni** : usare Azure Kubernetes Service (AKS) insieme al centro sicurezza per ottenere una maggiore visibilità sui nodi AKS. Esaminare gli avvisi del Centro sicurezza per le minacce e le attività dannose rilevate nell'host e a livello di cluster. Il Centro sicurezza implementa l'analisi continua degli eventi di sicurezza non elaborati che si verificano in un cluster AKS, ad esempio i dati di rete, la creazione di processi e il registro di controllo di Kubernetes. Determinare se questa attività è un comportamento previsto o se l'applicazione non funziona correttamente. Usare le metriche e i log in monitoraggio di Azure per convalidare i risultati. 

- [Informazioni sull'integrazione dei servizi Kubernetes di Azure con il Centro sicurezza](../security-center/defender-for-kubernetes-introduction.md)

- [Come abilitare il livello standard del Centro sicurezza di Azure](../security-center/security-center-get-started.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida** : installare e abilitare Microsoft anti-malware per Azure per le macchine virtuali di Azure Kubernetes Service (AKS) e i nodi del set di scalabilità di macchine virtuali. Esaminare gli avvisi nel centro sicurezza per la correzione.

- [Microsoft Antimalware per Servizi cloud e Macchine virtuali di Azure](../security/fundamentals/antimalware.md)

- [Guida di riferimento agli avvisi di sicurezza](../security-center/alerts-reference.md)

- [Avvisi per i contenitori-cluster del servizio Azure Kubernetes](../security-center/alerts-reference.md#alerts-akscluster)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida** : Azure Kubernetes Service (AKS) usa il progetto CoreDNS per la risoluzione e la gestione DNS del cluster.

Abilitare la registrazione delle query DNS applicando la configurazione documentata in coredns-Custom ConfigMap. 

- [Personalizzare CoreDNS con il servizio Azure Kubernetes](coredns-custom.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Linee guida** : usare kubectl, un client della riga di comando, in Azure Kubernetes Service (AKS) per gestire un cluster Kubernetes e ottenere i log dal nodo AKS a scopo di risoluzione dei problemi. Kubectl è già installato se si usa Azure Cloud Shell. Per installare kubectl localmente, usare il cmdlet ' install-AzAksKubectl '.

- [Guida introduttiva: distribuire un cluster del servizio Kubernetes di Azure con PowerShell](kubernetes-walkthrough-powershell.md)

- [Ottenere i log di kubelet dai nodi del cluster del servizio Azure Kubernetes](kubelet-logs.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni** : il servizio Azure KUBERNETES (AKS) non fornisce una soluzione di gestione delle identità che archivia gli account utente e le password normali. Con l'integrazione di Azure Active Directory (Azure AD), è possibile concedere a utenti o gruppi l'accesso alle risorse Kubernetes all'interno di uno spazio dei nomi o nel cluster. 

Eseguire query ad hoc per individuare gli account che sono membri dei gruppi amministrativi AKS con il modulo Azure AD PowerShell

Usare l'interfaccia della riga di comando di Azure per operazioni come ' ottenere le credenziali di accesso per un cluster Kubernetes gestito ' per facilitare la riconciliazione dell'accesso a intervalli regolari. Implementare questo processo per memorizzare un inventario aggiornato degli account del servizio, un altro tipo di utente primario in AKS. Applicare le raccomandazioni relative alla gestione delle identità e dell'accesso del Centro sicurezza.

- [Come integrare AKS con Azure AD](./azure-ad-integration-cli.md)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Come monitorare l'identità e l'accesso con il Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Indicazioni** : il servizio Azure KUBERNETES (AKS) non ha il concetto di password predefinite comuni e non fornisce una soluzione di gestione delle identità in cui è possibile archiviare gli account utente e le password normali. Con l'integrazione di Azure Active Directory (Azure AD), è possibile concedere l'accesso in base al ruolo alle risorse AKS all'interno di uno spazio dei nomi o nel cluster. 

Eseguire query ad hoc per individuare gli account che sono membri dei gruppi amministrativi AKS con il modulo Azure AD PowerShell

- [Informazioni sulle opzioni di accesso e identità per AKS](concepts-identity.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Linee guida** : integrare l'autenticazione utente per i cluster del servizio Azure KUBERNETES (AKS) con Azure Active Directory (Azure ad). Accedere a un cluster AKS usando un token di autenticazione Azure AD. Configurare il controllo degli accessi in base al ruolo Kubernetes (RBAC) per l'accesso amministrativo alle informazioni di configurazione Kubernetes (kubeconfig) e alle autorizzazioni, agli spazi dei nomi e alle risorse cluster. 

Creare criteri e procedure per l'utilizzo di account amministrativi dedicati. Implementare le raccomandazioni per la gestione dell'identità e dell'accesso del Centro sicurezza.

- [Come monitorare l'identità e l'accesso con il Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

- [Controllare l'accesso alle risorse cluster](azure-ad-rbac.md)

- [Usare i controlli degli accessi in base al ruolo di Azure](control-kubeconfig-access.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida** : usare Single Sign-on per il servizio Azure Kubernetes con l'autenticazione integrata Azure Active Directory (Azure ad) per un cluster AKS.

- [Come visualizzare i log Kubernetes, gli eventi e le metriche pod in tempo reale](../azure-monitor/insights/container-insights-livedata-overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Linee guida** : integrare l'autenticazione per il servizio Azure KUBERNETES (AKS) con Azure Active Directory (Azure ad). 

Abilitare Azure AD Multi-Factor Authentication (multi-factor authentication) e seguire le indicazioni relative alla gestione delle identità e degli accessi del Centro sicurezza.

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md) 

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida** : usare una workstation con accesso con privilegi (Paw), con multi-factor authentication (multi-factor authentication), configurato per accedere ai cluster di Azure Kubernetes Service (AKS) specificati e alle risorse correlate.
- [Informazioni sulle workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Indicazioni** : usare i report di sicurezza Azure Active Directory (Azure ad) con l'autenticazione Azure ad integrata per il servizio Azure KUBERNETES (AKS). Gli avvisi possono essere generati quando si verifica un'attività sospetta o non sicura nell'ambiente. Usare il Centro sicurezza per monitorare le attività di identità e accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità degli utenti e l'attività di accesso nel centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: gestire le risorse di Azure solo dalle località approvate

**Linee guida** : usare i percorsi denominati di accesso condizionale per consentire l'accesso ai cluster di Azure Kubernetes Service (AKS) solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree. Questa operazione richiede l'autenticazione integrata per AKS con Azure Active Directory (Azure AD).

Limitare l'accesso al server API AKS da un set limitato di intervalli di indirizzi IP, in quanto riceve le richieste per eseguire le azioni nel cluster per creare risorse o ridimensionare il numero di nodi. 

- [Proteggere l'accesso al server API usando gli intervalli di indirizzi IP autorizzati in Azure Kubernetes Service (AKS)](api-server-authorized-ip-ranges.md)

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida** : usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale per il servizio Azure KUBERNETES (AKS). Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito e Salt, hash e archivia in modo sicuro le credenziali utente.

Usare i ruoli predefiniti di AKS con il controllo degli accessi in base al ruolo di Azure (RBAC di Azure): collaboratore e proprietario dei criteri delle risorse per le operazioni di assegnazione dei criteri al cluster Kubernetes

- [Panoramica dei criteri di Azure](../governance/policy/overview.md)

- [Come integrare Azure AD con AKS](./azure-ad-integration-cli.md) 

- [Integrare Azure AD gestiti da AKS](managed-aad.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni** : usare i report di sicurezza Azure Active Directory (Azure ad) con l'autenticazione Azure ad integrata per il servizio Azure KUBERNETES (AKS). Eseguire ricerche nei log Azure AD per individuare gli account obsoleti. 

Eseguire le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. Correggere le raccomandazioni di identità e accesso dal centro sicurezza.

Tenere presente i ruoli usati a scopo di supporto o per la risoluzione dei problemi. Ad esempio, tutte le azioni del cluster eseguite dal supporto tecnico Microsoft (con consenso dell'utente) vengono effettuate con un ruolo predefinito di "modifica" di Kubernetes denominato AKS-support-Role. Il supporto AKS è abilitato con questo ruolo per modificare la configurazione e le risorse del cluster per risolvere i problemi e diagnosticare i problemi del cluster. Questo ruolo, tuttavia, non può modificare le autorizzazioni né creare ruoli o associazioni di ruolo. Questo accesso al ruolo viene abilitato solo in ticket di supporto attivi con accesso just-in-time (JIT).
 
- [Opzioni di accesso e identità per il servizio Azure Kubernetes](concepts-identity.md)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

- [Come monitorare l'identità dell'utente e l'attività di accesso nel centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida** : integrare l'autenticazione utente per il servizio Azure KUBERNETES (AKS) con Azure Active Directory (Azure ad). Creare le impostazioni di diagnostica per Azure AD, inviando i log di controllo e accesso a un'area di lavoro di Azure Log Analytics. Configurare gli avvisi desiderati, ad esempio quando un account disattivato tenta di accedere, all'interno di un'area di lavoro di Azure Log Analytics.
- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Come creare, visualizzare e gestire gli avvisi del log con monitoraggio di Azure](../azure-monitor/platform/alerts-log.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Linee guida** : integrare l'autenticazione utente per il servizio Azure KUBERNETES (AKS) con Azure Active Directory (Azure ad). Usare i rilevamenti dei rischi e la funzionalità di protezione delle identità di Azure AD per configurare risposte automatiche per rilevare azioni sospette correlate alle identità utente. Inserire i dati in Sentinel di Azure per ulteriori indagini in base alle esigenze aziendali.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida** : non applicabile al servizio Azure KUBERNETES (AKS) perché non è supportato da Customer Lockbox.
- [Elenco dei servizi Customer Lockbox supportati](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Non applicabile

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni** : usare i tag sulle risorse correlate alle distribuzioni di Azure Kubernetes Service (AKS) per semplificare il monitoraggio delle risorse di Azure che archiviano o elaborano informazioni riservate.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Aggiornare i tag per i cluster gestiti](/rest/api/aks/managedclusters/updatetags)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida** : isolare logicamente i team e i carichi di lavoro nello stesso cluster con il servizio Azure KUBERNETES (AKS) per fornire il minor numero di privilegi, con ambito alle risorse richieste da ogni team. 

Usare lo spazio dei nomi in Kubernetes per creare un limite di isolamento logico. Prendere in considerazione l'implementazione di funzionalità Kubernetes aggiuntive per l'isolamento e il multi-tenant, ad esempio la pianificazione, la rete, l'autenticazione/autorizzazione e i contenitori.

Implementare sottoscrizioni e/o gruppi di gestione distinti per ambienti di sviluppo, test e produzione. Separare i cluster AKS con la rete distribuendo tali cluster in reti virtuali distinte, che sono contrassegnate in modo appropriato.

- [Informazioni sulle procedure consigliate per l'isolamento del cluster in AKS](operator-best-practices-cluster-isolation.md)

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Informazioni sulle procedure consigliate per la sicurezza e la connettività di rete in AKS](operator-best-practices-network.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida** : usare una soluzione di terze parti da Azure Marketplace sui perimetri di rete che monitora il trasferimento non autorizzato di informazioni riservate e blocca tali trasferimenti mentre invia avvisi ai professionisti della sicurezza delle informazioni.

Microsoft gestisce la piattaforma sottostante e considera tutti i contenuti dei clienti come sensibili e passa a grandi lunghezze per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Elenco di porte, indirizzi e nomi di dominio obbligatori per la funzionalità AKS](limit-egress-traffic.md)

- [Come configurare le impostazioni di diagnostica per il firewall di Azure](../firewall/firewall-diagnostics.md)

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsibilità** : Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida** : creare un controller di ingresso HTTPS e usare i propri certificati TLS (o, facoltativamente, crittografare) per le distribuzioni del servizio Kubernetes di Azure (AKS). 

Per impostazione predefinita, il traffico in uscita Kubernetes viene crittografato tramite HTTPS/TLS. Esaminare il traffico in uscita potenzialmente non crittografato dalle istanze di AKS per un monitoraggio aggiuntivo. Questo può includere il traffico NTP, il traffico DNS, il traffico HTTP per il recupero di aggiornamenti in alcuni casi. 

- [Come creare un controller di ingresso HTTPS in AKS e usare i propri certificati TLS](ingress-own-tls.md)

- [Come creare un controller di ingresso HTTPS in AKS con la crittografia di Let](ingress-tls.md)

- [Elenco di porte e protocolli in uscita potenziali usati da AKS](limit-egress-traffic.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni** : le funzionalità di identificazione dei dati, classificazione e prevenzione della perdita non sono ancora disponibili per le risorse di calcolo o di archiviazione di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.
Microsoft gestisce la piattaforma sottostante e considera tutti i contenuti dei clienti come sensibili e passa a grandi lunghezze per difendersi dalla perdita di dati e dall'esposizione dei clienti. 

Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: usare RBAC di Azure per gestire l'accesso alle risorse

**Indicazioni** : usare il sistema di autorizzazione controllo degli accessi in base al ruolo di Azure (RBAC) basato su Azure Resource Manager per offrire una gestione degli accessi con granularità fine delle risorse di Azure.

Configurare Azure Kubernetes Service (AKS) per usare Azure Active Directory (Azure AD) per l'autenticazione utente. Accedere a un cluster AKS usando Azure AD token di autenticazione usando questa configurazione. 

Usare i ruoli predefiniti del servizio contenitore di Azure con controllo degli accessi in base al ruolo e proprietario dei criteri delle risorse per le operazioni di assegnazione dei criteri al cluster AKS

- [Come controllare l'accesso alle risorse del cluster usando RBAC e identità Azure AD di Azure in AKS](azure-ad-rbac.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni** : le funzionalità di identificazione dei dati, classificazione e prevenzione della perdita non sono ancora disponibili per le risorse di calcolo o di archiviazione di Azure. Implementare una soluzione di terze parti, se necessaria ai fini della conformità.
Microsoft gestisce la piattaforma sottostante e considera tutti i contenuti dei clienti come sensibili e passa a grandi lunghezze per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida** : i due tipi principali di archiviazione forniti per i volumi in Azure Kubernetes Service (AKS) sono supportati da dischi di azure o file di Azure. Entrambi i tipi di archiviazione usano Azure crittografia del servizio di archiviazione (SSE), che esegue la crittografia dei dati inattivi per migliorare la sicurezza. Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft.

La crittografia dei dati inattivi con chiavi gestite dal cliente è disponibile per la crittografia dei dischi del sistema operativo e dei dati nei cluster AKS per un maggiore controllo sulle chiavi di crittografia. I clienti sono responsabili delle attività di gestione delle chiavi, ad esempio il backup e la rotazione delle chiavi. Attualmente i dischi non possono essere crittografati mediante Crittografia dischi di Azure a livello di nodo del servizio Azure Kubernetes.

- [Procedure consigliate per archiviazione e backup nel servizio Azure Kubernetes](operator-best-practices-storage.md)

- [Usare le chiavi personalizzate (BYOK) con i dischi di Azure in Azure Kubernetes Service (AKS)](azure-disk-customer-managed-keys.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida** : usare monitoraggio di Azure per i contenitori per monitorare le prestazioni dei carichi di lavoro del contenitore distribuiti nei cluster Kubernetes gestiti ospitati nel servizio Azure KUBERNETES (AKS). 

Configurare gli avvisi per la creazione proattiva di notifiche o log quando l'utilizzo della CPU e della memoria nei nodi o nei contenitori supera le soglie definite o quando si verifica un cambiamento dello stato di integrità nel cluster in corrispondenza dell'infrastruttura o del rollup dello stato dei nodi. 

Usare log attività di Azure per monitorare i cluster AKS e le risorse correlate a un livello elevato. Eseguire l'integrazione con Prometeo per visualizzare le metriche dell'applicazione e del carico di lavoro che raccoglie da nodi e Kubernetes usando le query per creare avvisi personalizzati, dashboard ed eseguire analisi dettagliate.

- [Informazioni sul monitoraggio di Azure per i contenitori](../azure-monitor/insights/container-insights-overview.md)

- [Come abilitare monitoraggio di Azure per i contenitori](../azure-monitor/insights/container-insights-onboard.md)

- [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee guida** : usare il Centro sicurezza per monitorare le container Registry di Azure, incluse le istanze del servizio Azure KUBERNETES (AKS) per le vulnerabilità. Abilitare il bundle dei registri contenitori nel centro sicurezza per assicurarsi che il Centro sicurezza sia pronto per l'analisi delle immagini che vengono inserite nel registro di sistema.

Ricevere una notifica nel dashboard del Centro sicurezza quando vengono rilevati problemi dopo che il Centro sicurezza ha analizzato l'immagine usando Qualys. La funzionalità bundle registri contenitori offre una maggiore visibilità sulle vulnerabilità delle immagini usate nei registri basati su Azure Resource Manager. 

Usare il Centro sicurezza per le raccomandazioni di utilità pratica per ogni vulnerabilità. Questi consigli includono una classificazione di gravità e linee guida per la correzione. 

- [Procedure consigliate per la gestione e la sicurezza delle immagini del contenitore nel servizio Azure Kubernetes](../security-center/defender-for-container-registries-introduction.md)

- [Informazioni sulle procedure consigliate per la gestione e la sicurezza delle immagini del contenitore in AKS](operator-best-practices-container-image-management.md)

- [Informazioni sull'integrazione del registro contenitori con il Centro sicurezza di Azure](../security-center/defender-for-container-registries-introduction.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Linee guida** : gli aggiornamenti della sicurezza vengono applicati automaticamente ai nodi Linux per proteggere i cluster del servizio Azure KUBERNETES (AKS) del cliente. Questi aggiornamenti includono correzioni della sicurezza del sistema operativo o gli aggiornamenti del kernel. 

Si noti che il processo per tenere aggiornati i nodi di Windows Server differisce da quelli che eseguono Linux perché i nodi di Windows Server non ricevono aggiornamenti giornalieri. Al contrario, i clienti devono eseguire un aggiornamento sui pool di nodi di Windows Server nei cluster AKS che distribuiscono nuovi nodi con l'immagine e le patch di base di Windows Server usando il pannello di controllo di Azure o l'interfaccia della riga di comando di Azure. Questi aggiornamenti contengono miglioramenti alla sicurezza o alle funzionalità per AKS.

- [Informazioni sul modo in cui vengono applicati gli aggiornamenti ai nodi del cluster AKS che eseguono Linux](node-updates-kured.md)

- [Come aggiornare un pool di nodi AKS per i cluster AKS che usano nodi di Windows Server](use-multiple-node-pools.md#upgrade-a-node-pool)

- [Aggiornamenti dell'immagine del nodo del servizio Kubernetes di Azure (AKS)](node-image-upgrade.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: distribuire una soluzione di gestione delle patch automatizzata per i titoli software di terze parti

**Linee guida** : implementare un processo manuale per verificare che le applicazioni di terze parti del nodo del cluster di Azure Kubernetes Service (AKS) rimangano valide per la durata della durata del cluster. Questa operazione può richiedere l'abilitazione di aggiornamenti automatici, il monitoraggio dei nodi o l'esecuzione di riavvii periodici.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee guida** : esportare i risultati dell'analisi del Centro sicurezza a intervalli coerenti e confrontare i risultati per verificare che le vulnerabilità siano state corrette. 

Usare il cmdlet di PowerShell "Get-AzSecurityTask" per automatizzare il recupero delle attività di sicurezza consigliate dal centro sicurezza per rafforzare il comportamento di sicurezza e i risultati dell'analisi delle vulnerabilità di correzione.

- [Come usare PowerShell per visualizzare le vulnerabilità individuate dal centro sicurezza di Azure](/powershell/module/az.security/get-azsecuritytask?view=azps-3.3.0)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni** : usare la classificazione di gravità fornita dal centro sicurezza per definire le priorità della correzione delle vulnerabilità. 

Usare il sistema di valutazione delle vulnerabilità comuni (CVSS) (o un altro sistema di assegnazione dei punteggi fornito dallo strumento di analisi) se si usa uno strumento di valutazione della vulnerabilità incorporato, ad esempio Qualys o Rapid7, offerto da Azure.

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida** : usare Azure Resource Graph per eseguire query e individuare tutte le risorse, ad esempio calcolo, archiviazione, rete e così via, all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure classiche possano essere individuate tramite Graph di risorse, è consigliabile creare e usare risorse basate su Azure Resource Manager in futuro.

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee guida** : applicare i tag alle risorse di Azure con i metadati per organizzarli in modo logico in una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni** : usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia degli asset. 

Applicare macchie, etichette o tag quando si crea un pool di nodi del servizio Kubernetes di Azure (AKS). Tutti i nodi all'interno del pool di nodi erediteranno anche tale macchia, etichetta o tag.

Macchie, etichette o tag possono essere usati per riconciliare l'inventario a intervalli regolari e garantire che le risorse non autorizzate vengano eliminate in modo tempestivo dalle sottoscrizioni.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e tag utente](../azure-resource-manager/management/tag-resources.md)

- [Cluster gestiti-Aggiorna Tag](/rest/api/aks/managedclusters/updatetags)

- [Specificare un Taint, un'etichetta o un tag per un pool di nodi](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definire e gestire un inventario delle risorse di Azure approvate

**Linee guida** : definire un elenco di risorse di Azure approvate e software approvato per le risorse di calcolo in base alle esigenze aziendali dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida** : usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:
-   Tipi di risorse non consentiti 

-   Tipi di risorse consentiti

Usare il grafico risorse di Azure per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Assicurarsi che tutte le risorse di Azure presenti nell'ambiente siano approvate in base ai requisiti aziendali dell'organizzazione.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida** : usare rilevamento modifiche di automazione di Azure e le funzionalità di inventario per individuare il software installato nell'ambiente. 

Raccolta e visualizzazione dell'inventario per software, file, Daemon Linux, servizi Windows e chiavi del registro di sistema di Windows nei computer e monitoraggio di applicazioni software non approvate. 

Tieni traccia delle configurazioni dei tuoi computer per aiutare a individuare i problemi operativi nell'ambiente e a comprendere meglio lo stato dei computer.

- [Come abilitare l'inventario delle macchine virtuali di Azure](../automation/automation-tutorial-installed-software.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida** : usare rilevamento modifiche di automazione di Azure e le funzionalità di inventario per individuare il software installato nell'ambiente. 

Raccolta e visualizzazione dell'inventario per software, file, Daemon Linux, servizi Windows e chiavi del registro di sistema di Windows nei computer e monitoraggio di applicazioni software non approvate. 

Tieni traccia delle configurazioni dei tuoi computer per aiutare a individuare i problemi operativi nell'ambiente e a comprendere meglio lo stato dei computer.

- [Come abilitare l'inventario delle macchine virtuali di Azure](../automation/automation-tutorial-installed-software.md)

- [Come usare il monitoraggio dell'integrità dei file](../security-center/security-center-file-integrity-monitoring.md)

- [Informazioni su Rilevamento modifiche di Azure](../automation/change-tracking/overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Linee guida** : usare rilevamento modifiche di automazione di Azure e le funzionalità di inventario per individuare il software installato nell'ambiente. 

Raccolta e visualizzazione dell'inventario per software, file, Daemon Linux, servizi Windows e chiavi del registro di sistema di Windows nei computer e monitoraggio di applicazioni software non approvate. 

Tieni traccia delle configurazioni dei tuoi computer per aiutare a individuare i problemi operativi nell'ambiente e a comprendere meglio lo stato dei computer.

Abilitare l'analisi adattiva delle applicazioni nel centro sicurezza per le applicazioni esistenti nell'ambiente in uso.

- [Come abilitare l'inventario delle macchine virtuali di Azure](../automation/automation-tutorial-installed-software.md)

 
Come usare l'applicazione adattiva del Centro sicurezza di Azure
- [Controlli](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida** : usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare il grafico risorse di Azure per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/index.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

**Linee guida** : usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni usando le definizioni dei criteri predefinite.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni** : usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.
- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni** : il servizio Azure KUBERNETES (AKS) non fornisce una soluzione di gestione delle identità in cui vengono archiviati gli account utente e le password normali. Usare invece Azure Active Directory (Azure AD) come soluzione di identità integrata per i cluster AKS. 

Concedere agli utenti o ai gruppi l'accesso alle risorse Kubernetes all'interno di uno spazio dei nomi o nel cluster usando Azure AD l'integrazione. 

Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri dei gruppi amministrativi AKS e usarli per riconciliare l'accesso a intervalli regolari. Usare l'interfaccia della riga di comando di Azure per operazioni come ' ottenere le credenziali di accesso per un cluster Kubernetes gestito. Implementare le raccomandazioni per la gestione dell'identità e dell'accesso del Centro sicurezza.

- [Gestire AKS con l'interfaccia della riga di comando di Azure](/cli/azure/aks?view=azure-cli-latest)

- [Informazioni sull'integrazione di AKS e Azure AD](concepts-identity.md)

- [Come integrare AKS con Azure AD](./azure-ad-integration-cli.md)

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Come monitorare l'identità e l'accesso con il Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida** : usare le funzionalità di Azure Kubernetes Service (AKS) per isolare logicamente i team e i carichi di lavoro nello stesso cluster per il minor numero di privilegi, con ambito per le risorse richieste da ogni team. 

Implementare lo spazio dei nomi in Kubernetes per creare un limite di isolamento logico. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. servizio contenitore" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze del servizio Kubernetes di Azure (AKS). 

Esaminare e implementare le funzionalità e le considerazioni aggiuntive di Kubernetes per l'isolamento e la multi-tenant per includere le informazioni seguenti: pianificazione, rete, autenticazione/autorizzazione e contenitori. Usare inoltre sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Separare i cluster AKS con le reti virtuali, le subnet contrassegnate in modo appropriato e protette con un Web Application Firewall (WAF).

- [Informazioni sulle procedure consigliate per l'isolamento del cluster in AKS](operator-best-practices-cluster-isolation.md)

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Informazioni sulle procedure consigliate per la sicurezza e la connettività di rete in AKS](operator-best-practices-network.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida** : usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. servizio contenitore" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze del servizio Kubernetes di Azure (AKS). Usare le definizioni di criteri di Azure predefinite.

Esempi di definizioni di criteri predefinite per AKS includono:

• Applicare l'ingresso HTTPS nel cluster Kubernetes

• È necessario definire intervalli IP autorizzati nei servizi Kubernetes

• È necessario usare il controllo di accesso Role-Based (RBAC) nei servizi Kubernetes

• Verificare solo le immagini contenitore consentite nel cluster Kubernetes

Esportare un modello della configurazione di AKS in JavaScript Object Notation (JSON) con Azure Resource Manager. Esaminarlo periodicamente per assicurarsi che queste configurazioni soddisfino i requisiti di sicurezza per l'organizzazione. Usare le raccomandazioni del Centro sicurezza di Azure come linea di base di configurazione sicura per le risorse di Azure. 

- [Come configurare e gestire i criteri di sicurezza di AKS Pod](use-pod-security-policies.md)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Linee guida** : i cluster Azure Kubernetes cluster (AKS) vengono distribuiti nelle macchine virtuali host con un sistema operativo con ottimizzazione per la sicurezza. Il sistema operativo host prevede procedure aggiuntive per la protezione avanzata incorporate per ridurre la superficie di attacco e consentire la distribuzione dei contenitori in modo sicuro. 

Azure applica patch giornaliere (incluse le patch di sicurezza) agli host di macchine virtuali AKS con alcune patch che richiedono un riavvio. I clienti sono responsabili della pianificazione dei riavvii degli host di macchine virtuali AKS in base alle esigenze. 

- [Protezione avanzata per il sistema operativo host del nodo AKS Agent](security-hardened-vm-host-image.md)

- [Informazioni sulla protezione avanzata degli host di macchine virtuali AKS](security-hardened-vm-host-image.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida** : proteggere il cluster Azure Kubernetes Service (AKS) usando i criteri di sicurezza pod.  Limitare i pod che possono essere pianificati per migliorare la sicurezza del cluster. 

I pod che richiedono risorse non consentite non possono essere eseguiti nel cluster AKS. 

Usare anche i criteri di Azure [deny] e gli effetti [Distribuisci se non esistono] per applicare impostazioni sicure per le risorse di Azure correlate alle distribuzioni AKS (ad esempio, reti virtuali, subnet, firewall di Azure, account di archiviazione e così via). 

Creare definizioni di criteri di Azure personalizzate usando gli alias degli spazi dei nomi seguenti: 

• Microsoft. servizio contenitore

• Microsoft. Network

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Linee guida** : i cluster del servizio Azure KUBERNETES (AKS) vengono distribuiti nelle macchine virtuali host con un sistema operativo con ottimizzazione per la sicurezza. Il sistema operativo host prevede procedure aggiuntive per la protezione avanzata incorporate per ridurre la superficie di attacco e consentire la distribuzione dei contenitori in modo sicuro. 

Fare riferimento all'elenco di controlli di sicurezza di rete (SID) di Center integrati nel sistema operativo host.  

- [Protezione avanzata per il sistema operativo host del nodo AKS Agent](security-hardened-vm-host-image.md)

- [Comprendere la configurazione dello stato dei cluster AKS](concepts-clusters-workloads.md#control-plane)

- [Informazioni sulla protezione avanzata degli host di macchine virtuali AKS](security-hardened-vm-host-image.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Indicazioni** : usare Azure Repos per archiviare e gestire in modo sicuro le configurazioni se si usano definizioni di criteri di Azure personalizzate. Esportare un modello della configurazione del servizio Kubernetes di Azure (AKS) in JavaScript Object Notation (JSON) con Azure Resource Manager. Esaminarlo periodicamente per assicurarsi che le configurazioni soddisfino i requisiti di sicurezza per l'organizzazione. 

Implementare soluzioni di terze parti, ad esempio la bonifica, per creare un file di configurazione che dichiara le risorse per il cluster Kubernetes. È possibile rafforzare la distribuzione di AKS implementando le procedure di sicurezza consigliate e archiviando la configurazione come codice in un percorso protetto.

- [Definire un cluster Kubernetes](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

Protezione avanzata per il sistema operativo host del nodo AKS Agent

security-hardened-vm-host-image.md

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Linee guida** : non applicabile al servizio Azure KUBERNETES (AKS). AKS fornisce un sistema operativo host con ottimizzazione per la sicurezza per impostazione predefinita. Non esiste alcuna opzione corrente per selezionare un sistema operativo alternativo o personalizzato.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida** : usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni usando le definizioni di criteri predefinite e gli alias di criteri di Azure nello spazio dei nomi "Microsoft. servizio contenitore". 

Creare criteri personalizzati da controllare e applicare le configurazioni di sistema. Sviluppare un processo e una pipeline per la gestione delle eccezioni dei criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come usare gli alias](../governance/policy/concepts/definition-structure.md#aliases)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Linee guida** : i cluster del servizio Azure KUBERNETES (AKS) vengono distribuiti nelle macchine virtuali host con un sistema operativo con ottimizzazione per la sicurezza. Il sistema operativo host prevede procedure aggiuntive per la protezione avanzata incorporate per ridurre la superficie di attacco e consentire la distribuzione dei contenitori in modo sicuro. 

Fare riferimento all'elenco di controlli di sicurezza di rete (SID) di Center integrati negli host AKS.  

- [Protezione avanzata per il sistema operativo host del nodo AKS Agent](security-hardened-vm-host-image.md)

- [Informazioni sulla protezione avanzata degli host di macchine virtuali AKS](security-hardened-vm-host-image.md)

- [Comprendere la configurazione dello stato dei cluster AKS](concepts-clusters-workloads.md#control-plane)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida** : usare il Centro sicurezza per eseguire analisi di base per le risorse correlate alle distribuzioni del servizio Kubernetes di Azure (AKS). Gli esempi di risorse includono ma non sono limitati al cluster AKS, alla rete virtuale in cui è stato distribuito il cluster AKS, all'account di archiviazione di Azure usato per tenere traccia dello stato di bonifica o Azure Key Vault istanze usate per le chiavi di crittografia per i dischi dati e il sistema operativo del cluster AKS.

- [Come correggere le raccomandazioni nel centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Indicazioni** : usare le raccomandazioni del contenitore del Centro sicurezza nella &amp; sezione "app di calcolo" per eseguire analisi di base per i cluster del servizio Azure Kubernetes (AKS). Ricevere una notifica nel dashboard del Centro sicurezza quando vengono rilevati problemi di configurazione o vulnerabilità. Questa operazione richiede l'abilitazione del bundle facoltativo dei registri contenitori, che consente al centro sicurezza di eseguire l'analisi dell'immagine.  

- [Informazioni sulle raccomandazioni per i contenitori nel Centro sicurezza di Azure](../security-center/container-security.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida** : integrare Azure Key Vault con un cluster di Azure Kubernetes Service (AKS) usando un'unità FlexVolume. Usare Azure Key Vault per archiviare e ruotare regolarmente i segreti, ad esempio credenziali, chiavi dell'account di archiviazione o certificati. Questo driver consente al cluster del servizio Azure Kubernetes di recuperare le credenziali in modo nativo da Azure Key Vault e fornirle in tutta sicurezza solo al pod che le ha richieste. Usare un'identità gestita da Pod per richiedere l'accesso a Key Vault e recuperare le credenziali necessarie tramite il driver FlexVolume. Assicurarsi che l'eliminazione temporanea di Azure Key Vault sia abilitata. 

Limitare l'esposizione delle credenziali non definendo le credenziali nel codice dell'applicazione. 

Evitare l'utilizzo di credenziali fisse o condivise. 

- [Concetti relativi alla sicurezza per le applicazioni e i cluster nel servizio Azure Kubernetes](concepts-security.md)

- [Come usare Key Vault con il cluster AKS](developer-best-practices-pod-security.md#limit-credential-exposure)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida** : non definire le credenziali nel codice dell'applicazione come procedura di sicurezza consigliata. Usare le identità gestite per le risorse di Azure per consentire a un pod di eseguire l'autenticazione in qualsiasi servizio di Azure che la supporti, incluso Azure Key Vault. Al Pod viene assegnata un'identità di Azure per l'autenticazione Azure Active Directory (Azure AD) e viene ricevuto un token digitale che può essere presentato ad altri servizi di Azure che controllano se il Pod è autorizzato ad accedere al servizio ed eseguire le azioni necessarie. 

Si noti che le identità gestite da Pod sono destinate all'uso solo con i pod Linux e le immagini del contenitore. Eseguire il provisioning Azure Key Vault per archiviare e recuperare le chiavi e le credenziali digitali. Chiavi come quelle usate per crittografare i dischi del sistema operativo, i dati del cluster AKS possono essere archiviati in Azure Key Vault.

Le entità servizio possono anche essere usate nei cluster AKS. Tuttavia, i cluster che usano entità servizio possono raggiungere uno stato in cui l'entità servizio deve essere rinnovata per far funzionare il cluster. La gestione delle entità servizio aggiunge complessità, motivo per cui è più facile usare le identità gestite. Gli stessi requisiti di autorizzazione si applicano sia per le entità servizio sia per le identità gestite.

- [Informazioni sulle identità gestite e Key Vault con il servizio Azure Kubernetes (AKS)](developer-best-practices-pod-security.md#limit-credential-exposure)

- [Identità di pod di Azure Active Directory](https://github.com/Azure/aad-pod-identity)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni** : implementare Credential Scanner per identificare le credenziali all'interno del codice. Credential scanner consiglia inoltre di trasferire le credenziali individuate a posizioni più sicure, ad esempio Azure Key Vault con raccomandazioni.

Limitare l'esposizione delle credenziali non definendo le credenziali nel codice dell'applicazione. ed evitare l'utilizzo di credenziali condivise. Per archiviare e recuperare le chiavi e le credenziali digitali, è necessario utilizzare Azure Key Vault. Usare le identità gestite per le risorse di Azure per consentire al pod di richiedere l'accesso ad altre risorse. 

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Procedure consigliate per gli sviluppatori per la sicurezza Pod](developer-best-practices-pod-security.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Malware Defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: usare il software antimalware gestito centralmente

**Linee guida** : AKS gestisce il ciclo di vita e le operazioni dei nodi dell'agente per conto dell'utente. la modifica delle risorse IaaS associate ai nodi dell'agente non è supportata. Tuttavia, per i nodi Linux è possibile usare i set di daemon per installare software personalizzato come una soluzione antimalware.

- [Guida di riferimento agli avvisi di sicurezza](../security-center/alerts-reference.md)

- [Avvisi per i contenitori-cluster del servizio Azure Kubernetes](../security-center/alerts-reference.md#alerts-akscluster)

- [Responsabilità condivisa AKS e set di DAEMON](support-policies.md#shared-responsibility)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida** : pre-analisi di tutti i file caricati nelle risorse di AKS. Usare il rilevamento delle minacce del Centro sicurezza per i servizi dati per rilevare il malware caricato negli account di archiviazione se si usa un account di archiviazione di Azure come archivio dati o per tenere traccia dello stato di bonifica per il cluster AKS. 

- [Informazioni sul rilevamento delle minacce del Centro sicurezza di Azure per i servizi dati](../security-center/azure-defender.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: assicurarsi che il software e le firme antimalware siano aggiornati

**Linee guida** : AKS gestisce il ciclo di vita e le operazioni dei nodi dell'agente per conto dell'utente. la modifica delle risorse IaaS associate ai nodi dell'agente non è supportata. Tuttavia, per i nodi Linux è possibile usare i set di daemon per installare software personalizzato come una soluzione antimalware.

- [Guida di riferimento agli avvisi di sicurezza](../security-center/alerts-reference.md)

- [Avvisi per i contenitori-cluster del servizio Azure Kubernetes](../security-center/alerts-reference.md#alerts-akscluster)

- [Responsabilità condivisa AKS e set di DAEMON](support-policies.md#shared-responsibility)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Linee guida** : eseguire il backup dei dati usando uno strumento appropriato per il tipo di archiviazione, ad esempio Velero, che può eseguire il backup di volumi permanenti insieme a risorse e configurazioni aggiuntive del cluster. Periodicamente, verificare l'integrità e la sicurezza di tali backup. 

Rimuovere lo stato dalle applicazioni prima del backup. Nei casi in cui non è possibile eseguire questa operazione, eseguire il backup dei dati da volumi permanenti e testare regolarmente le operazioni di ripristino per verificare l'integrità dei dati e i processi necessari.

- [Procedure consigliate per l'archiviazione e i backup in AKS](operator-best-practices-storage.md)

- [Procedure consigliate per la continuità aziendale e il ripristino di emergenza in AKS](operator-best-practices-multi-region.md)

- [Informazioni Azure Site Recovery](../site-recovery/site-recovery-overview.md)

- [Come configurare Velero in Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida** : eseguire il backup dei dati usando uno strumento appropriato per il tipo di archiviazione, ad esempio Velero, che può eseguire il backup di volumi permanenti insieme a risorse e configurazioni aggiuntive del cluster. 

Esegui backup automatici regolari di Key Vault certificati, chiavi, account di archiviazione gestiti e segreti con i comandi di PowerShell. 

Ad esempio:

Backup-AzKeyVaultCertificate Backup-AzKeyVaultKey Backup-AzKeyVaultManagedStorageAccount Backup-AzKeyVaultSecret

- [Come eseguire il backup di certificati Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Come eseguire il backup di Key Vault account di archiviazione gestiti](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Come eseguire il backup di Key Vault segreti](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [Come abilitare backup di Azure](../backup/index.yml)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida** : eseguire periodicamente il ripristino dei dati del contenuto all'interno di Velero backup. Se necessario, testare il ripristino in una rete virtuale isolata.

Eseguire periodicamente il ripristino dei dati di Key Vault certificati, chiavi, account di archiviazione gestiti e segreti con i comandi di PowerShell. 

Ad esempio:

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret

- [Come ripristinare Key Vault certificati](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

- [Come ripristinare chiavi di Key Vault](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

- [Come ripristinare Key Vault account di archiviazione gestiti](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Come ripristinare Key Vault segreti](/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0)

- [Come ripristinare i file dal backup della macchina virtuale di Azure](../backup/backup-azure-restore-files-from-vm.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida** : eseguire il backup dei dati usando uno strumento appropriato per il tipo di archiviazione, ad esempio Velero, che può eseguire il backup di volumi permanenti insieme a risorse e configurazioni aggiuntive del cluster. 

Abilitare Soft-Delete in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose se Azure Key Vault viene usato con per le distribuzioni di Azure Kubernetes Service (AKS).

- [Informazioni su crittografia del servizio di archiviazione di Azure](../storage/common/storage-service-encryption.md)

- [Come abilitare l'eliminazione temporanea in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni** : creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Linee guida** : definire le priorità degli avvisi che devono essere esaminati prima con il Centro sicurezza assegnato agli avvisi. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.
Contrassegnare chiaramente le sottoscrizioni, ad esempio produzione e non di produzione, e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni** : con cadenza regolare, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi. Fare riferimento alla pubblicazione del NIST per identificare i punti deboli e i gap e rivedere il piano in base alle esigenze.

- [Guida ai programmi di test, formazione e esercizio per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni** : le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida** : esportare avvisi e consigli del Centro sicurezza usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È anche possibile scegliere il connettore dati del Centro sicurezza per trasmettere gli avvisi ad Azure Sentinel in base ai requisiti aziendali dell'organizzazione.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni** : usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite App per la logica negli avvisi di sicurezza e nelle raccomandazioni.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Indicazioni** : seguire le regole Microsoft per assicurarsi che i propri test di penetrazione non violino i criteri Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [È possibile trovare altre informazioni sulla strategia Microsoft e sull'esecuzione dei test di penetrazione Red Teaming sull'infrastruttura cloud gestita da Microsoft, sui servizi e sulle applicazioni qui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)
