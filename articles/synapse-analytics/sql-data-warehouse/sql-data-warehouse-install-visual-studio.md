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
ms.openlocfilehash: aa2f903611f52f8f7a8ede0040b592a7dddd0e89
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584459"
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

- [Viste materializzate](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (in corso)
- [Indici Columnstore cluster ordinati](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (in corso)
- [Istruzione COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (in corso)
- [Gestione del carico di lavoro](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) - gruppi e classificatori del carico di lavoro (in corso)Workload management - workload groups and classifiers (in progress)
- [Sicurezza a livello di riga](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - Invia un ticket di supporto o vota [qui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) per ottenere la funzione supportata.
- [Maschera dati dinamica](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - Invia un ticket di supporto o vota [qui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) per ottenere la funzione supportata. 
- [PREVISIONE (funzione PREDICT)](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) 
- [Le tabelle con vincoli](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints) non sono supportate. Per questi oggetti tabella, impostare l'azione di compilazione su "Nessuno".

## <a name="next-steps"></a>Passaggi successivi

Ora che si dispone della versione più recente di SSDT, si è pronti per [connettersi](sql-data-warehouse-query-visual-studio.md) al pool SQL.




<!--Other-->

[Scarica Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
