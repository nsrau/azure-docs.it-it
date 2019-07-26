---
title: Installare Visual Studio e SSDT per SQL Data Warehouse | Microsoft Docs
description: Installare Visual Studio e SQL Server Data Tools (SSDT) per Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/05/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: aa904ac62ce41cc89945ddfdef2e426143e42bb4
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479484"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Installare Visual Studio e SSDT per SQL Data Warehouse
Utilizzare Visual Studio 2019 per sviluppare applicazioni per SQL Data Warehouse. Attualmente Visual Studio 2019 SSDT non è supportato per SQL Data Warehouse. 

L'uso di Visual Studio con SSDT consente di usare la Esplora oggetti di SQL Server per esplorare tabelle, viste, stored procedure e molti altri oggetti in modo visivo nel SQL Data Warehouse. Consente inoltre di eseguire query.

> [!NOTE]
> SQL Data Warehouse non supporta ancora i progetti di database di Visual Studio. Per ricevere aggiornamenti periodici su questa funzionalità, votare in [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>Passaggio 1: Installazione di Visual Studio
Seguire questi collegamenti per scaricare e installare Visual Studio. Se Visual Studio 2013 o versione successiva è già installato, è possibile procedere al passaggio 2 per installare SSDT.

1. [Scaricare Visual Studio][].
2. Seguire quanto indicato nella guida all' [installazione di Visual Studio][Installing Visual Studio] in MSDN e scegliere le configurazioni predefinite.

## <a name="step-2-install-ssdt"></a>Passaggio 2: Installazione di SSDT
Per installare SSDT per Visual Studio, per prima cosa selezionare un aggiornamento di SSDT dall'interno di Visual Studio seguendo questa procedura.

1. In Visual Studio fare clic su **Strumenti** / **Estensioni e aggiornamenti** / **Aggiornamenti**
2. Selezionare **Aggiornamenti del prodotto**, quindi cercare **Aggiornamento di Microsoft SQL Server per strumenti di database**

Se non viene trovato un aggiornamento, è necessario che sia installata la versione più recente. Per verificare che SSDT sia installato, fare clic su **Guida** / **Informazioni su Microsoft Visual Studio** e cercare SQL Server Data Tools nell'elenco. Se l'opzione per l'installazione non è disponibile da Visual Studio, è possibile visitare la pagina di [download di SSDT][SSDT Download] per scaricare e installare SSDT manualmente.

## <a name="next-steps"></a>Passaggi successivi
Ora che è disponibile la versione più recente di SSDT, è possibile [connettersi][connect] al SQL data warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Scaricare Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
