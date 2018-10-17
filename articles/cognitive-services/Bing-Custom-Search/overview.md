---
title: Informazioni su Ricerca personalizzata Bing
titlesuffix: Azure Cognitive Services
description: Panoramica generale di Ricerca personalizzata Bing.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: overview
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: 2483bf36bb18af21bc454e08f3321b33094c43c8
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814305"
---
# <a name="what-is-bing-custom-search"></a>Informazioni su Ricerca personalizzata Bing

Ricerca personalizzata Bing consente di creare esperienze di ricerca personalizzata per gli argomenti a cui si è interessati. Ad esempio, se si è proprietari di un sito Web che offre un'esperienza di ricerca, è possibile specificare i domini, i siti Web e le pagine Web cercati da Bing. Gli utenti visualizzano risultati della ricerca specifici per il contenuto a cui sono interessati anziché scorrere i risultati della ricerca che possono includere contenuto irrilevante.

Per creare una visualizzazione personalizzata del Web, usare il [portale](https://customsearch.ai) di Ricerca personalizzata Bing. Il portale consente di creare un'istanza di ricerca personalizzata che specifica i domini, i siti Web e le pagine Web in cui si vuole eseguire la ricerca con Bing e i siti Web in cui non si vuole eseguire la ricerca. Oltre a specificare gli URL del contenuto che si conosce, è anche possibile usare il portale per trovare contenuto pertinente che potrebbe essere utile aggiungere.

Il portale consente anche di aggiungere una pagina Web specifica in alto nei risultati della ricerca, se l'utente immette un termine di ricerca specifico. 

Dopo aver definito l'istanza, è possibile integrare la ricerca personalizzata nel sito Web, nell'app desktop o nell'app per dispositivi mobili chiamando l'API Ricerca personalizzata. Se è disponibile un sito o un'applicazione basato su Web, è possibile delegare all'interfaccia utente ospitata il rendering dell'interfaccia di ricerca.

La figura seguente mostra la semplicità di integrazione della ricerca personalizzata.

![alt immagine](./media/bcs-overview.png "Come funziona Ricerca personalizzata Bing.")

## <a name="adding-custom-search-box-suggestions"></a>Aggiunta di suggerimenti personalizzati della casella di ricerca

È possibile arricchire la propria esperienza di ricerca con i suggerimenti personalizzati della casella di ricerca. Questa funzionalità consente di offrire suggerimenti di ricerca personalizzati pertinenti all'esperienza di ricerca dell'utente. Man mano che l'utente digita nella casella di ricerca, nell'elenco a discesa vengono suggerite stringhe di query in base alla stringa di query parziale dell'utente. È possibile specificare se restituire solo i suggerimenti personalizzati o se includere anche i suggerimenti di Bing. [Altre informazioni](define-custom-suggestions.md).

## <a name="adding-custom-image-search-experience"></a>Aggiunta dell'esperienza di ricerca di immagini personalizzata

È possibile arricchire la propria esperienza di ricerca con le immagini. Come per i risultati Web, la ricerca personalizzata supporta la ricerca di immagini nell'elenco di siti Web dell'istanza. [Altre informazioni](get-images-from-instance.md).

## <a name="adding-custom-video-search-experience"></a>Aggiunta dell'esperienza di ricerca di video personalizzata

È possibile arricchire la propria esperienza di ricerca con i video. Come per i risultati Web, la ricerca personalizzata supporta la ricerca di video nell'elenco di siti Web dell'istanza. [Altre informazioni](get-videos-from-instance.md).

## <a name="sharing-your-custom-search-instance-with-others"></a>Condivisione dell'istanza di ricerca personalizzata con altri utenti

È possibile condividere l'istanza con i membri del team per eseguire con facilità la modifica e il test dell'istanza stessa in modo collaborativo. [Altre informazioni](share-your-custom-search.md).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare rapidamente, vedere [Create your first Bing Custom Search instance](quick-start.md) (Creare la prima istanza di Ricerca personalizzata Bing).

Per informazioni dettagliate sulla personalizzazione dell'istanza di ricerca, vedere [Definire un'istanza di ricerca personalizzata](define-your-custom-view.md).

È consigliabile acquisire familiarità con il contenuto di riferimento per ognuno degli endpoint di ricerca personalizzata. La documentazione di riferimento contiene gli endpoint, le intestazioni e i parametri di query da usare per richiedere risultati della ricerca. Include anche le definizioni degli oggetti della risposta.

- [API Ricerca personalizzata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [API Ricerca personalizzata immagini](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [API Ricerca personalizzata video](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [API Suggerimenti automatici personalizzati](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)


Per non violare nessuna delle regole relative all'uso dei risultati della ricerca, vedere [Bing Use and Display Requirements](./use-and-display-requirements.md) (Requisiti per l'uso e la visualizzazione di Bing).