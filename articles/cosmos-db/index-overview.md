---
title: Indicizzazione in Azure Cosmos DB
description: Informazioni sull'indicizzazione in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: c0525cfba16fb61f8388ae4d6a693be3bb71674c
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628530"
---
# <a name="indexing-in-azure-cosmos-db"></a>Indicizzazione in Azure Cosmos DB

Azure Cosmos DB è un database senza schema e consente di eseguire rapidamente l'iterazione sull'applicazione senza dover gestire schemi o indici. Per impostazione predefinita, Azure Cosmos DB indicizza automaticamente tutti gli elementi nel contenitore senza richiedere schemi o indici secondari da parte degli sviluppatori.

## <a name="items-as-trees"></a>Elementi come strutture ad albero

Proiettando gli elementi in un contenitore come documenti JSON e rappresentandoli come alberi, Azure Cosmos DB normalizza sia la struttura che i valori dell'istanza tra gli elementi nel concetto unificante di una  **struttura di percorso codificata dinamicamente**. In questa rappresentazione, ogni etichetta in un documento JSON, che include i nomi delle proprietà e i relativi valori, diventa un nodo dell'albero. Le foglie dell'albero contengono i valori effettivi e i nodi intermedi contengono le informazioni dello schema. L'immagine seguente rappresenta gli alberi creati per due elementi (1 e 2) in un contenitore:

![Rappresentazione in forma di albero per due diversi elementi in un contenitore di Azure Cosmos](./media/index-overview/indexing-as-tree.png)

Uno pseudo nodo radice viene creato come elemento padre dei nodi effettivi corrispondenti alle etichette sottostanti nel documento. Le strutture dei dati annidati determinano la gerarchia dell'albero. I nodi artificiali intermedi etichettati con valori numerici (per esempio, 0, 1, ....) sono utilizzati per rappresentare le enumerazioni e gli indici di matrice.

## <a name="index-paths"></a>Percorsi di indice

Azure Cosmos DB progetta elementi come documenti JSON e l'indice come alberi. È possibile quindi usare i criteri per i percorsi all'interno dell'albero. È possibile scegliere di includere o escludere i percorsi dall'indicizzazione. Questo consente di migliorare le prestazioni di scrittura e ridurre lo spazio di archiviazione dell'indice per gli scenari in cui i modelli di query sono già noti. Per altre informazioni, vedere [Percorsi di indice](index-paths.md).

## <a name="indexing-under-the-hood"></a>Indicizzazione: dietro le quinte

Il database di Azure Cosmos applica l'indicizzazione automatica dei dati, dove ogni percorso di un albero è indicizzato a meno che non si configuri per escludere determinati percorsi.

Il database di Azure Cosmos utilizza una struttura dei dati di indice invertita per archiviare le informazioni di ogni articolo e per facilitare una rappresentazione efficiente per l'esecuzione di query. La struttura ad albero dell'indice è un documento che viene costruito con l'unione di tutte le strutture ad albero che rappresentano singoli elementi nel contenitore. La struttura ad albero dell'indice aumenta nel corso del tempo man mano che vengono aggiunti nuovi elementi o gli elementi esistenti vengono aggiornati nel contenitore. A differenza dell'indicizzazione del database relazionale, Azure Cosmos DB non riavvia l'indicizzazione da zero quando vengono introdotti nuovi campi, ma vengono aggiunti nuovi elementi alla struttura esistente. 

Ogni nodo dell'albero dell'indice è una voce di indice che contiene i valori di etichetta e di posizione, chiamati termine, e gli ID degli elementi, chiamati pubblicazioni. Le pubblicazioni nelle parentesi graffe (ad esempio {1,2}) nella figura dell'indice invertito corrispondono agli elementi, ad esempio Document1 e Document2, contenenti il valore di etichetta specificato. Un'importante implicazione del trattamento uniforme delle etichette dello schema e dei valori delle istanze è che tutti gli elementi vengono compressi all'interno di un indice di grandi dimensioni. Un valore di istanza che è ancora nelle foglie non viene ripetuto, può essere in ruoli diversi tra gli elementi, con etichette di schemi diverse, ma è lo stesso valore. L'immagine seguente mostra l'indice invertito per diversi elementi:

![Indicizzazione dietro le quinte, Indice invertito](./media/index-overview/inverted-index.png)

> [!NOTE]
> L'indice invertito può apparire simile alle strutture di indicizzazione usate in un motore di ricerca nel dominio di recupero delle informazioni. Con questo metodo, Azure Cosmos DB consente di cercare nel database qualsiasi elemento indipendentemente dalla relativa struttura dello schema.

Per il percorso normalizzato, l'indice codifica il percorso di inoltro direttamente dalla radice al valore, insieme alle informazioni sul tipo di valore. Il percorso e il valore sono codificati per fornire diversi tipi di indicizzazione, ad esempio intervallo, tipi spaziali. La codifica del valore è progettata per fornire un valore univoco o una composizione di un set di percorsi.

## <a name="querying-with-indexes"></a>Esecuzione di query con indici

L'indice invertito consente a una query di identificare rapidamente i documenti che corrispondono al predicato della query. Trattando uniformemente sia lo schema che i valori di istanza in termini di percorsi, l'indice invertito è anche una struttura ad albero. Di conseguenza, l'indice e i risultati possono essere serializzati in un documento JSON valido e restituiti come documenti stessi in quanto restituiti nella rappresentazione ad albero. Questo metodo consente la ricorsione sui risultati per l'esecuzione di query aggiuntive. L'immagine seguente illustra un esempio dell'indicizzazione in una query di punto:  

![Esempio di query di punto](./media/index-overview/index-point-query.png)

Per una query di intervallo, GermanTax è una funzione definita dall'utente eseguita durante l'elaborazione della query. La funzione definita dall'utente è una qualsiasi funzione Javascript registrata, che può fornire una logica di programmazione avanzata integrata nella query. L'immagine seguente illustra un esempio dell'indicizzazione in una query di intervallo:

![Esempio di query di intervallo](./media/index-overview/index-range-query.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indicizzazione sono disponibili negli articoli seguenti:

- [Criterio di indicizzazione](index-policy.md)
- [Tipi di indice](index-types.md)
- [Percorsi di indice](index-paths.md)
- [Come gestire i criteri di indicizzazione](how-to-manage-indexing-policy.md)