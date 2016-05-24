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
   ms.date="05/16/2016"
   ms.author="sonyama;barbkess"/>

# Installare Visual Studio 2015 e SSDT per SQL Data Warehouse

Per sviluppare applicazioni per SQL Data Warehouse, è consigliabile usare Visual Studio 2015 con la versione più recente di SQL Server Data Tools (SSDT). È supportato anche Visual Studio 2013 con SSDT.

Per eseguire query dall'ambiente di sviluppo integrato (Integrated Development Environment, IDE) di Visual Studio, è necessario anche l'**Aggiornamento di Microsoft SQL Server per strumenti di database**. Dopo aver installato questa estensione sarà possibile visualizzare gli oggetti di database nell'albero di Esplora oggetti ed eseguire query nell'istanza di SQL Data Warehouse.

> [AZURE.NOTE] SQL Data Warehouse non supporta ancora i progetti di database di Visual Studio. Questa funzionalità verrà aggiunta in una versione futura.

## Passaggio 1: Installare Visual Studio 2015

Seguire i collegamenti seguenti per scaricare e installare Visual Studio 2015. Se Visual Studio 2013 o 2015 è già installato, è possibile procedere al passaggio 2 per installare SSDT.

1. [Scaricare Visual Studio 2015][] da Visual Studio Team Services.
2. Seguire quanto indicato nella guida all'[installazione di Visual Studio][] in MSDN e scegliere le configurazioni predefinite.

## Passaggio 2: Installare SSDT

Per installare SSDT per Visual Studio è sufficiente selezionare un aggiornamento di SSDT dall'interno di Visual Studio seguendo questa procedura.

1. In Visual Studio fare clic su **Strumenti** / **Estensioni e aggiornamenti** / **Aggiornamenti**.
2. Selezionare **Aggiornamenti prodotti** e quindi cercare **Aggiornamento di Microsoft SQL Server per strumenti di database**.

Se non viene trovato un aggiornamento, la versione più recente è già installata. Per verificare che SSDT sia installato, fare clic su **Guida** / **Informazioni su Microsoft Visual Studio** e cercare SQL Server Data Tools nell'elenco.

## Passaggi successivi

Dopo aver installato la versione più recente di SSDT, fare clic su [Connetti][] per connettersi al database.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[Connetti]: ./sql-data-warehouse-get-started-connect.md

<!--Other-->
[Scaricare Visual Studio 2015]: https://www.visualstudio.com/downloads/
[installazione di Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx

<!---HONumber=AcomDC_0518_2016-->