---
title: Ottenere gli eventi delle risorse in Servizio app di Azure
description: Informazioni su come ottenere gli eventi delle risorse tramite i log attività e Griglia di eventi nell'app del servizio app.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: c5c84891187c540c0b24162cf5c8c7f96e9e731a
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962299"
---
# <a name="get-resource-events-in-azure-app-service"></a>Ottenere gli eventi delle risorse in Servizio app di Azure

Servizio app di Azure fornisce strumenti predefiniti per monitorare lo stato e l'integrità delle risorse. Gli eventi delle risorse consentono di comprendere le modifiche apportate alle risorse dell'app Web sottostanti e di prendere le misure necessarie. Sono esempi di questi eventi il ridimensionamento delle istanze, l'aggiornamento delle impostazioni dell'applicazione, il riavvio dell'app Web e molti altri. In questo articolo si apprenderà come visualizzare i [log attività di Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log) e abilitare [Griglia di eventi](../event-grid/index.yml) per monitorare gli eventi delle risorse correlati all'app Web del servizio app.

> [!NOTE]
> L'integrazione del servizio app con Griglia di eventi è una funzionalità in **anteprima**. [Per altri dettagli, vedere l'annuncio.](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>Visualizzare i log attività di Azure
I log attività di Azure contengono gli eventi delle risorse generati dalle operazioni eseguite sulle risorse nella sottoscrizione. Entrambe le azioni utente nei modelli del portale di Azure e di Azure Resource Manager contribuiscono alla generazione degli eventi acquisiti dal log attività. 

I log attività di Azure per il servizio app indicano in dettaglio, ad esempio:
- quali operazioni sono state eseguite sulle risorse (ad esempio piani del servizio app)
- chi ha avviato l'operazione
- quando si è verificata l'operazione;
- lo stato dell'operazione;
- i valori delle proprietà per facilitare la ricerca dell'operazione

### <a name="what-can-you-do-with-azure-activity-logs"></a>Che cosa è possibile fare con i log attività di Azure?

I log attività di Azure possono essere sottoposti a query usando il portale di Azure, PowerShell, l'API REST o l'interfaccia della riga di comando. È possibile inviare i log a un account di archiviazione, a un hub eventi e a Log Analytics. È anche possibile analizzarli in Power BI o creare avvisi per restare aggiornati sugli eventi delle risorse.

[Visualizzare e recuperare gli eventi del log attività di Azure.](../azure-monitor/platform/activity-log.md#view-the-activity-log)

## <a name="ship-activity-logs-to-event-grid"></a>Inviare i log di attività a Griglia di eventi

Mentre i log attività sono basati sull'utente, è disponibile una nuova integrazione di [Griglia di eventi](../event-grid/index.yml) con il servizio app (in anteprima), che registra sia le azioni utente che gli eventi automatici. Con Griglia di eventi è possibile configurare un gestore per rispondere a detti eventi. Usare, ad esempio, Griglia di eventi per attivare immediatamente una funzione senza server per eseguire l'analisi dell'immagine ogni volta che una nuova foto viene aggiunta a un contenitore di archiviazione BLOB.

In alternativa è possibile usare Griglia di eventi con App per la logica per elaborare i dati ovunque, senza scrivere codice. Griglia di eventi connette le origini dati e i gestori di eventi. Usare, ad esempio, Griglia di eventi per attivare immediatamente una funzione senza server per eseguire l'analisi dell'immagine ogni volta che una nuova foto viene aggiunta a un contenitore di archiviazione BLOB.

[Visualizzare le proprietà e lo schema per gli eventi di Servizio app di Azure.](../event-grid/event-schema-app-service.md)

## <a name="next-steps"></a><a name="nextsteps"></a> Passaggi successivi
* [Eseguire query sui log con Monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md)
* [Come monitorare il Servizio app di Azure](web-sites-monitor.md)
* [Risoluzione dei problemi del Servizio app di Azure in Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analizzare i log delle app in HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)