---
title: Panoramica di Operations Management Suite (OMS) | Documentazione Microsoft
description: "Microsoft Operations Management Suite (OMS) è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud.  Questo articolo illustra il valore di OMS, identifica i diversi servizi e le offerte inclusi in OMS e fornisce i collegamenti al contenuto dettagliato."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: bwren
ms.openlocfilehash: 94dedebe48060441cd3167fea87f6b721eb14517
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="what-is-operations-management-suite-oms"></a>Informazioni su Operations Management Suite (OMS)
Questo articolo offre un'introduzione a Operations Management Suite (OMS), tra cui una breve panoramica del valore aziendale che ne deriva, i servizi e le soluzioni di gestione inclusi e le offerte che comprendono i diversi servizi e soluzioni.  Sono inclusi collegamenti alla documentazione dettagliata sulla distribuzione e sull'uso di ogni servizio e soluzione.

## <a name="from-on-premises-to-the-cloud"></a>Da locale a cloud
Microsoft offre da tempo prodotti per la gestione degli ambienti aziendali.  Diversi prodotti sono stati consolidati nel 2007 nella suite di prodotti per la gestione System Center,  che includeva Configuration Manager che offre funzionalità come la distribuzione e l'inventario software, Operations Manager che consente il monitoraggio proattivo di sistemi e applicazioni, Orchestrator che include runbook per automatizzare i processi manuali e Data Protection Manager per il backup e ripristino di dati critici.

Con il passaggio al cloud di diverse risorse di calcolo, i prodotti System Center hanno acquisito altre funzionalità cloud, ad esempio Operations Manager e Orchestrator per la gestione delle risorse in Azure.  Erano però ancora sostanzialmente progettati come soluzioni locali e richiedevano un considerevole investimento dal punto di vista della distribuzione e della manutenzione dell'ambiente di gestione locale.  Per sfruttare pienamente il cloud e supportare le applicazioni future, era necessario un nuovo approccio alla gestione.

## <a name="introducing-operations-management-suite"></a>Introduzione a Operations Management Suite
Operations Management Suite (OMS) è una raccolta di servizi di gestione progettati nel cloud sin dall'inizio.  Invece di distribuire e gestire risorse locali, i componenti di OMS sono completamente ospitati in Azure.  La configurazione è minima ed è possibile essere operativi in davvero pochi minuti.  

- **Costi minimi e complessità della distribuzione.**  Dato che tutti i componenti e i dati di OMS vengono archiviati in Azure, è possibile essere operativi in breve tempo senza difficoltà e senza investimenti in componenti locali.
- **Scalabilità in base ai livelli cloud.**  Non è necessario preoccuparsi di pagare per risorse di calcolo inutili o di esaurire lo spazio di archiviazione perché il cloud consente di pagare solo ciò che si usa effettivamente e di passare rapidamente al carico richiesto.  È possibile iniziare con la gestione di poche risorse per poi passare a un piano superiore per l'intero ambiente.
- **Sfruttare le funzionalità più recenti.**  Nei servizi OMS vengono continuamente aggiunte e aggiornate diverse funzionalità.  Si ha sempre accesso alle funzionalità più recenti senza dover distribuire aggiornamenti.
- **Servizi integrati.**  Ogni singolo servizio OMS apporta un considerevole valore, ma tutti insieme consentono di risolvere complessi scenari di gestione.  Un runbook in Automazione di Azure, ad esempio, può gestire un processo di failover con Azure Site Recovery e quindi registrare le informazioni in Log Analytics per generare un avviso.
- **Conoscenza globale.**  Le soluzioni di gestione in OMS hanno sempre accesso alle informazioni più recenti.  La soluzione Sicurezza e controllo, ad esempio, può eseguire un'analisi delle minacce sulla base delle minacce più recenti rilevate in tutto il mondo.
- **Accesso ovunque.**  Per accedere all'ambiente di gestione è sufficiente un browser.  Installare l'app OMS nello smartphone per accedere subito ai dati di monitoraggio.

### <a name="is-it-just-for-the-cloud"></a>Non solo cloud
Anche se i servizi OMS vengono eseguiti nel cloud, possono tuttavia gestire in modo efficace anche l'ambiente locale.  Inserire in qualsiasi computer Windows o Linux del data center un agente che invierà i dati a Log Analytics, dove potranno essere analizzati con tutti gli altri dati raccolti dai servizi cloud o locali.  Usare Backup di Azure e Azure Site Recovery per sfruttare il cloud per il backup e la disponibilità elevata per le risorse locali.  
I runbook nel cloud in genere non possono accedere alle risorse locali, ma è possibile installare un agente anche in uno o più computer che ospiteranno i runbook nel data center.  Quando si avvia un runbook, si specifica solo se lo si vuole eseguire nel cloud o in un ruolo di lavoro locale.

## <a name="hybrid-management-with-system-center"></a>Gestione ibrida con System Center
Se esiste già un'installazione di System Center, è possibile integrare questi componenti con i servizi OMS per offrire una soluzione ibrida sia per l'ambiente locale che per l'ambiente cloud sfruttando le particolari caratteristiche di ogni prodotto.  Connettere il gruppo di gestione di Operations Manager a Log Analytics per analizzare gli agenti gestiti nel cloud.  Usare il processo di backup esistente con Data Protection Manager per eseguire il backup dei dati nel cloud.  


## <a name="oms-services"></a>Servizi OMS
Le funzionalità principali di OMS vengono fornite da un set di servizi eseguiti in Azure.  Ogni servizio fornisce una funzione di gestione specifica ed è possibile combinare i servizi per realizzare scenari di gestione diversi.

|| Service | DESCRIZIONE |
|:--|:--|:--|
| ![Log Analytics](media/operations-management-suite-overview/icon-log-analytics.png) | Log Analytics | Monitora e analizza la disponibilità e le prestazioni di risorse diverse, inclusi i computer fisici e le macchine virtuali. |
| ![Automazione di Azure](media/operations-management-suite-overview/icon-automation.png) | Automazione | Automatizza i processi manuali ed applica le configurazioni per i computer fisici e le macchine virtuali. |
| ![Backup di Azure](media/operations-management-suite-overview/icon-backup.png) | Backup | Esegue il backup e ripristino dei dati critici. |
| ![Azure Site Recovery](media/operations-management-suite-overview/icon-site-recovery.png) | Site Recovery | Offre disponibilità elevata per le applicazioni critiche. |

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) fornisce servizi di monitoraggio per OMS raccogliendo i dati delle risorse gestite in un repository centrale.  Questi dati possono includere eventi, dati sulle prestazioni o dati personalizzati forniti tramite l'API. Dopo essere stati raccolti, i dati sono disponibili per generare avvisi, per l'analisi e per l'esportazione.  Questo metodo consente di consolidare i dati di diverse origini per poter combinare i dati dei servizi di Azure con l'ambiente locale esistente.  Separa anche nettamente la raccolta dei dati dall'azione eseguita su tali dati in modo che tutte le azioni siano disponibili per tutti i tipi di dati.  

![Panoramica di Log Analytics](media/operations-management-suite-overview/overview-log-analytics.png)

#### <a name="collecting-data"></a>Raccolta dei dati
È possibile immettere i dati nel repository in diversi modi per consentire a Log Analytics di analizzarli.

- **Computer e macchine virtuali Windows o Linux.**  Si installa Microsoft Monitoring Agent nei computer o nelle macchine virtuali [Windows](../log-analytics/log-analytics-windows-agent.md) e [Linux](../log-analytics/log-analytics-linux-agents.md) da cui si vogliono raccogliere i dati.  L'agente scaricherà automaticamente da Log Analytics la configurazione che definisce gli eventi e i dati sulle prestazioni da raccogliere.  È possibile installare facilmente l'agente nelle macchine virtuali eseguite in Azure usando il portale di Azure.  Se esiste già un ambiente Operations Manager, è possibile connettere il gruppo di gestione a Log Analytics e avviare automaticamente la raccolta dati da tutti gli agenti esistenti.
- **Servizi di Azure.**  Log Analytics raccoglie i dati di telemetria da [Diagnostica di Azure e Monitoraggio di Azure](../log-analytics/log-analytics-azure-storage.md) nel repository per poter monitorare le risorse di Azure.
- **API di raccolta dati.**  Log Analytics ha un'[API REST per popolare i dati da qualsiasi client](../log-analytics/log-analytics-data-collector-api.md).  In questo modo è possibile raccogliere dati da applicazioni di terze parti o implementare scenari di gestione personalizzati.  Un metodo comune prevede l'uso di un runbook in Automazione di Azure per raccogliere i dati e quindi l'uso dell'API dell'agente di raccolta dati per scriverli nel repository.

#### <a name="reporting-and-analyzing-data"></a>Creazione di report e analisi dei dati
Log Analytics include un linguaggio di query avanzato per estrarre i dati archiviati nel repository.  Poiché i dati di tutte le origini vengono archiviati come record, è possibile analizzare i dati di più origini in una singola query.
  
Oltre all'analisi ad hoc, Log Analytics consente di creare report e analizzare i dati da una query in diversi modi.

- **Viste e dashboard.**  Le [viste](../log-analytics/log-analytics-view-designer.md) e i [dashboard](../log-analytics/log-analytics-dashboards.md) visualizzano i risultati di una query nel portale.  Le soluzioni di gestione normalmente includono viste che analizzano i dati dalla soluzione.  È anche possibile creare viste personalizzate per analizzare i dati e renderli subito disponibili nel portale personalizzato.
- **Esportazione.**  È possibile esportare i risultati di qualsiasi query per poterli analizzare al di fuori di Log Analytics.  È anche possibile programmare un'esportazione periodica in [Power BI](../log-analytics/log-analytics-powerbi.md) che offre importanti funzionalità di visualizzazione e analisi.
- **API di ricerca log.**  Log Analytics ha un'[API REST per raccogliere i dati da qualsiasi client](../log-analytics/log-analytics-log-search-api.md).  In questo modo è possibile lavorare a livello di codice sui dati raccolti nel repository o accedervi da un altro strumento di monitoraggio.

#### <a name="alerting"></a>Creazione di avvisi
Log Analytics può [avvisare in modo proattivo](../log-analytics/log-analytics-alerts.md) o eseguire un'azione correttiva quando rileva un problema.  Come tutte le altre operazioni di analisi in Log Analytics, anche questa viene eseguita con una ricerca log.  Questa ricerca viene eseguita periodicamente e viene creato un avviso se i risultati corrispondono a determinati criteri.

![Avvisi Log Analytics](media/operations-management-suite-overview/overview-alerts.png)

Oltre a creare un record di avviso nel repository di Log Analytics, gli avvisi possono eseguire queste azioni.

- **Posta elettronica.**  Inviare un messaggio di posta elettronica per avvisare in modo proattivo di un problema rilevato.
- **Runbook.**  Un avviso in Log Analytics può avviare un runbook in Automazione di Azure.  Ciò avviene in genere per cercare di risolvere il problema rilevato.  Il runbook può essere avviato nel cloud in caso di problema in Azure o in un altro cloud oppure può essere avviato in un agente locale per un problema in un computer fisico o in una macchina virtuale.
- **Webhook.**  Un avviso può avviare un webhook e passargli i dati dei risultati della ricerca log.  Ciò consente l'integrazione con i servizi esterni, ad esempio un sistema di avvisi alternativo, oppure può essere provata un'azione correttiva per un sito Web esterno.

### <a name="azure-automation"></a>Automazione di Azure
[Automazione di Azure](http://azure.microsoft.com/documentation/services/automation) fornisce l'automazione dei processi e la gestione della configurazione a OMS.  Automatizza i processi manuali e consente di applicare le configurazioni per i computer fisici e virtuali.  

#### <a name="process-automation"></a>Automazione dei processi
Automazione di Azure automatizza i processi manuali usando i [runbook](../automation/automation-runbook-types.md) che si basano su uno script di PowerShell o su un flusso di lavoro di PowerShell.  Include anche asset che supportano i runbook, ad esempio variabili che possono essere condivise tra più runbook e credenziali e connessioni che consentono di archiviare informazioni crittografate che potrebbero essere necessarie a un runbook per l'autenticazione.
I runbook offrono l'automazione dei processi per gli altri servizi della suite.  Poiché ognuno degli altri servizi è accessibile con PowerShell o tramite un'API REST, è possibile creare runbook per eseguire funzioni come la raccolta dei dati di gestione in Log Analytics o l'avvio di un backup con Backup di Azure.

##### <a name="accessing-resources"></a>Accesso alle risorse
Poiché i runbook si basano su PowerShell, possono gestire qualsiasi risorsa accessibile con i cmdlet di PowerShell.  Quando si [carica un modulo](../automation/automation-integration-modules.md) nell'account di Automazione, diventa disponibile per tutti i runbook in tale account. 
 
Quando i runbook vengono eseguiti nel cloud, possono accedere a qualsiasi risorsa accessibile dal cloud.  Può trattarsi di risorse nella sottoscrizione di Azure, in un altro cloud, ad esempio Amazon Web Services (AWS), o in un servizio accessibile tramite un'API REST.  I runbook nel cloud non vengono eseguiti con alcuna credenziale, ma possono sfruttare gli asset di Automazione, ad esempio credenziali, connessioni e certificati, per l'autenticazione con le risorse a cui accedono.

Le risorse nel data center molto probabilmente non sono accessibili da un runbook in esecuzione nel cloud.  È tuttavia possibile installare uno o più [ruoli di lavoro ibridi per runbook](../automation/automation-hybrid-runbook-worker.md) nel data center per eseguire i runbook che richiedono l'accesso alle risorse locali.  Quando si avvia un runbook, si specifica se deve essere eseguito nel cloud o in un ruolo di lavoro specifico.

![Runbook di Automazione di Azure](media/operations-management-suite-overview/overview-runbooks.png)

##### <a name="starting-a-runbook"></a>Avvio di un runbook
Poiché i runbook possono essere [avviati con diversi metodi](../automation/automation-starting-a-runbook.md), è possibile includerli in svariati scenari di gestione.  

- **Portale di Azure.**  Come gli altri servizi di Azure, Automazione di Azure può essere gestito dal portale di Azure.  Oltre ad avviare i runbook, è possibile importarli o crearne di nuovi.
- **Pianificazione.**  È possibile pianificare l'avvio dei runbook a intervalli regolari.  In questo modo è possibile ripetere automaticamente un processo di gestione periodico o raccogliere dati in Log Analytics.
- **PowerShell e API.**  È possibile avviare i runbook e passare le informazioni sui parametri necessarie da un cmdlet di PowerShell o dall'API REST di Automazione di Azure.  
- **Webhook.**  È possibile creare un webhook per qualsiasi runbook che ne consenta l'avvio da applicazioni o siti Web esterni.
- **Avviso di Log Analytics.**  Un avviso in Log Analytics può avviare automaticamente un runbook per cercare di correggere il problema identificato dall'avviso.

#### <a name="configuration-management"></a>Gestione della configurazione
[PowerShell DSC (Desired State Configuration)](../automation/automation-dsc-overview.md) è una piattaforma di gestione in Windows PowerShell che consente di distribuire e di applicare la configurazione di computer fisici e macchine virtuali.  Automazione di Azure gestisce le configurazioni DSC e fornisce un server di pull nel cloud a cui gli agenti possono accedere per recuperare le configurazioni necessarie.

![Automation DSC per Azure](media/operations-management-suite-overview/overview-dsc.png)

### <a name="azure-backup-and-azure-site-recovery"></a>Backup di Azure e Azure Site Recovery
Backup di Azure e Azure Site Recovery contribuiscono alla continuità aziendale e al ripristino di emergenza.  Entrambi hanno funzionalità che consentono di assicurarsi che le applicazioni rimangano disponibili quando si verificano interruzioni e che riprendano le normali operazioni quando i sistemi tornano online.  Entrambi i servizi contribuiscono agli obiettivi del punto di ripristino (RPO, Recovery Point Objective) e agli obiettivi del tempo di ripristino (RTO, Recovery Time Objective) definiti per l'organizzazione. L'obiettivo RPO definisce il limite accettabile in cui i dati non sono disponibili durante un'interruzione e l'obiettivo RTO limita il periodo di tempo accettabile in cui un servizio o un'app non è disponibile durante un'interruzione.

#### <a name="azure-backup"></a>Backup di Azure
[Backup di Azure](http://azure.microsoft.com/documentation/services/backup) fornisce servizi di backup e ripristino dei dati per OMS.  Protegge i dati delle applicazioni e li conserva per anni, senza investimenti di capitali e con costi operativi minimi.  Consente di eseguire il backup dei dati da server Windows fisici e virtuali, oltre che dei carichi di lavoro di applicazioni come SQL Server e SharePoint.  Può inoltre essere usato da System Center Data Protection Manager (DPM) per replicare i dati protetti in Azure per la ridondanza e l'archiviazione a lungo termine.

I dati protetti in Backup di Azure vengono archiviati in un insieme di credenziali di backup che si trova in una determinata area geografica. I dati vengono replicati nella stessa area e, a seconda del tipo di insieme di credenziali, possono anche essere replicati in un'altra area per una maggiore resilienza.

Backup di Azure prevede tre scenari fondamentali.

- **Computer Windows con l'agente di Backup di Azure.** Esegue il backup di file e cartelle da qualsiasi client o server Windows direttamente nell'insieme di credenziali di backup di Azure.<br><br>![Computer Windows con l'agente di Backup di Azure](media/operations-management-suite-overview/overview-backup-01.png)
- **System Center Data Protection Manager (DPM) o server di Backup di Microsoft Azure.** Sfrutta DPM o il server di Backup di Microsoft Azure per eseguire il backup di file e cartelle, oltre che dei carichi di lavoro di applicazioni come SQL e SharePoint, nell'archiviazione locale e quindi di eseguire la replica nell'insieme di credenziali di backup di Azure. Supporta macchine virtuali Windows e Linux in Hyper-V o VMware.<br><br>![System Center Data Protection Manager (DPM) o server di Backup di Microsoft Azure](media/operations-management-suite-overview/overview-backup-02.png)
- **Estensioni delle macchine virtuali di Azure.** Esegue il backup di macchine virtuali Windows o Linux in Azure nell'insieme di credenziali di backup di Azure.<br><br>![Estensioni delle macchine virtuali di Azure](media/operations-management-suite-overview/overview-backup-03.png)



#### <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) garantisce la continuità aziendale orchestrando la replica delle macchine virtuali e dei computer fisici locali in Azure o in un sito secondario. Se il sito primario non è disponibile, verrà eseguito il failover alla località secondaria in modo che gli utenti possano continuare a lavorare e il failback quando i sistemi riprendono a funzionare normalmente. 

Azure Site Recovery fornisce disponibilità elevata per i server e le applicazioni.  Favorisce la continuità aziendale e una strategia di ripristino di emergenza coordinando le operazioni di replica, failover e ripristino di macchine virtuali Hyper-V locali, macchine virtuali VMware e server fisici Windows o Linux. È possibile replicare le macchine in un data center secondario o estendere il data center replicandole in Azure. Site Recovery fornisce inoltre funzionalità per semplificare il failover e il ripristino dei carichi di lavoro. Si integra con i meccanismi di ripristino di emergenza come SQL Server AlwaysOn e prevede piani di ripristino per il failover semplificato dei carichi di lavoro organizzati su più livelli in diversi computer.

Azure Site Recovery prevede tre scenari di replica fondamentali.

- **Replica di macchine virtuali Hyper-V.**  Se le macchine virtuali Hyper-V vengono gestite nei cloud VMM, è possibile eseguire la replica in un data center secondario o nell'archiviazione di Azure. La replica in Azure viene eseguita tramite una connessione Internet sicura. La replica in un data center secondario viene eseguita tramite la LAN.  Se le macchine virtuali Hyper-V non vengono gestite da VMM, è possibile eseguire la replica solo in una risorsa di archiviazione di Azure. La replica in Azure viene eseguita tramite una connessione Internet sicura.<br><br>![Replica di macchine virtuali Hyper-V](media/operations-management-suite-overview/overview-siterecovery-hyperv.png)
- **Replica di macchine virtuali VMware.**  È possibile replicare le macchine virtuali VMware in un data center secondario che esegue VMware o nell'archiviazione di Azure. La replica in Azure può essere eseguita tramite una VPN da sito a sito, Azure ExpressRoute o una connessione Internet sicura. La replica in un data center secondario viene eseguita tramite il canale dati di InMage Scout.<br><br>![Replica di macchine virtuali VMware](media/operations-management-suite-overview/overview-siterecovery-vmware.png)
- **Replica di server fisici Windows e Linux.**  È possibile replicare i server fisici in un data center secondario o nell'archiviazione di Azure. La replica in Azure può essere eseguita tramite una VPN da sito a sito, Azure ExpressRoute o una connessione Internet sicura. La replica in un data center secondario viene eseguita tramite il canale dati di InMage Scout. Azure Site Recovery dispone di una soluzione OMS che visualizza alcune statistiche, ma è necessario usare il portale di Azure per qualsiasi operazione.<br><br>![Replica di server fisici Windows e Linux](media/operations-management-suite-overview/overview-siterecovery-physical.png)


Site Recovery archivia i metadati in insiemi di credenziali che si trovano in una determinata area geografica di Azure. Nessun dato replicato viene archiviato dal servizio Site Recovery.

## <a name="management-solutions"></a>Soluzioni di gestione
Le [soluzioni di gestione](operations-management-suite-solutions.md) sono set predefiniti di logica che implementano un particolare scenario di gestione sfruttando uno o più servizi OMS.  Sono disponibili diverse soluzioni Microsoft e dei partner che è possibile aggiungere facilmente alla sottoscrizione di Azure per aumentare il valore dell'investimento in OMS.  I partner possono creare le proprie soluzioni per supportare le applicazioni e i servizi e fornirle agli utenti tramite Azure Marketplace o i modelli di avvio rapido.

Un valido esempio di soluzione che sfrutta più servizi per offrire funzionalità aggiuntive è la [soluzione Gestione aggiornamenti](oms-solution-update-management.md).  Questa soluzione usa l'agente di Log Analytics per Windows e Linux per raccogliere informazioni sugli aggiornamenti necessari in ogni agente.  Scrive questi dati nel repository di Log Analytics dove è possibile analizzarli con un dashboard incluso.  Quando si crea una distribuzione, i runbook in Automazione di Azure vengono usati per installare gli aggiornamenti necessari.  L'intero processo viene gestito nel portale e non è necessario preoccuparsi dei dettagli sottostanti.

![Soluzione](media/operations-management-suite-overview/overview-solution.png)

La maggior parte delle soluzioni può eseguire una o più delle funzioni seguenti.

- Raccogliere Informazioni aggiuntive.  Log Analytics raccoglie svariati dati da client e servizi, inclusi eventi e dati sulle prestazioni.  Una soluzione di gestione può raccogliere informazioni aggiuntive non disponibili in altre origini dati, spesso usando i runbook di Automazione di Azure.
- Fornire un'analisi aggiuntiva delle informazioni raccolte.  Le soluzioni di gestione includono dashboard e viste che consentono l'analisi e la visualizzazione dei dati.  Questi collegano a loro volta alle ricerche log predefinite che consentono di esaminare i dati dettagliati.  Possono anche eseguire l'analisi sui dati già raccolti nel repository, ad esempio cercando negli eventi di sicurezza schemi che indicano una minaccia.
- Aggiungere funzionalità.  Alcune soluzioni fornite da Microsoft possono basarsi sulle funzionalità dei servizi principali per aggiungere altre funzionalità.  Service Map, ad esempio, fornisce la propria console per rilevare ed eseguire il mapping dei server e delle relative dipendenze in tempo reale.
Microsoft e i partner aggiungono periodicamente soluzioni a OMS consentendo di accrescere continuamente il valore dell'investimento.  È possibile cercare e installare le soluzioni Microsoft tramite il catalogo delle soluzioni nel portale di OMS o cercare e installare soluzioni sia Microsoft che dei partner tramite Azure Marketplace nel portale di Azure.  

![Raccolta soluzioni](media/operations-management-suite-overview/solution-gallery.png)


## <a name="next-steps"></a>Passaggi successivi
* Informazioni su [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)
* Informazioni su [Automazione di Azure](../automation/automation-intro.md)
* Informazioni su [Backup di Azure](http://azure.microsoft.com/documentation/services/backup)
* Informazioni su [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery)
* Trovare le [soluzioni disponibili](../log-analytics/log-analytics-add-solutions.md) nelle diverse offerte OMS. 

