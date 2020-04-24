---
title: Codifica della disponibilità elevata di servizi multimediali di Azure
description: Informazioni su come eseguire il failover a un account di servizi multimediali secondari se si verifica un'interruzione del Data Center a livello di area o un errore.
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
# <a name="media-services-high-availability-encoding"></a>Codifica di disponibilità elevata di servizi multimediali 

Il servizio di codifica di servizi multimediali di Azure è una piattaforma di elaborazione batch a livello di area e non è attualmente progettata per la disponibilità elevata in una singola area. Il servizio di codifica non fornisce attualmente il failover immediato del servizio se si verifica un'interruzione del Data Center a livello di area o un errore del componente sottostante o dei servizi dipendenti, ad esempio archiviazione, SQL. Questo articolo illustra come distribuire servizi multimediali per mantenere un'architettura a disponibilità elevata con failover e garantire la disponibilità ottimale per le applicazioni.

Seguendo le linee guida e le procedure consigliate descritte nell'articolo, è possibile ridurre il rischio di errori di codifica, ritardi e ridurre al minimo il tempo di ripristino se si verifica un'interruzione in una singola area.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Come creare un sistema di codifica tra più aree

* [Creare](create-account-cli-how-to.md) due o più account di servizi multimediali di Azure.

    I due account devono trovarsi in aree diverse. Per altre informazioni, vedere [aree in cui viene distribuito il servizio servizi multimediali di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).
* Caricare i file multimediali nella stessa area da cui si prevede di inviare il processo. Per altre informazioni su come avviare la codifica, vedere [creare un input del processo da un URL HTTPS](job-input-from-http-how-to.md) o [creare un input del processo da un file locale](job-input-from-local-file-how-to.md).

    Se è necessario inviare nuovamente il [processo](transforms-jobs-concept.md) a un'altra area, è possibile usare JobInputHttp o usare [Copy-Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) per copiare i dati dal contenitore di asset di origine a un contenitore di asset nell'area alternativa.
* Sottoscrivere i messaggi JobStateChange in ogni account tramite griglia di eventi di Azure. Per altre informazioni, vedi:

    * [Esempio di analisi audio](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) che Mostra come monitorare un processo con griglia di eventi di Azure, inclusa l'aggiunta di un fallback nel caso in cui i messaggi di griglia di eventi di Azure vengano ritardati per qualche motivo.
    * [Schemi di Griglia di eventi di Azure per gli eventi di Servizi multimediali](media-services-event-schemas.md)
    * Eseguire [la registrazione per gli eventi tramite il portale di Azure o l'interfaccia della](reacting-to-media-services-events.md) riga di comando (è anche possibile usare EVENTGRID Management SDK)
    * [Microsoft. Azure. EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (che supporta gli eventi di servizi multimediali in modo nativo).

    È anche possibile utilizzare gli eventi di griglia di eventi tramite funzioni di Azure.
* Quando si crea un [processo](transforms-jobs-concept.md):

    * Selezionare in modo casuale un account dall'elenco degli account attualmente in uso (questo elenco conterrà normalmente entrambi gli account, ma se vengono rilevati problemi potrebbe contenere solo un account). Se l'elenco è vuoto, viene generato un avviso in modo che un operatore possa esaminarlo.
    * Il materiale sussidiario generale è che è necessaria una [media reserved Unit](media-reserved-units-cli-how-to.md) per ogni [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) , a meno che non si usi [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) , in cui sono consigliate 3 media reserved Unit per JobOutput.
    * Ottenere il numero di media reserved Unit (MRU) per l'account scelto. Se il conteggio **media reserved Units** corrente non è già al valore massimo, aggiungere il numero di MRU necessari per il processo e aggiornare il servizio. Se la frequenza di invio del processo è elevata e si eseguono spesso query sul MRU per trovare il numero massimo, usare una cache distribuita per il valore con un timeout ragionevole.
    * Mantieni un conteggio del numero di processi in corso.

* Quando il gestore JobStateChange riceve una notifica che indica che un processo ha raggiunto lo stato pianificato, registrare l'ora in cui entra nello stato della pianificazione e l'area o l'account usato.
* Quando il gestore JobStateChange riceve una notifica che un processo ha raggiunto lo stato di elaborazione, contrassegnare il record per il processo come elaborazione.
* Quando il gestore JobStateChange riceve una notifica che un processo ha raggiunto lo stato terminato/con errori/annullato, contrassegnare il record per il processo come finale e decrementare il numero di processi in corso. Ottenere il numero di media reserved Unit per l'account scelto e confrontare il numero MRU corrente con il conteggio dei processi in corso. Se il numero di Inflight è inferiore al numero di MRU, diminuirlo e aggiornare il servizio.
* Disporre di un processo separato che analizza periodicamente i record dei processi
    
    * Se nello stato pianificato sono presenti processi che non sono stati avanzati per lo stato di elaborazione in un periodo di tempo ragionevole per una determinata area, rimuovere tale area dall'elenco degli account attualmente in uso.  A seconda dei requisiti aziendali, è possibile decidere di annullare immediatamente tali processi e di inviarli nuovamente all'altra area. In alternativa, è possibile concedere loro più tempo per passare allo stato successivo.
    * A seconda del numero di media reserved Unit configurati per l'account e della frequenza di invio, potrebbero essere presenti processi nello stato in coda, che il sistema non ha ancora prelevato per l'elaborazione.  Se l'elenco di processi nello stato in coda aumenta oltre un limite accettabile in un'area, tali processi possono essere annullati e inviati all'altra area.  Tuttavia, può trattarsi di un sintomo di non disporre di un numero sufficiente di media reserved Unit configurati nell'account per il carico corrente.  Se necessario, è possibile richiedere una quota di unità riservata multimediale più elevata tramite il supporto tecnico di Azure.
    * Se un'area è stata rimossa dall'elenco di account, monitorarla per il ripristino prima di aggiungerla di nuovo all'elenco.  L'integrità regionale può essere monitorata tramite i processi esistenti nell'area (se non sono stati annullati e inviati nuovamente), aggiungendo l'account all'elenco dopo un periodo di tempo e dagli operatori che monitorano le comunicazioni di Azure sulle interruzioni che potrebbero influire su servizi multimediali di Azure.
    
Se si rileva che il numero di MRU è molto lungo, spostare la logica di decremento nell'attività periodica. Fare in modo che la logica di invio del pre-processo confronti il numero di Inflight al numero di MRU corrente per verificare se è necessario aggiornare il MRU.

## <a name="next-steps"></a>Passaggi successivi

* [Creazione di flussi di video su richiesta tra aree](media-services-high-availability-streaming.md)
* Estrai [esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
