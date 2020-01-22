---
title: Usare l'API Video Indexer per personalizzare un modello Persona - Azure
titleSuffix: Azure Media Services
description: Questo articolo illustra come personalizzare un modello Persona con l'API Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 370e9e515359e2e2e598db90aa379f796b13c3fe
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76292400"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Personalizzare un modello Persona con l'API Video Indexer

Video Indexer supporta il rilevamento del viso e l'identificazione di celebrità nei contenuti video. La funzionalità di identificazione di celebrità include circa un milione di visi basandosi su origini dati di uso comune, ad esempio IMDB, Wikipedia e i principali influencer di LinkedIn. I visi che la funzionalità di identificazione di celebrità non riconosce vengono rilevati, ma lasciati senza nome. Dopo aver caricato il video di proprio interesse in Video Indexer e aver ottenuto i risultati, è possibile tornare indietro e assegnare un nome ai visi che non sono stati riconosciuti. Dopo aver etichettato un viso con un nome, il viso e il nome vengono aggiunti al modello delle persone del proprio account. Video Indexer identificherà quindi questo viso in tutti i video futuri e precedenti.

È possibile usare l'API Video Indexer per modificare i visi rilevati in un video, come descritto in questo argomento. È anche possibile usare il sito Web di Video Indexer, come descritto in [Personalizzare il modello Persona usando il sito Web di Video Indexer](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Gestire più modelli Persona 

Video Indexer supporta più modelli Persona per ogni account. Questa funzionalità è attualmente disponibile solo tramite le API Video Indexer.

Se l'account si occupa di scenari di casi d'uso diversi, può essere preferibile creare più modelli Persona per account. Se ad esempio il contenuto è relativo allo sport, è possibile creare un modello Persona separato per ogni sport (football, pallacanestro, calcio e così via). 

Dopo aver creato un modello, è possibile usarlo specificando l'ID modello di un modello Persona specifico quando si carica un video o lo si indicizza una o più volte. L'esecuzione del training di un nuovo viso per un video aggiorna il modello personalizzato specifico a cui è associato il viso.

Ogni account prevede un limite di 50 modelli Persona. Se non è necessario il supporto di più modelli Persona, non assegnare un ID di modello Persona al video durante il caricamento, l'indicizzazione o la reindicizzazione. In questo caso, Video Indexer usa il modello Persona personalizzato predefinito nell'account.

## <a name="create-a-new-person-model"></a>Creare un nuovo modello Persona

Per creare un nuovo modello di persona nell'account specificato, usare l'API di [creazione di un modello person](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?) .

La risposta specifica il nome e l'ID di modello generato del modello Persona che è stato appena creato seguendo il formato dell'esempio riportato di seguito.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

È quindi necessario usare il valore di **id** per il parametro **personModelId** quando [si carica un video da indicizzare](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) o [si reindicizza un video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Eliminare un modello Persona

Per eliminare un modello Person personalizzato dall'account specificato, usare l'API [Elimina un modello person](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?) . 

Dopo che il modello Persona è stato eliminato, l'indice dei video correnti che usavano tale modello rimane invariato fino alla reindicizzazione. Con la reindicizzazione, i visi che avevano un nome nel modello eliminato non vengono riconosciuti da Video Indexer nei video correnti che erano stati indicizzati usando tale modello. I visi vengono tuttavia rilevati. I video correnti che sono stati indicizzati usando il modello eliminato useranno il modello Persona predefinito dell'account. Se i visi presenti nel modello eliminato sono denominati anche nel modello predefinito dell'account, continueranno a essere riconosciuti nei video.

Non viene restituito contenuto quando il modello Persona viene eliminato.

## <a name="get-all-person-models"></a>Ottenere tutti i modelli Persona

Per ottenere tutti i modelli Person nell'account specificato, usare l'API [get an person Model](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?) .

La risposta restituisce un elenco di tutti i modelli Persona nell'account (incluso il modello predefinito nell'account specificato) e ciascuno dei relativi nomi e ID, seguendo il formato di esempio riportato di seguito.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

È possibile scegliere il modello da usare per un video specificando il valore di **id** del modello Persona per il parametro **personModelId** quando [si carica un video da indicizzare](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) o[si reindicizza un video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Aggiornare un viso

Questo comando consente di aggiornare un viso nel video con un nome usando l'ID del video e l'ID del viso. Viene quindi aggiornato il modello Persona a cui è stato associato il video durante il caricamento, l'indicizzazione o la reindicizzazione. Se non è stato assegnato alcun modello Persona, il comando aggiorna il modello Persona predefinito dell'account. 

Dopo questo aggiornamento, lo stesso viso viene riconosciuto negli altri video correnti che condividono lo stesso modello Persona. L'identificazione del viso negli altri video attuali può richiedere alcuni minuti poiché si tratta di un processo batch.

È possibile aggiornare il nome di un viso identificato in Video Indexer come celebrità. Il nuovo nome assegnato avrà la precedenza sull'identificazione di celebrità predefinita.

Per aggiornare il volto, usare l'API di [aggiornamento di un viso video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?) .

I nomi sono univoci per i modelli Persona. Se pertanto si attribuisce lo stesso valore del parametro **name** a due visi nello stesso modello Persona, Video Indexer visualizza i visi come la stessa persona e li converge quando si reindicizza il video. 

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello Persona usando il sito Web di Video Indexer](customize-person-model-with-website.md)
