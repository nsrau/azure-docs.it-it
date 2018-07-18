---
title: Informazioni su Ricerca personalizzata Bing | Microsoft Docs
description: Panoramica generale di Ricerca personalizzata Bing
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: 7cd61fc63d0d7734b842ed222c67c6753da9a418
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374737"
---
# <a name="what-is-bing-custom-search"></a>Informazioni su Ricerca personalizzata Bing

Ricerca personalizzata Bing consente di creare esperienze di ricerca personalizzata per gli argomenti a cui si è interessati. Ad esempio, se si è proprietari di un sito Web che offre un'esperienza di ricerca, è possibile specificare i domini, i siti Web e le pagine Web cercati da Bing. Gli utenti visualizzano risultati della ricerca specifici per il contenuto a cui sono interessati anziché scorrere i risultati della ricerca che possono includere contenuto irrilevante.

Per creare una visualizzazione personalizzata del Web, usare il [portale](https://customsearch.ai) di Ricerca personalizzata Bing. Il portale consente di creare un'istanza di ricerca personalizzata che specifica i domini, i siti Web e le pagine Web in cui si vuole eseguire la ricerca con Bing e i siti Web in cui non si vuole eseguire la ricerca. Oltre a specificare gli URL del contenuto che si conosce, è anche possibile usare il portale per trovare contenuto pertinente che potrebbe essere utile aggiungere.

Il portale consente anche di aggiungere una pagina Web specifica in alto nei risultati della ricerca, se l'utente immette un termine di ricerca specifico. 

Dopo aver definito l'istanza, è possibile integrare la ricerca personalizzata nel sito Web, nell'app desktop o nell'app per dispositivi mobili chiamando l'API Ricerca personalizzata. Se è disponibile un sito o un'applicazione basato su Web, è possibile delegare all'interfaccia utente ospitata il rendering dell'interfaccia di ricerca.

La figura seguente mostra la semplicità di integrazione della ricerca personalizzata.

![alt immagine](./media/bcs-overview.png "Come funziona Ricerca personalizzata Bing.")

## <a name="customize-search-suggestions"></a>Personalizzare i suggerimenti di ricerca

Se si è sottoscritto il livello appropriato di Ricerca personalizzata (vedere le [pagine sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), è possibile personalizzare i suggerimenti di ricerca visualizzati durante l'uso di Ricerca personalizzata. L'API Suggerimenti automatici personalizzati restituisce un elenco di query suggerite in base a una stringa di query parziale fornita dall'utente. Suggerimenti automatici personalizzati consente di fornire suggerimenti di ricerca personalizzati, pertinenti all'esperienza di ricerca dell'utente. È possibile specificare se restituire solo i suggerimenti personalizzati o includere i suggerimenti di Bing. Se vengono inclusi suggerimenti Bing, i suggerimenti personalizzati vengono visualizzati prima dei suggerimenti forniti da Bing. I suggerimenti di Bing sono limitati al contesto dell'istanza di Ricerca personalizzata.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare rapidamente, vedere [Create your first Bing Custom Search instance](quick-start.md) (Creare la prima istanza di Ricerca personalizzata Bing).

Per informazioni dettagliate sulle opzioni disponibili per personalizzare l'istanza di ricerca, vedere [Define a custom search instance](define-your-custom-view.md) (Definire un'istanza di Ricerca personalizzata).

Acquisire familiarità con le informazioni di riferimento sull'[API Ricerca personalizzata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference). La documentazione di riferimento contiene l'elenco di endpoint, intestazioni e parametri di query da usare per richiedere i risultati della ricerca. Include anche le definizioni degli oggetti della risposta.

Per informazioni su come personalizzare i suggerimenti, vedere [Define custom search suggestions](define-custom-suggestions.md) (Definire suggerimenti di ricerca personalizzati).

Per non violare nessuna delle regole relative all'uso dei risultati della ricerca, vedere [Bing Use and Display Requirements](./use-and-display-requirements.md) (Requisiti per l'uso e la visualizzazione di Bing).