---
title: Registrazione e controllo di Azure | Microsoft Docs
description: Informazioni sull'uso dei dati di registrazione per ottenere informazioni approfondite sull'applicazione.
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 032aa4a6cedd49ff9c3b4803561b8b187e8f9af5
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="azure-logging-and-auditing"></a>Registrazione e controllo di Azure
## <a name="introduction"></a>Introduzione
### <a name="overview"></a>Panoramica
Per consentire ai clienti attuali e potenziali di Azure di comprendere e usare le diverse funzionalità correlate alla sicurezza disponibili nel contesto della piattaforma Azure, Microsoft ha sviluppato una serie di white paper, panoramiche sulla sicurezza, procedure consigliate ed elenchi di controllo. Gli argomenti, che variano nei contenuti e negli approfondimenti, vengono aggiornati periodicamente. Il presente documento fa parte di questa serie, come riepilogato nella sezione Sunto di seguito.
### <a name="azure-platform"></a>Piattaforma Azure
Azure è una piattaforma aperta e flessibile di servizi cloud che supporta la più ampia gamma di sistemi operativi, linguaggi di programmazione, framework, strumenti, database e dispositivi.

Ad esempio, è possibile:
-   Eseguire i contenitori Linux con l'integrazione Docker.

-   Compilare le app con JavaScript, Python, .NET, PHP, Java e Node.js.

-   Compilare i back-end per dispositivi iOS, Android e Windows.

I servizi cloud pubblici di Azure supportano le stesse tecnologie già considerate affidabili e usate da milioni di sviluppatori e professionisti IT.

Quando si compilano asset IT o se ne esegue la migrazione in un provider di servizi cloud, si dipende dalla capacità di tale organizzazione di proteggere le applicazioni e i dati con i servizi e i controlli forniti per gestire la sicurezza degli asset basati sul cloud.

L'infrastruttura di Azure è stata progettata, dalla struttura fino alle applicazioni, per ospitare milioni di clienti contemporaneamente e fornisce alle aziende una solida base per poter soddisfare le esigenze di sicurezza. Azure offre anche un'ampia gamma di opzioni di sicurezza configurabili, con la possibilità di controllarle per poter personalizzare la sicurezza e soddisfare così i requisiti univoci di ogni distribuzione. Grazie a questo documento è possibile soddisfare tali requisiti.

### <a name="abstract"></a>Sunto
Il controllo e la registrazione di eventi relativi alla sicurezza e di avvisi correlati, sono componenti importanti di una strategia di protezione dei dati efficace. I report e i log di sicurezza offrono un record elettronico delle attività sospette e aiutano a rilevare modelli che possono segnalare tentativi esterni di penetrazione nella rete e attacchi interni. È possibile usare la funzione di controllo per monitorare l'attività dell'utente, documentare la conformità alle normative, eseguire analisi forensi e altro ancora. Tramite gli avvisi si ottiene la notifica immediata quando si verificano eventi che riguardano la sicurezza.

I prodotti e servizi di Microsoft Azure offrono opzioni di controllo e registrazione di sicurezza configurabili che consentono di identificare e correggere i gap nei meccanismi e nei criteri di sicurezza per evitare violazioni. I servizi di Microsoft offrono alcune (e in alcuni casi, tutte) delle opzioni seguenti: sistemi centralizzati di monitoraggio, registrazione e analisi per una visibilità continua; avvisi tempestivi; report che consentono di gestire i grandi volumi di informazioni provenienti da dispositivi e servizi.

I dati di log di Microsoft Azure possono essere esportati in sistemi SIEM per l'analisi e si integrano con le soluzioni di controllo di terze parti.

Questo white paper offre un'introduzione alle operazioni di creazione, raccolta e analisi dei log di sicurezza dai servizi ospitati in Azure, oltre ad aiutare i clienti a ottenere informazioni più approfondite sulla sicurezza nelle proprie distribuzioni di Azure. L'ambito di questo white paper è limitato alle applicazioni e servizi creati e distribuiti in Azure.

> [!Note]
> Alcune indicazioni contenute in questo documento possono comportare un maggior consumo delle risorse di calcolo, di rete o dei dati, con un aumento dei costi di licenza o sottoscrizione.

## <a name="types-of-logs-in-azure"></a>Tipi di log in Azure
Le applicazioni cloud sono complesse e hanno molte parti mobili. I log offrono la possibilità di garantire il funzionamento e l'integrità dell'applicazione. Consente anche di prevenire i problemi potenziali o di risolvere quelli precedenti. Inoltre, è possibile usare i dati di registrazione per ottenere informazioni approfondite sull'applicazione. utili per migliorarne le prestazioni o la manutenibilità oppure per automatizzare azioni che altrimenti richiederebbero un intervento manuale.

Azure produce registrazioni complete per ogni servizio di Azure. I log sono classificati nei seguenti tipi principali:
-   **Log di gestione/controllo**, che offrono visibilità sulle operazioni CREATE, UPDATE e DELETE di Azure Resource Manager. [I log attività di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) sono un esempio di questo tipo di log.

-   **Log del piano dati**, che offrono visibilità sugli eventi generati come parte dell'uso di una risorsa di Azure. Sono esempi di questo tipo il log eventi di sistema di Windows, il log di sicurezza, il log applicazioni di una macchina virtuale e i [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) configurati tramite Monitoraggio di Azure.


-   **Eventi elaborati**, che offrono informazioni sugli eventi o avvisi analizzati dopo essere stati elaborati per conto dell'utente. Esempi di questo tipo sono i brevi [avvisi emessi dal Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) dopo aver eseguito l'[elaborazione e l'analisi della sottoscrizione](https://docs.microsoft.com/azure/security-center/security-center-intro).

La tabella seguente elenca i più importanti tipi di log disponibili in Azure.

| Categoria di log | Tipo di log | Usi | Integrazione |
| ------------ | -------- | ------ | ----------- |
|[Log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Gli eventi del piano di controllo sulle risorse di Azure Resource Manager|   Offrono informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione.| API REST e [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Log di diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|dati frequenti sul funzionamento delle risorse di Azure Resource Manager nella sottoscrizione| Offrono informazioni approfondite sulle operazioni eseguite dalla risorsa stessa| Monitoraggio di Azure, [Stream](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Creazione di report in AAD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Log e report|Attività di accesso dell'utente e informazioni sulle attività di sistema riguardo alla gestione di utenti e gruppi|[API Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Macchine virtuali e servizi cloud](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)|Log eventi di Windows e Syslog Linux|    Acquisisce i dati di sistema e i dati di registrazione nelle macchine virtuali e li trasferisce all'account di archiviazione desiderato.|   Windows tramite [WAD](https://docs.microsoft.com/azure/azure-diagnostics) (archiviazione di diagnostica di Windows Azure) e Linux in Monitoraggio di Azure|
|[Analisi archiviazione](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Esegue la registrazione di archiviazione e offre i dati delle metriche per un account di archiviazione.|Offre informazioni approfondite per tenere traccia delle richieste, analizzare le tendenze d'uso e diagnosticare i problemi relativi al proprio account di archiviazione.|    API REST o [libreria client](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Log dei flussi del gruppo di sicurezza di rete (NSG)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|Formato JSON e mostra i flussi in ingresso e in uscita in base a ciascuna regola|Visualizzare le informazioni sul traffico IP in entrata e in uscita tramite un gruppo di sicurezza di rete|[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Log, eccezioni e diagnostica personalizzata|    Servizio APM di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme.| API REST, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Dati di elaborazione/avvisi di sicurezza| Avviso del Centro sicurezza di Azure, avviso OMS| Informazioni e avvisi sulla sicurezza.|   API REST, JSON|

### <a name="activity-log"></a>Log attività
Il [log attività di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) offre informazioni approfondite in merito alle operazioni eseguite sulle risorse nella sottoscrizione. Il log attività era noto in precedenza come "log di controllo" o "log operativi", perché segnala [eventi del piano di controllo](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) per le sottoscrizioni dell'utente. L'uso del log attività permette di acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse nella sottoscrizione. Consente inoltre di comprendere lo stato dell'operazione e altre proprietà specifiche. Il log attività non include le operazioni di lettura (GET)

I termini PUT, POST, DELETE si riferiscono a tutte le operazioni di scrittura sulle risorse contenute nel log attività. Ad esempio, è possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

![Log attività](./media/azure-log-audit/azure-log-audit-fig1.png)


Per recuperare eventi dal log attività è possibile usare il portale di Azure, l'[interfaccia della riga di comando](https://docs.microsoft.com/azure/storage/storage-azure-cli), i cmdlet di PowerShell e l'[API REST di Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Il periodo di conservazione dei dati per i log attività è di 19 giorni.

Scenari di integrazione
-   [Creare un avviso di posta elettronica o webhook che attiva un evento del log attività.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email)

-   [Trasmetterlo a un hub eventi](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) per l'inserimento da parte di un servizio di terze parti o di una soluzione di analisi personalizzata come Power BI.

-   Analizzarlo in Power BI usando il [pacchetto di contenuto di Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

-   [Salvarlo in un account di archiviazione per l'archiviazione o l'ispezione manuale](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). È possibile specificare il tempo di conservazione in giorni tramite i profili di log.

-   Eseguire query e visualizzarla nel portale di Azure.

-   Eseguire query tramite l'API REST, i cmdlet di PowerShell o l'interfaccia della riga di comando.

-   Esportare il log attività con i profili di log in [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

È possibile usare un account di archiviazione o lo [spazio dei nomi dell'hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) che non sia nella stessa sottoscrizione di quello che crea il log. L'utente che configura l'impostazione deve disporre dell'accesso [RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) appropriato a entrambe le sottoscrizioni.
### <a name="azure-diagnostic-logs"></a>Log di diagnostica di Azure
I log di diagnostica di Azure sono generati da una risorsa che offre dati completi e frequenti sul suo funzionamento. Il contenuto di questi log varia in base al tipo di risorsa. Ad esempio, i [log eventi del sistema di Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) sono una categoria di log di diagnostica per le VM, mentre i[ log delle code, delle tabelle e dei BLOB](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) sono categorie di log di diagnostica per gli account di archiviazione. I log di diagnostica sono diversi dal log attività, che offre informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione.

![Log di diagnostica di Azure](./media/azure-log-audit/azure-log-audit-fig2.png)

I log di diagnostica di Azure offrono più opzioni di configurazione, vale a dire che il portale di Azure può usare l'API REST, PowerShell e l'interfaccia della riga di comando (CLI).

**Scenari di integrazione**
-   Salvarli in un [account di archiviazione](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione in giorni tramite le Impostazioni di diagnostica.

-   [Trasmetterli a ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs)Hub eventi per l'inserimento da parte di un servizio di terze parti o una soluzione di analisi personalizzata come [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

-   Analizzarli con [OMS Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

**Servizi supportati, schema per i log di diagnostica e categorie di log supportati per ogni tipo di risorsa**


| Service | Schema e documenti | Tipo di risorsa | Categoria |
| ------- | ------------- | ------------- | -------- |
|Bilanciamento del carico| [Analisi dei log per il servizio di bilanciamento del carico di Azure (anteprima)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent|
|||Microsoft.Network/loadBalancers| LoadBalancerProbeHealthStatus
|Gruppi di sicurezza di rete|[Analisi dei log per i gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|
|||Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|
|Gateway applicazione|[Registrazione diagnostica per il gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|
|Insieme di credenziali di chiave|[Registrazione dell'insieme di credenziali delle chiavi di Azure](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Ricerca di Azure|[Abilitazione e uso di Analisi del traffico di ricerca](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Archivio Data Lake|[Accesso ai log di diagnostica per Archivio Data Lake di Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts|Audit|
|Data Lake Analytics|[Accesso ai log di diagnostica per Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts|Audit|
|||Microsoft.DataLakeAnalytics/accounts|Requests|
|||Microsoft.DataLakeStore/accounts|Requests|
|App per la logica|[Schema di rilevamento personalizzato per le app per la logica B2B](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows|WorkflowRuntime|
|||Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|
|Azure Batch|[Registrazione diagnostica di Azure Batch](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Automazione di Azure|[Analisi dei log per Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts|JobLogs|
|||Microsoft.Automation/automationAccounts|JobStreams|
|Hub eventi|[Log di diagnostica di Hub eventi in Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces|ArchiveLogs|
|||Microsoft.EventHub/namespaces|OperationalLogs|
|Analisi dei flussi|[Log di diagnostica dei processi](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs|Esecuzione|
|||Microsoft.StreamAnalytics/streamingjobs|Creazione|
|Bus di servizio|[Log di diagnostica del bus di servizio di Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Creazione di report in Azure Active Directory
Azure Active Directory (Azure AD) include la sicurezza, l’attività e i report di controllo per la directory. La [Guida alla creazione di report in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) consente ai clienti di identificare le azioni con privilegi che si sono verificate nella propria istanza di Azure Active Directory. Le azioni con privilegi includono modifiche di elevazione dei privilegi, ad esempio la creazione dei ruoli o le reimpostazioni delle password, la modifica delle configurazioni dei criteri, ad esempio i criteri delle password o le modifiche alla configurazione della directory, ad esempio le modifiche alle impostazioni di federazione del dominio.

Nei report è incluso il record di controllo per il nome dell'evento, l'attore che ha eseguito l'azione, la risorsa di destinazione interessata dalla modifica e la data e l'ora (UTC). I clienti possono recuperare l'elenco degli eventi di controllo per la propria istanza di Azure Active Directory tramite il [portale di Azure](https://portal.azure.com/), come descritto in [Visualizzare i log di controllo](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Di seguito è riportato un elenco dei report compresi:

| Report sulla sicurezza | Report sull’attività | Report di controllo |
| :--------------- | :--------------- | :------------ |
|Accessi da origini sconosciute| Utilizzo dell'applicazione: riepilogo| Report di controllo della Directory|
|Accessi dopo più errori|  Utilizzo dell'applicazione: dettagliato||
|Accessi da più aree geografiche|    Dashboard dell'applicazione||
|Accessi da indirizzi IP con attività sospette|   Errori di provisioning dell'account||
|Attività di accesso irregolare|    Dispositivi dell’utente singolo||
|Accessi da dispositivi potenzialmente infetti|   Attività dell’utente singolo||
|Utenti con anomalie dell'attività di accesso| Report attività dei gruppi||
||Report attività di registrazione per la reimpostazione password||
||Attività di reimpostazione password|||

I dati di questi report possono essere molto utili per le applicazioni, ad esempio i sistemi SIEM e gli strumenti di controllo e business intelligence. L'API di creazione report di Azure AD fornisce l'accesso ai dati dal codice tramite un set di API basate su REST. È possibile chiamare queste [API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) da numerosi strumenti e linguaggi di programmazione.

Gli eventi nel report di controllo di Azure Active Directory vengono conservati per 180 giorni.

> [!Note]
> Per altre informazioni sulla conservazione dei report, vedere la pagina [Criteri di conservazione dei report di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Per i clienti interessati alla conservazione gli eventi di controllo per periodi più lunghi, l'API di creazione report consente di eseguire regolarmente il pull degli [eventi di controllo](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) in un archivio dati separato.

### <a name="virtual-machine-logs-using-azure-diagnostics"></a>i log delle macchine virtuali con Diagnostica di Azure
[Diagnostica di Azure](https://docs.microsoft.com/azure/azure-diagnostics) è la funzionalità integrata in Azure che consente la raccolta di dati di diagnostica in un'applicazione distribuita. È possibile usare l'estensione di diagnostica da alcune origini differenti. Sono attualmente supportati i [ruoli di lavoro e Web del servizio cloud di Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me) e

![i log delle macchine virtuali con Diagnostica di Azure](./media/azure-log-audit/azure-log-audit-fig3.png)

[Macchine virtuali di Azure](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) che eseguono Microsoft Windows e [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview).

È possibile abilitare la funzionalità Diagnostica di Azure in una macchina virtuale nei modi seguenti:

-   Usando Visual Studio (vedere [Debug di un servizio cloud o di una macchina virtuale di Azure in Visual Studio)](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

-   [Configurare Diagnostica di Azure in una macchina virtuale Azure da remoto](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

-   [Usare PowerShell per abilitare Diagnostica di Azure nelle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

-   [Creare una macchina virtuale Windows con monitoraggio e diagnostica con un modello di Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>di Analisi archiviazione
L'[Analisi archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) esegue la registrazione e restituisce i dati delle metriche per un account di archiviazione. È possibile utilizzare questi dati per tenere traccia delle richieste, analizzare le tendenze d'uso e diagnosticare i problemi relativi al proprio account di archiviazione. La registrazione di Analisi archiviazione è disponibile per i [servizi BLOB, di accodamento e tabelle](https://docs.microsoft.com/azure/storage/storage-introduction). Analisi archiviazione registra informazioni dettagliate sulle richieste riuscite e non a un servizio di archiviazione.

Queste informazioni possono essere utilizzate per monitorare le singole richieste e per diagnosticare problemi relativi a un servizio di archiviazione. Le richieste vengono registrate in base al massimo sforzo. Le voci di registro vengono create solo se esistono richieste effettuate per l'endpoint di servizio. Se, ad esempio, un account di archiviazione presenta un'attività nell'endpoint BLOB ma non negli endpoint di tabelle o di accodamento, saranno creati solo log relativi al servizio BLOB.

Per utilizzare Analisi archiviazione, è necessario abilitarla singolarmente per ciascun servizio che si desidera monitorare. È possibile abilitarla nel [portale di Azure](https://portal.azure.com/); per informazioni dettagliate, vedere [Monitorare un account di archiviazione nel portale di Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). È inoltre possibile abilitare Analisi archiviazione a livello di codice tramite l'API REST o la libreria client. Per abilitare l'Analisi archiviazione per ogni servizio, usare le operazioni che consentono di impostare le proprietà dei servizi.

I dati aggregati vengono archiviati in un BLOB noto (per la registrazione) e in tabelle note (per le metriche), a cui è possibile accedere tramite le API del servizio BLOB e del servizio tabelle.

L'Analisi archiviazione può archiviare fino a un massimo di 20 TB di dati. Tale limite è indipendente dal limite totale dell'account di archiviazione. Tutti i log vengono archiviati in [BLOB in blocchi](https://docs.microsoft.com/azure/storage/storage-analytics) all'interno di un contenitore denominato $logs, che viene creato automaticamente quando viene abilitata l'Analisi archiviazione per un account di archiviazione.

> [!Note]
> Per altre informazioni sulla fatturazione e sui criteri di conservazione dei dati, vedere [Analisi archiviazione e fatturazione](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
>
> [!Note]
> Per informazioni sui limiti dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Vengono registrati i tipi seguenti di richieste autenticate e anonime.



| Autenticata  | Anonima|
| :------------- | :-------------|
| Richieste riuscite | Richieste riuscite |
|Richieste non riuscite, tra cui errori di timeout, limitazione, rete, autorizzazione e di altro tipo | Richieste tramite una firma di accesso condiviso (SAS), incluse le richieste riuscite e non riuscite |
| Richieste tramite una firma di accesso condiviso (SAS), incluse le richieste riuscite e non riuscite |Errori di timeout per client e server |
|   Richieste ai dati di analisi |    Richieste GET non riuscite con codice di errore 304 (non modificate) |
| Le richieste eseguite dalla stessa Analisi archiviazione, ad esempio, la creazione oppure l'eliminazione di log, non vengono registrate. Un elenco completo dei dati registrati è documentato negli argomenti [Operazioni registrate in Analisi archiviazione e messaggi di stato](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [Formato log Analisi archiviazione](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Tutte le altre richieste anonime non riuscite non vengono registrate. Un elenco completo dei dati registrati è documentato negli argomenti [Storage Analytics Logged Operations and Status Messages](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) (Operazioni registrate e messaggi di stato nell'Analisi archiviazione ) e [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) (Formato dei registri di Analisi archiviazione). |

### <a name="azure-networking-logs"></a>Log di rete di Azure
Il monitoraggio e la registrazione di rete in Azure comprende due categorie generali:

-   [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher): tra le funzionalità di questo servizio è incluso il monitoraggio di rete basato su scenari. Il servizio include l'acquisizione pacchetti, l'hop successivo, la verifica del flusso IP, la visualizzazione dei gruppi di sicurezza e i registri dei flussi dei gruppi di sicurezza di rete. A differenza del monitoraggio a livello di singole risorse di rete, il monitoraggio a livello di scenario consente una visualizzazione completa delle risorse di rete.

-   [Monitoraggio delle risorse](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring): il monitoraggio a livello di risorsa include funzionalità di log di diagnostica, metriche, risoluzione dei problemi e integrità delle risorse. Tutte queste funzionalità vengono compilate a livello di risorsa di rete.

![Log di rete di Azure](./media/azure-log-audit/azure-log-audit-fig4.png)

Network Watcher è un servizio a livello di area che permette di monitorare e diagnosticare le condizioni al livello di scenario di rete da, verso e all'interno di Azure. Gli strumenti di visualizzazione e diagnostica di rete disponibili in Network Watcher permettono di comprendere, diagnosticare e ottenere informazioni dettagliate sulla rete in Azure.

**Registrazione dei flussi del gruppo di sicurezza di rete**: la registrazione dei flussi del gruppo di sicurezza di rete permette di acquisire i log relativi al traffico consentito o negato dalle regole di sicurezza nel gruppo. Sono scritti in formato JSON e mostrano i flussi in ingresso e in uscita in base a regole, scheda di rete a cui si applica il flusso, informazioni su 5 tuple relative al flusso (IP di origine/destinazione, porta di origine/destinazione, protocollo), e se il traffico è consentito o meno.

### <a name="network-security-group-flow-logging"></a>Registrazione dei flussi del gruppo di sicurezza di rete

I [log dei flussi del gruppo di sicurezza di rete](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) sono una funzionalità di Network Watcher che consente di visualizzare le informazioni sul traffico IP in entrata e in uscita tramite un gruppo di sicurezza di rete. Sono scritti in formato JSON e mostrano i flussi in ingresso e in uscita in base a regole, scheda di rete a cui si applica il flusso, informazioni su 5 tuple relative al flusso (IP di origine/destinazione, porta di origine/destinazione, protocollo), e se il traffico è consentito o meno.

Anche se i log dei flussi specificano come destinazione gruppi di sicurezza di rete, non vengono visualizzati come gli altri log. I log dei flussi vengono archiviati solo all'interno di un account di archiviazione.

Ai log dei flussi si applicano gli stessi criteri di conservazione degli altri log. Il criterio di conservazione dei log può essere impostato su un valore compreso tra 1 giorno e 365 giorni. Se non viene impostato alcun criterio di conservazione, i log vengono conservati per sempre.

**Log di diagnostica**

Le risorse di rete creano eventi periodici e spontanei, che vengono registrati negli account di archiviazione e inviati a un hub eventi oppure a Log Analytics. Questi log contengono informazioni dettagliate sull'integrità delle singole risorse e possono essere visualizzati con strumenti quali Power BI e Log Analytics. Per informazioni su come visualizzare i log di diagnostica, vedere l'articolo relativo a [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Log di diagnostica](./media/azure-log-audit/azure-log-audit-fig5.png)

Sono disponibili log di diagnostica per il [servizio di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), i [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), le route e il [gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Network Watcher offre una visualizzazione dei log di diagnostica contenente tutte le risorse di rete che supportano la registrazione diagnostica. Da questa visualizzazione è possibile abilitare e disabilitare le risorse di rete in modo facile e veloce.


Oltre alle precedenti funzionalità di registrazione, Network Watcher dispone attualmente delle funzionalità seguenti:
- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): offre una visualizzazione a livello di rete che mostra le varie interconnessioni e le associazioni tra le risorse di rete in un gruppo di risorse.

- [Acquisizione pacchetti variabile](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): acquisisce i dati dei pacchetti all'interno e all'esterno di una macchina virtuale. La versatilità è data dalle opzioni di filtro avanzato e dai controlli ottimizzati, come la possibilità di impostare l'ora e il limite di dimensioni. I dati dei pacchetti possono essere archiviati in un'archiviazione BLOB o in un disco locale nel formato .cap.

-   [Verifica flusso IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): verifica se un pacchetto viene accettato o rifiutato in base ai relativi parametri sul flusso di informazioni, costituiti da informazioni a 5 tuple, ovvero l'indirizzo IP di destinazione, l'indirizzo IP di origine, la porta di destinazione, la porta di origine e il protocollo. Se il pacchetto viene rifiutato da un gruppo di sicurezza, vengono restituiti la regola e il gruppo che hanno rifiutato il pacchetto.

-   [Hop successivo](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): determina l'hop successivo per i pacchetti indirizzati nell'infrastruttura di rete di Azure, permettendo così di diagnosticare eventuali route definite dall'utente non configurate in modo corretto.

-   [Visualizzazione dei gruppi di sicurezza](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): ottiene le regole di sicurezza valide e applicate in una macchina virtuale.

-   [Risoluzione dei problemi di connessione e gateway di rete virtuale](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): permette di risolvere i problemi relativi al gateway di rete virtuale e alle connessioni.

-   [Limite sottoscrizioni di rete](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits): permette di visualizzare l'uso delle risorse di rete rispetto ai limiti.

### <a name="application-insight"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme, che consente di monitorare un'applicazione Web live. Il servizio rileva automaticamente le anomalie nelle prestazioni e include avanzati strumenti di analisi che consentono di diagnosticare i problemi e conoscere come viene effettivamente usata l'app dagli utenti.

 Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità.

 Funziona per le app su un'ampia gamma di piattaforme, tra cui .NET o J2EE, ospitate in locale o nel cloud. Si integra con il processo DevOps e offre punti di connessione per diversi strumenti di sviluppo.

![Application Insights](./media/azure-log-audit/azure-log-audit-fig6.png)

Application Insights è destinato al team di sviluppo, a cui consente di comprendere le prestazioni e le modalità d'uso dell'app. Esegue il monitoraggio di:

-   **Frequenza delle richieste, tempi di risposta e percentuali di errore**: trovare le pagine più visitate, gli orari di visita e la posizione degli utenti.  Vedere quali pagine abbiano prestazioni migliori. Se i tempi di risposta e le percentuali di errore aumentano di pari passo con le richieste, è probabile che ci sia un problema di assegnazione delle risorse.

-   **Tassi di dipendenza, tempi di risposta e percentuali di errore**: trovare quali servizi esterni causino un rallentamento.

-   **Eccezioni**: analizzare le statistiche aggregate o selezionare istanze specifiche e approfondire l'analisi dello stack e le richieste correlate. Vengono segnalate eccezioni di server e browser.

-   **Visualizzazioni pagina e prestazioni di carico**, segnalate dai browser degli utenti.

-   **Chiamate AJAX** dalle pagine Web: tassi, tempi di risposta e percentuali di errore.

-   **Numeri di utenti e sessioni**.

-   **Contatori delle prestazioni** dai computer server Windows o Linux, ad esempio l'uso di CPU, memoria e rete.

-   **Diagnostica dell'host** da Docker o Azure.

-   **Log di traccia di diagnostica** dall'app, in modo da poter correlare gli eventi di traccia con le richieste.

-   **Eventi e metriche personalizzati** scritti dall'utente stesso nel codice del client o del server per tracciare eventi aziendali come gli articoli venduti o le partite vinte.

**Elenco degli scenari di integrazione e relative descrizioni:**

| Scenari di integrazione | DESCRIZIONE |
| --------------------- | :---------- |
|[Mappa delle applicazioni](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|I componenti dell'applicazione, con le metriche e gli avvisi chiave.||
|[Ricerca diagnostica dei dati dell'istanza](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Cercare e filtrare eventi come richieste, eccezioni, chiamate a dipendenze, tracce di log e visualizzazioni di pagina.||
|[Esplora metriche per i dati aggregati](https://docs.microsoft.com/azure/application-insights/app-insights-metrics-explorer)|Esaminare, filtrare e segmentare dati aggregati come frequenza delle richieste, errori, eccezioni, tempi di risposta e tempi di caricamento delle pagine.||
|[Dashboard](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Combinare dati di più risorse e condividerli con altri utenti. Ideale per le applicazioni multi-componente e per la visualizzazione continua negli spazi del team.||
|[Flusso di metriche live](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)|Quando si distribuisce una nuova build, controllare questi indicatori delle prestazioni in tempo quasi reale per verificare che tutto funzioni come previsto.||
|[Analisi](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Questo avanzato linguaggio di query consente di trovare risposta a domande approfondite sull'utilizzo e sulle prestazioni dell'app.||
|[Avvisi automatici e manuali](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Gli avvisi automatici si adattano ai modelli normali di telemetria dell'app e si attivano quando i dati si discostano dal modello consueto. È anche possibile impostare avvisi su livelli particolari delle metriche standard o personalizzate.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Vedere i dati sulle prestazioni nel codice. Passare al codice dall'analisi dello stack.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integrare le metriche di uso con altra business intelligence.||
|[API REST](https://dev.applicationinsights.io/)|Scrivere codice per eseguire query su metriche e dati non elaborati.||
|[Esportazione continua](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Eseguire l'esportazione bulk dei dati non elaborati nell'archivio quando arrivano.||

### <a name="azure-security-center-alerts"></a>Avvisi del Centro sicurezza di Azure
Il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) raccoglie, analizza e integra automaticamente i dati di log delle risorse di Azure, della rete e delle soluzioni dei partner connesse, come soluzioni di protezione endpoint e firewall, per rilevare le minacce reali e ridurre i falsi positivi. Il Centro sicurezza visualizza un elenco degli avvisi di sicurezza in ordine di priorità, nonché le informazioni necessarie per analizzare rapidamente il problema e indicazioni per risolvere un attacco.

Il sistema di rilevamento delle minacce del Centro sicurezza funziona mediante la raccolta automatica di informazioni sulla sicurezza dalle risorse di Azure, dalla rete e dalle soluzioni dei partner connessi. Per identificare le minacce, analizza queste informazioni, correlando spesso quelle raccolte da più origini. Gli avvisi di sicurezza sono classificati in ordine di priorità nel Centro sicurezza insieme a indicazioni su come su correggere la minaccia.

![Centro sicurezza di Azure](./media/azure-log-audit/azure-log-audit-fig7.png)

Il Centro sicurezza si avvale di analisi della sicurezza avanzate, che vanno ben oltre gli approcci basati sulle firme. I progressi tecnologici in ambito Big Data e [Machine Learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) vengono applicati per valutare gli eventi nell'intera l'infrastruttura cloud, rilevando minacce che sarebbe impossibile identificare con approcci manuali e stimando l'evoluzione degli attacchi. Queste analisi della sicurezza includono:

-   **Intelligence per le minacce integrata**: cerca gli attori dannosi noti ricorrendo alle informazioni sulle minacce globali da prodotti e servizi Microsoft, Microsoft Digital Crimes Unit (DCU) e Microsoft Security Response Center (MSRC), nonché da feed esterni.

-   **Analisi del comportamento**: applica i modelli noti per individuare comportamenti dannosi.

-   **Rilevamento anomalie**: usa la tecnica di profilatura statistica per creare una baseline cronologica. Genera avvisi sulle deviazioni dalle baseline stabilite che risultano conformi a un potenziale vettore di attacco .


Molti team dedicati alle operazioni di sicurezza e alla risposta ai problemi fanno affidamento su una soluzione SIEM (Security Information and Event Management) come punto di partenza per la valutazione e l'analisi degli avvisi di sicurezza. Con l'integrazione dei log di Azure, i clienti possono sincronizzare gli avvisi del Centro sicurezza e gli eventi di sicurezza delle macchine virtuali, raccolti da Diagnostica di Azure e dai log di controllo di Azure, con la propria soluzione SIEM o di analisi dei log quasi in tempo reale.


## <a name="log-analytics"></a>Log Analytics

Log Analytics è un servizio di [Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) che consente di raccogliere e analizzare i dati generati dalle risorse nel cloud e negli ambienti locali. Offre informazioni in tempo reale usando la ricerca integrata e i dashboard personalizzati per analizzare rapidamente milioni di record in tutti i carichi di lavoro e i server, indipendentemente dalla loro posizione fisica.

![Log Analytics](./media/azure-log-audit/azure-log-audit-fig8.png)

Al centro di Log Analytics è presente l'archivio OMS, ospitato nel cloud di Azure. I dati vengono raccolti nel repository da origini connesse configurando le origini dati e aggiungendo soluzioni alla sottoscrizione. Le origini dati e le soluzioni creeranno diversi tipi di record con uno specifico set di proprietà, ma che possono comunque essere analizzati insieme nelle query al repository. In questo modo è possibile usare gli stessi strumenti e metodi per lavorare con diversi tipi di dati raccolti da diverse origini.

Le origini connesse sono i computer e altre risorse che generano dati raccolti da Log Analytics. Sono inclusi gli agenti installati nei computer [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) e [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) che si connettono direttamente o gli agenti in un [gruppo di gestione di System Center Operations Manager connesso](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Log Analytics può anche raccogliere dati da [Archiviazione di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

[origini dati](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) sono i diversi tipi di dati raccolti da ogni origine connessa. Sono inclusi gli eventi e i [dati sulle prestazioni](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) ricavati dagli agenti [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) e Linux, oltre alle origini quali [log di IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) e [log di testo personalizzati](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). È possibile configurare ciascuna origine dati che si vuole raccogliere e la configurazione viene inviata automaticamente a ogni origine connessa.

Esistono quattro diversi modi per [raccogliere log e metriche per i servizi di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage):
1.  Da Diagnostica di Azure direttamente a Log Analytics (Diagnostica nella tabella seguente)

2.  Da Diagnostica di Azure ad Archiviazione di Azure a Log Analytics (Archiviazione nella tabella seguente)

3.  Connettori per i servizi di Azure (Connettori nella tabella seguente)

4.  Script per raccogliere e inviare i dati a Log Analytics (spazi vuoti nella tabella seguente e per i servizi non elencati)

| Service | Tipo di risorsa | Log | Metriche | Soluzione |
| :------ | :------------ | :--- | :------ | :------- |
|Gateway di applicazione|  Microsoft.Network/<br>applicationGateways|  Diagnostica|Diagnostica|    [Analisi dei ](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)gateway applicazione[ di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Connettore|  Connettore|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)[Connector (Anteprima)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Account di Automazione|   Microsoft.Automation/<br>AutomationAccounts|    Diagnostica||       [Altre informazioni](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Account Batch|    Microsoft.Batch/<br>batchAccounts|  Diagnostica|    Diagnostica||
|Servizi cloud classici||       Archiviazione||       [Altre informazioni](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Servizi cognitivi|    Microsoft.CognitiveServices/<br>account|       Diagnostica|||
|Data Lake Analytics|   Microsoft.DataLakeAnalytics/<br>account|   Diagnostica|||
|Data Lake Store|   Microsoft.DataLakeStore/<br>account|   Diagnostica|||
|Spazio dei nomi dell'hub eventi|   Microsoft.EventHub/<br>spazi dei nomi|  Diagnostica|    Diagnostica||
|Hub IoT|  Microsoft.Devices/<br>Hub IoT||     Diagnostica||
|Insieme di credenziali di chiave| Microsoft.KeyVault/<br>insiemi di credenziali|  Diagnostica  || [KeyVault Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Servizi di bilanciamento del carico|    Microsoft.Network/<br>loadBalancers|    Diagnostica|||
|App per la logica|    Microsoft.Logic/<br>flussi di lavoro|  Diagnostica|    Diagnostica||
||Microsoft.Logic/<br>integrationAccounts||||
|Gruppi di sicurezza di rete|   Microsoft.Network/<br>networksecuritygroups|Diagnostica||   [Analisi del gruppo di sicurezza di rete di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Insiemi di credenziali di ripristino|   Microsoft.RecoveryServices/<br>insiemi di credenziali|||[Azure Recovery Services Analytics (Anteprima)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Servizi di ricerca|   Microsoft.Search/<br>searchServices|    Diagnostica|    Diagnostica||
|Spazio dei nomi del bus di servizio| Microsoft.ServiceBus/<br>spazi dei nomi|    Diagnostica|Diagnostica|    [Service Bus Analytics (Anteprima)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Archiviazione||    [Service Fabric Analytics (anteprima)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (versione 12)| Microsoft.Sql/<br>servers/<br>database||       Diagnostica||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|Archiviazione|||         Script| [Azure Storage Analytics (Anteprima)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Macchine virtuali|  Microsoft.Compute/<br>virtualMachines|  Estensione|  Estensione||
||||Diagnostica||
|Set di scalabilità di macchine virtuali|   Microsoft.Compute/<br>virtualMachines    ||Diagnostica||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|Server farm Web|Microsoft.Web/<br>serverfarms||   Diagnostica
|Microsoft Azure| Microsoft.Web/<br>siti ||      Diagnostica|    [Altre informazioni](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites/<br>slot|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Integrazione dei log con i sistemi SIEM locali
L'[integrazione dei log di Azure](https://www.microsoft.com/download/details.aspx?id=53324) consente di integrare i log non elaborati delle risorse di Azure nei sistemi **SIEM di gestione degli eventi e delle informazioni di sicurezza locali**.

![Integrazione dei log](./media/azure-log-audit/azure-log-audit-fig9.png)

L'integrazione dei log di Azure raccoglie i dati di Diagnostica di Azure dalle macchine virtuali Windows (WAD), i log attività di Azure, gli avvisi del Centro sicurezza di Azure e i log dei provider di risorse di Azure. Questa integrazione fornisce un dashboard unificato per tutti gli asset, locali o su cloud, consentendo di aggregare, correlare, analizzare e inviare avvisi per gli eventi di sicurezza.



L'integrazione dei log di Azure supporta attualmente l'integrazione dei log di attività di Azure, del log eventi della macchina virtuale Windows inclusa nella sottoscrizione di Azure, degli avvisi del Centro sicurezza di Azure, dei log di Diagnostica di Azure e dei log di controllo di Azure Active Directory.

| Tipo di log | Log Analytics con supporto JSON (Splunk, ArcSight, Qradar) |
| :------- | :-------------------------------------------------------- |
|Log di controllo AAD|    Sì|
|Log attività| Sì|
|Avvisi del Centro sicurezza di Azure |Sì|
|Log di diagnostica (log di risorse)|  Sì|
|Log VM|   Sì, tramite Eventi inoltrati e non attraverso JSON|


La tabella seguente illustra la categoria Log e i dettagli dell'integrazione SIEM.

[Introduzione all'integrazione dei log di Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started) - L'esercitazione illustra come installare l'integrazione dei log di Azure e integrare i log dall'archiviazione di Azure WAD, i log attività di Azure, gli avvisi del Centro sicurezza di Azure e i log di controllo di Azure Active Directory.

Scenari di integrazione

-   [Passaggi per la configurazione dei partner](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – Questo post di blog mostra come configurare l'integrazione dei log di Azure per lavorare con le soluzioni partner Splunk, HP ArcSight e IBM QRadar.

-   [Domande frequenti sull'integrazione dei log di Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq) - Queste domande frequenti riguardano l'integrazione dei log di Azure.

-   [Integrazione degli avvisi del Centro sicurezza con l'integrazione dei log di Azure](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration) - Questo documento mostra come sincronizzare gli avvisi del Centro sicurezza di Azure, insieme agli eventi di sicurezza delle macchine virtuali raccolti da Diagnostica di Azure e dai log di controllo di Azure, con la propria soluzione SIEM o di analisi dei log.

## <a name="next-steps"></a>Fasi successive

- [Controllo e registrazione](https://www.microsoft.com/trustcenter/security/auditingandlogging)

Proteggere i dati mantenendo la visibilità e rispondendo rapidamente agli avvisi di sicurezza tempestivi

- [Raccolta dei log di controllo e di registrazione di sicurezza all'interno di Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)

Le impostazioni da applicare per assicurarsi che le istanze di Azure raccolgano i log di controllo e di sicurezza corretti.

- [Configurare le impostazioni di controllo per una raccolta di siti](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)

In qualità di amministratore della raccolta di siti, si può recuperare la cronologia delle azioni eseguite da un utente specifico e la cronologia delle azioni eseguite durante un particolare intervallo di date. 

- [Cercare il log di controllo nel Centro sicurezza e conformità di Office 365](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US)

È possibile ricorrere al Centro sicurezza e conformità di Office 365 per cercare il log di controllo unificato per visualizzare l'attività dell'utente e dell'amministratore nell'organizzazione di Office 365.


