---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: e92d1c65d9601c23e7e785f07e2de3e43ea6612b
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684651"
---
Le query per il contenitore vengono fatturate al piano tariffario della risorsa di Azure usato per il `<ApiKey>`.

I contenitori di servizi cognitivi non sono concessi in licenza per l'esecuzione senza connettersi all'endpoint di fatturazione per il controllo. I clienti devono consentire i contenitori di comunicare le informazioni di fatturazione con endpoint di fatturazione in qualsiasi momento. I contenitori di Servizi cognitivi non inviano a Microsoft i dati dei clienti, ad esempio l'immagine o il testo analizzato. 

### <a name="connecting-to-azure"></a>La connessione ad Azure

Il contenitore deve i valori degli argomenti fatturazione per l'esecuzione. Questi valori consentono il contenitore per la connessione all'endpoint di fatturazione. Il contenitore segnala l'utilizzo ogni 10-15 minuti. Se il contenitore non può connettersi all'interno della finestra di tempo consentito ad Azure, il contenitore continuerà a essere eseguita ma non di servire query fino a quando non viene ripristinato l'endpoint di fatturazione. La connessione è tentata 10 volte nella stesso intervallo di tempo di 10-15 minuti. Se non è possibile connettersi all'endpoint di fatturazione all'interno di 10 tentativi, il contenitore cesserà di funzionare. 

### <a name="billing-arguments"></a>Argomenti di fatturazione

Tutte e tre delle seguenti opzioni deve essere specificati con i valori validi per consentire il `docker run` comando per avviare il contenitore:

| Opzione | DESCRIZIONE |
|--------|-------------|
| `ApiKey` | La chiave API della risorsa Servizio cognitivo usata per tenere traccia delle informazioni di fatturazione.<br/>Il valore di questa opzione deve essere impostato su una chiave API per la risorsa di cui è stato effettuato il provisioning specificata in `Billing`. |
| `Billing` | L'endpoint della risorsa Servizio cognitivo usato per tenere traccia delle informazioni di fatturazione.<br/>Il valore di questa opzione deve essere impostato per l'URI di una risorsa di Azure con provisioning dell'endpoint.|
| `Eula` | Indica che è stata accettata la licenza per il contenitore.<br/>Il valore di questa opzione deve essere impostato su `accept`. |


