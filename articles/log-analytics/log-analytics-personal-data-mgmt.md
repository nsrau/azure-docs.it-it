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
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 3692c83a4991fc67ec176687bd076ab14e4c640d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129371"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics"></a>Materiale sussidiario per i dati personali archiviati in Log Analytics

Log Analytics è un archivio dati in cui sono disponibili i dati personali. Questo articolo illustrerà dove trovare questo tipo di dati in Log Analytics, oltre alle funzionalità disponibili per gestire tali dati.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategia per la gestione dei dati personali

Anche se la definizione della strategia per la gestione degli eventuali dati personali spetta all'utente e all'azienda, di seguito sono riepilogati alcuni approcci possibili, elencati in ordine di preferenza da un punto di vista tecnico, dal più al meno indicato:

* Dove possibile, interrompere la raccolta, offuscare, anonimizzare o modificare in altro modo i dati raccolti così da non considerarli "privati". Si tratta dell'approccio _di gran lunga_ preferibile, dal momento che evita la necessità di creare una strategia di gestione dei dati molto costosa e a forte impatto.
* Se non è possibile, tentare di normalizzare i dati per ridurre l'impatto sulle prestazioni e la piattaforma dei dati. Ad esempio, anziché registrare un ID utente esplicito, creare dati di ricerca che mettano in correlazione il nome utente e i relativi dettagli con un ID interno registrabile in un'altra posizione. In questo modo, se uno degli utenti aziendali chiede di eliminare le proprie informazioni personali, potrebbe essere sufficiente eliminare solo la riga nella tabella di ricerca corrispondente all'utente. 
* Infine, in caso di raccolta dei dati privati, creare un processo basato sul percorso dell'API di eliminazione e sul percorso dell'API di query esistente per soddisfare gli eventuali obblighi relativi all'esportazione e all'eliminazione di tutti i dati privati associati a un utente. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Dove cercare i dati privati in Log Analytics?

Log Analytics è un archivio flessibile che, pur definendo uno schema per i dati, consente di sostituire qualsiasi campo con valori personalizzati. Inoltre, può essere inserito uno schema personalizzato. Di conseguenza, è impossibile dire esattamente dove si trovano i dati privati in una specifica area di lavoro. I percorsi seguenti, tuttavia, sono un punto di partenza ideale per un inventario:

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
* *Dati personalizzati*: Log Analytics consente la raccolta con diversi metodi: log personalizzati e campi personalizzati, l'[API di raccolta dati HTTP](log-analytics-data-collector-api.md) e dati personali raccolti come parte dei log eventi di sistema. Tutti questi elementi potrebbero contenere dati privati e devono essere esaminati per verificare la presenza di dati di questo tipo.
* *Dati acquisiti dalle soluzioni*: dato che il meccanismo delle soluzioni è aperto, è consigliabile esaminare tutte le tabelle generate dalle soluzioni per garantire la conformità.

## <a name="how-to-export-and-delete-private-data"></a>Come esportare ed eliminare dati privati

Come indicato nella sezione [Strategia per la gestione dei dati personali](#strategy-for-personal-data-handling) riportata in precedenza, si consiglia __vivamente__, se possibile, di ristrutturare i criteri di raccolta dati in modo da disabilitare la raccolta di dati privati, offuscarli o anonimizzarli oppure modificarli in altro modo per evitare che siano considerati "privati". La gestione dei dati comporterà prima di tutto costi per l'utente e il suo team per la definizione e l'automatizzazione di una strategia, la creazione di un'interfaccia per i clienti per l'interazione con i dati personali, oltre a costi di manutenzione continuativi. Inoltre, risulta costosa dal punto di vista dei calcoli per Log Analytics e un volume elevato di chiamate simultanee alle query o alle API di ripulitura potrebbe ripercuotersi negativamente su tutte le altre interazioni con le funzionalità di Log Analytics. Detto ciò, sussistono effettivamente alcuni validi scenari in cui i dati privati devono essere raccolti. In questi casi, i dati devono essere gestiti come descritto in questa sezione.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Visualizzare ed esportare i dati

Per visualizzare ed esportare le richieste di dati, deve essere usata l'[API di query](https://dev.loganalytics.io/). Spetta all'utente implementare la logica per la conversione della forma dei dati in una appropriata da offrire agli utenti. [Funzioni di Azure](https://azure.microsoft.com/services/functions/) è una valida soluzione per ospitare tale logica.

### <a name="delete"></a>Delete

> [!WARNING]
> Le operazioni di eliminazione in Log Analytics sono distruttive e non reversibili. Prestare la massima attenzione durante l'esecuzione.

Nell'ambito della gestione dei dati privati è stato reso disponibile un percorso dell'API di *ripulitura*. Questo percorso deve essere usato solo in casi limitati a causa dei rischi associati, del potenziale impatto sulle prestazioni e della possibilità di un'asimmetria di tutte le aggregazioni, le misure e altri aspetti dei dati di Log Analytics. Vedere la sezione [Strategia per la gestione dei dati personali](#strategy-for-personal-data-handling) per approcci alternativi per la gestione dei dati privati.

La ripulitura è un'operazione con privilegi elevati che nessuna app o nessun utente in Azure (incluso anche il proprietario della risorsa) avrà l'autorizzazione di eseguire senza disporre esplicitamente di un ruolo in Azure Resource Manager. Questo ruolo è _Responsabile ripulitura dati_ e deve essere delegato con attenzione a causa della potenziale perdita di dati. 

Dopo che è stato assegnato il ruolo di Azure Resource Manager, sono disponibili due nuovi percorsi dell'API: 

* [POST purge] (https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) - accetta un oggetto che specifica i parametri dei dati da eliminare e restituisce un GUID di riferimento 
* GET purge status - la chiamata a POST purge restituirà un'intestazione 'x-ms-status-location' che includerà un URL che è possibile chiamare per determinare lo stato dell'API di ripulitura. Ad esempio: 

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperatonalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

Anche se è probabile che la maggior parte delle operazioni di ripulitura venga completata molto più rapidamente rispetto al Contratto di servizio, a causa dell'impatto elevato sulla piattaforma dati usata da Log Analytics, il Contratto di servizio formale per il completamento delle operazioni di pulitura è impostato su 30 giorni. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul modo in cui i dati vengono raccolti, elaborati e protetti, vedere [Sicurezza dei dati di Log Analytics](log-analytics-data-security.md).