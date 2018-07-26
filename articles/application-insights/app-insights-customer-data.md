---
title: Materiale sussidiario per i dati personali archiviati in Azure Application Insights | Microsoft Docs
description: Questo articolo descrive come gestire i dati personali archiviati in Azure Application Insights e i metodi per identificarli e rimuoverli.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.reviewer: Evgeny.Ternovsky
ms.author: mbullwin
ms.openlocfilehash: a59b57c546f18a7d91160f2ae7282af82fc42160
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044714"
---
# <a name="guidance-for-personal-data-stored-in-application-insights"></a>Materiale sussidiario per i dati personali archiviati in Application Insights

Application Insights è un archivio dati in cui sono disponibili i dati personali. Questo articolo illustra dove trovare questo tipo di dati in Application Insights, oltre alle funzionalità disponibili per gestire questi dati.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategia per la gestione dei dati personali

Anche se la definizione della strategia per la gestione degli eventuali dati personali spetta all'utente e all'azienda, di seguito sono riepilogati alcuni approcci possibili, elencati in ordine di preferenza da un punto di vista tecnico dal più al meno indicato:
* Dove possibile, interrompere la raccolta, offuscare, anonimizzare o modificare in altro modo i dati raccolti così da non considerarli _privati_. Questo metodo è l'approccio preferibile, dal momento che evita la necessità di creare una strategia di gestione dei dati costosa e a forte impatto.
* Se non è possibile, tentare di normalizzare i dati per ridurre l'impatto sulle prestazioni e la piattaforma dei dati. Ad esempio, anziché registrare un ID utente esplicito, creare dati di ricerca che mettano in correlazione il nome utente e i relativi dettagli con un ID interno registrabile in un'altra posizione. In questo modo, se uno degli utenti aziendali chiede di eliminare le proprie informazioni personali, potrebbe essere sufficiente eliminare solo la riga nella tabella di ricerca corrispondente all'utente. 
* Infine, in caso di raccolta dei dati privati, creare un processo basato sul percorso dell'API di eliminazione e sul percorso dell'API di query esistente per soddisfare gli eventuali obblighi relativi all'esportazione e all'eliminazione di tutti i dati privati associati a un utente.

## <a name="where-to-look-for-private-data-in-application-insights"></a>Dove cercare i dati privati in Application Insights?

Application Insights è un archivio totalmente flessibile che, pur definendo uno schema per i dati, consente di sostituire qualsiasi campo con valori personalizzati. Di conseguenza, è impossibile dire esattamente dove si trovano i dati privati in una specifica applicazione. I percorsi seguenti, tuttavia, sono un punto di partenza ideale per un inventario:

* *Indirizzi IP*: mentre Application Insights offuscherà per impostazione predefinita tutti i campi degli indirizzi IP su "0.0.0.0", si tratta di un modello comune per eseguire l'override di questo valore con l'indirizzo IP effettivo dell'utente mantenere le informazioni della sessione. Per trovare qualsiasi tabella che contenga i valori nella colonna dell'indirizzo IP diverso da "0.0.0.0" nelle ultime 24 ore, è possibile usare la seguente query di Analytics:
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
* *I dati in memoria e in transito*: Application Insights monitoreranno le eccezioni, le richieste, le chiamate di dipendenza e le tracce. I dati privati possono essere spesso raccolti nel codice e a livello di chiamata HTTP. Esaminare le eccezioni, le richieste, le dipendenze e le tabelle di tracce per identificare tali dati. Usare gli [inizializzatori di telemetria](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) laddove possibile per offuscare questi dati.
* *Acquisizioni di Snapshot Debugger*: la funzione [Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) in Application Insights consente di raccogliere gli snapshot di debug ogni volta che viene intercettata un'eccezione nell'istanza di produzione dell'applicazione. Gli snapshot esporranno la traccia dell'analisi dello stack che porta alle eccezioni, così come i valori per le variabili locali in ogni fase nello stack. Sfortunatamente, questa funzione non consente l'eliminazione selettiva dei punti snap, l'accesso a livello di codice ai dati all'interno dello snapshot. Pertanto, se la frequenza di conservazione predefinita dello snapshot non soddisfa i requisiti di conformità, il consiglio consiste nel disattivare la funzione.

## <a name="how-to-export-and-delete-private-data"></a>Come esportare ed eliminare dati privati

Si consiglia __vivamente__, quando possibile, di ristrutturare i criteri di raccolta dati in modo da disabilitare la raccolta di dati privati, offuscarli o anonimizzarli oppure modificarli in altro modo per evitare che siano considerati "privati". La gestione dei dati dopo che sono stati raccolti comporterà prima di tutto costi per l'utente e il suo team per la definizione e l'automatizzazione di una strategia, la creazione di un'interfaccia per i clienti per l'interazione con i dati personali, oltre a costi di manutenzione continuativi. Inoltre, risulta costosa dal punto di vista dei calcoli per Application Insights e un volume elevato di chiamate simultanee alle query o alle API di ripulitura potrebbe ripercuotersi negativamente su tutte le altre interazioni con le funzionalità di Application Insights. Detto ciò, sussistono effettivamente alcuni validi scenari in cui i dati privati devono essere raccolti. In questi casi, i dati devono essere gestiti come descritto in questa sezione.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Visualizzare ed esportare i dati

Per visualizzare ed esportare le richieste di dati, deve essere usata l'[API di query](https://dev.applicationinsights.io/quickstart). Spetta all'utente implementare la logica per la conversione della forma dei dati in una appropriata da offrire agli utenti. [Funzioni di Azure](https://azure.microsoft.com/services/functions/) è una valida soluzione per ospitare tale logica.

### <a name="delete"></a>Delete

> [!WARNING]
> Le operazioni di eliminazione in Application Insights sono distruttive e non reversibili. Prestare la massima attenzione durante l'esecuzione.

Nell'ambito della gestione dei dati privati è stato reso disponibile una storia del percorso dell'API di "ripulitura". Questo percorso deve essere usato solo in casi limitati a causa dei rischi associati, del potenziale impatto sulle prestazioni e della possibilità di un'asimmetria di tutte le aggregazioni, le misure e altri aspetti dei dati di Application Insights. Vedere la sezione [Strategia per la gestione dei dati personali](#strategy-for-personal-data-handling) indicata in precedenza per approcci alternativi per la gestione dei dati privati.

La ripulitura è un'operazione con privilegi elevati che nessuna app o nessun utente in Azure (incluso anche il proprietario della risorsa) avrà l'autorizzazione di eseguire senza disporre esplicitamente di un ruolo in Azure Resource Manager. Questo ruolo è _Responsabile ripulitura dati_ e deve essere delegato con attenzione a causa della potenziale perdita di dati.

Dopo che è stato assegnato il ruolo di Azure Resource Manager, sono disponibili due nuovi percorsi dell'API, una documentazione disponibile per lo sviluppatore e una forma di chiamata collegata:

* [POST purge](https://docs.microsoft.com/rest/api/application-insights/components/purge) - accetta un oggetto che specifica i parametri dei dati da eliminare e restituisce un GUID di riferimento
* GET purge status - la chiamata a POST purge restituirà un'intestazione 'x-ms-status-location' che includerà un URL che è possibile chiamare per determinare lo stato dell'API di ripulitura. Ad esempio: 
   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Anche se la maggior parte delle operazioni di ripulitura possono essere completate molto più rapidamente rispetto al Contratto di servizio, a causa dell'impatto elevato sulla piattaforma dati usata da Application Insights, **il Contratto di servizio formale per il completamento delle operazioni di pulitura è impostato su 30 giorni**.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul modo in cui i dati vengono raccolti, elaborati e protetti, vedere [Sicurezza dei dati di Application Insights](app-insights-data-retention-privacy.md).