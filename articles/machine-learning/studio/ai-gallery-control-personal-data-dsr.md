---
title: Visualizzare ed eliminare i dati da Azure AI Gallery | Microsoft Docs
description: È possibile esportare ed eliminare i dati utente interni al prodotto da Azure AI Gallery usando l'interfaccia o l'API catalogo di AI Gallery. Questo articolo illustra i passaggi da eseguire.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: cgronlun
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.date: 05/25/2018
ms.openlocfilehash: bc6ffa912d7914c8662dbde623e04947540ac149
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659510"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Visualizzare ed eliminare i dati utente interni al prodotto da Azure AI Gallery

È possibile visualizzare ed eliminare i dati utente interni al prodotto da Azure AI Gallery usando l'interfaccia o l'API catalogo di AI Gallery. Questo articolo spiega come procedere.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Visualizzare i dati in AI Gallery con l'interfaccia utente

È possibile visualizzare gli elementi pubblicati tramite l'interfaccia utente del sito Web di Azure AI Gallery. Gli utenti possono visualizzare soluzioni, progetti, esperimenti e altri elementi pubblicati, sia pubblici che non in elenco:

1.  Accedere ad [Azure AI Gallery](https://gallery.azure.ai/).
2.  Fare clic sull'immagine del profilo nell'angolo superiore destro e quindi sul nome dell'account per caricare la pagina del profilo.
3.  La pagina del profilo visualizza tutti gli elementi pubblicati nella raccolta, inclusi gli elementi non in elenco.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Usare l'API catalogo di AI Gallery per visualizzare i dati

È possibile visualizzare a livello di codice i dati raccolti tramite l'API catalogo di AI Gallery, accessibile all'indirizzo https://catalog.cortanaanalytics.com/entities. Per visualizzare i dati, è necessario specificare l'ID autore. Per visualizzare le entità non in elenco tramite l'API catalogo, è necessario un token di accesso.

Le risposte del catalogo vengono restituite in formato JSON.

### <a name="get-an-author-id"></a>Ottenere un ID autore
L'ID autore è basato sull'indirizzo di posta elettronica usato per eseguire la pubblicazione in Azure AI Gallery. Non cambia mai.

1.  Accedere ad [Azure AI Gallery](https://gallery.azure.ai/).
2.  Fare clic sull'immagine del profilo nell'angolo superiore destro e quindi sul nome dell'account per caricare la pagina del profilo.
3.  L'URL nella barra degli indirizzi visualizza l'ID alfanumerico dopo `authorId=`. Ad esempio, per l'URL: `https://gallery.cortanaintelligence.com/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    L'ID autore è: `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Ottenere il token di accesso

Per visualizzare le entità non in elenco tramite l'API catalogo, è necessario un token di accesso. Senza il token di accesso, gli utenti possono comunque visualizzare le entità pubbliche e altre informazioni utente.

Per ottenere un token di accesso, è necessario esaminare l'intestazione `DataLabAccessToken` di una richiesta HTTP inviata dal browser all'API catalogo mentre si è connessi:

1.  Accedere ad [Azure AI Gallery](https://gallery.azure.ai/).
2.  Fare clic sull'immagine del profilo nell'angolo superiore destro e quindi sul nome dell'account per caricare la pagina del profilo.
3.  Aprire il riquadro Strumenti di sviluppo del browser premendo F12, selezionare la scheda Rete e aggiornare la pagina. 
4. Filtrare le richieste sulla stringa *catalog* digitando nella casella di testo Filtro.
5.  Nelle richieste all'URL `https://catalog.cortanaanalytics.com/entities` trovare una richiesta GET e selezionare la scheda *Intestazioni*. Scorrere verso il basso fino alla sezione *Intestazioni della richiesta*.
6.  Sotto l'intestazione `DataLabAccessToken` c'è il token alfanumerico. Per garantire la sicurezza dei dati, non condividere questo token.

### <a name="view-user-information"></a>Visualizzare le informazioni utente
Usando l'ID autore ottenuto nei passaggi precedenti, visualizzare le informazioni del profilo di un utente sostituendo `[AuthorId]` nell'URL seguente:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Ad esempio, la richiesta di URL:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Restituisce una risposta simile a:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Visualizzare le entità pubbliche

L'API catalogo archivia informazioni sulle entità pubblicate in Azure AI Gallery che possono anche essere visualizzate direttamente nel [sito Web di AI Gallery](https://gallery.azure.ai/). 

Per visualizzare le entità pubblicate, visitare l'URL seguente, sostituendo `[AuthorId]` con l'ID autore ottenuto nel passaggio [Ottenere un ID autore](#get-an-author-ID).

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Ad esempio: 

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Visualizzare le entità pubbliche e non in elenco

Questa query visualizza solo le entità pubbliche. Per visualizzare tutte le entità, incluse quelle non in elenco, specificare il token di accesso ottenuto nella sezione precedente.

1.  Usando uno strumento come [Postman](https://www.getpostman.com), creare una richiesta HTTP GET per l'URL del catalogo, come descritto in [Ottenere il token di accesso](#get-your-access-token).
2.  Creare un'intestazione di richiesta HTTP denominata `DataLabAccessToken`, con il valore impostato sul token di accesso.
3.  Inviare la richiesta HTTP.

> [!TIP]
> Se le entità non in elenco non vengono visualizzate nelle risposte dell'API catalogo, è possibile che l'utente abbia un token di accesso non valido o scaduto. Disconnettersi da Azure AI Gallery e ripetere la procedura descritta in [Ottenere il token di accesso](#get-your-access-token) per rinnovare il token. 
