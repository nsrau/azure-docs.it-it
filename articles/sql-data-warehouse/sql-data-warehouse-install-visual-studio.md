---
title: Installare Visual Studio e SSDT per SQL Data Warehouse | Microsoft Docs
description: Installare Visual Studio e SQL Server Data Tools (SSDT) per Azure SQL Data Warehouse
services: sql-data-warehouse
documentationcenter: NA
author: antvgski
manager: jhubbard
editor: 
ms.assetid: 0ed9b406-9b42-4fe6-b963-fe0a5b48aac1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: connect
ms.date: 03/30/2017
ms.author: anvang;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: f7023b78c241a7bc8014276cd0bfa455165b42cc
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017

---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Installare Visual Studio e SSDT per SQL Data Warehouse
Per sviluppare applicazioni per SQL Data Warehouse, è consigliabile usare la versione più recente di Visual Studio con la versione più recente di SQL Server Data Tools (SSDT).  È supportato anche Visual Studio 2013 Update 5 con SSDT per la compatibilità con le versioni precedenti.  

L'uso di Visual Studio con SSDT consentirà di usare Esplora oggetti di SQL Server per esplorare visivamente tabelle, viste, stored procedure e molti altri oggetti in SQL Data Warehouse, nonché eseguire query.

> [!NOTE]
> SQL Data Warehouse non supporta ancora i progetti di database di Visual Studio.  Questa funzionalità verrà aggiunta in una versione futura.
> 
> 

## <a name="step-1-install-visual-studio"></a>Passaggio 1: Installare Visual Studio
Seguire questi collegamenti per scaricare e installare Visual Studio. Se Visual Studio 2013 o versione successiva è già installato, è possibile procedere al passaggio 2 per installare SSDT.

1. [Scaricare Visual Studio][].
2. Seguire le istruzioni di [Installazione di Visual Studio][Installing Visual Studio] in MSDN e scegliere le configurazioni predefinite.

## <a name="step-2-install-ssdt"></a>Passaggio 2: Installare SSDT
Per installare SSDT per Visual Studio è sufficiente selezionare un aggiornamento di SSDT dall'interno di Visual Studio seguendo questa procedura.

1. In Visual Studio fare clic su **Strumenti** / **Estensioni e aggiornamenti** / **Aggiornamenti**
2. Selezionare **Aggiornamenti del prodotto**, quindi cercare **Aggiornamento di Microsoft SQL Server per strumenti di database**

Se non viene trovato un aggiornamento, significa che la versione più recente è già installata.  Per verificare che SSDT sia installato, fare clic su **Guida** / **Informazioni su Microsoft Visual Studio** e cercare SQL Server Data Tools nell'elenco.  La versione più recente di SSDT è 14.0.60525.0.  Se l'opzione di installazione non è disponibile da Visual Studio, in alternativa è possibile visitare la pagina [Scaricare SQL Server Data Tools (SSDT)][SSDT Download] per scaricare e installare SSDT manualmente.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver installato la versione più recente di SSDT, è possibile [connettersi][connect] a SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Scaricare Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx

