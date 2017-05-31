---
title: 'Strumento del portale per Azure Cosmos DB: Esplora script | Microsoft Docs'
description: Informazioni su Esplora script di Cosmos DB, uno strumento del portale di Azure che consente di gestire elementi di programmazione sul lato server di Cosmos DB quali stored procedure di JavaScript, trigger e funzioni definite dall&quot;utente.
keywords: Editor JavaScript
services: cosmosdb
author: kirillg
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 9d0620da-2449-4c17-82a4-24aaa46e9b3e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: kirillg
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e9c3e92dd1c35ff6893565e61f7e7b940b6ede7e
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="create-and-run-stored-procedures-triggers-and-user-defined-functions-using-the-azure-cosmos-db-script-explorer"></a>Creare ed eseguire stored procedure, trigger e funzioni definite dall'utente usando Esplora script di Cosmos DB
Questo articolo fornisce una panoramica di Esplora script di [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/), un editor JavaScript del portale di Azure che consente di visualizzare ed eseguire elementi di programmazione sul lato server di Cosmos DB quali stored procedure, trigger e funzioni definite dall'utente. Per altre informazioni sulla programmazione sul lato server di Cosmos DB, vedere l'articolo [Stored procedure, trigger del database e funzioni definite dall'utente](documentdb-programming.md).

## <a name="launch-script-explorer"></a>Avviare Esplora script
1. Nel [portale di Azure](https://portal.azure.com) fare clic sull'![icona di Azure Cosmos DB](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **Azure Cosmos DB** nel riquadro di spostamento sinistro. 

    Se **Azure Cosmos DB** non è visibile, fare clic su **Altri servizi** nella parte inferiore e quindi sull'![icona di Azure Cosmos DB](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **Azure Cosmos DB**.
2. Nel menu delle risorse fare clic su **Esplora script**.
   
    ![Schermata del comando Esplora script](./media/documentdb-view-scripts/scriptexplorercommand.png)
   
    Gli elenchi a discesa **Database** e **Raccolta** vengono precompilati in base al contesto in cui viene avviato Esplora script.  Ad esempio, se viene avviato da un pannello di database, viene prepopolato il database corrente.  Se viene avviato da un pannello della raccolta, viene prepopolata la raccolta corrente.
3. Usare gli elenchi a discesa **Database** e **Raccolta** per modificare facilmente la raccolta da cui sono attualmente visualizzati gli script senza dover chiudere e riavviare Esplora script.  
4. Esplora script supporta anche l'applicazione di filtri al set di script attualmente caricato in base alla relativa proprietà ID.  Digitare i criteri nella casella di filtro. I risultati presenti nell'elenco di Esplora script verranno filtrati in base ai criteri forniti.
   
    ![Schermata di Esplora script con risultati del filtro](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)

    > [!IMPORTANT] 
    > La funzionalità di filtro di Esplora script consente di filtrare solo dal set di script ***attualmente*** caricato e non aggiorna automaticamente la raccolta selezionata.

1. Per aggiornare l'elenco di script caricati da Esplora script, fare semplicemente clic sul comando **Aggiorna** nella parte superiore del pannello.
   
    ![Schermata del comando di aggiornamento di Esplora script](./media/documentdb-view-scripts/scriptexplorerrefresh.png)

## <a name="create-view-and-edit-stored-procedures-triggers-and-user-defined-functions"></a>Creare, visualizzare e modificare stored procedure, trigger e funzioni definite dall'utente
Esplora script consente di eseguire facilmente operazioni CRUD sugli elementi di programmazione lato server di Cosmos DB.  

* Per creare uno script, è sufficiente fare clic sul comando di creazione applicabile all'interno di Esplora script, fornire un ID, immettere il contenuto dello script e fare clic su **Salva**.
  
    ![Schermata dell'opzione di creazione di Esplora script con l'editor JavaScript](./media/documentdb-view-scripts/scriptexplorercreatecommand.png)
* Quando si crea un trigger, è necessario specificare anche il tipo di trigger e l'operazione del trigger
  
    ![Schermata dell’opzione di creazione trigger di Esplora script](./media/documentdb-view-scripts/scriptexplorercreatetrigger.png)
* Fare clic sullo script a cui si è interessati per visualizzarlo.
  
    ![Schermata dell’esperienza di visualizzazione script di Esplora script](./media/documentdb-view-scripts/scriptexplorerviewscript.png)
* Per modificare uno script, è sufficiente apportare le modifiche desiderate nell'editor JavaScript e fare clic su **Salva**.
  
    ![Schermata dell’esperienza di visualizzazione script di Esplora script](./media/documentdb-view-scripts/scriptexplorereditscript.png)
* Per annullare le modifiche in sospeso a uno script, fare semplicemente clic sul comando **Annulla** .
  
    ![Schermata dell’esperienza di annullamento modifiche di Esplora script](./media/documentdb-view-scripts/scriptexplorerdiscardchanges.png)
* Esplora script consente, inoltre, di visualizzare facilmente le proprietà di sistema dello script attualmente caricato facendo clic sul comando **Proprietà** .
  
    ![Schermata della vista delle proprietà dello script di Esplora script](./media/documentdb-view-scripts/scriptproperties.png)
  
  > [!NOTE]
  > La proprietà timestamp (_ts) viene rappresentata internamente come epoch time, ma Esplora script consente di visualizzare il valore in formato GMT leggibile.
  > 
  > 
* Per eliminare uno script, selezionarlo in Esplora script e scegliere il comando **Elimina** .
  
    ![Schermata del comando di eliminazione di Esplora script](./media/documentdb-view-scripts/scriptexplorerdeletescript1.png)
* Confermare l'azione di eliminazione facendo clic su **Sì** oppure annullarla facendo clic su **No**.
  
    ![Schermata del comando di eliminazione di Esplora script](./media/documentdb-view-scripts/scriptexplorerdeletescript2.png)

## <a name="execute-a-stored-procedure"></a>Eseguire una stored procedure
> [!WARNING]
> L’esecuzione di stored procedure in Esplora script non è ancora supportata per le raccolte partizionate lato server. Per altre informazioni, visitare [Partizionamento e ridimensionamento in DocumentDB](documentdb-partition-data.md).
> 
> 

Esplora script consente di eseguire stored procedure lato server dal portale di Azure.

* Quando si apre il pannello di una stored procedure appena creata, viene fornito uno script predefinito (*prefix*). Per eseguire lo script *prefix* o uno script personale, aggiungere un *ID* e gli *input*. Per le stored procedure che accettano più parametri, tutti gli input devono trovarsi all'interno di una matrice, ad esempio *["foo", "bar"]*.
  
    ![Schermata del pannello Stored procedure di Esplora script per l'aggiunta di input e l'esecuzione di una stored procedure](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-input.png)
* Per eseguire una stored procedure, è sufficiente fare clic sul comando **Salva ed esegui** nel riquadro dell'editor di script.
  
  > [!NOTE]
  > Il comando **Salva ed esegui** salva la stored procedure prima dell'esecuzione, sovrascrivendo così la versione della stored procedure salvata in precedenza.
  > 
  > 
* Per le esecuzioni di stored procedure con esito positivo viene visualizzato lo stato *La stored procedure è stata salvata e l'esecuzione è riuscita* e i risultati restituiti vengono inseriti nel riquadro *Risultati*.
  
    ![Schermata del pannello Stored procedure di Esplora script per l'esecuzione di una stored procedure](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure.png)
* Se l'esecuzione genera un errore, viene visualizzato nel riquadro *Risultati* .
  
    ![Schermata della vista delle proprietà dello script di Esplora script Esecuzione di una stored procedure con errori](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-error.png)

## <a name="work-with-scripts-outside-the-portal"></a>Usare gli script all'esterno del portale
Esplora script del portale di Azure è solo uno dei modi per utilizzare stored procedure, trigger e funzioni definite dall'utente in DocumentDB. Gli script possono essere usati anche con l'API REST e gli [SDK client](documentdb-sdk-dotnet.md). La documentazione dell'API REST include esempi dell'uso di [stored procedure con REST](https://msdn.microsoft.com/library/azure/mt489092.aspx), [funzioni definite dall'utente con REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) e [trigger con REST](https://msdn.microsoft.com/library/azure/mt489116.aspx). Sono disponibili anche esempi che illustrano come usare [script con C#](documentdb-dotnet-samples.md#server-side-programming-examples) e [script con Node.js](documentdb-nodejs-samples.md#server-side-programming-examples).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla programmazione lato server di DocumentDB, vedere l'articolo relativo a [Stored procedure, trigger del database e funzioni definite dall'utente](documentdb-programming.md) .



