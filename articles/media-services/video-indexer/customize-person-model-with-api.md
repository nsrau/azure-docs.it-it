---
title: Usare l'API Video Indexer per personalizzare un modello Persona - Azure
titlesuffix: Azure Media Services
description: Questo articolo illustra come personalizzare un modello Persona con l'API Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: e5a34a75c73401c567a0e898a1ce9f85cde96586
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360514"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Personalizzare un modello Persona con l'API Video Indexer

Video Indexer supporta il rilevamento viso e il riconoscimento dei personaggi famosi nei contenuti video. La funzionalità di identificazione di celebrità include circa un milione di visi basandosi su origini dati di uso comune, ad esempio IMDB, Wikipedia e i principali influencer di LinkedIn. I visi che la funzionalità di riconoscimento dei personaggi famosi non riconosce vengono rilevati, ma lasciati senza nome. Dopo aver caricato il video di proprio interesse in Video Indexer e aver ottenuto i risultati, è possibile tornare indietro e assegnare un nome ai visi che non sono stati riconosciuti. Dopo aver etichettato un viso con un nome, il viso e il nome vengono aggiunti al modello Persona del proprio account. Video Indexer riconoscerà quindi questo viso in tutti i video futuri e precedenti.

È possibile usare l'API Video Indexer per modificare i visi rilevati in un video, come descritto in questo argomento. È anche possibile usare il sito Web di Video Indexer, come descritto in [Personalizzare il modello Persona usando il sito Web di Video Indexer](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Gestire più modelli Persona 

Video Indexer supporta più modelli Persona per ogni account. Questa funzionalità è attualmente disponibile solo tramite le API Video Indexer.

Se l'account si occupa di scenari di casi d'uso diversi, può essere preferibile creare più modelli Persona per account. Se ad esempio il contenuto è relativo allo sport, è possibile creare un modello Persona separato per ogni sport (football, pallacanestro, calcio e così via). 

Dopo aver creato un modello, è possibile usarlo specificando l'ID modello di un modello Persona specifico quando si carica un video o lo si indicizza una o più volte. L'esecuzione del training di un nuovo viso per un video aggiorna il modello personalizzato specifico a cui è associato il viso.

Ogni account prevede un limite di 50 modelli Persona. Se non è necessario il supporto di più modelli Persona, non assegnare un ID di modello Persona al video durante il caricamento, l'indicizzazione o la reindicizzazione. In questo caso, Video Indexer usa il modello Persona personalizzato predefinito nell'account.

## <a name="create-a-new-person-model"></a>Creare un nuovo modello Persona

Creare un nuovo modello Persona nell'account specificato. 

### <a name="request-url"></a>URL richiesta

Si tratta di una richiesta POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Di seguito è riportata la richiesta in Curl.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}"
```

[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?).

### <a name="request-parameters"></a>Parametri della richiesta 

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|stringa|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountId|stringa|Sì|Identificatore univoco globale per l'account|
|name|stringa|Sì|Nome per il modello Persona|
|accessToken|stringa|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|

### <a name="request-body"></a>Corpo della richiesta

Per questa chiamata non è necessario specificare altri dati nel corpo della richiesta.

### <a name="response"></a>Risposta

La risposta specifica il nome e l'ID di modello generato del modello Persona che è stato appena creato seguendo il formato dell'esempio riportato di seguito.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

È quindi necessario usare il valore di **id** per il parametro **personModelId** quando [si carica un video da indicizzare](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) o [si reindicizza un video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Eliminare un modello Persona

Eliminare un modello Persona personalizzato dall'account specificato. 

Dopo che il modello Persona è stato eliminato, l'indice dei video correnti che usavano tale modello rimane invariato fino alla reindicizzazione. Con la reindicizzazione, i visi che avevano un nome nel modello eliminato non vengono riconosciuti da Video Indexer nei video correnti che erano stati indicizzati usando tale modello. I visi vengono tuttavia rilevati. I video correnti che sono stati indicizzati usando il modello eliminato useranno il modello Persona predefinito dell'account. Se i visi presenti nel modello eliminato sono denominati anche nel modello predefinito dell'account, continueranno a essere riconosciuti nei video.

### <a name="request-url"></a>URL richiesta

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}
```

Di seguito è riportata la richiesta in Curl.
```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}"
```

[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?).

### <a name="request-parameters"></a>Parametri della richiesta

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|stringa|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountId|stringa|Sì|Identificatore univoco globale per l'account|
|id|stringa|Sì|L'id del modello Persona (generato quando viene creato il modello Persona)|
|accessToken|stringa|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|

### <a name="request-body"></a>Corpo della richiesta

Per questa chiamata non è necessario specificare altri dati nel corpo della richiesta.

### <a name="response"></a>Risposta

Non viene restituito contenuto quando il modello Persona viene eliminato.

## <a name="get-all-person-models"></a>Ottenere tutti i modelli Persona

Ottenere tutti i modelli Persona nell'account specificato. 

### <a name="request-call"></a>Chiamata di richiesta

Si tratta di una richiesta GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Di seguito è riportata la richiesta in Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}"
```

[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?).

### <a name="request-parameters"></a>Parametri della richiesta

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|stringa|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountId|stringa|Sì|Identificatore univoco globale per l'account|
|accessToken|stringa|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|

### <a name="request-body"></a>Corpo della richiesta

Per questa chiamata non è necessario specificare altri dati nel corpo della richiesta.

### <a name="response"></a>Risposta

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

Dopo questo aggiornamento, lo stesso viso viene riconosciuto negli altri video correnti che condividono lo stesso modello Persona. Il riconoscimento del viso negli altri video correnti può richiedere alcuni minuti poiché si tratta di un processo batch.

È possibile aggiornare il nome di un viso riconosciuto in Video Indexer come un personaggio famoso. Il nuovo nome assegnato avrà la precedenza sul riconoscimento dei personaggi famosi integrato.

### <a name="request-call"></a>Chiamata di richiesta

Si tratta di una richiesta POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}
```

Di seguito è riportata la richiesta in Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}"
```

[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?).

### <a name="request-parameters"></a>Parametri della richiesta

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|stringa|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountId|stringa|Sì|Identificatore univoco globale per l'account|
|videoId|stringa|Sì|ID del video in cui viene visualizzato il viso che si desidera aggiornare. Viene creato quando il video viene caricato e indicizzato.|
|faceId|numero intero|Sì|ID del viso che verrà aggiornato. È possibile ottenerlo dall'indice del video|
|accessToken|stringa|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|
|name|stringa|Sì|Nuovo nome con cui aggiornare il viso.|

I nomi sono univoci per i modelli Persona. Se pertanto si attribuisce lo stesso valore del parametro **name** a due visi nello stesso modello Persona, Video Indexer visualizza i visi come la stessa persona e li converge quando si reindicizza il video. 

### <a name="request-body"></a>Corpo della richiesta

Per questa chiamata non è necessario specificare altri dati nel corpo della richiesta.

### <a name="response"></a>Risposta

Non viene restituito alcun contenuto quando il viso viene aggiornato correttamente.

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello Persona usando il sito Web di Video Indexer](customize-person-model-with-website.md)
