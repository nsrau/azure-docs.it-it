---
title: 'Strumento del portale di Azure DocumentDB: Esplora query | Documentazione Microsoft'
description: Informazioni su Esplora query di DocumentDB, un editor di query SQL nel portale di Azure per la scrittura e l&quot;esecuzione di query SQL in una raccolta DocumentDB NoSQL.
keywords: scrittura di query sql, editor di query sql
services: documentdb
author: kirillg
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: ac378240-b11f-4522-ae9f-09da3a6f9c16
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: kirillg
translationtype: Human Translation
ms.sourcegitcommit: fba82c5c826da7d1912814b61c5065ca7f726011
ms.openlocfilehash: c4c2cd8ddcb541b2619f7e1b140222899f15f87c
ms.lasthandoff: 02/23/2017


---
# <a name="write-edit-and-run-sql-queries-for-documentdb-using-query-explorer-in-the-azure-portal"></a>Scrivere, modificare ed eseguire query SQL per DocumentDB con Esplora query nel portale di Azure
L'articolo offre una panoramica di Esplora Query di [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/), uno strumento del portale di Azure che consente di scrivere, modificare ed eseguire query SQL in relazione a una [raccolta DocumentDB](documentdb-create-collection.md).

1. Nel [portale di Azure](https://portal.azure.com) nel riquadro di navigazione a sinistra, fare clic sull'![icona di Azure DocumentDB](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **NoSQL (DocumentDB)**. 

    Se **NoSQL (DocumentDB)** non è visibile, fare clic su **Altri servizi** nella parte inferiore, quindi fare clic sull'![icona di Azure DocumentDB](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **NoSQL (DocumentDB)**.
2. Nel menu delle risorse fare clic su **Esplora query**. 
   
    ![Screenshot del portale di Azure con Esplora query evidenziato](./media/documentdb-query-collections-query-explorer/queryexplorercommand.png)
3. Nel pannello **Esplora query** selezionare i **database** e le **raccolte** per eseguire query dagli elenchi a discesa e digitare la query da eseguire. 
   
    Gli elenchi a discesa **Database** e **Raccolte** vengono precompilati a seconda del contesto in cui viene avviato lo strumento Esplora query. 
   
    È disponibile una query predefinita, `SELECT TOP 100 * FROM c` .  È possibile accettare la query predefinita o crearne una usando il linguaggio di query SQL descritto nel [foglio informativo sulle query SQL](documentdb-sql-query-cheat-sheet.md) o nell'articolo [Query e sintassi SQL in DocumentDB](documentdb-sql-query.md).
   
    Fare clic su **Esegui query** per visualizzare i risultati.
   
    ![Screenshot della scrittura di query SQL in Esplora query, un editor di query SQL](./media/documentdb-query-collections-query-explorer/queryexplorerinitial.png)
4. Il pannello **Risultati** mostra l'output della query. 
   
    ![Screenshot dei risultati della scrittura di query SQL in Esplora query](./media/documentdb-query-collections-query-explorer/queryresults1.png)

## <a name="work-with-results"></a>Usare i risultati
Per impostazione predefinita, Esplora query restituisce risultati in set di 100.  Se la query produce più di 100 risultati, usare semplicemente i comandi **Pagina successiva** e **Pagina precedente** per spostarsi all'interno del set di risultati.

![Schermata del supporto di paginazione di Esplora query](./media/documentdb-query-collections-query-explorer/queryresultspagination.png)

Perché le query abbiano esito positivo, il pannello **Informazioni** deve contenere metriche quali l'addebito richiesto, il numero di round trip eseguiti dalla query, il set di risultati attualmente visualizzato e specificare l'eventuale presenza di altri risultati a cui è possibile accedere con il comando **Pagina successiva**, come descritto in precedenza.

![Schermata delle informazioni sulle query di Esplora query](./media/documentdb-query-collections-query-explorer/queryinformation.png)

## <a name="use-multiple-queries"></a>Usare più query
Se si usano più query e si vuole passare rapidamente da una all'altra, è possibile immettere tutte le query nella casella di testo di query del pannello **Esplora query**, evidenziare quella da eseguire e fare clic su **Esegui query** per visualizzare i risultati.

![Screenshot della scrittura di più query SQL in Esplora query, un editor di query SQL, in cui sono evidenziate ed eseguite singole query](./media/documentdb-query-collections-query-explorer/queryexplorerhighlightandrun.png)

## <a name="add-queries-from-a-file-into-the-sql-query-editor"></a>Aggiungere query da un file nell'editor di query SQL
È possibile caricare il contenuto di un file esistente tramite il comando **Carica file** .

![Screenshot che illustra come caricare query SQL da un file in Esplora query usando Carica file](./media/documentdb-query-collections-query-explorer/loadqueryfile.png)

## <a name="troubleshoot"></a>Risoluzione dei problemi
Se una query viene completata con errori, Esplora query visualizza un elenco di errori che può facilitare la risoluzione dei problemi.

![Schermata degli errori di query di Esplora query](./media/documentdb-query-collections-query-explorer/queryerror.png)

## <a name="run-documentdb-sql-queries-outside-the-portal"></a>Eseguire query SQL di DocumentDB all'esterno del portale
Esplora query nel portale di Azure è solo uno dei modi in cui è possibile eseguire query SQL in DocumentDB. È anche possibile eseguire query SQL usando l'[API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o gli [SDK client](documentdb-sdk-dotnet.md). Per altre informazioni sull'uso degli altri metodi, vedere [Esecuzione di query SQL](documentdb-sql-query.md#ExecutingSqlQueries)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla grammatica SQL di DocumentDB supportata in Esplora query, consultare l'articolo [Query SQL e sintassi SQL](documentdb-sql-query.md) o stampare il [foglio informativo di query SQL](documentdb-sql-query-cheat-sheet.md).
La pagina [Query Playground](https://www.documentdb.com/sql/demo) permette di testare le query online usando un set di dati di esempio.


