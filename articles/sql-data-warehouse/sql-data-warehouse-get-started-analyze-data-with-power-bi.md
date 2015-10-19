<properties
    pageTitle="Usare dati di SQL Data Warehouse con Power BI | Microsoft Azure"
    description="Analizzare dati di SQL Data Warehouse con Power BI"
    services="sql-data-warehouse"
    documentationCenter="NA"
    authors="lodipalm"
    manager="barbkess"
    editor=""/>

<tags
    ms.service="sql-data-warehouse"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="10/07/2015"
    ms.author="lodipalm"/>

# Analizzare dati con Power BI
Questa esercitazione illustra come usare Power BI per connettersi a SQL Data Warehouse e creare alcune visualizzazioni di base.

> [AZURE.NOTE]Per completare questa esercitazione, è necessario un database SQL Data Warehouse precaricato con il database di esempio AdventureWorksDW. In [Introduzione: effettuare il provisioning di SQL Data Warehouse](sql-data-warehouse-get-started-provision.md) viene illustrato come crearne uno.
> 
> Se si dispone già di un database SQL Data Warehouse, ma non di dati di esempio, è possibile [caricare manualmente i dati di esempio][].


## Connettersi al database SQL Data Warehouse

Per aprire Power BI e connettersi al database AdventureWorksDW:

1. Accedere al [portale di anteprima di Azure][].
2. Fare clic su **Database SQL** e scegliere il database SQL Data Warehouse AdventureWorks. 

    ![Trovare il database][1]

3. Fare clic sul pulsante Apri in Power BI.

    ![Pulsante Power BI][2]

4. Viene visualizzata la pagina di connessione a SQL Data Warehouse contenente le informazioni sul database. Immettere la password per connettersi completamente al database SQL Data Warehouse.


## Analizzare dati con Power BI

È ora possibile usare Power BI per analizzare i dati di esempio di AdventureWorksDW. Per eseguire l'analisi, AdventureWorksDW dispone di una visualizzazione denominata VenditeAggregate. Questa visualizzazione contiene alcune metriche chiave per l'analisi delle vendite della società.

1. Per creare una mappa del totale delle vendite in base al codice postale, fare clic sulla visualizzazione VenditeAggregate per espanderla e quindi selezionare le colonne CodicePostale e TotaleVendite. Power BI riconosce automaticamente tali informazioni come dati geografici e le inserisce direttamente in una mappa.

    ![Mappa di Power BI][3]

2. Per creare un grafico a barre delle vendite, è sufficiente fare clic sulla colonna TotaleVendite. Per aggiungere dettagli, trascinare il grafico RedditoClienti sul campo Asse a sinistra di VenditeAggregate per visualizzare le vendite in base al reddito dei clienti.

    ![Barra di Power BI][4]

3. Per creare una sequenza temporale delle vendite, fare clic su TotaleVendite, DataOrdine e Grafico a linee, ovvero la prima icona nella seconda riga sotto la voce Visualizzazioni.

    ![Riga di Power BI][5]

È possibile salvare in qualsiasi momento lo stato di avanzamento facendo clic sul pulsante **SALVA** nell'angolo superiore sinistro e salvando le visualizzazioni come report.

## Passaggi successivi
Dopo essersi esercitati con i dati di esempio, vedere come [sviluppare][], [caricare][] o [eseguire la migrazione][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-find-database.png
[2]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-button.png
[3]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-map.png
[4]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-bar.png
[5]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-line.png

<!--Article references-->
[eseguire la migrazione]: sql-data-warehouse-overview-migrate.md
[sviluppare]: sql-data-warehouse-overview-develop.md
[caricare]: sql-data-warehouse-overview-load.md
[caricare manualmente i dati di esempio]: sql-data-warehouse-get-started-manually-load-samples.md
[portale di anteprima di Azure]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!---HONumber=Oct15_HO2-->