---
title: Installare Visual Studio 2019
description: Installare Visual Studio e gli strumenti di sviluppo di SQL Server (SSDT) per Synapse SQLInstall Visual Studio and SQL Server Development Tools (SSDT) for Synapse SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b8e37a15f473e5777fe5412b785a0916bd78f095
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655905"
---
# <a name="getting-started-with-visual-studio-2019"></a>Introduzione a Visual Studio 2019

Visual Studio 2019 SQL Server Data Tools (SSDT) è un singolo strumento che consente di eseguire le operazioni seguenti:Visual Studio **2019** SQL Server Data Tools (SSDT) is a single tool allowing you to do the following:

- Connettere, eseguire query e sviluppare applicazioni
- Sfruttare un Esplora oggetti per esplorare visivamente tutti gli oggetti nel modello di dati, tra cui tabelle, viste, stored procedure e così via.
- Generare script DDL (Data Definition Language) T-SQL per gli oggetti
- Sviluppa il tuo data warehouse usando un approccio basato sullo stato con progetti di database SSDTDevelop your data warehouse using a state-based approach with SSDT Database Projects
- Integrare il progetto di database con sistemi di controllo del codice sorgente, ad esempio Git con Azure Repos
- Configurare pipeline di integrazione e distribuzione continue con server di automazione, ad esempio DevOps di AzureSet up continuous integration and deployment pipelines with automation servers such as Azure DevOps

## <a name="install-visual-studio-2019"></a>Installare Visual Studio 2019

Vedere [Scaricare Visual Studio 2019][] per scaricare e installare Visual Studio **16.3 e versioni successive.** Durante l'installazione, selezionare il carico di lavoro di archiviazione ed elaborazione dei dati. L'installazione autonoma di SSDT non è più necessaria in Visual Studio 2019.Standalone SSDT installation is no longer required in Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Funzionalità non supportate in SSDTUnsupported features in SSDT

In alcuni casi le versioni delle funzionalità per Synapse SQL potrebbero non includere il supporto per SSDT. Le seguenti funzionalità non sono attualmente supportate:

- [Viste materializzate](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Indici Columnstore cluster ordinati](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#examples--and-)
- [Istruzione COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Gestione del carico di lavoro](sql-data-warehouse-workload-management.md) - gruppi e classificatori del carico di lavoroWorkload management - workload groups and classifiers
- [Sicurezza a livello di riga](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
  - Invia un ticket di supporto o vota [qui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) per ottenere la funzione supportata.
- [Maschera dati dinamica](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
  - Invia un ticket di supporto o vota [qui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) per ottenere la funzione supportata.
- [Le tabelle con vincoli](sql-data-warehouse-table-constraints.md#table-constraints) non sono supportate. Per questi oggetti tabella, impostare l'azione di compilazione su "Nessuno".

## <a name="next-steps"></a>Passaggi successivi

Ora che si dispone della versione più recente di SSDT, si è pronti per [connettersi](sql-data-warehouse-query-visual-studio.md) al pool SQL.

<!--Other-->

[Scarica Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
