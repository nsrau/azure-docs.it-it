---
title: Installare Visual Studio 2019
description: Installare Visual Studio e SQL Server Development Tools (SSDT) per sinapsi SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/11/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 9f36fb952b21b058fb50dc567f714e8bdb665d6c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83200320"
---
# <a name="getting-started-with-visual-studio-2019"></a>Introduzione a Visual Studio 2019

Visual Studio **2019** SQL Server Data Tools (SSDT) è un singolo strumento che consente di eseguire le operazioni seguenti:

- Connessione, query e sviluppo di applicazioni
- Utilizzare Esplora oggetti per esplorare visivamente tutti gli oggetti nel modello di dati, tra cui tabelle, viste, stored procedure e così via.
- Genera script T-SQL Data Definition Language (DDL) per gli oggetti
- Sviluppare il data warehouse usando un approccio basato sullo stato con i progetti di database SSDT
- Integrare il progetto di database con sistemi di controllo del codice sorgente come Git con Azure Repos
- Configurare pipeline di integrazione e distribuzione continue con server di automazione come Azure DevOps

## <a name="install-visual-studio-2019"></a>Installare Visual Studio 2019

Vedere [scaricare Visual studio 2019](https://visualstudio.microsoft.com/downloads/) per scaricare e installare visual Studio **16,3 e versioni successive**. Durante l'installazione, selezionare il carico di lavoro elaborazione ed archiviazione dati. L'installazione autonoma di SSDT non è più necessaria in Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Funzionalità non supportate in SSDT

In alcuni casi le versioni delle funzionalità per sinapsi SQL possono non includere il supporto per SSDT. Le funzionalità seguenti non sono attualmente supportate:


- [Gestione del carico di lavoro](sql-data-warehouse-workload-management.md) -gruppi e classificatori del carico di lavoro
- [Sicurezza a livello di riga](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
  - Inviare un [ticket di supporto o votare](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) per ottenere la funzionalità supportata.
- [Maschera dati dinamica](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
   - Inviare un [ticket di supporto o votare](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) per ottenere la funzionalità supportata.

## <a name="next-steps"></a>Passaggi successivi

Ora che è disponibile la versione più recente di SSDT, è possibile [connettersi](sql-data-warehouse-query-visual-studio.md) al pool SQL.
