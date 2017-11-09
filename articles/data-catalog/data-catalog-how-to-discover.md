---
title: Come trovare le origini dati in Azure Data Catalog | Microsoft Docs
description: "Questo articolo illustra le procedure di individuazione degli asset di dati registrati con Azure Data Catalog, inclusi la ricerca, i filtri e l'uso delle funzionalità di evidenziazione dei risultati del portale di Azure Data Catalog."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: f72ae3a3-6573-4710-89a7-f13555e1968c
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/01/2017
ms.author: maroche
ms.openlocfilehash: 7c2a1d61bc13ba1cde404e2b732933d2d314e3e3
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Come trovare le origini dati in Azure Data Catalog
## <a name="introduction"></a>Introduzione
Azure Data Catalog è un servizio cloud completamente gestito che funge da sistema di registrazione e di individuazione per le origini dati aziendali. In altre parole, Data Catalog permette agli utenti di trovare, comprendere e usare le origini dati e consente alle organizzazioni di ottenere maggior valore dai dati esistenti. Dopo che un'origine dati è stata registrata con Data Catalog, i metadati vengono indicizzati dal servizio, in modo che sia possibile eseguire facilmente ricerche per trovare i dati necessari.

## <a name="searching-and-filtering"></a>Ricerca e filtri
L'individuazione in Data Catalog usa due meccanismi principali: ricerca e filtri.

La ricerca è stata progettata in modo da essere intuitiva ed efficiente. Per impostazione predefinita, i termini di ricerca vengono confrontati con qualsiasi proprietà del catalogo, tra cui le annotazioni indicate dall'utente.

I filtri sono progettati per completare la ricerca. È possibile selezionare caratteristiche specifiche, ad esempio esperti, tipo di origine dati, tipo di oggetto e tag. È possibile visualizzare gli asset di dati corrispondenti e vincolare i risultati della ricerca agli asset corrispondenti.

Usando una combinazione di ricerca e filtri, gli utenti possono spostarsi rapidamente tra le origini dati registrate con Data Catalog per trovare le origini dati di proprio interesse.

## <a name="search-syntax"></a>Sintassi di ricerca
Anche se la ricerca di testo libero predefinita è semplice e intuitiva, è possibile usare anche la sintassi di ricerca di Data Catalog per un maggiore controllo sui risultati della ricerca. La ricerca di Data Catalog supporta le tecniche seguenti:

| Tecnica | Uso | Esempio |
| --- | --- | --- |
| Ricerca di base |Ricerca di base che usa uno o più termini di ricerca. I risultati sono gli asset che corrispondono alle proprietà di uno o più termini specificati. |`sales data` |
| Ambito della proprietà |Restituire solo le origini dati dove il termine di ricerca corrisponde alla proprietà specificata. |`name:finance` |
| Operatori booleani |Ampliare o restringere una ricerca usando operazioni booleane. |`finance NOT corporate` |
| Raggruppamento con parentesi |Usare le parentesi per raggruppare parti della query per ottenere l'isolamento logico, soprattutto in combinazione con gli operatori booleani. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Operatori di confronto |Usare confronti invece di uguaglianze per le proprietà che hanno dati di tipo numero e data. |`modifiedTime > "11/05/2014"` |

Per altre informazioni sulla ricerca di Data Catalog, vedere l'articolo [Azure Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx).

## <a name="hit-highlighting"></a>Evidenziazione dei risultati
Quando si visualizzano i risultati della ricerca, le proprietà visualizzate che corrispondono ai criteri di ricerca specificati (ad esempio, nome di asset di dati, descrizione e tag) vengono evidenziate per semplificare l'identificazione del motivo per cui un asset di dati specificato è stato restituito da una determinata ricerca.

> [!NOTE]
> Per disattivare l'evidenziazione dei risultati, usare l'opzione **Evidenzia** nel portale di Data Catalog.
>
>

Quando si visualizzano i risultati della ricerca, il motivo per cui un asset di dati è incluso potrebbe non essere sempre evidente, anche con l'evidenziazione abilitata. Poiché tutte le proprietà vengono ricercate per impostazione predefinita, un asset di dati potrebbe essere restituito a causa di una corrispondenza con una proprietà a livello di colonna. E poiché più utenti possono annotare gli asset di dati registrati con tag e descrizioni, è possibile che non tutti i metadati vengano visualizzati nell'elenco dei risultati della ricerca.

Nella visualizzazione affiancata predefinita ogni riquadro che appare nei risultati della ricerca include un'icona **Visualizza corrispondenze con i termini di ricerca**, per poter visualizzare rapidamente il numero di corrispondenze e il relativo percorso e di passare a esse se necessario.

 ![Evidenziazione dei risultati e corrispondenze di ricerca nel portale catalogo dati di Azure.](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Riepilogo
Poiché la registrazione di un'origine dati con Data Catalog copia i metadati strutturali e descrittivi dall'origine dati al servizio catalogo, diventa più facile trovare e comprendere l'origine dati. Dopo avere registrato un'origine dati, è possibile trovarla usando i filtri e la ricerca dal portale di Data Catalog.

## <a name="next-steps"></a>Passaggi successivi
* Per istruzioni dettagliate su come trovare le origini dati, vedere [Introduzione ad Azure Data Catalog](data-catalog-get-started.md).
