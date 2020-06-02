---
title: Archiviare dati non strutturati usando Azure Cosmos DB e Funzioni
description: Archiviare dati non strutturati tramite Funzioni di Azure e Cosmos DB
ms.topic: quickstart
ms.date: 04/14/2020
ms.custom: mvc
ms.openlocfilehash: 09d9bbca7119539f31a4cea056f338cf28dfcd23
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121894"
---
# <a name="store-unstructured-data-using-azure-functions-and-azure-cosmos-db"></a>Archiviare dati non strutturati usando Funzioni di Azure e Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) offre un approccio ottimale per archiviare dati non strutturati e JSON. Insieme a Funzioni di Azure, Cosmos DB semplifica e velocizza l'archiviazione dei dati con una quantità minore di codice rispetto a quella necessaria per l'archiviazione dei dati in un database relazionale.

> [!NOTE]
> Al momento, il trigger e le associazioni di input e output di Azure Cosmos DB funzionano solo con gli account API SQL e API Graph.

In Funzioni di Azure, i binding di input e di output forniscono una modalità dichiarativa per connettersi a dati di servizio esterni dalla funzione. Questo articolo illustra come aggiornare una funzione esistente per l'aggiunta di un binding di output che archivia dati non strutturati in un documento di Azure Cosmos DB.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Creare un account Azure Cosmos DB

Prima di creare il binding di output è necessario un account Azure Cosmos DB che usa l'API SQL.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-an-output-binding"></a>Aggiungere un binding di output

1. Nel portale di Azure passare all'app per le funzioni creata in precedenza e selezionarla.

1. Selezionare **Funzioni** e quindi selezionare la funzione HttpTrigger.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-select-http-function.png" alt-text="Selezionare la funzione HTTP nel portale di Azure." border="true":::

1. Selezionare **Integrazione** e **+ Aggiungi output**.

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-add-output-binding.png" alt-text="Aggiungere un binding di output di Azure Cosmos DB." border="true":::

1. Usare le impostazioni di **Crea output** come specificato nella tabella:

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-configure-cosmosdb-binding.png" alt-text="Configurare il binding di output di Azure Cosmos DB." border="true":::

    | Impostazione      | Valore consigliato  | Descrizione                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Tipo di binding** | Azure Cosmos DB | Nome del tipo di binding da selezionare per creare il binding di output per Azure Cosmos DB. |
    | **Nome del parametro del documento** | taskDocument | Nome che fa riferimento all'oggetto Cosmos DB nel codice. |
    | **Nome database** | taskDatabase | Nome del database per il salvataggio dei documenti. |
    | **Nome raccolta** | taskCollection | Nome della raccolta di database. |
    | **Se true, crea il database e la raccolta di Cosmos DB** | Sì | La raccolta non esiste ancora, quindi crearla. |
    | **Connessione all'account Cosmos DB** | Nuova impostazione | Selezionare **Nuovo**, scegliere **Account Azure Cosmos DB** e l'**account di database** creato in precedenza, quindi selezionare **OK**. Viene creata un'impostazione applicazione per la connessione all'account. Questa impostazione viene usata dal binding per la connessione al database. |

1. Selezionare **OK** per creare il binding.

## <a name="update-the-function-code"></a>Aggiornare il codice funzione

Sostituire il codice della funzione esistente con quello seguente nel linguaggio scelto:

# <a name="c"></a>[C#](#tab/csharp)

Sostituire la funzione C# esistente con il codice seguente:

```csharp
#r "Newtonsoft.Json"

using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, out object taskDocument, ILogger log)
{
    string name = req.Query["name"];
    string task = req.Query["task"];
    string duedate = req.Query["duedate"];

    // We need both name and task parameters.
    if (!string.IsNullOrEmpty(name) && !string.IsNullOrEmpty(task))
    {
        taskDocument = new
        {
            name,
            duedate,
            task
        };

        return (ActionResult)new OkResult();
    }
    else
    {
        taskDocument = null;
        return (ActionResult)new BadRequestResult();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Sostituire la funzione JavaScript esistente con il codice seguente:

```js
module.exports = async function (context, req) {

    // We need both name and task parameters.
    if (req.query.name && req.query.task) {

        // Set the output binding data from the query object.
        context.bindings.taskDocument = req.query;

        // Success.
        context.res = {
            status: 200
        };
    }
    else {
        context.res = {
            status: 400,
            body: "The query options 'name' and 'task' are required."
        };
    }
};
```
---

Questo esempio di codice legge le stringhe della query di richiesta HTTP e le assegna ai campi dell'oggetto `taskDocument`. Il binding `taskDocument` invia i dati dell'oggetto di questo parametro di binding per l'archiviazione nel database di documenti associato. Il database viene creato alla prima esecuzione della funzione.

## <a name="test-the-function-and-database"></a>Testare la funzione e il database

1. Selezionare **Test**. In **Query** selezionare **+ Aggiungi parametro** e aggiungere i parametri seguenti alla stringa di query:

    + `name`
    + `task`
    + `duedate`

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png" alt-text="Testare la funzione." border="true":::


1. Selezionare **Esegui** e verificare che venga restituito uno stato 200.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function-output.png" alt-text="Testare la funzione." border="true":::


1. Nel portale di Azure cercare e selezionare **Azure Cosmos DB**.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-search-cosmos-db.png" alt-text="Cercare il servizio Cosmos DB." border="true":::

1. Scegliere l'account Azure Cosmos DB, quindi selezionare **Esplora dati**.

1. Espandere i nodi **TaskCollection**, selezionare il nuovo documento e verificare che contenga i valori della stringa di query, oltre ad alcuni metadati aggiuntivi.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-data-explorer-check-document.png" alt-text="Verificare i valori di stringa nel documento." border="true":::

È stato aggiunto un binding al trigger HTTP per archiviare dati non strutturati in un'istanza di Azure Cosmos DB.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul binding a un database Cosmos DB, vedere [Binding di Azure Cosmos DB in Funzioni di Azure](functions-bindings-cosmosdb.md).

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps-2.md)]
