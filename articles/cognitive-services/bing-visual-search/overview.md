---
title: Informazioni sull'API Ricerca visiva Bing
titleSuffix: Azure Cognitive Services
description: Ricerca visiva Bing fornisce informazioni dettagliate su un'immagine, ad esempio immagini simili o luoghi di acquisto degli oggetti raffigurati.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 03/27/2019
ms.author: scottwhi
ms.openlocfilehash: 4da4340176259265fd36c55d2b81ecfc3f4ed5f8
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657690"
---
# <a name="what-is-the-bing-visual-search-api"></a>Informazioni sull'API Ricerca visiva Bing

L'API Ricerca visiva Bing restituisce informazioni dettagliate relative a un'immagine. È possibile caricare un'immagine o specificarne l'URL. Le informazioni dettagliate sono immagini visivamente simili, luoghi di acquisto degli oggetti raffigurati nelle immagini, pagine Web che includono l'immagine e altro ancora. Le informazioni dettagliate restituite dall'API Ricerca visiva Bing sono simili a quelle visualizzate nel sito Bing.com/images.

Se si usa l'[API Ricerca immagini Bing](../bing-image-search/overview.md) è possibile usare i token di informazioni dettagliate restituiti dai risultati della ricerca dell'API per Ricerca visiva Bing invece di caricare un'immagine.

> [!IMPORTANT]
> Se si ottengono informazioni dettagliate sulle immagini usando l'API Ricerca immagini Bing, valutare l'opportunità di passare all'API Ricerca visiva Bing, che fornisce informazioni più complete.

## <a name="insights"></a>Informazioni dettagliate

Ricerca visiva Bing consente di individuare le informazioni dettagliate seguenti:

| Informazioni dettagliate                              | DESCRIZIONE |
|--------------------------------------|-------------|
| Immagini visivamente simili              | Elenco di immagini visivamente simili all'immagine di input. |
| Prodotti visivamente simili            | Prodotti simili a livello visivo al prodotto mostrato.            |
| Fonti per l'acquisto                     | Elenco di luoghi in cui è possibile acquistare il prodotto mostrato nell'immagine di input.            |
| Ricerche correlate                     | Elenco di ricerche correlate eseguite da altri utenti o basate sul contenuto dell'immagine.            |
| Pagine Web che includono l'immagine     | Pagine Web che includono l'immagine.            |
| Ricette                              | Pagine Web contenenti ricette per preparare il piatto illustrato nell'immagine di input.            |

Oltre alle informazioni dettagliate, Ricerca visiva Bing restituisce una varietà di termini (ovvero tag) derivati dall'immagine di input. I tag consentono agli utenti di esplorare i concetti trovati nell'immagine. Se ad esempio l'immagine di input è quella di un atleta famoso, uno dei tag potrebbe essere il nome dell'atleta e un altro tag potrebbe essere Sport. In alternativa, se l'immagine di input è una torta di mele, i tag possono essere Torta di mele, Torte e Dessert.

I risultati di Ricerca visiva Bing includono anche rettangoli di selezione per le aree di interesse nell'immagine. Se ad esempio l'immagine contiene personaggi famosi diversi, i risultati possono includere rettangoli di selezione per ogni personaggio famoso riconosciuto. In alternativa, se Bing riconosce un prodotto o un abito nell'immagine, il risultato può includere un rettangolo di selezione per l'elemento riconosciuto.

## <a name="workflow"></a>Flusso di lavoro

L'API Ricerca visiva Bing è un servizio Web RESTful, facile da chiamare da qualsiasi linguaggio di programmazione in grado di effettuare richieste HTTP e analizzare codice JSON. È possibile usare il servizio mediante l'API REST o l'SDK.

1. Creare un [account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per accedere alle API di Ricerca Bing. Se non si ha una sottoscrizione di Azure, è possibile [creare un account gratuito](https://azure.microsoft.com/free/). È possibile ottenere la chiave di sottoscrizione dal [portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#get-the-keys-for-your-subscription) dopo la creazione dell'account oppure il [sito Web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis) dopo l'attivazione di una versione di valutazione gratuita.
2. Inviare una richiesta all'API con una query di ricerca valida.
3. Elaborare la risposta dell'API tramite l'analisi del messaggio JSON restituito.

## <a name="next-steps"></a>Passaggi successivi

Provare prima la [demo interattiva](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/) dell'API Ricerca visiva Bing.
La demo illustra come personalizzare rapidamente una query di ricerca ed esplorare il Web alla ricerca di immagini.

Per iniziare rapidamente con la prima richiesta, vedere le guide introduttive: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

## <a name="see-also"></a>Vedere anche

* L'argomento di riferimento sulla [Ricerca visiva di immagini](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) contiene definizioni e informazioni sugli endpoint, le intestazioni, le risposte e i parametri di query che è possibile usare per richiedere i risultati della ricerca basata su immagine.

* I [requisiti per l'uso e la visualizzazione dell'API di Ricerca Bing](../bing-web-search/use-display-requirements.md) specificano gli usi accettabili dei contenuti e delle informazioni ottenute tramite le API di Ricerca Bing.
