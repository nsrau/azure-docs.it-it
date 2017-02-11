---
title: Installare Visual Studio e SSDT per SQL Data Warehouse | Microsoft Docs
description: Installare Visual Studio e SQL Server Data Tools (SSDT) per Azure SQL Data Warehouse
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 0ed9b406-9b42-4fe6-b963-fe0a5b48aac1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f7bafc793bf8abddd50b7976733b8fdf389a0366


---
# <a name="install-visual-studio-2015-and-ssdt-for-sql-data-warehouse"></a>Installare Visual Studio 2015 e SSDT per SQL Data Warehouse
Per sviluppare applicazioni per SQL Data Warehouse, è consigliabile usare Visual Studio 2015 con la versione più recente di SQL Server Data Tools (SSDT).  È supportato anche Visual Studio 2013 Update 5 con SSDT per la compatibilità con le versioni precedenti.  

L'uso di Visual Studio con SSDT consentirà di usare Esplora oggetti di SQL Server per esplorare visivamente tabelle, viste, stored procedure e molti altri oggetti in SQL Data Warehouse, nonché eseguire query.

> [!NOTE]
> SQL Data Warehouse non supporta ancora i progetti di database di Visual Studio.  Questa funzionalità verrà aggiunta in una versione futura.
> 
> 

## <a name="step-1-install-visual-studio-2015"></a>Passaggio 1: Installare Visual Studio 2015
Seguire i collegamenti seguenti per scaricare e installare Visual Studio 2015. Se Visual Studio 2013 o 2015 è già installato, è possibile procedere al passaggio 2 per installare SSDT.

1. [Download di Visual Studio 2015][Download di Visual Studio 2015].
2. Seguire quanto indicato nella guida all'[installazione di Visual Studio][installazione di Visual Studio] in MSDN e scegliere le configurazioni predefinite.

## <a name="step-2-install-ssdt"></a>Passaggio 2: Installare SSDT
Per installare SSDT per Visual Studio è sufficiente selezionare un aggiornamento di SSDT dall'interno di Visual Studio seguendo questa procedura.

1. In Visual Studio fare clic su **Strumenti** / **Estensioni e aggiornamenti** / **Aggiornamenti**
2. Selezionare **Aggiornamenti del prodotto**, quindi cercare **Aggiornamento di Microsoft SQL Server per strumenti di database**

Se non viene trovato un aggiornamento, significa che la versione più recente è già installata.  Per verificare che SSDT sia installato, fare clic su **Guida** / **Informazioni su Microsoft Visual Studio** e cercare SQL Server Data Tools nell'elenco.  La versione più recente di SSDT è 14.0.60525.0.  Se l'opzione di installazione non è disponibile da Visual Studio, in alternativa è possibile visitare la pagina [Scaricare la versione più recente di SQL Server Data Tools][Scaricare la versione più recente di SQL Server Data Tools] per scaricare e installare SSDT manualmente.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver installato la versione più recente di SSDT, è possibile [Connetti][Connetti] a SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[Connetti]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Download di Visual Studio 2015]: https://www.visualstudio.com/downloads/
[installazione di Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[Scaricare la versione più recente di SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx



<!--HONumber=Nov16_HO3-->


