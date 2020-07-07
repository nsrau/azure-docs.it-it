---
title: Personalizzare un modello di persona con Video Indexer API
titleSuffix: Azure Media Services
description: Informazioni su come personalizzare un modello di persona con l'API Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: fa41fca7f8ad96cf507aa6f04059b1254c8c3961
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80127899"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Personalizzare un modello Persona con l'API Video Indexer

Video Indexer supporta il rilevamento del viso e l'identificazione di celebrità nei contenuti video. La funzionalità di riconoscimento celebrità copre circa 1 milione visi in base all'origine dati comunemente richiesta, ad esempio IMDB, Wikipedia e i principali influencer di LinkedIn. I visi che non sono riconosciuti dalla funzionalità di riconoscimento della celebrità sono rilevati ma lasciati senza nome. Dopo aver caricato il video per Video Indexer e avere restituito i risultati, è possibile tornare indietro e denominare i visi che non sono stati riconosciuti. Dopo aver etichettato un viso con un nome, il viso e il nome vengono aggiunti al modello delle persone del proprio account. Video Indexer identificherà quindi questo viso in tutti i video futuri e precedenti.

È possibile usare l'API Video Indexer per modificare i visi rilevati in un video, come descritto in questo argomento. È anche possibile usare il sito Web di Video Indexer, come descritto in [Personalizzare il modello Persona usando il sito Web di Video Indexer](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Gestire più modelli Persona

Video Indexer supporta più modelli Persona per ogni account. Questa funzionalità è attualmente disponibile solo tramite le API Video Indexer.

Se l'account si occupa di scenari di casi d'uso diversi, può essere preferibile creare più modelli Persona per account. Ad esempio, se il contenuto è correlato a Sports, è possibile creare un modello person separato per ogni sport (calcio, basket, calcio e così via).

Dopo aver creato un modello, è possibile usarlo specificando l'ID modello di un modello Persona specifico quando si carica un video o lo si indicizza una o più volte. L'esecuzione del training di un nuovo viso per un video aggiorna il modello personalizzato specifico a cui è associato il viso.

Ogni account prevede un limite di 50 modelli Persona. Se non è necessario il supporto del modello a persona multipla, non assegnare un ID modello di persona al video durante il caricamento, l'indicizzazione o la reindicizzazione. In questo caso, Video Indexer usa il modello Persona personalizzato predefinito nell'account.

## <a name="create-a-new-person-model"></a>Creare un nuovo modello Persona

Per creare un nuovo modello di persona nell'account specificato, usare l'API di [creazione di un modello person](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?) .

La risposta specifica il nome e l'ID di modello generato del modello Persona che è stato appena creato seguendo il formato dell'esempio riportato di seguito.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Si usa quindi il valore **ID** per il parametro **personModelId** quando si [carica un video per indicizzare](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) o [reindicizzare un video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Eliminare un modello Persona

Per eliminare un modello Person personalizzato dall'account specificato, usare l'API [Elimina un modello person](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?) .

Dopo che il modello Persona è stato eliminato, l'indice dei video correnti che usavano tale modello rimane invariato fino alla reindicizzazione. Al momento della reindicizzazione, le facce denominate nel modello eliminato non verranno riconosciute da Video Indexer nei video correnti indicizzati con tale modello, ma i visi verranno comunque rilevati. I video correnti che sono stati indicizzati usando il modello eliminato useranno il modello Persona predefinito dell'account. Se i visi presenti nel modello eliminato sono denominati anche nel modello predefinito dell'account, continueranno a essere riconosciuti nei video.

Non viene restituito alcun contenuto quando il modello Person viene eliminato correttamente.

## <a name="get-all-person-models"></a>Ottenere tutti i modelli Persona

Per ottenere tutti i modelli Person nell'account specificato, usare l'API [get an person Model](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?) .

La risposta fornisce un elenco di tutti i modelli Person nell'account (incluso il modello person predefinito nell'account specificato) e ognuno dei relativi nomi e ID che seguono il formato dell'esempio riportato di seguito.

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

È possibile scegliere il modello che si vuole usare per un video usando il `id` valore del modello person per il `personModelId` parametro quando si [carica un video per indicizzare](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) o [reindicizzare un](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)video.

## <a name="update-a-face"></a>Aggiornare un viso

Questo comando consente di aggiornare un viso nel video con un nome usando l'ID del video e l'ID del viso. Questa azione Aggiorna quindi il modello person a cui è stato associato il video durante il caricamento, l'indicizzazione o la reindicizzazione. Se non è stato assegnato alcun modello Persona, il comando aggiorna il modello Persona predefinito dell'account.

Il sistema riconosce quindi le occorrenze dello stesso volto negli altri video correnti che condividono lo stesso modello di persona. L'identificazione del viso negli altri video attuali può richiedere alcuni minuti poiché si tratta di un processo batch.

È possibile aggiornare il nome di un viso identificato in Video Indexer come celebrità. Il nuovo nome assegnato avrà la precedenza sull'identificazione di celebrità predefinita.

Per aggiornare il volto, usare l'API di [aggiornamento di un viso video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?) .

I nomi sono univoci per i modelli person, quindi se si assegnano due visi diversi nello stesso modello di persona allo stesso `name` valore di parametro, video Indexer Visualizza i visi come la stessa persona e li converge dopo aver reindicizzato il video.

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello Persona usando il sito Web di Video Indexer](customize-person-model-with-website.md)
