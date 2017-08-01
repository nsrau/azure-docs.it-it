---
title: Archiviare dati non strutturati tramite Funzioni di Azure e Cosmos DB
description: Archiviare dati non strutturati tramite Funzioni di Azure e Cosmos DB
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, Cosmos DB, calcolo dinamico, architettura senza server
ms.assetid: 
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/08/2017
ms.author: rachelap
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 492c916a493bb8d5c5415fc517506e5c1ccffc56
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="store-unstructured-data-using-azure-functions-and-cosmos-db"></a>Archiviare dati non strutturati tramite Funzioni di Azure e Cosmos DB

Azure Cosmos DB offre un approccio ottimale per archiviare dati non strutturati e JSON. Insieme a Funzioni di Azure, Cosmos DB semplifica e velocizza l'archiviazione dei dati con una quantità minore di codice rispetto a quella necessaria per l'archiviazione dei dati in un database relazionale.

Questa esercitazione illustra l'uso del portale di Azure per la creazione di una funzione di Azure che archivia i dati non strutturati in un documento di Cosmos DB. 

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-a-function"></a>Creare una funzione

Creare un nuovo webhook generico in C# denominato `MyTaskList`.

1. Espandere l'elenco di funzioni esistenti e fare clic sul segno + per creare una nuova funzione
1. Selezionare GenericWebHook-CSharp e denominarlo `MyTaskList`

![Aggiungere una nuova app per le funzioni di tipo webhook generico in C#](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-new-functionapp.png)

## <a name="add-an-output-binding"></a>Aggiungere un binding di output

Una funzione di Azure può avere un trigger e un numero qualsiasi di binding di input oppure output. Per questo esempio verranno usati un trigger Richiesta HTTP e il documento di Cosmos DB come binding di output.

1. Fare clic sulla scheda *Integrazione* della funzione per visualizzare o modificare il trigger e i binding della funzione.
1. Scegliere il collegamento *Nuovo output* disponibile in alto a destra nella pagina.

Nota: il trigger Richiesta HTTP è già configurato ma è necessario aggiungere il binding al documento di Cosmos DB.

![Aggiungere un nuovo binding di output di Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-add-new-output-binding.png)

1. Immettere le informazioni necessarie per creare il binding. Usare la tabella seguente per determinare i valori.

![Configurare il binding di output di Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-configure-cosmosdb-binding.png)

|  Campo | Valore  |
|---|---|
| Nome del parametro del documento | Nome che fa riferimento all'oggetto Cosmos DB nel codice |
| Nome database | Nome del database per il salvataggio dei documenti |
| Nome raccolta | Nome del raggruppamento di database di Cosmos DB |
| Creare automaticamente il database e la raccolta Cosmos DB? | Sì o No |
| Connessione dell'account Cosmos DB | Stringa di connessione che fa riferimento al database Cosmos DB |

È anche necessario configurare la connessione al database Cosmos DB.

1. Fare clic sul collegamento "Nuovo" accanto all'etichetta "Connessione dell'account Cosmos DB".
1. Compilare i campi e selezionare le opzioni appropriate necessarie per creare il documento di Cosmos DB.

![Configurare la connessione di Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-CosmosDB.png)

|  Campo | Valore  |
|---|---|
| ID | ID univoco per il database Cosmos DB  |
| API NoSQL | Cosmos DB o MongoDB  |
| Sottoscrizione | Abbonamento a MSDN  |
| Gruppo di risorse  | Creare un nuovo gruppo o selezionarne uno esistente.  |
| Località  | Europa occidentale  |

1. Fare clic sul pulsante *OK* . È possibile che la creazione delle risorse da parte di Azure richieda qualche minuto.
1. Fare clic sul pulsante *Salva* .

## <a name="update-the-function-code"></a>Aggiornare il codice funzione

Sostituire il codice del modello della funzione con il codice seguente:

Si noti che il codice per questo esempio è solo in C#.

```csharp
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, out object taskDocument, TraceWriter log)
{
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    string task = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "task", true) == 0)
        .Value;

    string duedate = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "duedate", true) == 0)
        .Value;

    taskDocument = new {
        name = name,
        duedate = duedate.ToString(),
        task = task
    };

    if (name != "" && task != "") {
        return req.CreateResponse(HttpStatusCode.OK);
    }
    else {
        return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}

```

Questo esempio di codice legge le stringhe della query Richiesta HTTP e le assegna come membri di un oggetto `taskDocument`. L'oggetto `taskDocument` salva automaticamente i dati nel database Cosmos DB e crea anche il database al primo uso.

## <a name="test-the-function-and-database"></a>Testare la funzione e il database

1. Nella scheda della funzione fare clic sul collegamento *Test* nel lato destro del portale e immettere le stringhe di query HTTP seguenti:

| Stringa di query | Valore |
|---|---|
| name | Chris P. Bacon |
| attività | Fare un sandwich BLT |
| duedate | 12/05/2017 |

1. Fare clic sul collegamento *Esegui*.
1. Verificare che la funzione abbia restituito un codice di risposta *HTTP 200 OK*.

![Configurare il binding di output di Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png)

Confermare che è stata creata una voce nel database Cosmos DB.

1. Trovare il database nel portale di Azure e selezionarlo.
1. Selezionare l'opzione *Esplora dati*.
1. Espandere i nodi fino a raggiungere le voci del documento.
1. Confermare la voce del database. Nel database saranno presenti metadati aggiuntivi insieme ai dati specifici.

![Verificare la voce di Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-verify-cosmosdb-output.png)

Se i dati sono presenti nel documento, è stata creata correttamente una funzione di Azure che archivia dati non strutturati in un database Cosmos DB.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]

Per altre informazioni sul binding a un database Cosmos DB, vedere [Binding di Azure Cosmos DB in Funzioni di Azure](functions-bindings-documentdb.md).

