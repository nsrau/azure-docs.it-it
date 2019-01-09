---
title: Informazioni su Ricerca visiva Bing
titleSuffix: Azure Cognitive Services
description: Ricerca visiva Bing fornisce informazioni dettagliate su un'immagine, ad esempio immagini simili o luoghi di acquisto degli oggetti raffigurati.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: overview
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: aab17f8a0213c213920ebdfc2b2fe31248657a91
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742848"
---
# <a name="what-is-the-bing-visual-search-api"></a>Informazioni sull'API Ricerca visiva Bing

L'API Ricerca visiva Bing fornisce dettagli sulle immagini simili a quelli visualizzati in Bing.com/images. Caricando un'immagine o inserendone l'URL, questa API può identificare un'ampia gamma di informazioni su di essa, tra cui immagini visivamente simili, luoghi di acquisto, pagine Web che includono l'immagine e altro ancora. Se si usa l'[API Ricerca immagini Bing](../bing-image-search/overview.md) è possibile usare i token di informazioni dettagliate associati ai risultati della ricerca dell'API invece di caricare un'immagine.

## <a name="insights"></a>Informazioni dettagliate

Di seguito vengono indicate le informazioni dettagliate che Ricerca visiva consente di individuare:

| Informazioni dettagliate                              | DESCRIZIONE |
|--------------------------------------|-------------|
| Immagini visivamente simili              | Elenco di immagini visivamente simili all'immagine di input. |
| Prodotti visivamente simili            | Prodotti simili a livello visivo al prodotto mostrato.            |
| Fonti per l'acquisto                     | Elenco di luoghi in cui è possibile acquistare il prodotto mostrato nell'immagine di input.            |
| Ricerche correlate                     | Elenco di ricerche correlate eseguite da altri utenti o basate sul contenuto dell'immagine.            |
| Pagine Web che includono l'immagine     | Pagine Web che includono l'immagine.            |
| Ricette                              | Pagine Web contenenti ricette per preparare il piatto illustrato nell'immagine di input            |

Oltre a queste informazioni dettagliate, Ricerca visiva restituisce anche un set eterogeneo di termini (tag) derivati dall'immagine di input. Tali tag consentono agli utenti di esplorare i concetti trovati nell'immagine. Se ad esempio l'immagine di input è quella di un atleta famoso, uno dei tag potrebbe essere il nome dell'atleta e un altro tag potrebbe essere Sport. In alternativa, se l'immagine di input è una torta di mele, i tag possono essere Torta di mele, Torte o Dessert, che consentono agli utenti di esplorare concetti correlati.

I risultati di Ricerca visiva includono anche rettangoli di selezione per le aree di interesse nell'immagine. Se ad esempio l'immagine contiene personaggi famosi diversi, i risultati possono includere rettangoli di selezione per ogni personaggio famoso riconosciuto nell'immagine. In alternativa, se Bing riconosce un prodotto o un abito nell'immagine, il risultato può includere un rettangolo di selezione per il prodotto o l'abito riconosciuto.

> [!IMPORTANT]
> Se si ottengono informazioni dettagliate sulle immagini usando l'API Ricerca immagini Bing, valutare l'opportunità di passare all'API Ricerca visiva Bing, che fornisce informazioni più complete.

## <a name="workflow"></a>Flusso di lavoro

L'API Ricerca visiva Bing è un servizio Web RESTful, facile da chiamare da qualsiasi linguaggio di programmazione in grado di effettuare richieste HTTP e analizzare codice JSON. È possibile usare il servizio mediante l'API REST o il Software Development Kit.

1. Creare un account API Servizi cognitivi con accesso alle API di ricerca Bing. Se non si ha una sottoscrizione di Azure, è possibile creare un account gratuito.
2. Inviare una richiesta all'API con una query di ricerca valida.
3. Elaborare la risposta dell'API tramite l'analisi del messaggio JSON restituito.


## <a name="next-steps"></a>Passaggi successivi

Provare prima la [demo interattiva](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/) dell'API Ricerca immagini Bing.
Questa demo illustra come personalizzare rapidamente una query di ricerca e perlustrare il Web alla ricerca di immagini.

Quando si è pronti per chiamare l'API, creare un [account API servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Se non si ha una sottoscrizione di Azure, è possibile [creare un account](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuito.

Per iniziare rapidamente con la prima richiesta, vedere gli argomenti di avvio rapido: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).


## <a name="see-also"></a>Vedere anche 

* La documentazione di riferimento sull'[API Ricerca visiva Bing ](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) contiene definizioni e informazioni sugli endpoint, le intestazioni, le risposte dell'API e i parametri di query che è possibile usare per richiedere i risultati della ricerca basata su immagine.

* I [requisiti per l'uso e la visualizzazione di Bing](./use-and-display-requirements.md) specificano gli usi accettabili dei contenuti e delle informazioni ottenute tramite le API di ricerca di Bing.
