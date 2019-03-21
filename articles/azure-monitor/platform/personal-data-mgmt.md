---
title: Materiale sussidiario per i dati personali archiviati in Azure Log Analytics | Microsoft Docs
description: Questo articolo descrive come gestire i dati personali archiviati in Azure Log Analytics e i metodi per identificarli e rimuoverli.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: magoedte
ms.openlocfilehash: 9112d50384aba288038343ff9a14ed55542fb722
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58121350"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Materiale sussidiario per i dati personali archiviati in Log Analytics e Application Insights

Log Analytics è un archivio dati in cui sono disponibili i dati personali. Application Insights archivia questi dati in una partizione di Log Analytics. Questo articolo illustrerà dove trovare questo tipo di dati in Log Analytics e Application Insights, oltre alle funzionalità disponibili per gestire tali dati.

> [!NOTE]
> Ai fini di questo articolo i _dati di log_ fanno riferimento ai dati inviati a un'area di lavoro di Log Analytics, mentre i _dati dell'applicazione_ fanno riferimento ai dati raccolti da Application Insights.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategia per la gestione dei dati personali

Anche se la definizione della strategia per la gestione degli eventuali dati personali spetta all'utente e all'azienda, di seguito sono riepilogati alcuni approcci possibili, elencati in ordine di preferenza da un punto di vista tecnico, dal più al meno indicato:

* Dove possibile, interrompere la raccolta, offuscare, anonimizzare o modificare in altro modo i dati raccolti così da non considerarli "privati". Si tratta dell'approccio _di gran lunga_ preferibile, dal momento che evita la necessità di creare una strategia di gestione dei dati molto costosa e a forte impatto.
* Se non è possibile, tentare di normalizzare i dati per ridurre l'impatto sulle prestazioni e la piattaforma dei dati. Ad esempio, anziché registrare un ID utente esplicito, creare dati di ricerca che mettano in correlazione il nome utente e i relativi dettagli con un ID interno registrabile in un'altra posizione. In questo modo, se uno degli utenti aziendali chiede di eliminare le proprie informazioni personali, potrebbe essere sufficiente eliminare solo la riga nella tabella di ricerca corrispondente all'utente. 
* Infine, in caso di raccolta dei dati privati, creare un processo basato sul percorso dell'API di eliminazione e sul percorso dell'API di query esistente per soddisfare gli eventuali obblighi relativi all'esportazione e all'eliminazione di tutti i dati privati associati a un utente. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Dove cercare i dati privati in Log Analytics?

Log Analytics è un archivio flessibile che, pur definendo uno schema per i dati, consente di sostituire qualsiasi campo con valori personalizzati. Inoltre, può essere inserito uno schema personalizzato. Di conseguenza, è impossibile dire esattamente dove si trovano i dati privati in una specifica area di lavoro. I percorsi seguenti, tuttavia, sono un punto di partenza ideale per un inventario:

### <a name="log-data"></a>Dati di log

* *Indirizzi IP*: Log Analytics raccoglie una varietà di informazioni IP provenienti da diverse tabelle. Ad esempio, la query seguente mostra tutte le tabelle in cui sono stati raccolti gli indirizzi IPv4 nelle ultime 24 ore:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *ID utente*: gli ID utente si trovano in un'ampia gamma di soluzioni e di tabelle. È possibile cercare un nome utente specifico all'interno dell'intero set di dati con il comando search:
    ```
    search "[username goes here]"
    ```
  Ricordarsi di cercare non solo i nomi utente in un formato leggibile, ma anche gli identificatori univoci globali (GUID) che è possibile far risalire direttamente a un determinato utente.
* *ID dispositivo*: come gli ID utente, gli ID dispositivo in alcuni casi sono considerati "privati". Usare lo stesso approccio riportato in precedenza per gli ID utente per identificare le tabelle in cui questo aspetto potrebbe costituire un problema. 
* *Dati personalizzati*: Log Analytics consente la raccolta con diversi metodi: log personalizzati e campi personalizzati, l'[API di raccolta dati HTTP](../../azure-monitor/platform/data-collector-api.md) e dati personali raccolti come parte dei log eventi di sistema. Tutti questi elementi potrebbero contenere dati privati e devono essere esaminati per verificare la presenza di dati di questo tipo.
* *Dati acquisiti dalle soluzioni*: dato che il meccanismo delle soluzioni è aperto, è consigliabile esaminare tutte le tabelle generate dalle soluzioni per garantire la conformità.

### <a name="application-data"></a>Dati dell'applicazione

* *Indirizzi IP*: mentre Application Insights offuscherà per impostazione predefinita tutti i campi degli indirizzi IP su "0.0.0.0", si tratta di un modello comune per eseguire l'override di questo valore con l'indirizzo IP effettivo dell'utente per mantenere le informazioni della sessione. Per trovare qualsiasi tabella che contenga i valori nella colonna dell'indirizzo IP diverso da "0.0.0.0" nelle ultime 24 ore, è possibile usare la seguente query di Analytics:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *ID utente*: per impostazione predefinita, Application Insights userà ID generati in modo casuale per il rilevamento dell'utente e della sessione. Tuttavia, è frequente vedere questi campi sottoposti a override per archiviare un ID più rilevante per l'applicazione. Ad esempio: nomi utente, identificatori univoci globali di Azure AD, e così via. Questi ID sono spesso considerati nell'ambito come dati personali e, di conseguenza, devono essere gestiti in modo appropriato. Il consiglio dell'azienda è sempre quello di tentare di offuscare o anonimizzare completamente questi ID. I campi in cui questi valori sono presenti in genere comprendono session_Id, user_I, user_AuthenticatedId, user_AccountId, così come customDimensions.
* *Dati personalizzati*: Application Insights consente di accodare un set di dimensioni personalizzate per qualsiasi tipo di dati. Queste dimensioni possono essere *qualsiasi* dato. Usare la query seguente per identificare eventuali dimensioni personalizzate raccolte nelle ultime 24 ore:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Dati in memoria e in transito*: Application Insights monitorerà le eccezioni, le richieste, le chiamate di dipendenza e le tracce. I dati privati possono essere spesso raccolti nel codice e a livello di chiamata HTTP. Esaminare le eccezioni, le richieste, le dipendenze e le tabelle di tracce per identificare tali dati. Usare gli [inizializzatori di telemetria](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) laddove possibile per offuscare questi dati.
* *Acquisizioni di Snapshot Debugger*: la funzione [Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) in Application Insights consente di raccogliere gli snapshot di debug ogni volta che viene intercettata un'eccezione nell'istanza di produzione dell'applicazione. Gli snapshot esporranno la traccia dell'analisi dello stack che porta alle eccezioni, così come i valori per le variabili locali in ogni fase nello stack. Sfortunatamente, questa funzione non consente l'eliminazione selettiva dei punti snap, l'accesso a livello di codice ai dati all'interno dello snapshot. Pertanto, se la frequenza di conservazione predefinita dello snapshot non soddisfa i requisiti di conformità, il consiglio consiste nel disattivare la funzione.

## <a name="how-to-export-and-delete-private-data"></a>Come esportare ed eliminare dati privati

Come indicato nella sezione [Strategia per la gestione dei dati personali](#strategy-for-personal-data-handling) riportata in precedenza, si consiglia __vivamente__, se possibile, di ristrutturare i criteri di raccolta dati in modo da disabilitare la raccolta di dati privati, offuscarli o anonimizzarli oppure modificarli in altro modo per evitare che siano considerati "privati". La gestione dei dati comporterà prima di tutto costi per l'utente e il suo team per la definizione e l'automatizzazione di una strategia, la creazione di un'interfaccia per i clienti per l'interazione con i dati personali, oltre a costi di manutenzione continuativi. Inoltre, risulta costosa dal punto di vista dei calcoli per Log Analytics e Application Insights e un volume elevato di chiamate simultanee alle query o alle API di ripulitura potrebbe ripercuotersi negativamente su tutte le altre interazioni con le funzionalità di Log Analytics. Detto ciò, sussistono effettivamente alcuni validi scenari in cui i dati privati devono essere raccolti. In questi casi, i dati devono essere gestiti come descritto in questa sezione.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Visualizzare ed esportare i dati

Per entrambe le richieste di visualizzazione ed esportazione dei dati, utilizzare l'[API di query di Log Analytics](https://dev.loganalytics.io/) o l'[API di query di Application Insights](https://dev.applicationinsights.io/quickstart). Spetta all'utente implementare la logica per la conversione della forma dei dati in una appropriata da offrire agli utenti. [Funzioni di Azure](https://azure.microsoft.com/services/functions/) è una valida soluzione per ospitare tale logica.

### <a name="delete"></a>Delete

> [!WARNING]
> Le operazioni di eliminazione in Log Analytics sono distruttive e non reversibili. Prestare la massima attenzione durante l'esecuzione.

Nell'ambito della gestione dei dati privati è stato reso disponibile un percorso dell'API di *ripulitura*. Questo percorso deve essere usato solo in casi limitati a causa dei rischi associati, del potenziale impatto sulle prestazioni e della possibilità di un'asimmetria di tutte le aggregazioni, le misure e altri aspetti dei dati di Log Analytics. Vedere la sezione [Strategia per la gestione dei dati personali](#strategy-for-personal-data-handling) per approcci alternativi per la gestione dei dati privati.

La ripulitura è un'operazione con privilegi elevati che nessuna app o nessun utente in Azure (incluso anche il proprietario della risorsa) avrà l'autorizzazione di eseguire senza disporre esplicitamente di un ruolo in Azure Resource Manager. Questo ruolo è _Responsabile ripulitura dati_ e deve essere delegato con attenzione a causa della potenziale perdita di dati. 

Dopo che è stato assegnato il ruolo di Azure Resource Manager, sono disponibili due nuovi percorsi dell'API: 

#### <a name="log-data"></a>Dati di log

* [POST purge](https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) - accetta un oggetto che specifica i parametri dei dati da eliminare e restituisce un GUID di riferimento 
* GET purge status - la chiamata a POST purge restituirà un'intestazione 'x-ms-status-location' che includerà un URL che è possibile chiamare per determinare lo stato dell'API di ripulitura. Ad esempio: 

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperatonalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Anche se è probabile che la maggior parte delle operazioni di ripulitura venga completata molto più rapidamente rispetto al Contratto di servizio, a causa dell'impatto elevato sulla piattaforma dati usata da Log Analytics, **il Contratto di servizio formale per il completamento delle operazioni di pulitura è impostato su 30 giorni**. 

#### <a name="application-data"></a>Dati dell'applicazione

* [POST purge](https://docs.microsoft.com/rest/api/application-insights/components/purge) - accetta un oggetto che specifica i parametri dei dati da eliminare e restituisce un GUID di riferimento
* GET purge status - la chiamata a POST purge restituirà un'intestazione 'x-ms-status-location' che includerà un URL che è possibile chiamare per determinare lo stato dell'API di ripulitura. Ad esempio: 

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Anche se la maggior parte delle operazioni di ripulitura possono essere completate molto più rapidamente rispetto al Contratto di servizio, a causa dell'impatto elevato sulla piattaforma dati usata da Application Insights, **il Contratto di servizio formale per il completamento delle operazioni di pulitura è impostato su 30 giorni**.

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni su come i dati di Log Analitica sono raccolti, elaborati e protetti, vedere [la protezione dei dati di Log Analitica](../../azure-monitor/platform/data-security.md).
- Per altre informazioni sul modo in cui i dati vengono raccolti, elaborati e protetti, vedere [Sicurezza dei dati di Application Insights](../../azure-monitor/app/data-retention-privacy.md).
