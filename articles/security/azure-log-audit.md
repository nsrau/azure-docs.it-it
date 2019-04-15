---
title: Registrazione e controllo di Azure | Microsoft Docs
description: Informazioni sull'uso dei dati di registrazione per ottenere informazioni approfondite sull'applicazione.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: TomSh
ms.openlocfilehash: 492beba1040cef3d5a910cc9db3fe16b41c33cd6
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301588"
---
# <a name="azure-logging-and-auditing"></a>Registrazione e controllo di Azure

Azure offre un'ampia gamma di opzioni di controllo e registrazione della sicurezza configurabili che consentono di identificare eventuali lacune nei criteri e nei meccanismi di sicurezza. Questo articolo offre informazioni per la generazione, la raccolta e l'analisi dei log di protezione di servizi ospitati in Azure.

> [!Note]
> Alcune indicazioni incluse in questo articolo potrebbero comportare un maggiore utilizzo delle risorse di dati, rete o calcolo e un aumento dei costi di licenza o di sottoscrizione.

## <a name="types-of-logs-in-azure"></a>Tipi di log in Azure

Le applicazioni cloud sono complesse e hanno molte parti mobili. I log forniscono dati per mantenere operative le applicazioni. Consentono di risolvere i problemi esistenti o prevenirne altri e risultano utili per migliorare le prestazioni o la manutenibilità delle applicazioni oppure per automatizzare azioni che altrimenti richiederebbero un intervento manuale.

I log di Azure sono suddivisi nei tipi seguenti:
* **Log di gestione/controllo**, che offrono informazioni sulle operazioni CREATE, UPDATE e DELETE di Azure Resource Manager. Per altre informazioni, vedere la [Log attività di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

* **Log del piano dati**, che offrono informazioni sugli eventi generati durante l'uso di una risorsa di Azure. Sono esempi di questo tipo il registro eventi di sistema di Windows, il log di sicurezza, il log applicazioni di una macchina virtuale e i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) configurati tramite Monitoraggio di Azure.

* **Eventi elaborati**, che offrono informazioni sugli eventi o avvisi analizzati dopo essere stati elaborati per conto dell'utente. Esempi di questo tipo sono i brevi [avvisi emessi dal Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) dopo aver eseguito l'[elaborazione e l'analisi della sottoscrizione](https://docs.microsoft.com/azure/security-center/security-center-intro).

La tabella seguente elenca i più importanti tipi di log disponibili in Azure.

| Categoria di log | Tipo di log | Uso | Integrazione |
| ------------ | -------- | ------ | ----------- |
|[Log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Gli eventi del piano di controllo sulle risorse di Azure Resource Manager|   Offrono informazioni dettagliate sulle operazioni eseguite sulle risorse nella sottoscrizione.|    API REST e [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Log di diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Dati frequenti sul funzionamento delle risorse di Azure Resource Manager nella sottoscrizione|    Offrono informazioni dettagliate sulle operazioni eseguite dalla risorsa stessa.| Monitoraggio di Azure, [Stream](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Creazione di report di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Log e report | Segnalano attività di accesso dell'utente e informazioni sulle attività di sistema riguardo alla gestione di utenti e gruppi.|[API Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Macchine virtuali e servizi cloud](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|Servizio Registro eventi di Windows e Syslog Linux|  Acquisisce i dati di sistema e i dati di registrazione nelle macchine virtuali e li trasferisce all'account di archiviazione desiderato.|   Windows tramite [WAD](https://docs.microsoft.com/azure/azure-diagnostics) (archiviazione di Diagnostica di Windows Azure) e Linux in Monitoraggio di Azure|
|[Analisi archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Esegue la registrazione di archiviazione e offre i dati delle metriche per un account di archiviazione|Offre informazioni dettagliate per tenere traccia delle richieste, analizzare le tendenze d'uso e diagnosticare i problemi relativi al proprio account di archiviazione.|   API REST o [libreria client](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Log dei flussi del gruppo di sicurezza di rete (NSG)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|Formato JSON, mostra i flussi in ingresso e in uscita in base a ciascuna regola|Visualizza le informazioni sul traffico IP in entrata e in uscita tramite un gruppo di sicurezza di rete.|[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Log, eccezioni e diagnostica personalizzata|   Offre un servizio di monitoraggio delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme.| API REST, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Dati di elaborazione/avvisi di sicurezza|    Avvisi del Centro sicurezza di Azure e di Azure Log Analytics|   Offre informazioni e avvisi sulla sicurezza.|  API REST, JSON|

### <a name="activity-logs"></a>Log attività

I [log attività di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) offrono informazioni dettagliate sulle operazioni eseguite sulle risorse nella sottoscrizione. I log attività erano noti in precedenza come "log di controllo" o "log operativi", perché segnalano [eventi del piano di controllo](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) per le sottoscrizioni. 

I log attività consentono di determinare "cosa, chi e quando" per le operazioni di scrittura (PUT, POST e DELETE). Permettono anche di comprendere lo stato dell'operazione e altre proprietà specifiche. I log attività non includono le operazioni di lettura (GET).

In questo articolo i termini PUT, POST e DELETE si riferiscono a tutte le operazioni di scrittura sulle risorse contenute in un log attività. Ad esempio, è possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

![Diagramma del log di attività](./media/azure-log-audit/azure-log-audit-fig1.png)

Per recuperare eventi da un log attività, è possibile usare il portale di Azure, l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli), i cmdlet di PowerShell e l'[API REST di Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Il periodo di conservazione dei dati per i log attività è di 90 giorni.

Scenari di integrazione per un evento del log attività:

* [Creare un avviso di posta elettronica o webhook attivato da un evento del log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email).

* [Trasmetterlo a un hub eventi](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) per l'inserimento da parte di un servizio di terze parti o di una soluzione di analisi personalizzata come Power BI.

* Analizzarlo in Power BI usando il [pacchetto di contenuto di Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Salvarlo in un account di archiviazione per l'archiviazione o l'ispezione manuale](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). È possibile specificare il tempo di conservazione in giorni tramite i profili di log.

* Eseguire query e visualizzarla nel portale di Azure.

* Eseguire query tramite l'API REST, i cmdlet di PowerShell o l'interfaccia della riga di comando di Azure.

* Esportare il log attività con i profili di log in [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

È possibile usare un account di archiviazione o lo [spazio dei nomi dell'hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) che non sia nella stessa sottoscrizione di quello che crea il log. L'utente che configura l'impostazione deve avere il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) appropriato a entrambe le sottoscrizioni.

### <a name="azure-diagnostics-logs"></a>Log di diagnostica di Azure

I log di diagnostica di Azure sono generati da una risorsa che offre dati completi e frequenti sul suo funzionamento. Il contenuto di questi log varia in base al tipo di risorsa. Ad esempio, i [registri eventi di sistema di Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) sono una categoria di log di diagnostica per macchine virtuali, mentre i [log di BLOB, tabelle e coda](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) sono categorie di log di diagnostica per gli account di archiviazione. I log di diagnostica differiscono dai log attività, che offrono informazioni dettagliate sulle operazioni eseguite sulle risorse nella sottoscrizione.

![Diagrammi di log di diagnostica di Azure](./media/azure-log-audit/azure-log-audit-fig2.png)

I log di diagnostica di Azure offrono più opzioni di configurazione, tra cui il portale di Azure, l'API REST, PowerShell e l'interfaccia della riga di comando di Azure.

**Scenari di integrazione**

* Salvarli in un [account di archiviazione](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione in giorni usando le impostazioni di diagnostica.

* [Trasmetterli all'hub eventi](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) per l'inserimento da parte di un servizio di terze parti o una soluzione di analisi personalizzata come [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Analizzarli con [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Servizi supportati, schema per i log di diagnostica e categorie di log supportate per ogni tipo di risorsa**


| Service | Schema e documentazione | Tipo di risorsa | Categoria |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Log Analytics per Load Balancer (anteprima)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers| LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Gruppi di sicurezza di rete|[Log Analytics per i gruppi di sicurezza di rete (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Gateway applicazione di Azure|[Registrazione diagnostica per il gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Registri di insiemi di credenziali delle chiavi](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Ricerca di Azure|[Abilitazione e uso di Analisi del traffico di ricerca](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Archivio Azure Data Lake|[Accesso ai log di diagnostica per Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Audit<br>Requests|
|Azure Data Lake Analytics.|[Accesso ai log di diagnostica per Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Audit<br>Requests|
|App per la logica di Azure|[Schema di rilevamento personalizzato per le app per la logica B2B](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Log di diagnostica di Azure Batch](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Automazione di Azure|[Log Analytics per Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Hub eventi di Azure|[Log di diagnostica degli hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Analisi di flusso di Azure|[Log di diagnostica dei processi](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Esecuzione<br>Creazione|
|Bus di servizio di Azure|[Log di diagnostica del bus di servizio](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Creazione di report in Azure Active Directory

Azure Active Directory (Azure AD) include report di controllo, sicurezza e attività per la directory di un utente. Il [report di controllo di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) consente di identificare le azioni con privilegi che si sono verificate nell'istanza di Azure AD dell'utente. Le azioni con privilegi includono modifiche di elevazione dei privilegi, ad esempio la creazione dei ruoli o le reimpostazioni delle password, la modifica delle configurazioni dei criteri, ad esempio i criteri delle password, o le modifiche alla configurazione della directory, ad esempio le modifiche alle impostazioni di federazione del dominio.

Nei report è incluso il record di controllo per il nome dell'evento, l'utente che ha eseguito l'azione, la risorsa di destinazione interessata dalla modifica e la data e l'ora (UTC). Gli utenti possono recuperare l'elenco degli eventi di controllo per Azure AD tramite il [portale di Azure](https://portal.azure.com/), come descritto nella pagina relativa alla [visualizzazione dei log di controllo](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

I report inclusi sono elencati nella tabella seguente:

| Report sulla sicurezza | Report sull’attività | Report di controllo |
| :--------------- | :--------------- | :------------ |
|Accessi da origini sconosciute| Utilizzo dell'applicazione: riepilogo| Report di controllo della Directory|
|Accessi dopo più errori|  Utilizzo dell'applicazione: dettagliato||
|Accessi da più aree geografiche|    Dashboard dell'applicazione||
|Accessi da indirizzi IP con attività sospette|   Errori di provisioning dell'account||
|Attività di accesso irregolare|    Dispositivi dell’utente singolo||
|Accessi da dispositivi potenzialmente infetti|   Attività dell'utente singolo||
|Utenti con anomalie dell'attività di accesso| Report attività dei gruppi||
||Report attività di registrazione per la reimpostazione password||
||Attività di reimpostazione password|||

I dati di questi report possono essere molto utili per le applicazioni, ad esempio i sistemi di informazioni di sicurezza e gestione degli eventi e gli strumenti di controllo e business intelligence. L'API di creazione report di Azure AD fornisce l'accesso ai dati dal codice tramite un set di API basate su REST. È possibile chiamare queste [API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) da numerosi strumenti e linguaggi di programmazione.

Gli eventi nel report di controllo di Azure AD vengono conservati per 180 giorni.

> [!Note]
> Per altre informazioni sulla conservazione dei report, vedere [Criteri di conservazione dei report di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Se si è interessati a conservare più a lungo gli eventi di controllo, usare l'API di creazione report per eseguire periodicamente il pull degli [eventi di controllo](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) in un archivio dati distinto.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Log delle macchine virtuali che usano Diagnostica di Azure

[Diagnostica di Azure](https://docs.microsoft.com/azure/azure-diagnostics) è la funzionalità integrata in Azure che consente la raccolta di dati di diagnostica in un'applicazione distribuita. È possibile usare l'estensione di diagnostica da una qualsiasi di varie origini. Sono attualmente supportati i [ruoli di lavoro e Web del servizio cloud di Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Log delle macchine virtuali che usano Diagnostica di Azure](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineslearnpathsdeploy-a-website-with-azure-virtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>[Macchine virtuali di Azure](/learn/paths/deploy-a-website-with-azure-virtual-machines/) che eseguono Microsoft Windows e [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)

È possibile abilitare la funzionalità Diagnostica di Azure in una macchina virtuale eseguendo una di queste operazioni:

* [Usare Visual Studio per tracciare macchine virtuali di Azure](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Configurare Diagnostica di Azure in remoto in una macchina virtuale di Azure](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [Usare PowerShell per configurare la diagnostica nelle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Creare una macchina virtuale Windows con monitoraggio e diagnostica mediante un modello di Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>di Analisi archiviazione

[Analisi archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) esegue la registrazione e fornisce le metriche dei dati per un account di archiviazione. È possibile utilizzare questi dati per tenere traccia delle richieste, analizzare le tendenze d'uso e diagnosticare i problemi relativi al proprio account di archiviazione. La registrazione dell'Analisi archiviazione di Azure è disponibile per i [servizi di archiviazione di BLOB, code e tabelle di Azure](https://docs.microsoft.com/azure/storage/storage-introduction). Analisi archiviazione registra informazioni dettagliate sulle richieste riuscite e non a un servizio di archiviazione.

È possibile usare queste informazioni per monitorare le singole richieste e per diagnosticare problemi relativi a un servizio di archiviazione. Le richieste vengono registrate in base al massimo sforzo. Le voci di registro vengono create solo se esistono richieste effettuate per l'endpoint di servizio. Se, ad esempio, un account di archiviazione presenta un'attività nell'endpoint BLOB ma non negli endpoint tabella o coda, saranno creati solo log relativi al servizio di archiviazione BLOB.

Per usare Analisi archiviazione, abilitarla singolarmente per ciascun servizio che si desidera monitorare. È possibile abilitarla nel [portale di Azure](https://portal.azure.com/). Per altre informazioni, vedere [Monitorare un account di archiviazione nel portale di Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). È inoltre possibile abilitare Analisi archiviazione a livello di codice tramite l'API REST o la libreria client. Per abilitare l'Analisi archiviazione per ogni servizio, usare le operazioni che consentono di impostare le proprietà dei servizi.

I dati aggregati vengono archiviati in un BLOB noto (per la registrazione) e in tabelle note (per le metriche), a cui è possibile accedere tramite le API del servizio di archiviazione BLOB e del servizio di archiviazione tabelle.

Analisi archiviazione può archiviare un massimo di 20 TB di dati. Tale limite è indipendente dal limite totale dell'account di archiviazione. Tutti i log vengono archiviati in [BLOB di blocchi](https://docs.microsoft.com/azure/storage/storage-analytics) in un contenitore denominato $logs, che viene creato automaticamente quando viene abilitata Analisi archiviazione per un account di archiviazione.

> [!Note]
> * Per altre informazioni sulla fatturazione e sui criteri di conservazione dei dati, vedere [Informazioni sull'analisi dell'archiviazione e sulla fatturazione](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * Per altre informazioni sui limiti dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Analisi archiviazione registra i tipi seguenti di richieste autenticate e anonime.

| Autenticata  | Anonima|
| :------------- | :-------------|
| Richieste riuscite | Richieste riuscite |
|Richieste non riuscite, tra cui errori di timeout, limitazione, rete, autorizzazione e di altro tipo | Richieste tramite una firma di accesso condiviso, incluse le richieste riuscite e non riuscite |
| Richieste tramite una firma di accesso condiviso, incluse le richieste riuscite e non riuscite |Errori di timeout per client e server |
|   Richieste ai dati di analisi |    Richieste GET non riuscite con codice di errore 304 (non modificate) |
| Le richieste eseguite dalla stessa Analisi archiviazione, ad esempio, la creazione oppure l'eliminazione di log, non vengono registrate. Un elenco completo dei dati registrati è documentato negli argomenti [Operazioni registrate di Analisi archiviazione e messaggi di stato](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [Formato log Analisi archiviazione](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Tutte le altre richieste anonime non riuscite non vengono registrate. Un elenco completo dei dati registrati è documentato negli argomenti [Operazioni registrate di Analisi archiviazione e messaggi di stato](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [Formato log Analisi archiviazione](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Log di rete di Azure

Il monitoraggio e la registrazione di rete in Azure comprende due categorie generali:

* [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Tra le funzionalità di questo servizio è incluso il monitoraggio di rete basato su scenari. Il servizio include l'acquisizione pacchetti, l'hop successivo, la verifica del flusso IP, la visualizzazione dei gruppi di sicurezza e i log dei flussi dei gruppi di sicurezza di rete. A differenza del monitoraggio a livello di singole risorse di rete, il monitoraggio a livello di scenario consente una visualizzazione completa delle risorse di rete.

* [Monitoraggio delle risorse](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Il monitoraggio a livello di risorsa include quattro funzionalità, ovvero log di diagnostica, metriche, risoluzione dei problemi e integrità delle risorse. Tutte queste funzionalità vengono compilate a livello di risorsa di rete.

![Log di rete di Azure](./media/azure-log-audit/azure-log-audit-fig4.png)

Network Watcher è un servizio a livello di area che permette di monitorare e diagnosticare le condizioni al livello di scenario di rete da, verso e all'interno di Azure. Gli strumenti di visualizzazione e diagnostica di rete disponibili in Network Watcher permettono di comprendere, diagnosticare e ottenere informazioni dettagliate sulla rete in Azure.

### <a name="network-security-group-flow-logging"></a>Registrazione dei flussi dei gruppi di sicurezza di rete

I [log dei flussi dei gruppi di sicurezza di rete (NSG)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) sono una funzionalità di Network Watcher con cui è possibile visualizzare le informazioni sul traffico IP in entrata e in uscita tramite un gruppo di sicurezza di rete. Questi log dei flussi sono scritti in formato JSON e mostrano:
* Flussi in ingresso e in uscita in base a ciascuna regola.
* La scheda di interfaccia di rete che si applica al flusso.
* Informazioni a 5 tuple sul flusso, IP di origine o destinazione, porta di origine o destinazione e protocollo.
* Indica se il traffico è stato consentito o negato.

Anche se i log dei flussi specificano come destinazione gruppi di sicurezza di rete, non vengono visualizzati come gli altri log. I log dei flussi vengono archiviati solo all'interno di un account di archiviazione.

Ai log dei flussi si applicano gli stessi criteri di conservazione degli altri log. Il criterio di conservazione dei log può essere impostato su un valore compreso tra 1 giorno e 365 giorni. Se non viene impostato alcun criterio di conservazione, i log vengono conservati per sempre.

**Log di diagnostica**

Le risorse di rete creano eventi periodici e spontanei, che vengono registrati negli account di archiviazione e inviati a un hub eventi oppure a Log Analytics. Questi log contengono informazioni dettagliate sull'integrità delle singole risorse e possono essere visualizzati con strumenti quali Power BI e Log Analytics. Per informazioni su come visualizzare i log di diagnostica, vedere [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Log di diagnostica](./media/azure-log-audit/azure-log-audit-fig5.png)

Sono disponibili log di diagnostica per il [servizio di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), i [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), le route e il [gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Network Watcher offre una visualizzazione dei log di diagnostica contenente tutte le risorse di rete che supportano la registrazione diagnostica. Da questa visualizzazione è possibile abilitare e disabilitare le risorse di rete in modo facile e veloce.


Oltre alle funzionalità di registrazione già citate, Network Watcher offre attualmente le funzionalità seguenti:
- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): Offre una visualizzazione a livello di rete che mostra le varie interconnessioni e le associazioni tra le risorse di rete in un gruppo di risorse.

- [Acquisizione pacchetti variabile](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): Acquisisce i dati dei pacchetti in ingresso e in uscita da una macchina virtuale. Opzioni di filtro avanzate e controlli ottimizzati, ad esempio impostazioni di limitazione di tempo e dimensioni, offrono versatilità. È possibile memorizzare i dati dei pacchetti in un archivio BLOB o nel disco locale in formato di file con estensione *cap*.

* [Verifica del flusso IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): Controlla se un pacchetto viene accettato o rifiutato in base ai relativi parametri sul flusso di informazioni, costituiti da informazioni a 5 tuple, ovvero l'indirizzo IP di destinazione, l'indirizzo IP di origine, la porta di destinazione, la porta di origine e il protocollo. Se il pacchetto viene rifiutato da un gruppo di sicurezza, vengono restituiti la regola e il gruppo che hanno rifiutato il pacchetto.

* [Hop successivo](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Determina l'hop successivo per i pacchetti instradati nell'infrastruttura di rete di Azure, permettendo così di diagnosticare eventuali route definite dall'utente non configurate in modo corretto.

* [Visualizzazione dei gruppi di sicurezza](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): Ottiene le regole di sicurezza valide e applicate in una macchina virtuale.

* [Risoluzione dei problemi di connessione e del gateway di rete virtuale](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Consente di risolvere i problemi di connessioni e gateway di rete virtuale.

* [Limiti delle sottoscrizioni di rete](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Consente di visualizzare l'uso delle risorse di rete rispetto ai limiti.

### <a name="application-insights"></a>Application Insights

[Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) è un servizio estendibile di monitoraggio delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme, che consente di monitorare applicazioni Web live. Il servizio rileva automaticamente le anomalie nelle prestazioni e include avanzati strumenti di analisi che consentono di diagnosticare i problemi e conoscere come viene effettivamente usata l'app dagli utenti.

Application Insights è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità.

Funziona per le app su un'ampia gamma di piattaforme, tra cui .NET, Node.js e Java EE, ospitate in locale o nel cloud. Si integra con il processo DevOps e offre punti di connessione per diversi strumenti di sviluppo.

![Diagramma di Application Insights](./media/azure-log-audit/azure-log-audit-fig6.png)

Application Insights è destinato al team di sviluppo, a cui consente di comprendere le prestazioni e le modalità d'uso dell'app. Esegue il monitoraggio di:

* **Frequenza delle richieste, tempi di risposta e percentuali di errore**: Consente di trovare le pagine più visitate, gli orari di visita e la posizione degli utenti. Vedere quali pagine abbiano prestazioni migliori. Se i tempi di risposta e le percentuali di errore aumentano di pari passo con le richieste, è probabile che ci sia un problema di assegnazione delle risorse.

* **Tassi di dipendenza, tempi di risposta e percentuali di errore**: Consente di individuare se i servizi esterni rallentano l'utente.

* **Eccezioni**: Consente di analizzare le statistiche aggregate o selezionare istanze specifiche e approfondire l'analisi dello stack e le richieste correlate. Vengono segnalate eccezioni di server e browser.

* **Prestazioni relative alle visualizzazioni pagina e al caricamento**: Ottenere i report dai browser degli utenti.

* **Chiamate AJAX**: Consente di ottenere tassi, tempi di risposta e percentuali di errore delle pagine Web.

* **Numeri di utenti e sessioni**.

* **Contatori delle prestazioni**: Consente di ottenere dati dai computer server Windows o Linux, ad esempio l'utilizzo di CPU, memoria e rete.

* **Diagnostica dell'host**: Consente di ottenere dati da Docker o Azure.

* **Log di traccia di diagnostica**: Consente di ottenere dati dall'app in modo da poter correlare gli eventi di traccia con le richieste.

* **Eventi e metriche personalizzati**: Consente di ottenere dati scritti dall'utente stesso nel codice del client o del server per tracciare eventi aziendali come gli articoli venduti o le partite vinte.

La tabella seguente elenca e descrive gli scenari di integrazione:

| Scenario di integrazione | DESCRIZIONE |
| --------------------- | :---------- |
|[Mappa delle applicazioni](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|I componenti dell'applicazione, con le metriche e gli avvisi chiave.||
|[Ricerca diagnostica dei dati dell'istanza](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Cercare e filtrare eventi come richieste, eccezioni, chiamate a dipendenze, tracce di log e visualizzazioni di pagina.||
|[Esplora metriche per i dati aggregati](https://docs.microsoft.com/azure/azure-monitor/app/metrics-explorer)|Esaminare, filtrare e segmentare dati aggregati come frequenza delle richieste, errori, eccezioni, tempi di risposta e tempi di caricamento delle pagine.||
|[Dashboard](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Combinare dati di più risorse e condividerli con altri utenti. Ideale per le applicazioni multi-componente e per la visualizzazione continua negli spazi del team.||
|[Flusso di metriche live](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)|Quando si distribuisce una nuova build, controllare questi indicatori delle prestazioni in tempo quasi reale per verificare che tutto funzioni come previsto.||
|[Analisi](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Questo avanzato linguaggio di query consente di trovare risposta a domande approfondite sull'utilizzo e sulle prestazioni dell'app.||
|[Avvisi automatici e manuali](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Gli avvisi automatici si adattano ai criteri normali di telemetria dell'app e si attivano quando i dati si discostano dal criterio consueto. È anche possibile impostare avvisi su livelli particolari delle metriche standard o personalizzate.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Visualizzare i dati sulle prestazioni nel codice. Passare al codice dall'analisi dello stack.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integrare le metriche di uso con altra business intelligence.||
|[API REST](https://dev.applicationinsights.io/)|Scrivere codice per eseguire query su metriche e dati non elaborati.||
|[Esportazione continua](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Eseguire l'esportazione bulk dei dati non elaborati nell'archivio quando arrivano.||

### <a name="azure-security-center-alerts"></a>Avvisi del Centro sicurezza di Azure

Il sistema di rilevamento delle minacce del Centro sicurezza di Azure funziona mediante la raccolta automatica di informazioni sulla sicurezza dalle risorse di Azure, dalla rete e dalle soluzioni dei partner connessi. Per identificare le minacce, analizza queste informazioni, correlando spesso quelle raccolte da più origini. Gli avvisi di sicurezza sono classificati in ordine di priorità nel Centro sicurezza insieme a indicazioni su come su correggere la minaccia. Per altre informazioni, vedere [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).

![Diagramma del Centro sicurezza di Azure](./media/azure-log-audit/azure-log-audit-fig7.png)

Il Centro sicurezza si avvale di analisi della sicurezza avanzate, che vanno ben oltre gli approcci basati sulle firme. Applica innovazioni in dati di grandi dimensioni e tecnologie di [apprendimento automatico](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) per valutare eventi nell'intera infrastruttura cloud. In questo modo vengono rilevate minacce altrimenti impossibili da identificare mediante approcci manuali e previsione dell'evoluzione di attacchi. Queste analisi della sicurezza includono:

* **Intelligence per le minacce integrata**: Cerca gli attori dannosi noti ricorrendo alle informazioni sulle minacce globali da prodotti e servizi Microsoft, Microsoft Digital Crimes Unit (DCU) e Microsoft Security Response Center (MSRC), nonché da feed esterni.

* **Analisi del comportamento**: Vengono applicati schemi noti per individuare comportamenti dannosi.

* **Rilevamento anomalie**: Usa la tecnica di profilatura statistica per creare una baseline cronologica. Genera avvisi sulle deviazioni dalle baseline stabilite che risultano conformi a un potenziale vettore di attacco .

Molti team dedicati alle operazioni di sicurezza e alla risposta ai problemi fanno affidamento su una soluzione di informazioni di sicurezza e gestione degli eventi come punto di partenza per la valutazione e l'analisi degli avvisi di sicurezza. Con Integrazione log di Azure è possibile sincronizzare gli avvisi del Centro sicurezza e gli eventi di sicurezza delle macchine virtuali, raccolti da Diagnostica di Azure e dai log di controllo, con la propria soluzione Log Analytics o di informazioni di sicurezza e gestione degli eventi quasi in tempo reale.

## <a name="log-analytics"></a>Log Analytics

Log Analytics è un servizio di Azure che permette di raccogliere e analizzare i dati generati dalle risorse nel cloud e negli ambienti locali. Offre informazioni dettagliate in tempo reale usando la ricerca integrata e i dashboard personalizzati per analizzare rapidamente milioni di record in tutti i carichi di lavoro e i server, indipendentemente dalla loro posizione fisica.

![Diagramma di Log Analytics](./media/azure-log-audit/azure-log-audit-fig8.png)

Al centro di Log Analytics è presente l'area di lavoro, ospitata nel cloud di Azure. Log Analytics raccoglie i dati nell'area di lavoro da origini connesse configurando le origini dati e aggiungendo soluzioni alla sottoscrizione. Origini dati e soluzioni creano tipi di record diversi, ognuno con un proprio set di proprietà. Ma origini e soluzioni possono comunque essere analizzate insieme nelle query all'area di lavoro. Questa funzionalità consente di usare gli stessi strumenti e metodi per lavorare con diversi tipi di dati raccolti da diverse origini.

Le origini connesse sono i computer e altre risorse che generano dati raccolti da Log Analytics. Le origini possono includere gli agenti installati nei computer [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) e [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) che si connettono direttamente o gli agenti in un [gruppo di gestione di System Center Operations Manager connesso](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Log Analytics può anche raccogliere dati da [account di archiviazione di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

Le [origini dati](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) sono i diversi tipi di dati raccolti da ogni origine connessa. Le origini includono eventi e [dati sulle prestazioni](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) ricavati dagli agenti [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) e Linux, oltre a origini quali i [log di IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) e i [log di testo personalizzati](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). È possibile configurare ciascuna origine dati che si vuole raccogliere e la configurazione viene inviata automaticamente a ogni origine connessa.

Esistono quattro modi per [raccogliere log e metriche per i servizi di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage):

* Da Diagnostica di Azure direttamente a Log Analytics (**Diagnostica** nella tabella seguente)

* Da Diagnostica di Azure ad Archiviazione di Azure a Log Analytics (**Archiviazione** nella tabella seguente)

* Connettori per i servizi di Azure (**Connettore** nella tabella seguente)

* Script per raccogliere e pubblicare i dati in Log Analytics (celle vuote nella tabella seguente e per i servizi non elencati)

| Service | Tipo di risorsa | Log | Metriche | Soluzione |
| :------ | :------------ | :--- | :------ | :------- |
|Gateway applicazione di Azure| Microsoft.Network/<br>applicationGateways|  Diagnostica|Diagnostica|    [Analisi dei ](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)gateway applicazione[ di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Connettore|  Connettore|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)[Connector (Anteprima)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Account di Automazione di Azure| Microsoft.Automation/<br>AutomationAccounts|    Diagnostica||       [Altre informazioni](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Account di Azure Batch|  Microsoft.Batch/<br>batchAccounts|  Diagnostica|    Diagnostica||
|Servizi cloud classici||       Archiviazione||       [Altre informazioni](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Servizi cognitivi|    Microsoft.CognitiveServices/<br>account|       Diagnostica|||
|Azure Data Lake Analytics.| Microsoft.DataLakeAnalytics/<br>account|   Diagnostica|||
|Archivio Azure Data Lake| Microsoft.DataLakeStore/<br>account|   Diagnostica|||
|Spazio dei nomi dell'hub eventi| Microsoft.EventHub/<br>spazi dei nomi|  Diagnostica|    Diagnostica||
|Hub IoT Azure| Microsoft.Devices/<br>Hub IoT||     Diagnostica||
|Azure Key Vault|   Microsoft.KeyVault/<br>insiemi di credenziali|  Diagnostica  || [Analisi dell'insieme di credenziali delle chiavi](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    Diagnostica|||
|App per la logica di Azure|  Microsoft.Logic/<br>flussi di lavoro|  Diagnostica|    Diagnostica||
||Microsoft.Logic/<br>integrationAccounts||||
|Gruppi di sicurezza di rete|   Microsoft.Network/<br>networksecuritygroups|Diagnostica||   [Analisi del gruppo di sicurezza di rete di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Insiemi di credenziali di ripristino|   Microsoft.RecoveryServices/<br>insiemi di credenziali|||[Azure Recovery Services Analytics (Anteprima)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Servizi di ricerca|   Microsoft.Search/<br>searchServices|    Diagnostica|    Diagnostica||
|Spazio dei nomi del bus di servizio| Microsoft.ServiceBus/<br>spazi dei nomi|    Diagnostica|Diagnostica|    [Service Bus Analytics (Anteprima)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Archiviazione||    [Service Fabric Analytics (anteprima)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (versione 12)| Microsoft.Sql/<br>servers/<br>database||       Diagnostica||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|Archiviazione|||         Script| [Azure Storage Analytics (Anteprima)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Macchine virtuali di Azure|    Microsoft.Compute/<br>virtualMachines|  Estensione|  Estensione||
||||Diagnostica||
|set di scalabilità di macchine virtuali|    Microsoft.Compute/<br>virtualMachines    ||Diagnostica||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|Server farm Web|Microsoft.Web/<br>serverfarms||   Diagnostica
|Siti Web|  Microsoft.Web/<br>siti ||      Diagnostica|    [Altre informazioni](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites/<br>slot|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Integrazione log con sistemi locali di informazioni di sicurezza e gestione degli eventi

Con Integrazione log di Azure è possibile integrare log non elaborati delle risorse di Azure nel sistema locale di informazioni di sicurezza e gestione degli eventi locali (SIEM). Il download di AzLog è stato disabilitato il 27 giugno 2018. Per materiale sussidiario su cosa fare dopo, vedere il post [Use Azure monitor to integrate with SIEM tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) (Usare Monitoraggio di Azure per eseguire l'integrazione con gli strumenti per le informazioni di sicurezza e gestione degli eventi)

![Diagramma di Integrazione log](./media/azure-log-audit/azure-log-audit-fig9.png)

Integrazione log raccoglie i dati di Diagnostica di Azure dalle macchine virtuali Windows, dai log attività di Azure, dagli avvisi del Centro sicurezza di Azure e dai log dei provider di risorse di Azure. Questa integrazione fornisce un dashboard unificato per tutti gli asset, locali o su cloud, consentendo di aggregare, correlare, analizzare e inviare avvisi per gli eventi di sicurezza.

Integrazione log supporta attualmente l'integrazione dei log attività di Azure, del log eventi delle macchine virtuali Windows incluse nella sottoscrizione di Azure, degli avvisi del Centro sicurezza di Azure, dei log di Diagnostica di Azure e dei log di controllo di Azure AD.

| Tipo di log | Log Analytics con supporto JSON (Splunk, ArcSight e IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Log di controllo di Azure AD|   Sì|
|Log attività| Sì|
|Avvisi del Centro sicurezza |Sì|
|Log di diagnostica (log di risorse)|  Sì|
|Log VM|   Sì, tramite eventi inoltrati e non attraverso JSON|

[Introduzione a Integrazione log di Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started): Questa esercitazione illustra come installare Integrazione log di Azure e integrare i log dall'archiviazione di Azure, dai log attività di Azure, dagli avvisi del Centro sicurezza di Azure e dai log di controllo di Azure AD.

Scenari di integrazione per informazioni di sicurezza e gestione degli eventi:

* [Partner configuration steps (Procedura per la configurazione partner)](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): Questo post di blog illustra come configurare Integrazione log di Azure per usare le soluzioni partner Splunk, HP ArcSight e IBM QRadar.

* [Domande frequenti su Integrazione log di Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq): Questo articolo contiene le risponde alle domande sull'integrazione dei log di Azure.

* [Integrazione degli avvisi del Centro sicurezza con Integrazione log di Azure](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration): Questo articolo mostra come sincronizzare gli avvisi del Centro sicurezza di Azure, insieme agli eventi di sicurezza delle macchine virtuali raccolti da Diagnostica di Azure e dai log di controllo di Azure, con la propria soluzione Log Analytics o di informazioni di sicurezza e gestione degli eventi.

## <a name="next-steps"></a>Passaggi successivi

- [Controllo e registrazione](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview): Proteggere i dati mantenendo la visibilità e rispondendo rapidamente agli avvisi di sicurezza tempestivi.

- [Raccolta dei log di controllo e di registrazione di sicurezza all'interno di Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Applicare queste impostazioni per assicurarsi che le istanze di Azure raccolgano i log di controllo e di sicurezza corretti.

- [Configurare le impostazioni di controllo per una raccolta siti](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Gli amministratori di raccolte siti possono recuperare la cronologia delle azioni di singoli utenti e di quelle eseguite durante un intervallo di date specifico. 

- [Cercare il log di controllo nel Centro sicurezza e conformità di Office 365](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Il Centro sicurezza e conformità di Office 365 consente di cercare il log di controllo unificato e visualizzare l'attività dell'utente e dell'amministratore nell'organizzazione di Office 365.


