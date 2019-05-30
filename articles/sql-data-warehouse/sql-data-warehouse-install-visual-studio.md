---
title: Installare Visual Studio e SSDT per SQL Data Warehouse | Microsoft Docs
description: Installare Visual Studio e SQL Server Data Tools (SSDT) per Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/05/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: b2e34f1f72b1b0aa76d4a3031102d052118dae5f
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304121"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Installare Visual Studio e SSDT per SQL Data Warehouse
Usare Visual Studio 2019 per sviluppare applicazioni per SQL Data Warehouse. Visual Studio 2019 SSDT non è attualmente supportato per SQL Data Warehouse. 

L'uso di Visual Studio con SSDT consente di usare Esplora oggetti di SQL Server per esplorare le tabelle, viste, stored procedure e molti altri oggetti visivamente in SQL Data Warehouse. Consente inoltre di eseguire query.

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

Si deve avere la versione più recente installata se non viene trovato un aggiornamento. Per verificare che SSDT sia installato, fare clic su **Guida** / **Informazioni su Microsoft Visual Studio** e cercare SQL Server Data Tools nell'elenco. Se l'opzione di installazione non è disponibile da Visual Studio, è possibile visitare il [Download di SSDT] [ SSDT Download] pagina per scaricare e installare SSDT manualmente.

## <a name="next-steps"></a>Passaggi successivi
Ora che avete la versione più recente di SSDT, si è pronti per [connettersi] [ connect] a SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Scaricare Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
