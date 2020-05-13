---
title: Ottenere gli eventi delle risorse nel servizio app Azure
description: Informazioni su come ottenere gli eventi delle risorse tramite i log attività e griglia di eventi nell'app del servizio app.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: 1fd283f95823a67319dc467a3a1d6251193182da
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124738"
---
# <a name="get-resource-events-in-azure-app-service"></a>Ottenere gli eventi delle risorse nel servizio app Azure

App Azure servizio fornisce strumenti predefiniti per monitorare lo stato e l'integrità delle risorse. Gli eventi delle risorse consentono di comprendere le modifiche apportate alle risorse dell'app Web sottostante e di eseguire le azioni necessarie. Gli esempi di eventi includono il ridimensionamento di istanze, l'aggiornamento delle impostazioni dell'applicazione, il riavvio dell'app Web e molti altri. In questo articolo si apprenderà come visualizzare i [log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) e abilitare [griglia di eventi](https://docs.microsoft.com/azure/event-grid/) per monitorare gli eventi delle risorse correlati all'app Web del servizio app.

> [!NOTE]
> L'integrazione del servizio app con griglia di eventi è in **Anteprima**. [Per altri dettagli, vedere l'annuncio.](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>Visualizzare i log attività di Azure
I log attività di Azure contengono gli eventi delle risorse generati dalle operazioni eseguite sulle risorse nella sottoscrizione. Entrambe le azioni utente nei modelli portale di Azure e Azure Resource Manager contribuiscono agli eventi acquisiti dal log attività. 

Log attività di Azure per i dettagli del servizio app, ad esempio:
- operazioni eseguite sulle risorse (ad esempio, piani di servizio app)
- chi ha avviato l'operazione
- quando si è verificata l'operazione;
- lo stato dell'operazione;
- valori delle proprietà che consentono di ricercare l'operazione

### <a name="what-can-you-do-with-azure-activity-logs"></a>Che cosa è possibile fare con i log attività di Azure?

I log attività di Azure possono essere sottoposti a query usando il portale di Azure, PowerShell, l'API REST o l'interfaccia della riga di comando. È possibile inviare i log a un account di archiviazione, a un hub eventi e a Log Analytics. È anche possibile analizzarli in Power BI o creare avvisi per rimanere aggiornati sugli eventi delle risorse.

[Visualizzare e recuperare gli eventi del log attività di Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

## <a name="ship-activity-logs-to-event-grid"></a>Spedire i log attività a griglia di eventi

Mentre i log attività sono basati sull'utente, è disponibile una nuova integrazione di [griglia di eventi](https://docs.microsoft.com/azure/event-grid/) con il servizio app (anteprima) che consente di registrare sia le azioni utente che gli eventi automatici. Con griglia di eventi è possibile configurare un gestore per rispondere agli eventi detti. Usare, ad esempio, Griglia di eventi per attivare immediatamente una funzione senza server per eseguire l'analisi dell'immagine ogni volta che una nuova foto viene aggiunta a un contenitore di archiviazione BLOB.

In alternativa è possibile usare Griglia di eventi con App per la logica per elaborare i dati ovunque, senza scrivere codice. Griglia di eventi connette le origini dati e i gestori di eventi. Usare, ad esempio, Griglia di eventi per attivare immediatamente una funzione senza server per eseguire l'analisi dell'immagine ogni volta che una nuova foto viene aggiunta a un contenitore di archiviazione BLOB.

### <a name="supported-event-types"></a>Tipi di evento supportati
| Tipo evento |Descrizione|
| -----------| ------------- |
| Microsoft.web/sites | WebApp |
| BackupOperationCompleted |Backup del webapp completato correttamente|
| BackupOperationFailed | Backup di webapp non riuscito|
| RestoreOperationStarted |Il ripristino da un backup è stato avviato|
| RestoreOperationCompleted |Il ripristino da un backup è stato completato|
| RestoreOperationFailed |Il ripristino da backup non è riuscito|
| SlotSwapStarted |Lo scambio di slot è stato avviato|
| SlotSwapCompleted |Lo scambio di slot è stato completato|
| SlotSwapFailed |Lo scambio di slot non è riuscito|
| SlotSwapWithPreviewStarted |Lo scambio di slot con l'anteprima è stato avviato|
| SlotSwapWithPreviewCancelled |Lo scambio di slot con anteprima non è riuscito|
| AppUpdated | |
| Riavviato | Il webapp è stato riavviato |
| Arrestato | Il webapp è stato arrestato |
| ChangedAppSettings | Le impostazioni dell'app in WebApp sono state modificate |
| - | - |
| Microsoft. Web/serverfarms | (Piano di servizio app) |
| AspUpdated | Il piano di servizio app è stato aggiornato. L'oggetto evento contiene informazioni dettagliate sulle proprietà che sono state modificate. |
| Aumentare e ridurre le prestazioni | Il piano di servizio app è stato ridimensionato. L'oggetto evento contiene il numero di istanze.|


## <a name="next-steps"></a><a name="nextsteps"></a> Passaggi successivi
* [Eseguire query sui log con monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md)
* [Come monitorare il Servizio app di Azure](web-sites-monitor.md)
* [Risoluzione dei problemi del Servizio app di Azure in Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analizzare i log delle app in HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
