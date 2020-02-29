---
title: Disponibilità elevata di servizi multimediali di Azure
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
ms.openlocfilehash: e2212c2ad5a0ab1899d281e99a89b33a2fc0298f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202286"
---
# <a name="azure-media-services-high-availability-guidance"></a>Guida alla disponibilità elevata di servizi multimediali di Azure

Il servizio di codifica di servizi multimediali di Azure è una piattaforma di elaborazione batch a livello di area e non è attualmente progettata per la disponibilità elevata in una singola area. Il servizio di codifica non fornisce attualmente il failover immediato del servizio se si verifica un'interruzione del Data Center a livello di area o un errore del componente sottostante o dei servizi dipendenti, ad esempio archiviazione, SQL e così via.  Questo articolo illustra come distribuire servizi multimediali per mantenere un'architettura a disponibilità elevata con failover e garantire la disponibilità ottimale per le applicazioni. 

Seguendo le linee guida e le procedure consigliate descritte nell'articolo, è possibile ridurre il rischio di errori di codifica, ritardi e ridurre al minimo il tempo di ripristino se si verifica un'interruzione in una singola area.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Come creare un sistema di codifica tra più aree

* [Creare](create-account-cli-how-to.md) due o più account di servizi multimediali di Azure.
* Sottoscrivere i messaggi **JobStateChange** in ogni account.

    * In servizi multimediali V3 viene eseguita tramite griglia di eventi di Azure. Per altre informazioni, vedere:
    
       * [Esempi di griglia di eventi](../../event-grid/receive-events.md) 
       * [Schemi di griglia di eventi di Azure per gli eventi di servizi multimediali](media-services-event-schemas.md) 
       * Eseguire [la registrazione per gli eventi tramite il portale di Azure o l'interfaccia della](reacting-to-media-services-events.md) riga di comando (è anche possibile usare EVENTGRID Management SDK)
       * [Microsoft. Azure. EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (che supporta gli eventi di servizi multimediali in modo nativo). 
       
        È anche possibile utilizzare gli eventi di griglia di eventi tramite funzioni di Azure.
    * In servizi multimediali V2 questa operazione viene eseguita tramite [NotificationEndpoints](../previous/media-services-dotnet-check-job-progress-with-webhooks.md).
* Quando si [Crea un processo](transforms-jobs-concept.md):

    * Selezionare in modo casuale un account dall'elenco degli account attualmente in uso (questo elenco conterrà normalmente entrambi gli account, ma se vengono rilevati problemi potrebbe contenere solo un account). Se l'elenco è vuoto, viene generato un avviso in modo che un operatore possa esaminarlo.
    * Indicazioni generali sono necessari una [media reserved Unit](media-reserved-units-cli-how-to.md) per attività o [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (a meno che non si usi [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) in V3).
    * Ottenere il numero di [media reserved Unit](media-reserved-units-cli-how-to.md) (MRU) per l'account scelto. Se il conteggio **media reserved Units** corrente non è già al valore massimo, aggiungere il numero di MRU necessari per il processo e aggiornare il servizio. Se la frequenza di invio del processo è elevata e si eseguono spesso query sul MRU per trovare il numero massimo, usare una cache distribuita per il valore con un timeout ragionevole.
    * Mantieni un conteggio del numero di processi in corso.
* Quando il gestore JobStateChange riceve una notifica che indica che un processo ha raggiunto lo stato pianificato, registrare l'ora in cui entra nello stato della pianificazione e l'area o l'account usato.    
* Quando il gestore JobStateChange riceve una notifica che un processo ha raggiunto lo stato di elaborazione, contrassegnare il record per il processo come elaborazione.
* Quando il gestore JobStateChange riceve una notifica che un processo ha raggiunto lo stato terminato/con errori/annullato, contrassegnare il record per il processo come finale e decrementare il numero di processi in corso. Ottenere il numero di media reserved Unit per l'account scelto e confrontare il numero MRU corrente con il conteggio dei processi in corso. Se il numero di Inflight è inferiore al numero di MRU, diminuirlo e aggiornare il servizio.
* Disporre di un processo separato che analizza periodicamente i record dei processi. Se nello stato pianificato sono presenti processi che non sono stati avanzati per lo stato di elaborazione in un periodo di tempo ragionevole per una determinata area, rimuovere tale area dall'elenco degli account attualmente in uso.

    * A seconda dei requisiti aziendali, è possibile decidere di annullare immediatamente tali processi e di inviarli nuovamente all'altro account. In alternativa, è possibile concedere loro più tempo per passare allo stato successivo.   
    * Dopo un periodo di tempo, aggiungere di nuovo l'account all'elenco attualmente usato (supponendo che l'area sia stata ripristinata).
    
Se si rileva che il numero di MRU è molto lungo, spostare la logica di decremento nell'attività periodica. Fare in modo che la logica di invio del pre-processo confronti il numero di Inflight al numero di MRU corrente per verificare se è necessario aggiornare il MRU.

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Come creare flussi di video su richiesta tra aree 

* Il flusso tra aree video on demand comporta la duplicazione di [Asset](assets-concept.md), [Criteri chiave](content-key-policy-concept.md) simmetrica (se usati), [criteri di streaming](streaming-policy-concept.md)e [localizzatori di streaming](streaming-locators-concept.md). 
* Sarà necessario creare i criteri in entrambe le aree e mantenerli aggiornati. 
* Quando si creano i localizzatori di streaming, è necessario usare lo stesso valore di ID localizzatore, il valore ID ContentKey e il valore ContentKey.  
* Se si sta codificando il contenuto, è consigliabile codificare il contenuto nell'area A e pubblicarlo, quindi copiare il contenuto codificato nell'area B e pubblicarlo usando gli stessi valori dell'area A.
* È possibile usare gestione traffico nei nomi host per l'origine e il servizio di distribuzione delle chiavi (nella configurazione di servizi multimediali questo aspetto sarà simile a un URL del server di chiavi personalizzato).

## <a name="next-steps"></a>Passaggi successivi

* [Creare un account](create-account-cli-how-to.md)
* Estrai [esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
