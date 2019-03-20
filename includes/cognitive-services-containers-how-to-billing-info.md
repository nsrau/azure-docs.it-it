---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2019
ms.openlocfilehash: 200e2dfd2dd4f9aedd9256b307491a0b207ea124
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57964180"
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
| `Billing` | L'endpoint della risorsa Servizio cognitivo usato per tenere traccia delle informazioni di fatturazione.<br/>Il valore di questa opzione deve essere impostato sull'URI dell'endpoint di una risorsa LUIS di Azure di cui è stato effettuato il provisioning.|
| `Eula` | Indica che è stata accettata la licenza per il contenitore.<br/>Il valore di questa opzione deve essere impostato su `accept`. |


