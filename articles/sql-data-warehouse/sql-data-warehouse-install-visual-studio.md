<properties
   pageTitle="Installare Visual Studio e SSDT per SQL Data Warehouse | Microsoft Azure"
   description="Installare Visual Studio e SQL Server Data Tools (SSDT) per Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess"/>  

# Installare Visual Studio 2015 e SSDT per SQL Data Warehouse

Per sviluppare applicazioni per SQL Data Warehouse, è consigliabile usare Visual Studio 2015 con la versione più recente di SQL Server Data Tools (SSDT). È supportato anche Visual Studio 2013 Update 5 con SSDT per la compatibilità con le versioni precedenti.

L'uso di Visual Studio con SSDT consentirà di usare Esplora oggetti di SQL Server per esplorare visivamente tabelle, viste, stored procedure e molti altri oggetti in SQL Data Warehouse, nonché eseguire query.

> [AZURE.NOTE] SQL Data Warehouse non supporta ancora i progetti di database di Visual Studio. Questa funzionalità verrà aggiunta in una versione futura.

## Passaggio 1: Installare Visual Studio 2015

Seguire i collegamenti seguenti per scaricare e installare Visual Studio 2015. Se Visual Studio 2013 o 2015 è già installato, è possibile procedere al passaggio 2 per installare SSDT.

1. [Download di Visual Studio 2015][].
2. Seguire quanto indicato nella guida all'[installazione di Visual Studio][] in MSDN e scegliere le configurazioni predefinite.

## Passaggio 2: Installare SSDT

Per installare SSDT per Visual Studio è sufficiente selezionare un aggiornamento di SSDT dall'interno di Visual Studio seguendo questa procedura.

1. In Visual Studio fare clic su **Strumenti** / **Estensioni e aggiornamenti** / **Aggiornamenti**.
2. Selezionare **Aggiornamenti del prodotto** e quindi cercare **Aggiornamento di Microsoft SQL Server per strumenti di database**.

Se non viene trovato un aggiornamento, significa che la versione più recente è già installata. Per verificare che SSDT sia installato, fare clic su **Guida** / **Informazioni su Microsoft Visual Studio** e cercare SQL Server Data Tools nell'elenco. La versione più recente di SSDT è 14.0.60525.0. Se l'opzione di installazione non è disponibile da Visual Studio, in alternativa è possibile visitare la pagina [Scaricare la versione più recente di SQL Server Data Tools][] per scaricare e installare SSDT manualmente.

## Passaggi successivi

Dopo aver installato la versione più recente di SSDT, fare clic su [Connetti][] per connettersi a SQL Data Warehouse.

<!--Anchors-->  

<!--Image references-->

<!--Articles-->
[Connetti]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Download di Visual Studio 2015]: https://www.visualstudio.com/downloads/
[installazione di Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[Scaricare la versione più recente di SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!---HONumber=AcomDC_0817_2016-->