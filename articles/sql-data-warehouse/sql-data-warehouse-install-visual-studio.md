<properties
   pageTitle="Installare Visual Studio e/o SSDT per SQL Data Warehouse | Microsoft Azure"
   description="Installare Visual Studio e/o gli strumenti di sviluppo SSDT per Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/21/2015"
   ms.author="twounder;barbkess"/>

# Installare Visual Studio 2015 e/o SSDT per SQL Data Warehouse

Per sviluppare applicazioni per SQL Data Warehouse, è consigliabile usare Visual Studio 2013 o una versione più recente in combinazione con la versione più recente di SQL Server Data Tools (SSDT).

Per eseguire query dall'ambiente di sviluppo integrato (Integrated Development Environment, IDE) di Visual Studio, è sufficiente installare SSDT. Verrà installato l'IDE di VIsual Studio insieme a SSDT per poter usare Esplora oggetti di SQL Server per connettersi al server SQL di Azure. A quel punto, sarà possibile visualizzare ed eseguire query sui database SQL Data Warehouse.


## Passaggio 1: Scaricare e installare Visual Studio

Se si sceglie di installare Visual Studio, con SQL Data Warehouse è possibile usare sia Visual Studio 2013 sia Visual Studio 2015. Se Visual Studio 2013 o 2015 è già installato, procedere al passaggio 2 per installare SSDT.

Per installare Visual Studio 2015:

1. [Scaricare Visual Studio 2015](https://www.visualstudio.com/downloads) da Visual Studio Online. 
2. Per la procedura di installazione, seguire quanto indicato nella guida all'[installazione di Visual Studio](https://msdn.microsoft.com/library/e2h7fzkw.aspx) su MSDN e scegliere le configurazioni predefinite.

## Passaggio 2: Scaricare e installare la versione più recente di SQL Server Data Tools (SSDT) 

Che sia installato Visual Studio o meno, è comunque necessario installare la versione di SQL Server Data Tools (SSDT) più recente che supporta SQL Data Warehouse.

Per installare la versione più recente di SSDT:

1. [Scaricare SQL Server Data Tools Preview](https://msdn.microsoft.com/library/mt204009.aspx) per Visual Studio 2013 o 2015.
2. Eseguire l'installazione seguendo le istruzioni fornite nel sito di download.

## Passaggi successivi

Dopo aver installato la versione più recente di SSDT, fare clic su [Connetti](./sql-data-warehouse-get-started-connect.md) per connettersi al database.

<!--Anchors-->

<!--Image references-->

<!---HONumber=Oct15_HO4-->