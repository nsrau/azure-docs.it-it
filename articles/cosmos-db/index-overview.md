---
title: Indicizzazione in Azure Cosmos DB
description: Informazioni sull'indicizzazione in Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: ecf53251020ce1b639a5bf8da65f5d31ff699db9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265696"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indicizzazione in Azure Cosmos DB - panoramica

Azure Cosmos DB è un database senza schema e consente di eseguire rapidamente l'iterazione sull'applicazione senza dover gestire schemi o indici. Per impostazione predefinita, Azure Cosmos DB indicizza automaticamente tutti gli elementi nel contenitore senza richiedere schemi o indici secondari da parte degli sviluppatori.

## <a name="items-as-trees"></a>Elementi come strutture ad albero

Proiezione di elementi in un contenitore come documenti JSON e che li rappresentano come strutture ad albero, Azure Cosmos DB Normalizza la struttura e i valori dell'istanza tra gli elementi nel concetto di unificare un **in modo dinamico con codifica struttura del percorso** . In questa rappresentazione, ogni etichetta in un documento JSON, che include sia i nomi delle proprietà e i relativi valori, diventa un nodo dell'albero. La foglia dell'albero di contenga i valori effettivi e i nodi intermedi contengono le informazioni sullo schema. L'immagine seguente rappresenta gli alberi creati per due elementi (1 e 2) in un contenitore di Azure Cosmos:

![Rappresentazione in forma di albero per due diversi elementi in un contenitore di Azure Cosmos](./media/index-overview/indexing-as-tree.png)

Un nodo radice pseudo viene creato come elemento padre ai nodi effettivi corrispondenti per le etichette nel documento JSON di sotto. Le strutture dei dati annidati determinano la gerarchia dell'albero. I nodi artificiali intermedi etichettati con valori numerici (per esempio, 0, 1, ....) sono utilizzati per rappresentare le enumerazioni e gli indici di matrice.

## <a name="index-paths"></a>Percorsi di indice

Azure Cosmos DB proietta gli elementi in un contenitore di Azure Cosmos come documenti JSON e l'indice come alberi. È quindi possibile ottimizzare i criteri di indice per i percorsi all'interno dell'albero. È possibile scegliere di includere o escludere i percorsi dall'indicizzazione. Questo consente di migliorare le prestazioni di scrittura e ridurre lo spazio di archiviazione dell'indice per gli scenari in cui i modelli di query sono già noti. Per altre informazioni, vedere [percorsi di indice](index-paths.md).

## <a name="indexing-under-the-hood"></a>Indicizzazione: Dietro le quinte

Si applica il database di Azure Cosmos *l'indicizzazione automatica* ai dati, in cui ogni percorso in un albero viene indicizzato, a meno che non si configura per escludere determinati percorsi.

Il database di Azure Cosmos utilizza una struttura dei dati di indice invertita per archiviare le informazioni di ogni articolo e per facilitare una rappresentazione efficiente per l'esecuzione di query. La struttura dell'indice è un documento che viene costruito con l'unione di tutte le strutture ad albero che rappresentano i singoli elementi in un contenitore. La struttura dell'indice cresce nel tempo, quando vengono aggiunti nuovi elementi o gli elementi esistenti vengono aggiornati nel contenitore. A differenza di indicizzazione del database relazionale, Azure Cosmos DB non riavvia l'indicizzazione da zero, quando vengono introdotti nuovi campi. Nuovi elementi vengono aggiunti alla struttura dell'indice esistente. 

Ogni nodo dell'albero dell'indice è una voce di indice che contiene i valori di etichetta e la posizione, denominati il *termine*e gli ID degli elementi di *registrazioni*. Le registrazioni delle parentesi graffe (ad esempio {1,2}) nella figura indice invertito corrispondono agli elementi, ad esempio *Document1* e *Document2* contenente il valore di etichetta specificato. Un'importante implicazione del trattamento in modo uniforme le etichette di schema sia i valori dell'istanza è che tutti gli elementi vengono compressi all'interno di un indice di grandi dimensioni. Un valore di istanza che è ancora nelle foglie non viene ripetuto, può essere in ruoli diversi tra gli elementi, con etichette di schemi diverse, ma è lo stesso valore. L'immagine seguente mostra l'indice invertito per due diversi elementi:

![Indicizzazione dietro le quinte, Indice invertito](./media/index-overview/inverted-index.png)

> [!NOTE]
> L'indice invertito può apparire simile alle strutture di indicizzazione usate in un motore di ricerca nel dominio di recupero delle informazioni. Con questo metodo, Azure Cosmos DB consente di cercare nel database qualsiasi elemento indipendentemente dalla relativa struttura dello schema.

Per il percorso normalizzato, l'indice codifica il percorso di inoltro direttamente dalla radice al valore, insieme alle informazioni sul tipo di valore. Il percorso e il valore sono codificati per fornire tipi diversi di indicizzazione, ad esempio l'intervallo, spaziale e così via. La codifica del valore è progettata per fornire un valore univoco o una composizione di un set di percorsi.

## <a name="querying-with-indexes"></a>Esecuzione di query con indici

L'indice invertito consente a una query di identificare rapidamente i documenti che corrispondono al predicato della query. Trattando sia lo schema sia i valori dell'istanza in modo uniforme in termini di percorsi, l'indice invertito è anche una struttura ad albero. Di conseguenza, l'indice e i risultati possono essere serializzati in un documento JSON valido e restituiti come documenti stessi in quanto restituiti nella rappresentazione ad albero. Questo metodo consente la ricorsione sui risultati per l'esecuzione di query aggiuntive. L'immagine seguente illustra un esempio dell'indicizzazione in una query di punto:  

![Esempio di query di punto](./media/index-overview/index-point-query.png)

Per una query di intervallo *GermanTax* è un [funzione definita dall'utente](stored-procedures-triggers-udfs.md#udfs) eseguito come parte dell'elaborazione delle query. La funzione definita dall'utente è qualsiasi funzione JavaScript registrata, che può fornire la logica di programmazione avanzata integrata nella query. L'immagine seguente illustra un esempio dell'indicizzazione in una query di intervallo:

![Esempio di query di intervallo](./media/index-overview/index-range-query.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indicizzazione sono disponibili negli articoli seguenti:

- [Criterio di indicizzazione](index-policy.md)
- [Tipi di indice](index-types.md)
- [Percorsi di indice](index-paths.md)
- [Come gestire i criteri di indicizzazione](how-to-manage-indexing-policy.md)
