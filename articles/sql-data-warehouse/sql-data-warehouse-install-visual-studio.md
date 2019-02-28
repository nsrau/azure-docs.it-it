---
title: Installare Visual Studio e SSDT per SQL Data Warehouse | Microsoft Docs
description: Installare Visual Studio e SQL Server Data Tools (SSDT) per Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: e27a3d4f2fe4b8421b637e485d77d6376d15bf9a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55461492"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Installare Visual Studio e SSDT per SQL Data Warehouse
Per sviluppare applicazioni per SQL Data Warehouse, è consigliabile usare la versione più recente di Visual Studio con la versione più recente di SQL Server Data Tools (SSDT).  È supportato anche Visual Studio 2013 Update 5 con SSDT per la compatibilità con le versioni precedenti.  

L'uso di Visual Studio con SSDT consentirà di usare Esplora oggetti di SQL Server per esplorare visivamente tabelle, viste, stored procedure e molti altri oggetti in SQL Data Warehouse, nonché eseguire query.

> [!NOTE]
> SQL Data Warehouse non supporta ancora i progetti di database di Visual Studio. Per ricevere aggiornamenti periodici su questa funzionalità, votare in [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>Passaggio 1: Installazione di Visual Studio
Seguire questi collegamenti per scaricare e installare Visual Studio. Se Visual Studio 2013 o versione successiva è già installato, è possibile procedere al passaggio 2 per installare SSDT.

1. [Scaricare Visual Studio][].
2. Seguire le istruzioni di [Installazione di Visual Studio][Installing Visual Studio] in MSDN e scegliere le configurazioni predefinite.

## <a name="step-2-install-ssdt"></a>Passaggio 2: Installazione di SSDT
Per installare SSDT per Visual Studio, per prima cosa selezionare un aggiornamento di SSDT dall'interno di Visual Studio seguendo questa procedura.

1. In Visual Studio fare clic su **Strumenti** / **Estensioni e aggiornamenti** / **Aggiornamenti**
2. Selezionare **Aggiornamenti del prodotto**, quindi cercare **Aggiornamento di Microsoft SQL Server per strumenti di database**

Se non viene trovato un aggiornamento, significa che la versione più recente è già installata.  Per verificare che SSDT sia installato, fare clic su **Guida** / **Informazioni su Microsoft Visual Studio** e cercare SQL Server Data Tools nell'elenco. La versione più recente di SSDT è 14.0.60525.0. Se l'opzione di installazione non è disponibile da Visual Studio, in alternativa è possibile visitare la pagina [Scaricare SQL Server Data Tools (SSDT)][SSDT Download] per scaricare e installare SSDT manualmente.

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
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
