---
title: Cercare una vista personalizzata - Ricerca personalizzata Bing
titlesuffix: Azure Cognitive Services
description: Descrive come cercare una vista personalizzata del Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: maheshb
ms.openlocfilehash: 9c0c4fc21f6f4b23b14c8020ecd7e95c63dedcbf
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388556"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Chiamare l'istanza di Ricerca personalizzata Bing dal portale

Dopo aver configurato l'esperienza di ricerca personalizzata, è possibile testarla dall'interno del [portale](https://customsearch.ai) di Ricerca personalizzata Bing. 

![screenshot del portale di ricerca personalizzata Bing](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Creare una query di ricerca 

Dopo aver effettuato l'accesso al [portale](https://customsearch.ai) di Ricerca personalizzata Bing, selezionare l'istanza di ricerca e fare clic sulla scheda **Produzione**. Sotto **Endpoint** selezionare un endpoint API, ad esempio API Web. La sottoscrizione determina quali endpoint vengono visualizzati.

Per creare una query di ricerca, immettere i valori dei parametri per l'endpoint. Si noti che i parametri visualizzati nel portale possono cambiare a seconda dell'endpoint scelto. Vedere le [riferimento all'API ricerca personalizzata](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) per altre informazioni. Per modificare la sottoscrizione che viene utilizzata l'istanza di ricerca, aggiungere la chiave di sottoscrizione appropriata e aggiornare i parametri di immissione sul mercato e/o linguaggio appropriati.

Alcuni parametri importanti sono i seguenti:


|Parametro  |Descrizione  |
|---------|---------|
|Query     | Il termine da cercare. Disponibile solo per gli endpoint Web, Image e Autosuggest |
|ID di configurazione personalizzato | L'ID di configurazione dell'istanza di Ricerca personalizzata selezionata. Questo campo è di sola lettura. |
|Market     | Il mercato da cui deriveranno i risultati. Disponibile solo per gli endpoint Web, Image, Video e Hosted UI.        |
|Chiave di sottoscrizione | La chiave di sottoscrizione con cui eseguire il test. È possibile selezionare una chiave nell'elenco a discesa o immetterla manualmente.          |

Facendo clic su **Parametri aggiuntivi** vengono visualizzati i parametri seguenti:  

|Parametro  |Descrizione  |
|---------|---------|
|Ricerca sicura     | Un filtro utilizzato per filtrare le pagine Web per il contenuto per adulti. Disponibile solo per gli endpoint Web, Image, Video e Hosted UI.        |
|Lingua dell'interfaccia utente    | La lingua utilizzata per le stringhe dell'interfaccia utente. Ad esempio, se si abilitano immagini e video nell'Hosted UI, le schede**Image** e **Video** usano la lingua specificata.        |
|Conteggio     | Il numero di risultati della ricerca da restituire nella risposta. Disponibile solo per gli endpoint Web, Image e Video.         |
|Offset    | Il numero di risultati della ricerca da ignorare prima di restituire i risultati. Disponibile solo per gli endpoint Web, Image e Video.        |
    
Dopo aver specificato tutte le opzioni necessarie, fare clic su **Call** per visualizzare la risposta JSON nel riquadro di destra. Se si seleziona l'endpoint Hosted UI, è possibile provare l'esperienza di ricerca nel riquadro inferiore.

## <a name="change-your-bing-custom-search-subscription"></a>Modificare la sottoscrizione di ricerca personalizzata Bing

È possibile modificare la sottoscrizione associata all'istanza di ricerca personalizzata Bing senza creare una nuova istanza. Per le chiamate API inviato e addebitati a una nuova sottoscrizione, creare una nuova risorsa di ricerca personalizzata Bing nel portale di Azure. Usare la nuova chiave di sottoscrizione nelle richieste API, con ID di configurazione personalizzata. dell'istanza

## <a name="next-steps"></a>Passaggi successivi

- [Richiamare la visualizzazione personalizzata con C#](./call-endpoint-csharp.md)
- [Richiamare la visualizzazione personalizzata con Java](./call-endpoint-java.md)
- [Richiamare la visualizzazione personalizzata con NodeJs](./call-endpoint-nodejs.md)
- [Richiamare la visualizzazione personalizzata con Python](./call-endpoint-python.md)

- [Richiamare la visualizzazione personalizzata con C#](./sdk-csharp-quick-start.md)
