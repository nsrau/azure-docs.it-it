---
title: Cartelle di lavoro di monitoraggio di Azure-trasformare i dati JSON con JSONPath
description: Come usare JSONPath nelle cartelle di lavoro di monitoraggio di Azure per trasformare i risultati dei dati JSON restituiti da un endpoint sottoposto a query nel formato desiderato.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: efa54933ac7d57ec0dcff9ae11b6fb5c2c87a897
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87081390"
---
# <a name="how-to-use-jsonpath-to-transform-json-data-in-workbooks"></a>Come usare JSONPath per trasformare i dati JSON nelle cartelle di lavoro

Le cartelle di lavoro di sono in grado di eseguire query sui dati da molte origini. Alcuni endpoint, ad esempio [Azure Resource Manager](../../azure-resource-manager/management/overview.md) o un endpoint personalizzato, possono restituire i risultati in formato JSON. Se i dati JSON restituiti dall'endpoint sottoposto a query non sono configurati in un formato desiderato, è possibile usare JSONPath per trasformare i risultati.

JSONPath è un linguaggio di query per JSON simile a XPath for XML. Analogamente a XPath, JSONPath consente l'estrazione e la filtraggio dei dati fuori dalla struttura JSON.

Utilizzando la trasformazione JSONPath, gli autori di cartelle di lavoro sono in grado di convertire JSON in una struttura di tabella. La tabella può quindi essere usata per tracciare le [visualizzazioni delle cartelle di lavoro](workbooks-visualizations.md).

## <a name="using-jsonpath"></a>Uso di JSONPath

1. Impostare la cartella di lavoro in modalità di modifica facendo clic sulla voce *Modifica* sulla barra degli strumenti.
2. Utilizzare il collegamento *Aggiungi*  >  *query* per aggiungere un controllo query alla cartella di lavoro.
3. Selezionare l'origine dati come *JSON*.
4. Usare l'editor JSON per inserire il frammento di codice JSON seguente
    ```json
    { "store": {
        "books": [ 
          { "category": "reference",
            "author": "Nigel Rees",
            "title": "Sayings of the Century",
            "price": 8.95
          },
          { "category": "fiction",
            "author": "Evelyn Waugh",
            "title": "Sword of Honour",
            "price": 12.99
          },
          { "category": "fiction",
            "author": "Herman Melville",
            "title": "Moby Dick",
            "isbn": "0-553-21311-3",
            "price": 8.99
          },
          { "category": "fiction",
            "author": "J. R. R. Tolkien",
            "title": "The Lord of the Rings",
            "isbn": "0-395-19395-8",
            "price": 22.99
          }
        ],
        "bicycle": {
          "color": "red",
          "price": 19.95
        }
      }
    }
    ```  

Si supponga che l'oggetto JSON riportato sopra venga considerato come una rappresentazione dell'inventario di un negozio. L'attività consiste nel creare una tabella dei libri disponibili del negozio elencando i titoli, gli autori e i prezzi.

1. Selezionare la scheda *impostazioni risultato* e cambiare il formato del risultato in *JSON Path*.
2. Applicare le impostazioni del percorso JSON seguenti:

    Tabella percorso JSON: `$.store.books` . Questo campo rappresenta il percorso della radice della tabella. In questo caso, ci occupiamo dell'inventario del libro del negozio. Il percorso della tabella filtra il codice JSON per le informazioni sul libro.

   | ID colonna | Percorso JSON della colonna |
   |:-----------|:-----------------|
   | Titolo      | `$.title`        |
   | Autore     | `$.author`       |
   | Prezzo      | `$.price`        |

    Gli ID di colonna saranno le intestazioni di colonna. I campi dei percorsi JSON della colonna rappresentano il percorso dalla radice della tabella al valore della colonna.

1. Per applicare le impostazioni precedenti, fare clic su *Esegui query*

![ Modifica dell'elemento di query con origine dati JSON e formato del risultato del percorso JSON](./media/workbooks-jsonpath/query-jsonpath.png)

## <a name="next-steps"></a>Passaggi successivi
- [Cenni preliminari sulle cartelle di lavoro](workbooks-overview.md)
- [Gruppi in cartelle di lavoro di monitoraggio di Azure](workbooks-groups.md)
