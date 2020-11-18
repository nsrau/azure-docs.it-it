---
title: Baseline della sicurezza di Azure per Azure Load Balancer
description: La linea di base di sicurezza Azure Load Balancer fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 07dd369ceeefee85f4d9180a0b9b33a0d4c8bbdb
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698207"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Baseline della sicurezza di Azure per Azure Load Balancer

La linea di base di sicurezza di Azure per Microsoft Azure Load Balancer contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione. La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](../security/benchmarks/overview.md), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft. Per altre informazioni, vedere [Panoramica delle baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: usare i bilanciamento del carico di Azure interni per consentire solo il traffico verso le risorse back-end da determinate reti virtuali o reti virtuali con peering senza esposizione a Internet. Implementare un Load Balancer esterno con SNAT (Source Network Address Translation) per mascherare gli indirizzi IP delle risorse back-end per la protezione dall'esposizione diretta a Internet.

Azure offre due tipi di Load Balancer offerte, standard e Basic. Usare il Load Balancer Standard per tutti i carichi di lavoro di produzione. Implementare i gruppi di sicurezza di rete e consentire l'accesso solo alle porte attendibili e agli intervalli di indirizzi IP dell'applicazione. Nei casi in cui non è stato assegnato alcun gruppo di sicurezza di rete alla subnet back-end o alla scheda di interfaccia di rete delle macchine virtuali back-end, il traffico non sarà consentito per queste risorse dal servizio di bilanciamento del carico. Con i bilanciamenti del carico standard, fornire regole in uscita per definire NAT in uscita con un gruppo di sicurezza di rete. Esaminare le regole in uscita per ottimizzare il comportamento delle connessioni in uscita. 

L'uso di un Load Balancer Standard è consigliato per i carichi di lavoro di produzione e in genere il Load Balancer di base viene usato solo per i test, poiché il tipo di base è aperto per le connessioni da Internet per impostazione predefinita e non richiede gruppi di sicurezza di rete per l'operazione. 

- [Connessioni in uscita in Azure](load-balancer-outbound-connections.md)

- [Aggiornare Load Balancer pubblico di Azure](./upgrade-basic-standard.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

**Indicazioni**: il Load Balancer è un servizio pass-through poiché si basa sulle regole dei gruppi di sicurezza di rete applicate alle risorse back-end e sulle regole in uscita configurate per controllare l'accesso a Internet.

Esaminare le regole in uscita configurate per il Load Balancer Standard tramite il pannello regole in uscita della Load Balancer e il pannello regole di bilanciamento del carico in cui è possibile abilitare regole in uscita implicite.

Monitorare il numero di connessioni in uscita per tenere traccia della frequenza con cui le risorse stanno raggiungendo Internet. 

Usare il Centro sicurezza e seguire le raccomandazioni per la protezione della rete per proteggere le risorse di rete di Azure.

Seguire le raccomandazioni di sicurezza per le risorse back-end e abilitare i log di flusso dei gruppi di sicurezza di rete e inviare i log a un account di archiviazione di Azure per il controllo.

Inviare anche i log di flusso a un'area di lavoro Log Analytics e quindi usare Analisi del traffico per fornire informazioni dettagliate sui modelli di traffico nel cloud di Azure. I vantaggi di Analisi del traffico includono la possibilità di visualizzare l'attività di rete, identificare le aree sensibili e le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete non configurate.

- [Come abilitare i log dei flussi dei gruppi di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

- [Ricerca per categorie controllare le statistiche della connessione in uscita](./load-balancer-standard-diagnostics.md#how-do-i-check-my-outbound-connection-statistics)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Indicazioni**: definire in modo esplicito la connettività Internet e gli indirizzi IP di origine validi tramite le regole in uscita e i gruppi di sicurezza di rete con i Load Balancer per usare l'Intelligence per le minacce di Microsoft per la protezione delle applicazioni Web.

- [Integrare il firewall di Azure](../firewall/integrate-lb.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare la protezione standard DDoS (Distributed Denial of Service) di Azure nella rete virtuale di Azure per proteggersi da attacchi DDoS. 

Distribuire il firewall di Azure a ogni limite di rete dell'organizzazione con il filtro basato su Intelligence per le minacce abilitato e configurato per "avviso e negazione" per il traffico di rete dannoso.

 

Usare la protezione dalle minacce del Centro sicurezza per rilevare le comunicazioni con indirizzi IP dannosi noti. 

Il Load Balancer Standard è progettato per essere sicuro per impostazione predefinita e parte di una rete virtuale privata e isolata. Viene chiuso nei flussi in ingresso, a meno che non vengano aperti da gruppi di sicurezza di rete per consentire esplicitamente il traffico consentito e non consentire indirizzi IP dannosi noti. A meno che non esista un gruppo di sicurezza di rete in una subnet o una scheda di interfaccia di rete della risorsa della macchina virtuale dietro la Load Balancer, il traffico non è autorizzato a raggiungere questa risorsa. 

Distribuire il firewall di Azure a ogni limite di rete dell'organizzazione con il filtro basato su Intelligence per le minacce abilitato e configurato per "avvisare e negare" per il traffico di rete dannoso per impedire gli attacchi da indirizzi IP dannosi. 

Implementare informazioni aggiuntive per integrare il firewall di Azure con il Load Balancer.

Usare le funzionalità di protezione dalle minacce del Centro sicurezza per rilevare le comunicazioni con indirizzi IP dannosi noti. 

Il Centro sicurezza (livello standard) fornisce l'accesso just-in-time alle macchine virtuali e configura gli indirizzi IP di origine consentiti per consentire l'accesso solo da indirizzi o intervalli IP approvati.
 

Usare la funzionalità di protezione avanzata della rete adattiva del Centro sicurezza per consigliare le configurazioni dei gruppi di sicurezza di rete che limitano le porte e gli indirizzi IP di origine in base al traffico effettivo e alle informazioni sulle
 

- [Gestire Protezione DDoS di Azure Standard nel portale di Azure](../ddos-protection/manage-ddos-protection.md)

- [Filtro basato sull'intelligence sulle minacce del firewall di Azure](../firewall/threat-intel.md)

- [Protezione dalle minacce nel Centro sicurezza di Azure](../security-center/azure-defender.md)

- [Proteggere le porte di gestione con l'accesso JIT](../security-center/security-center-just-in-time.md)

- [Protezione avanzata della rete adattiva nel centro sicurezza di Azure](../security-center/security-center-adaptive-network-hardening.md)

- [Integrare il firewall di Azure con il Load Balancer](../firewall/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Indicazioni**: abilitare l'acquisizione di pacchetti Network Watcher per analizzare le attività anomale.

- [Come creare un'istanza di Network Watcher](../network-watcher/network-watcher-create.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida**: implementare un'offerta da Azure Marketplace che supporta la funzionalità di ID/IP con funzionalità di ispezione del payload per l'ambiente della Load Balancer. 

Usare l'Intelligence per le minacce di Azure firewall se l'ispezione del payload non è un requisito. Il filtro basato su Intelligence per le minacce del firewall di Azure viene usato per segnalare e/o bloccare il traffico da e verso domini e indirizzi IP dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

Distribuire la soluzione firewall scelta a ogni limite di rete dell'organizzazione per rilevare e/o bloccare il traffico dannoso.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Come configurare gli avvisi con il firewall di Azure](../firewall/threat-intel.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Indicazioni**: definire in modo esplicito la connettività Internet e gli indirizzi IP di origine validi tramite le regole in uscita e i gruppi di sicurezza di rete con i Load Balancer per usare le funzionalità di intelligence per le minacce di Microsoft per proteggere le applicazioni Web

- [Integrare il firewall di Azure](../firewall/integrate-lb.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag di servizio al posto di indirizzi IP specifici durante la creazione delle regole di sicurezza. Specificare il nome del tag di servizio nel campo di origine o di destinazione di una regola per consentire o negare il traffico per il servizio corrispondente. 

I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi. 

Per impostazione predefinita, ogni gruppo di sicurezza di rete include il tag del servizio AzureLoadBalancer per consentire il traffico del probe di integrità. 

Vedere la documentazione di Azure per tutti i tag di servizio disponibili per l'uso nelle regole del gruppo di sicurezza di rete.

- [Tag del servizio disponibili](../virtual-network/service-tags-overview.md#available-service-tags)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete con criteri di Azure.

USA i progetti di Azure per semplificare le distribuzioni su larga scala di Azure tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controlli RBAC di Azure e criteri, in una singola definizione di progetto. 

Applicare il progetto a nuove sottoscrizioni e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Esempi di criteri di Azure per la rete](../governance/policy/samples/built-in-policies.md#network)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida**: usare i tag delle risorse per i gruppi di sicurezza di rete e altre risorse correlate alla sicurezza di rete e al flusso del traffico. 

Usare il campo "Description" per documentare le regole che consentono il traffico da e verso una rete per le singole regole del gruppo di sicurezza di rete.

Implementare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore", che garantisce che tutte le risorse vengano create con tag e per notificare eventuali risorse senza tag esistenti.

Usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale di Azure](../virtual-network/quick-create-portal.md)

- [Come filtrare il traffico di rete con le regole del gruppo di sicurezza di rete](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse e rilevare le modifiche alle risorse di Azure. 

Creare avvisi in monitoraggio di Azure per ricevere una notifica quando vengono modificate le risorse critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni**: rivedere le modifiche apportate alle regole in uscita e ai gruppi di sicurezza di rete per i bilanciamenti del carico visualizzando il log attività nelle sottoscrizioni. 

Visualizzare i registri host interni per assicurarsi che le risorse back-end siano sicure.

Esportare questi log in Log Analytics o in un'altra piattaforma di archiviazione. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione.

Abilitare e caricare questi dati in Sentinel di Azure o in una SIEM di terze parti in base ai requisiti aziendali dell'organizzazione.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md)

- [Come raccogliere i log degli host interni della macchina virtuale di Azure con monitoraggio di Azure](../azure-monitor/learn/quick-collect-azurevm.md)

- [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Log attività della piattaforma](../azure-monitor/platform/activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni**: esaminare le informazioni di controllo e registrazione del piano di gestione e di controllo acquisite con i log attività per la Load Balancer di base. Queste impostazioni di acquisizione sono abilitate per impostazione predefinita. 

Usare i log attività per monitorare le azioni sulle risorse per visualizzare tutte le attività e il relativo stato. 

Determinare quali operazioni sono state eseguite sulle risorse nella sottoscrizione con i log attività: 

- chi ha avviato l'operazione
- quando si è verificata l'operazione;
- lo stato dell'operazione;

- i valori delle altre proprietà che potrebbero essere utili per esaminare l'operazione.

Recuperare le informazioni dal log attività tramite Azure PowerShell, l'interfaccia della riga di comando di Azure (CLI), l'API REST di Azure o l'portale di Azure. 

Implementare la diagnostica multidimensionale per le funzionalità di configurazione di Load Balancer Standard con monitoraggio di Azure.  Sono incluse le metriche disponibili per la sicurezza, incluse le informazioni sulle connessioni SNAT (Source Network Address Translation) e sulle porte. Sono disponibili anche metriche aggiuntive sui pacchetti SYN (Synchronize) e sui contatori di pacchetti. 

Recuperare le metriche multidimensionali a livello di codice tramite le API e scriverle in un account di archiviazione tramite l'opzione "tutte le metriche".

Usare il pacchetto di contenuto log di controllo di Azure con Microsoft Power BI per analizzare i dati con dashboard preconfigurati o per personalizzare le visualizzazioni in base alle esigenze.

Eseguire lo streaming dei log in un hub eventi o in un'area di lavoro Log Analytics. Possono anche essere estratti dall'archiviazione BLOB di Azure e visualizzati in diversi strumenti, ad esempio Excel e Power BI. 

Abilitare e caricare i dati in Sentinel di Azure o in una SIEM di terze parti in base ai requisiti aziendali.

- [Leggere questo articolo con istruzioni dettagliate per ogni metodo dettagliato nelle operazioni di controllo con Gestione risorse](../azure-resource-manager/management/view-activity-logs.md)

- [Log di Monitoraggio di Azure per Load Balancer Basic pubblico](./load-balancer-monitor-log.md)

- [Visualizzare i log attività per monitorare le azioni sulle risorse](../azure-resource-manager/management/view-activity-logs.md)

- [Recuperare le metriche multidimensionali a livello di codice tramite le API](./load-balancer-standard-diagnostics.md#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: il log attività è abilitato per impostazione predefinita e viene mantenuto per 90 giorni nell'archivio dei log eventi di Azure. Impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione in monitoraggio di Azure. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione.

- [Visualizzare i log attività per monitorare le azioni nell'articolo sulle risorse](../azure-resource-manager/management/view-activity-logs.md)

- [Modificare il periodo di conservazione dei dati in Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Come configurare i criteri di conservazione per i log dell'account di archiviazione di Azure](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida**: monitorare, gestire e risolvere i problemi relativi alle risorse Load Balancer standard usando la pagina Load Balancer della portale di Azure e la pagina integrità risorse in monitoraggio di Azure. Le metriche disponibili per la sicurezza includono le informazioni sulle connessioni SNAT (Source Network Address Translation) e sulle porte. Sono inoltre disponibili metriche sui pacchetti SYN (Synchronize) e sui contatori di pacchetti. 

Usare monitoraggio di Azure per esaminare lo stato del probe di integrità degli endpoint con metriche multidimensionali per i servizi di bilanciamento del carico standard, esterni e interni. Raccogliere le metriche a livello di codice tramite le API e scritte in un account di archiviazione tramite l'opzione "tutte le metriche".

I log di monitoraggio di Azure non sono disponibili per i bilanciamenti del carico di base interni. 

Usare monitoraggio di Azure per visualizzare il riepilogo dello stato del probe di integrità per ogni pool back-end per il Load Balancer esterno di base, ad esempio, il numero di istanze nel pool back-end che non ricevono richieste dall'Load Balancer a causa di errori del probe di integrità. 

Implementare la registrazione con Operational Insights di Azure per fornire statistiche sullo stato di integrità del servizio di bilanciamento del carico. 

Usare i log attività per visualizzare gli avvisi e monitorare le azioni sulle risorse e il relativo stato nelle sottoscrizioni di Azure. I log attività sono abilitati per impostazione predefinita e possono essere visualizzati nel portale di Azure. 

Usare Microsoft Power BI con il pacchetto di contenuto log di controllo di Azure e analizzare i dati con dashboard preconfigurati. Personalizzare le visualizzazioni in base alle esigenze aziendali. 

Eseguire lo streaming dei log in un hub eventi o in un'area di lavoro Log Analytics. Possono anche essere estratti dall'archiviazione BLOB di Azure e visualizzati in diversi strumenti, ad esempio Excel e Power BI. È possibile abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti.

- [Probe di integrità di Load Balancer](./load-balancer-custom-probe-overview.md)

- [API REST di Monitoraggio di Azure](/rest/api/monitor)

- [Come recuperare le metriche tramite l'API REST](/rest/api/monitor/metrics/list)

- [Diagnostica di Load Balancer Standard con metriche, avvisi e integrità delle risorse](./load-balancer-standard-diagnostics.md)

- [Log di Monitoraggio di Azure per Load Balancer Basic pubblico](./load-balancer-monitor-log.md)

- [Consente di visualizzare le metriche di Load Balancer nel portale di Azure](./load-balancer-standard-diagnostics.md#view-your-load-balancer-metrics-in-the-azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida**: usare il Centro sicurezza con log Analytics area di lavoro per il monitoraggio e l'invio di avvisi su attività anomale correlate ai Load Balancer nei log di sicurezza e negli eventi.

Abilitare e caricare i dati in Sentinel di Azure o in uno strumento SIEM di terze parti.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come gestire gli avvisi nel centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Come inviare un avviso sui dati del log di log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida**: non applicabile a Azure Load Balancer. Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Indicazioni**: non applicabile come Azure Load Balancer è un servizio di rete principale che non esegue query DNS.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: non applicabile a Azure Load Balancer perché questa raccomandazione si applica alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni**: il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) consente di gestire l'accesso alle risorse di Azure, ad esempio la Load Balancer tramite assegnazioni di ruolo. Assegnare questi ruoli a utenti, gruppi di entità servizio e identità gestite. 

Esegui l'inventario dei ruoli predefiniti e predefiniti per determinate risorse con strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o l'portale di Azure.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: usare RBAC di Azure per gestire l'accesso alle risorse

**Indicazioni**: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse Load Balancer

- [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: Load Balancer è un servizio pass-through che non archivia i dati dei clienti. Fa parte della piattaforma sottostante gestita da Microsoft. 

Microsoft considera tutti i contenuti dei clienti come sensibili e passa a grandi lunghezze per difendersi dalla perdita di dati e dall'esposizione dei clienti. 

Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e mantiene una suite di controlli e funzionalità affidabili per la protezione dei dati. 

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi quando le modifiche vengono apportate alle risorse di Azure critiche, ad esempio i bilanciamenti del carico usati per carichi di lavoro di produzione importanti.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte, protocolli e così via) nelle sottoscrizioni. È consigliabile Azure Resource Manager per creare e usare le risorse correnti. 

Verificare le autorizzazioni (lettura) appropriate nel tenant ed enumerare tutte le sottoscrizioni e le risorse di Azure nelle sottoscrizioni.

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee guida**: applicare i tag alle risorse di Azure con i metadati per organizzare logicamente in base a una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate laddove appropriato per organizzare e tenere traccia degli asset. 

Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalle sottoscrizioni.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definire e gestire un inventario delle risorse di Azure approvate

**Linee guida**: creare un elenco di risorse di Azure approvate in base alle esigenze organizzative, che è possibile utilizzare come meccanismo di elenco Consenti. Questo consentirà all'organizzazione di caricare tutti i servizi di Azure appena disponibili dopo essere stati rivisti e approvati formalmente dai normali processi di valutazione della sicurezza dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

Eseguire una query per individuare le risorse con il grafico delle risorse di Azure nelle sottoscrizioni di proprietà. 

Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale Azure ad per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "blocca l'accesso" per l'App "Microsoft Azure Management".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni**: il software necessario per le operazioni aziendali, ma può comportare un rischio maggiore per l'organizzazione, deve essere isolato all'interno della propria macchina virtuale e/o della rete virtuale e protetto in modo adeguato con un firewall di Azure o un gruppo di sicurezza di rete.

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione delle risorse di Azure. Usare le definizioni di criteri di Azure predefinite.

Azure Resource Manager è in grado di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per assicurarsi che le configurazioni soddisfino i requisiti di sicurezza per l'organizzazione.

Esportare i modelli di Azure Resource Manager in formati JavaScript Object Notation (JSON) ed esaminarli periodicamente per assicurarsi che le configurazioni soddisfino i requisiti di sicurezza dell'organizzazione. 

Implementare le raccomandazioni dal centro sicurezza come linea di base di configurazione sicura per le risorse di Azure. 

- [Come visualizzare gli alias dei criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Esportazione di una singola e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.  È anche possibile usare i modelli di Azure Resource Manager per gestire la configurazione di sicurezza delle risorse di Azure richieste dall'organizzazione. 

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

- [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Panoramica sui modelli di Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio definizioni personalizzate di criteri di Azure, modelli di Azure Resource Manager e script di configurazione dello stato desiderato. 

Concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se è integrato con Azure DevOps o in Active Directory se integrato con TFS.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di Azure usando i criteri di Azure.  Usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione di rete delle risorse di Azure. Implementare le definizioni dei criteri predefinite correlate alle risorse Azure Load Balancer specifiche.  Usare inoltre automazione di Azure per distribuire le modifiche di configurazione. per le risorse Azure Load Balancer.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come usare gli alias](../governance/policy/concepts/definition-structure.md#aliases)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida**: usare il Centro sicurezza per eseguire analisi di base per le risorse di Azure e i criteri di Azure per l'avviso e il controllo delle configurazioni delle risorse.

- [Come correggere le raccomandazioni nel centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. 

Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, oltre che sul livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare le sottoscrizioni usando i tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano dati sensibili.  

È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida**: esportare gli avvisi e le raccomandazioni del Centro sicurezza usando la funzionalità di esportazione continua per identificare i rischi per le risorse di Azure. 

Usare la funzionalità di esportazione continua nel centro sicurezza che consente di esportare avvisi e consigli manualmente o in modo continuo. 

Usare il connettore dati del Centro sicurezza per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza per attivare automaticamente le risposte agli avvisi e alle raccomandazioni di sicurezza per proteggere le risorse di Azure.

- [Come configurare l'automazione del flusso di lavoro in sicurezza immettere](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: seguire le regole di test di penetrazione Microsoft Cloud di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft. 

- [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)