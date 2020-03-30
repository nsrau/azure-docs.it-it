---
title: Progettazione della distribuzione dei log di Monitoraggio di Azure - Documenti Microsoft
description: Questo articolo descrive le considerazioni e i consigli per i clienti che si preparano a distribuire un'area di lavoro in Monitoraggio di Azure.This article describes the considerations and recommendations for customers preparing to deploy a workspace in Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/20/2019
ms.openlocfilehash: e493b07814821496f941a4b81402ba0b49acbede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248827"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>Progettazione della distribuzione dei log di Monitoraggio di AzureDesigning your Azure Monitor Logs deployment

Monitoraggio di Azure archivia i dati di [log](data-platform-logs.md) in un'area di lavoro di Log Analytics, ovvero una risorsa di Azure e un contenitore in cui i dati vengono raccolti, aggregati e funge da limite amministrativo. Sebbene sia possibile distribuire una o più aree di lavoro nella sottoscrizione di Azure, è necessario tenere presenti diverse considerazioni utili per assicurarsi che la distribuzione iniziale segua le linee guida per fornire un'area di lavoro economica, gestibile e scalabile per soddisfare le esigenze delle organizzazioni.

I dati in un'area di lavoro sono organizzati in tabelle, ognuna delle quali archivia diversi tipi di dati e dispone di un proprio set univoco di proprietà in base alla risorsa che genera i dati. La maggior parte delle origini dati scriverà nelle proprie tabelle in un'area di lavoro di Log Analytics.Most data sources will write to their own tables in a Log Analytics workspace.

![Esempio di modello di dati dell'area di lavoro](./media/design-logs-deployment/logs-data-model-01.png)

Un'area di lavoro Log Analytics offre:

* Posizione geografica per l'archiviazione dei dati.
* L'isolamento dei dati concede diritti di accesso a utenti diversi in base a una delle nostre strategie di progettazione consigliate.
* Ambito per la configurazione di impostazioni quali [il piano tariffario,](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier) [la conservazione](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)e il limite [dei dati.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#manage-your-maximum-daily-data-volume)

In questo articolo viene fornita una panoramica dettagliata delle considerazioni sulla progettazione e la migrazione, vengono fornite informazioni generali sul controllo degli accessi e una conoscenza delle implementazioni di progettazione consigliate per l'organizzazione IT.



## <a name="important-considerations-for-an-access-control-strategy"></a>Considerazioni importanti per una strategia di controllo degli accessi

L'identificazione del numero di aree di lavoro necessarie è influenzata da uno o più dei seguenti requisiti:

* Si opera in un'azienda globale ed è necessario che i dati di log siano archiviati in aree specifiche per motivi di sovranità o conformità dei dati.
* Si usa Azure e si intendono evitare costi di trasferimento dei dati in uscita tramite un'area di lavoro nella stessa area delle risorse di Azure da essa gestite.
* Si gestiscono più reparti o gruppi di business e si desidera che ognuno visualizzi i propri dati, ma non i dati di altri. Inoltre, non esiste alcun requisito aziendale per una visualizzazione consolidata tra reparti o gruppi di business.

Le organizzazioni IT oggi sono modellate in base a un ibrido centralizzato, decentralizzato o intermedio di entrambe le strutture. Di conseguenza, i seguenti modelli di distribuzione dell'area di lavoro sono stati comunemente utilizzati per eseguire il mapping a una di queste strutture organizzative:

* **Centralizzato:** tutti i log vengono archiviati in un'area di lavoro centrale e amministrati da un singolo team, con Monitoraggio di Azure che fornisce un accesso differenziato per team. In questo scenario, è facile da gestire, cercare tra le risorse e cross-correlate log. L'area di lavoro può aumentare in modo significativo a seconda della quantità di dati raccolti da più risorse nella sottoscrizione, con un sovraccarico amministrativo aggiuntivo per mantenere il controllo di accesso a utenti diversi. Questo modello è noto come "hub and spoke".
* **Decentralizzato:** ogni team ha la propria area di lavoro creata in un gruppo di risorse di cui è proprietario e gestito e i dati di log vengono separati per ogni risorsa. In questo scenario, l'area di lavoro può essere mantenuta protetta e il controllo dell'accesso è coerente con l'accesso alle risorse, ma è difficile cross-correlare i log. Gli utenti che necessitano di un'ampia visualizzazione di molte risorse non possono analizzare i dati in modo significativo.
* **Ibrido:** i requisiti di conformità del controllo di sicurezza complicano ulteriormente questo scenario perché molte organizzazioni implementano entrambi i modelli di distribuzione in parallelo. Ciò si traduce in genere in una configurazione complessa, costosa e difficile da mantenere con lacune nella copertura dei log.

Quando si usano gli agenti di Log Analytics per raccogliere dati, è necessario comprendere quanto segue per pianificare la distribuzione dell'agente:

* Per raccogliere dati dagli agenti Di Windows, è possibile configurare ogni agente in modo che [segnali a una o più aree di lavoro,](../../azure-monitor/platform/agent-windows.md)anche durante la creazione di report a un gruppo di gestione di System Center Operations Manager. L'agente Windows può segnalare fino a quattro aree di lavoro.
* L'agente Linux non supporta il multihoming e può creare report solo in una singola area di lavoro.

Se si utilizza System Center Operations Manager 2012 R2 o versione successiva:

* Ogni gruppo di gestione di Operations Manager può essere [connesso a una sola area di lavoro.](../platform/om-agents.md) 
* I computer Linux che segnalano a un gruppo di gestione devono essere configurati per creare report direttamente in un'area di lavoro di Log Analytics.Linux computers reporting to a management group must be configured to report directly to a Log Analytics workspace. Se i computer Linux stanno già segnalando direttamente a un'area di lavoro e si desidera monitorarli con Operations Manager, attenersi alla seguente procedura per creare report [a un gruppo](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)di gestione di Operations Manager . 
* È possibile installare l'agente Windows di Log Analytics nel computer Windows e fare in modo che segnali sia in Operations Manager integrato con un'area di lavoro che in un'area di lavoro diversa.

## <a name="access-control-overview"></a>Panoramica del controllo di accesso

Con il controllo degli accessi in base al ruolo, è possibile concedere a utenti e gruppi solo la quantità di accesso necessaria per lavorare con i dati di monitoraggio in un'area di lavoro. In questo modo è possibile allinearsi al modello operativo dell'organizzazione IT utilizzando un'unica area di lavoro per archiviare i dati raccolti abilitati in tutte le risorse. Ad esempio, si concede l'accesso al team responsabile dei servizi di infrastruttura ospitati nelle macchine virtuali di Azure e, di conseguenza, avranno accesso solo ai log generati dalle macchine virtuali. Questo segue il nostro nuovo modello di log del contesto delle risorse. La base per questo modello è per ogni record di log generato da una risorsa di Azure, viene associato automaticamente a questa risorsa. I log vengono inoltrati a un'area di lavoro centrale che rispetta l'ambito di autenticazione e il controllo degli accessi in base al ruolo in base alle risorse.

I dati a cui un utente ha accesso sono determinati da una combinazione di fattori elencati nella tabella seguente. Ognuno è descritto nelle sezioni seguenti.

| Fattore | Descrizione |
|:---|:---|
| [Modalità di accesso](#access-mode) | Metodo utilizzato dall'utente per accedere all'area di lavoro.  Definisce l'ambito dei dati disponibili e la modalità di controllo di accesso applicata. |
| [Modalità di controllo degli accessi](#access-control-mode) | Impostazione nell'area di lavoro che definisce se le autorizzazioni vengono applicate a livello di area di lavoro o di risorsa. |
| [Autorizzazioni](manage-access.md) | Autorizzazioni applicate a singoli o gruppi di utenti per l'area di lavoro o la risorsa. Definisce i dati a cui l'utente avrà accesso. |
| [Controllo degli accessi in base al ruolo a livello di](manage-access.md#table-level-rbac) | Autorizzazioni granulari facoltative che si applicano a tutti gli utenti indipendentemente dalla modalità di accesso o dalla modalità di controllo di accesso. Definisce i tipi di dati a cui un utente può accedere. |

## <a name="access-mode"></a>Modalità di accesso

La *modalità* di accesso si riferisce al modo in cui un utente accede a un'area di lavoro di Log Analytics e definisce l'ambito dei dati a cui può accedere. 

Gli utenti hanno due opzioni per accedere ai dati:

* **Workspace-context**: È possibile visualizzare tutti i log nell'area di lavoro per cui si è autorizzati. L'ambito delle query in questa modalità è limitato a tutti i dati di tutte le tabelle nell'area di lavoro. Questa è la modalità di accesso usata quando si accede ai log con l'area di lavoro come ambito, ad esempio quando si seleziona Log dal menu **Monitoraggio di Azure** nel portale di Azure.This is the access mode used when logs are accessed with the workspace as the scope, such as when you select **Logs** from the Azure Monitor menu in the Azure portal.

    ![Contesto di Log Analytics dall'area di lavoroLog Analytics context from workspace](./media/design-logs-deployment/query-from-workspace.png)

* **Contesto delle**risorse: quando si accede all'area di lavoro per una determinata risorsa, gruppo di risorse o sottoscrizione, ad esempio quando si seleziona **Log** dal menu delle risorse nel portale di Azure, è possibile visualizzare i log solo per le risorse in tutte le tabelle a cui si ha accesso. L'ambito delle query in questa modalità è limitato solo ai dati associati a tale risorsa. Questa modalità abilita anche il controllo degli accessi in base al ruolo granulare.

    ![Contesto di Log Analytics dalla risorsaLog Analytics context from resource](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > I log sono disponibili per le query del contesto delle risorse solo se sono stati associati correttamente alla risorsa pertinente. Attualmente, le seguenti risorse hanno limitazioni:
    > - Computer esterni ad Azure
    > - Service Fabric
    > - Application Insights
    >
    > È possibile verificare se i log sono associati correttamente alla risorsa eseguendo una query e controllando i record a cui si è interessati. Se l'ID risorsa corretto si trova nella proprietà [_ResourceId,](log-standard-properties.md#_resourceid) i dati sono disponibili per le query incentrate sulle risorse.

Monitoraggio di Azure determina automaticamente la modalità corretta a seconda del contesto da cui si esegue la ricerca log. L'ambito viene sempre presentato nella sezione in alto a sinistra di Log Analytics.

### <a name="comparing-access-modes"></a>Confronto delle modalità di accesso

Nella tabella seguente sono riepilogate le modalità di accesso:

| | Contesto dell'area di lavoro | Contesto delle risorse |
|:---|:---|:---|
| A chi è destinato ogni modello? | Amministrazione centrale. Amministratori che devono configurare la raccolta dei dati e gli utenti che devono accedere a un'ampia gamma di risorse. Attualmente necessario anche per gli utenti che devono accedere ai log per le risorse esterne ad Azure.Also currently required for users who need to access logs for resources outside of Azure. | Team applicativi. Amministratori delle risorse di Azure monitorate. |
| Che cosa richiede un utente per visualizzare i log? | Autorizzazioni per l'area di lavoro. Consultate **Autorizzazioni area di lavoro** in Gestire [l'accesso utilizzando le autorizzazioni dell'area di lavoro.](manage-access.md#manage-access-using-workspace-permissions) | Accesso in lettura alla risorsa. Vedere Autorizzazioni delle risorse in Gestire [l'accesso usando le autorizzazioni](manage-access.md#manage-access-using-azure-permissions)di Azure.See **Resource permissions** in Manage access using Azure permissions. Le autorizzazioni possono essere ereditate (ad esempio dal gruppo di risorse che lo contiene) o direttamente assegnate alla risorsa. L'autorizzazione ai log per la risorsa verrà assegnata automaticamente. |
| Qual è l'ambito delle autorizzazioni? | Workspace. Gli utenti con accesso all'area di lavoro possono eseguire query su tutti i log dell'area di lavoro dalle tabelle per le cui autorizzazioni dispongono. Vedere [Controllo degli accessi alle tabelle](manage-access.md#table-level-rbac) | Risorsa di Azure.Azure resource. L'utente può eseguire query sui log per risorse specifiche, gruppi di risorse o sottoscrizioni a cui hanno accesso da qualsiasi area di lavoro, ma non può eseguire query sui log per altre risorse. |
| In che modo i log di accesso utente possono accedere all'utente? | <ul><li>Avviare I log dal menu **Monitoraggio di Azure.Start** **Logs** from Azure Monitor menu.</li></ul> <ul><li>Avviare **i log** dalle **aree**di lavoro di Log Analytics .</li></ul> <ul><li>Da Cartelle [di lavoro](../visualizations.md#workbooks)di Monitoraggio di Azure .</li></ul> | <ul><li>Avviare **i log** dal menu per la risorsa di AzureStart Logs from the menu for the Azure resource</li></ul> <ul><li>Avviare I log dal menu **Monitoraggio di Azure.Start** **Logs** from Azure Monitor menu.</li></ul> <ul><li>Avviare **i log** dalle **aree**di lavoro di Log Analytics .</li></ul> <ul><li>Da Cartelle [di lavoro](../visualizations.md#workbooks)di Monitoraggio di Azure .</li></ul> |

## <a name="access-control-mode"></a>Modalità di controllo degli accessi

La *modalità di controllo* di accesso è un'impostazione in ogni area di lavoro che definisce la modalità di determinazione delle autorizzazioni per l'area di lavoro.

* **Richiedi autorizzazioni area di lavoro:** questa modalità di controllo non consente il controllo degli accessi in base al ruolo granulare. Affinché un utente può accedere all'area di lavoro, è necessario concedere loro le autorizzazioni per l'area di lavoro o per tabelle specifiche.

    Se un utente accede all'area di lavoro seguendo la modalità di contesto dell'area di lavoro, ha accesso a tutti i dati in qualsiasi tabella a cui è stato concesso l'accesso. Se un utente accede all'area di lavoro seguendo la modalità di contesto delle risorse, ha accesso solo ai dati per tale risorsa in qualsiasi tabella a cui è stato concesso l'accesso.

    Questa è l'impostazione predefinita per tutte le aree di lavoro create prima di marzo 2019.

* **Usa autorizzazioni per risorse o aree di lavoro:** questa modalità di controllo consente il controllo degli accessi in base al ruolo granulare. Agli utenti può essere concesso l'accesso solo ai `read` dati associati alle risorse che possono visualizzare assegnando l'autorizzazione di Azure.Users can be granted access to only data associated with resources they can view by assigning Azure permission. 

    Quando un utente accede all'area di lavoro in modalità di contesto dell'area di lavoro, vengono applicate le autorizzazioni dell'area di lavoro. Quando un utente accede all'area di lavoro in modalità contesto delle risorse, vengono verificate solo le autorizzazioni per le risorse e le autorizzazioni dell'area di lavoro vengono ignorate. Abilitare il controllo degli accessi in base al ruolo per un utente rimuovendolo dalle autorizzazioni dell'area di lavoro e consentendo il riconoscimento delle autorizzazioni per le risorse.

    Questa è l'impostazione predefinita per tutte le aree di lavoro create dopo marzo 2019.

    > [!NOTE]
    > Se un utente dispone solo delle autorizzazioni per le risorse per l'area di lavoro, è possibile accedere all'area di lavoro solo utilizzando la modalità di contesto delle risorse presupponendo che la modalità di accesso all'area di lavoro sia impostata su **Usa autorizzazioni risorsa o area di lavoro**.

Per informazioni su come modificare la modalità di controllo di accesso nel portale, con PowerShell o con un modello di Resource Manager, vedere Configurare la [modalità](manage-access.md#configure-access-control-mode)di controllo di accesso .

## <a name="ingestion-volume-rate-limit"></a>Limite di velocità del volume di ingestione

Monitoraggio di Azure è un servizio dati su larga scala che serve migliaia di clienti che inviano terabyte di dati ogni mese a un ritmo crescente. La soglia predefinita della frequenza di inserimento è impostata su **6 GB/min** per ogni area di lavoro. Si tratta di un valore approssimativo poiché le dimensioni effettive possono variare tra i tipi di dati a seconda della lunghezza del log e del relativo rapporto di compressione. Questo limite non si applica ai dati inviati dagli agenti o [dall'API dell'agente](data-collector-api.md)di raccolta dati.

Se si inviano dati a una frequenza più elevata a una singola area di lavoro, alcuni dati vengono eliminati e viene inviato un evento alla tabella *Operazione* nell'area di lavoro ogni 6 ore mentre la soglia continua a essere superata. Se il volume di inserimento continua a superare il limite di frequenza o si prevede di raggiungerlo a breve termine, è possibile richiedere un aumento all'area di lavoro aprendo una richiesta di supporto.
 
Per ricevere una notifica su un evento di questo tipo nell'area di lavoro, creare una regola di [avviso del log](alerts-log.md) utilizzando la query seguente con la logica di avviso basata sul numero di risultati grattugiare su zero.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


## <a name="recommendations"></a>Consigli

![Esempio di progettazione del contesto delle risorse](./media/design-logs-deployment/workspace-design-resource-context-01.png)

Questo scenario illustra la progettazione di una singola area di lavoro nella sottoscrizione dell'organizzazione IT che non è vincolata dalla sovranità dei dati o dalla conformità normativa o che deve eseguire il mapping alle aree in cui vengono distribuite le risorse. Consente ai team di amministrazione IT e di sicurezza delle organizzazioni di sfruttare la migliore integrazione con la gestione degli accessi di Azure e un controllo degli accessi più sicuro.

Tutte le risorse, le soluzioni di monitoraggio e le informazioni dettagliate, ad esempio Application Insights e Monitoraggio di Azure per le macchine virtuali, il supporto dell'infrastruttura e delle applicazioni gestite dai diversi team sono configurati per inoltrare i dati di log raccolti alle organizzazioni IT un'area di lavoro condivisa centralizzata. Agli utenti di ogni team viene concesso l'accesso ai log per le risorse a cui è stato concesso l'accesso.

Dopo aver distribuito l'architettura dell'area di lavoro, è possibile applicarla alle risorse di Azure con Criteri di [Azure.](../../governance/policy/overview.md) Fornisce un modo per definire i criteri e garantire la conformità con le risorse di Azure in modo che inviino tutti i log delle risorse a una determinata area di lavoro. Ad esempio, con le macchine virtuali di Azure o i set di scalabilità di macchine virtuali, è possibile usare criteri esistenti che valutano la conformità dell'area di lavoro e i risultati dei report oppure personalizzare per correggere se non sono conformi.  

## <a name="workspace-consolidation-migration-strategy"></a>Strategia di migrazione del consolidamento delle aree di lavoro

Per i clienti che hanno già distribuito più aree di lavoro e sono interessati a consolidare il modello di accesso al contesto delle risorse, è consigliabile adottare un approccio incrementale per eseguire la migrazione al modello di accesso consigliato e non tentare di ottenere questo risultato in modo rapido o aggressivo. Seguire un approccio graduale per pianificare, migrare, convalidare e ritirare seguendo una sequenza temporale ragionevole consentirà di evitare eventuali eventi imprevisti non pianificati o un impatto imprevisto sulle operazioni cloud. Se non si dispone di criteri di conservazione dei dati per motivi di conformità o aziendali, è necessario valutare il periodo di tempo appropriato per conservare i dati nell'area di lavoro da cui si esegue la migrazione durante il processo. Durante la riconfigurazione delle risorse da segnalare nell'area di lavoro condivisa, è comunque possibile analizzare i dati nell'area di lavoro originale in base alle esigenze. Al termine della migrazione, se si è in grado di conservare i dati nell'area di lavoro originale prima della fine del periodo di conservazione, non eliminarli.

Durante la pianificazione della migrazione a questo modello, considerare quanto segue:While planning your migration to this model, consider the following:

* Comprendere quali normative del settore e le politiche interne in materia di conservazione dei dati è necessario rispettare.
* Assicurarsi che i team dell'applicazione possano lavorare all'interno della funzionalità del contesto di risorse esistente.
* Identificare l'accesso concesso alle risorse per i team dell'applicazione e testare in un ambiente di sviluppo prima dell'implementazione nell'ambiente di produzione.
* Configurare l'area di lavoro per abilitare le **autorizzazioni Usa risorsa o area di lavoro**.
* Rimuovere i team dell'applicazione autorizzati a leggere ed eseguire query sull'area di lavoro.
* Abilitare e configurare le soluzioni di monitoraggio, ad esempio Monitoraggio di Azure per i contenitori e/o Monitoraggio di Azure per le macchine virtuali, gli account di automazione e le soluzioni di gestione, ad esempio Gestione aggiornamenti, Start/Stop macchine virtuali e così via, distribuite nell'area di lavoro originale.

## <a name="next-steps"></a>Passaggi successivi

Per implementare le autorizzazioni e i controlli di sicurezza consigliati in questa guida, [esaminare gestire l'accesso ai log](manage-access.md).
