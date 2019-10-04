---
title: Visualizzare i dati di SQL Data Warehouse con Power BI in Microsoft Azure
description: Visualizzare i dati di SQL Data Warehouse con Power BI
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 4650e171c180653e94d78451d66bba1bcc71b136
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241280"
---
# <a name="visualize-data-with-power-bi"></a>Visualizzare i dati con Power BI
Questa esercitazione illustra come usare Power BI per connettersi a SQL Data Warehouse e creare alcune visualizzazioni di base.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Per eseguire questa esercitazione, è necessario:

* Un'istanza di SQL Data Warehouse in cui sia precaricato il database AdventureWorksDW. Per effettuarne il provisioning di un data warehouse, vedere [Creare un Azure SQL Data Warehouse](create-data-warehouse-portal.md) e scegliere di caricare i dati di esempio. Se si ha già un data warehouse, ma non i dati di esempio, è possibile [caricare WideWorldImportersDW](load-data-wideworldimportersdw.md).

## <a name="1-connect-to-your-database"></a>1. Connettersi al database
Per aprire Power BI e connettersi al database AdventureWorksDW:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Database SQL** e scegliere il database SQL Data Warehouse AdventureWorks.
   
    ![Trovare il database](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. Fare clic sul pulsante Apri in Power BI.
   
    ![Pulsante Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. Verrà visualizzata la pagina di connessione di SQL Data Warehouse che visualizza l'indirizzo Web del database. Fare clic su Avanti.
   
    ![Connessione a Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Immettere il nome utente e la password di Azure SQL Server.
   
    ![Accesso a Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. Per aprire il database, fare clic sul set di dati AdventureWorksDW nel pannello sinistro.
   
    ![Apertura di AdventureWorksDW in Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. Creare un report
È ora possibile usare Power BI per analizzare i dati di esempio di AdventureWorksDW. Per eseguire l'analisi, AdventureWorksDW dispone di una visualizzazione denominata VenditeAggregate. Questa visualizzazione contiene alcune metriche chiave per l'analisi delle vendite della società.

1. Per creare una mappa degli importi di vendita in base al codice postale, nel riquadro con i campi a destra fare clic su AggregateSales per espanderlo. Fare clic sulle colonne PostalCode e SalesAmount per selezionarle.
   
    ![Power BI seleziona AggregateSales](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI riconosce automaticamente i dati geografici e li inserisce direttamente in una mappa.
   
    ![Mappa di Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. In questo passaggio viene creato un grafico a barre che mostra gli importi delle vendite per reddito del cliente. Per creare il grafico a barre, passare alla visualizzazione AggregateSales espansa. Fare clic sul campo SalesAmount. Trascinare il campo Customer Income a sinistra e rilasciarlo sull'asse.
   
    ![Power BI seleziona l'asse](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    Il grafico a barre a sinistra.
   
    ![Barra di Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. In questo passaggio viene creato un grafico a linee che mostra gli importi delle vendite per data dell'ordine. Per creare il grafico a linee, passare alla visualizzazione AggregateSales espansa. Fare clic su SalesAmount e OrderDate. Nella colonna Visualizzazioni fare clic sull'icona del grafico a linee che è la prima icona nella seconda riga in Visualizzazioni.
   
    ![Power BI seleziona il grafico a linee](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    Ora è disponibile un report che mostra tre diverse visualizzazioni dei dati.
   
    ![Riga di Power BI](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

Per salvare lo stato in qualsiasi momento, fare clic su **File** e selezionare **Salva**.

## <a name="using-direct-connect"></a>Uso della connessione diretta
Analogamente al database SQL di Azure, la connessione diretta con SQL Data Warehouse consente la distribuzione dinamica logica insieme alle capacità analitiche di Power BI. Con la connessione diretta le query vengono restituite ad Azure SQL Data Warehouse in tempo reale durante l'esplorazione dei dati.  Questa funzione, unitamente alla scalabilità di SQL Data Warehouse, permette di creare report dinamici in pochi minuti su terabyte di dati. L'introduzione del pulsante Apri in Power BI permette agli utenti di connettere direttamente Power BI al proprio SQL Data Warehouse, senza raccogliere informazioni da altre parti di Azure.

Quando si usa la connessione diretta:

* Specificare il nome completo del server in fase di connessione.
* Assicurarsi che le regole del firewall per il database siano configurate per consentire l'accesso ai servizi di Azure.
* Ogni azione, ad esempio la selezione di una colonna o l'aggiunta di un filtro, esegue direttamente una query nel data warehouse.
* I riquadri vengono aggiornati automaticamente e all'incirca ogni 15 minuti.
* Non sono disponibili domande e risposte per i set di dati di connessione diretta.
* Le modifiche di schema vengono integrate automaticamente.
* Tutte le query di connessione diretta scadranno dopo 2 minuti.

Queste limitazioni e note sono soggette a modifiche con il miglioramento delle esperienze.

## <a name="next-steps"></a>Passaggi successivi
Ora che ti forniamo una certa esercitati con i dati di esempio, vedere come [sviluppare](sql-data-warehouse-overview-develop.md) oppure [caricare](design-elt-data-loading.md). In alternativa, vedere il [sito Web di Power BI](https://www.powerbi.com/).
