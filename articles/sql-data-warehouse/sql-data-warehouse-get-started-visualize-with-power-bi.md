---
title: Visualizzare i dati di SQL Data Warehouse con Power BI in Microsoft Azure
description: Visualizzare i dati di SQL Data Warehouse con Power BI
services: sql-data-warehouse
documentationcenter: NA
author: mlee3gsd
manager: jhubbard
editor: 
ms.assetid: d7fb89d1-da1d-4788-a111-68d0e3fda799
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: martinle;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 8790bacecac0fa824189b5c212c2d803092ff4ed
ms.contentlocale: it-it
ms.lasthandoff: 01/30/2017


---
# <a name="visualize-data-with-power-bi"></a>Visualizzare i dati con Power BI
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Questa esercitazione illustra come usare Power BI per connettersi a SQL Data Warehouse e creare alcune visualizzazioni di base.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Per eseguire questa esercitazione, è necessario:

* Un'istanza di SQL Data Warehouse in cui sia precaricato il database AdventureWorksDW. Per effettuarne il provisioning, vedere [Creare un Azure SQL Data Warehouse][Create a SQL Data Warehouse] e scegliere di caricare i dati di esempio. Se si ha già un data warehouse, ma non i dati di esempio, è possibile [caricare manualmente i dati di esempio][load sample data manually].

## <a name="1-connect-to-your-database"></a>1. Connettersi al database
Per aprire Power BI e connettersi al database AdventureWorksDW:

1. Accedere al [portale di Azure][Azure portal].
2. Fare clic su **Database SQL** e scegliere il database SQL Data Warehouse AdventureWorks.
   
    ![Trovare il database][1]
3. Fare clic sul pulsante Apri in Power BI.
   
    ![Pulsante Power BI][2]
4. Verrà visualizzata la pagina di connessione di SQL Data Warehouse che visualizza l'indirizzo Web del database. Fare clic su Avanti.
   
    ![Connessione a Power BI][3]
5. Immettere il nome utente e la password del server Azure SQL per connettersi al database SQL Data Warehouse.
   
    ![Accesso a Power BI][4]
6. Dopo aver eseguito l'accesso a Power BI, fare clic sul set di dati AdventureWorksDW nel pannello sinistro. Verrà aperto il database.
   
    ![Apertura di AdventureWorksDW in Power BI][5]

## <a name="2-create-a-report"></a>2. Creare un report
È ora possibile usare Power BI per analizzare i dati di esempio di AdventureWorksDW. Per eseguire l'analisi, AdventureWorksDW dispone di una visualizzazione denominata VenditeAggregate. Questa visualizzazione contiene alcune metriche chiave per l'analisi delle vendite della società.

1. Per creare una mappa degli importi di vendita in base al codice postale, nel riquadro con i campi a destra fare clic su AggregateSales per espanderlo. Fare clic sulle colonne PostalCode e SalesAmount per selezionarle.
   
    ![Selezione di AggregateSales in Power BI][6]
   
    Power BI riconosce automaticamente tali informazioni come dati geografici e le inserisce direttamente in una mappa.
   
    ![Mappa di Power BI][7]
2. In questo passaggio viene creato un grafico a barre che mostra gli importi delle vendite per reddito del cliente. Per creare il grafico, passare alla visualizzazione AggregateSales espansa. Fare clic sul campo SalesAmount. Trascinare il campo Customer Income a sinistra e rilasciarlo sull'asse.
   
    ![Selezione asse in Power BI][8]
   
    Il grafico a barre è stato spostato a sinistra.
   
    ![Barra di Power BI][9]
3. In questo passaggio viene creato un grafico a linee che mostra gli importi delle vendite per data dell'ordine. Per creare il grafico, passare alla visualizzazione AggregateSales espansa. Fare clic su SalesAmount e OrderDate. Nella colonna Visualizzazioni fare clic sull'icona del grafico a linee. Si tratta della prima icona nella seconda riga in Visualizzazioni.
   
    ![Selezione del grafico a linee in Power BI][10]
   
    Ora è disponibile un report che mostra tre diverse visualizzazioni dei dati.
   
    ![Riga di Power BI][11]

Per salvare lo stato in qualsiasi momento, fare clic su **File** e selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi
Dopo essersi esercitati con i dati di esempio, si passerà ora alle operazioni di [sviluppo][develop], [caricamento][load] o [migrazione][migrate]. In alternativa, vedere il [sito Web di Power BI][Power BI website].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Azure portal]: https://portal.azure.com/
[Power BI website]: http://www.powerbi.com/

