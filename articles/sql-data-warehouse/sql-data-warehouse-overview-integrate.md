<properties
   pageTitle="Creare soluzioni integrate con SQL Data Warehouse | Microsoft Azure"
   description="Strumenti e partner con soluzioni che interagiscono con SQL Data Warehouse."
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
   ms.date="09/09/2015"
   ms.author="lodipalm"/>

#Utilizzare altri servizi con SQL Data Warehouse
Oltre alle funzionalità di base, SQL Data Warehouse consente agli utenti di utilizzare molti altri servizi in Azure insieme a esso. In particolare, sono state attualmente intraprese azioni per integrare completamente le informazioni seguenti:

+ Power BI
+ Data factory di Azure
+ Azure Machine Learning
+ Azure Stream Analytics

Stiamo lavorando per connettersi con altri servizi attraverso l'ecosistema di Azure.

##Power BI
Integrazione di Power BI consente di sfruttare la potenza di calcolo di Data Warehouse di SQL con la creazione di report dinamici e visualizzazione di Power BI. L’integrazione di Power BI include attualmente:

+ **Connessione diretta**: una connessione più avanzata con caratteristiche logiche SQL Data Warehouse. Questo fornisce un'analisi veloce su vasta scala.
+ **Aprire in Power BI**: il tasto 'Apri in Power BI' passa informazioni istanza a Power BI, consentendo una connessione più trasparente. 

Vedere[Integrare in Power BI](../sql-data-warehouse-integrate-power-bi.md)o[Documentazione di Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx)per ulteriori informazioni.

##Data factory di Azure
Azure Data Factory offre agli utenti una piattaforma gestita per creare pipeline estrazione-caricamento complesse. L’integrazione di SQL Data Warehouse con Azure Data Factory include quanto segue:

+ **Procedure di archiviazione**: orchestrare l'esecuzione delle procedure di archiviazione in SQL Data Warehouse.

Vedere[integrazione con Azure Data Factory](../sql-data-warehouse-integrate-azure-data-factory.md)o[documentazione di Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)per ulteriori informazioni.

##Azure Machine Learning
Azure Machine Learning è un servizio di analisi completamente gestito che consente agli utenti di creare modelli complessi sfruttando un ampio set di strumenti di previsione. SQL Data Warehouse è supportato come origine e destinazione per questi modelli con le funzionalità seguenti:

+ **Lettura dati:**Guida i modelli su larga scala mediante T-SQL su SQL Data Warehouse. 
+ **Scrittura dati:**registra le modifiche da qualsiasi modello a SQL Data Warehouse.

Vedere[Integrazione con Azure Machine Learning](../sql-data-warehouse-integrate-azure-machine-learning.md)o[Documentazione di Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)per ulteriori informazioni.

##Azure Stream Analytics
L’Analisi dei flussi di Azure è un'infrastruttura complessa, completamente gestita per l'elaborazione e l'utilizzo di dati degli eventi generati da Hub eventi di Azure. L’integrazione con SQL Data Warehouse consente la trasmissione di dati in modo efficace da elaborare e archiviare insieme ai dati relazionali permettendo un’analisi più approfondita, più avanzata.

+ **Output del processo:**inviare l'output da processi di analisi di flusso direttamente a SQL Data Warehouse.

Vedere [Integrazione con l’analisi dei flussi di Azure](../sql-data-warehouse-integrate-azure-stream-analytics.md) o [Documentazione dell’analisi dei flussi di Azure](https://azure.microsoft.com/documentation/services/stream-analytics/) per ulteriori informazioni.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-integrate-solution-partners.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Sept15_HO2-->