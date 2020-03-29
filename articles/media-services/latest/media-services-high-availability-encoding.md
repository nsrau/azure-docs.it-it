---
title: Codifica a disponibilità elevata di Servizi multimediali di AzureAzure Media Services high availability encoding
description: Informazioni su come eseguire il failover a un account di Servizi multimediali secondario se si verifica un errore o un errore del data center regionale.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: afaa7545fbcbab016249e73a2247817310c5cdfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78934195"
---
# <a name="media-services-high-availability-encoding"></a>Codifica ad alta disponibilità di Servizi multimediali 

Il servizio di codifica di Servizi multimediali di Azure è una piattaforma di elaborazione batch regionale e non attualmente progettata per la disponibilità elevata all'interno di una singola area. Il servizio di codifica attualmente non fornisce il failover immediato del servizio se si verifica un'interruzione del data center regionale o un errore del componente sottostante o dei servizi dipendenti (ad esempio archiviazione, SQL). In questo articolo viene illustrato come distribuire Servizi multimediali per mantenere un'architettura a disponibilità elevata con failover e garantire una disponibilità ottimale per le applicazioni.

Seguendo le linee guida e le procedure consigliate descritte nell'articolo, si ridurrà il rischio di errori di codifica, ritardi e si ridurrà al minimo i tempi di recupero se si verifica un'interruzione in una singola area.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Come costruire un sistema di codifica interregionale

* [Creare](create-account-cli-how-to.md) due (o più) account di Servizi multimediali di Azure.Create two (or more) Azure Media Services accounts.

    I due conti devono trovarsi in regioni diverse. Per altre informazioni, vedere [Aree in cui viene distribuito il servizio Servizi multimediali](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)di Azure.For more info, see regions in which the Azure Media Services service is deployed .
* Caricare i file multimediali nella stessa area geografica da cui si prevede di inviare il processo. Per altre informazioni su come avviare la codifica, vedere Creare un input di [processo da un URL HTTPS](job-input-from-http-how-to.md) o Creare un input di processo da un file [locale.](job-input-from-local-file-how-to.md)

    Se è quindi necessario inviare nuovamente il [processo](transforms-jobs-concept.md) a un'altra area, è possibile usare JobInputHttp o [Copy-Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) per copiare i dati dal contenitore Asset di origine a un contenitore Asset nell'area alternativa.
* Sottoscrivere i messaggi JobStateChange in ogni account tramite Griglia di eventi di Azure.Subscribe for JobStateChange messages in each account via Azure Event Grid. Per altre informazioni, vedere:

    * [Esempio](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) di analisi audio che illustra come monitorare un processo con Griglia di eventi di Azure, inclusa l'aggiunta di un fallback nel caso in cui i messaggi della griglia di eventi di Azure vengano ritardati per qualche motivo.
    * [Schemi di Griglia di eventi di Azure per gli eventi di Servizi multimediali](media-services-event-schemas.md)
    * [Eseguire la registrazione per gli eventi tramite il portale di Azure o l'interfaccia della riga di comando](reacting-to-media-services-events.md) (è anche possibile farlo con EventGrid Management SDK)
    * [Microsoft.Azure.EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (che supporta gli eventi di Servizi multimediali in modo nativo).

    È anche possibile usare gli eventi di Griglia di eventi tramite Funzioni di Azure.You can also consume Event Grid events via Azure Functions.
* Quando si crea un [lavoro](transforms-jobs-concept.md):

    * Selezionare casualmente un account dall'elenco degli account attualmente utilizzati (questo elenco normalmente contiene entrambi gli account, ma se vengono rilevati problemi può contenere un solo account). Se l'elenco è vuoto, generare un avviso in modo che un operatore possa analizzare.
    * Indicazioni generali è che è necessaria [un'unità riservata multimediale](media-reserved-units-cli-how-to.md) per [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (a meno che non si utilizza [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) in cui sono consigliate 3 unità riservate multimediali per JobOutput).
    * Ottenere il numero di unità riservate multimediali (MRUs) per l'account scelto. Se il numero di **unità riservate al supporto** corrente non è già al valore massimo, aggiungere il numero di unità MRU necessarie per il processo e aggiornare il servizio. Se la frequenza di invio dei processi è elevata e spesso si eseguono query sulle MRRu per individuare il massimo, utilizzare una cache distribuita per il valore con un timeout ragionevole.
    * Tenere un conteggio del numero di processi in volo.

* Quando il gestore JobStateChange riceve una notifica che un processo ha raggiunto lo stato pianificato, registrare l'ora in cui entra nello stato di pianificazione e nell'area/account utilizzato.
* Quando il gestore JobStateChange riceve una notifica che un processo ha raggiunto lo stato di elaborazione, contrassegnare il record per il processo come elaborazione.
* Quando il gestore JobStateChange riceve una notifica che un processo ha raggiunto lo stato Finished/Errored/Canceled, contrassegnare il record per il processo come finale e diminuire il conteggio dei processi in corso. Ottenere il numero di unità riservate multimediali per l'account scelto e confrontare il numero MRU corrente con il numero di processi in volo. Se il conteggio in corso è inferiore al conteggio MRU, diminuire e aggiornare il servizio.
* Avere un processo separato che esamina periodicamente i record dei lavori
    
    * Se si dispone di processi nello stato pianificato che non hanno avanzato lo stato di elaborazione in un periodo di tempo ragionevole per una determinata area, rimuovere tale area dall'elenco degli account attualmente utilizzati.  A seconda dei requisiti aziendali, è possibile decidere di annullare immediatamente tali processi e inviarli nuovamente all'altra area. Oppure, si potrebbe dare loro un po 'più di tempo per passare allo stato successivo.
    * A seconda del numero di unità riservate multimediali configurate nell'account e della frequenza di invio, potrebbero essere presenti processi nello stato in coda in cui il sistema non ha ancora prelevato per l'elaborazione.  Se l'elenco dei processi nello stato in coda supera un limite accettabile in un'area, tali processi possono essere annullati e inviati all'altra area.  Tuttavia, questo può essere un sintomo di non avere abbastanza unità riservate multimediali configurate sull'account per il carico corrente.  Se necessario, è possibile richiedere una quota di unità riservata ai supporti multimediali superiore tramite il supporto di Azure.You can request a higher Media Reserved Unit quota through Azure Support if necessary.
    * Se un'area è stata rimossa dall'elenco degli account, monitorarla per il ripristino prima di aggiungerla nuovamente all'elenco.  L'integrità regionale può essere monitorata tramite i processi esistenti nell'area (se non sono stati annullati e inviati nuovamente), aggiungendo nuovamente l'account all'elenco dopo un periodo di tempo e dagli operatori che monitorano le comunicazioni di Azure sulle interruzioni che potrebbero influire sulle interruzioni che potrebbero influire Servizi multimediali di Azure.Azure Media Services.
    
Se si rileva che il conteggio MRU è molto alto e verso il basso, spostare la logica di decremento nell'attività periodica. Fare in modo che la logica di invio pre-processo confronti il conteggio in corso con il conteggio MRU corrente per verificare se è necessario aggiornare le unità MRI.

## <a name="next-steps"></a>Passaggi successivi

* [Creare streaming tra aree video on demand](media-services-high-availability-streaming.md)
* Estrarre [esempi](https://docs.microsoft.com/samples/browse/?products=azure-media-services) di codice
