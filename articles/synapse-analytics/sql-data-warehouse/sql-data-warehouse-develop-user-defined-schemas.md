---
title: Utilizzo di schemi definiti dall'utenteUsing user-defined schemas
description: Suggerimenti per l'utilizzo di schemi T-SQL definiti dall'utente per sviluppare soluzioni nel pool Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7144fa75d156ca7aed9d8215592f89c167cfb221
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633464"
---
# <a name="user-defined-schemas-in-synapse-sql-pool"></a>Schemi definiti dall'utente nel pool SQL SynapseUser-defined schemas in Synapse SQL pool
Questo articolo è incentrato sulla fornitura di diversi suggerimenti per l'utilizzo di schemi T-SQL definiti dall'utente per sviluppare soluzioni nel pool SQL Synapse.This article focuses on providing several tips for using T-SQL user-defined schemas to develop solutions in Synapse SQL pool.

## <a name="schemas-for-application-boundaries"></a>Schemi per i limiti dell'applicazione

I data warehouse tradizionali spesso usano database separati per creare i limiti dell'applicazione in base a carico di lavoro, dominio o di sicurezza. 

Ad esempio, un data warehouse tradizionale di SQL Server sql Server potrebbe includere un database dell'area di gestione temporanea, un database del data warehouse e alcuni database di data mart. In questa topologia, ogni database opera come un carico di lavoro e limite di sicurezza nell'architettura.

Al contrario, il pool SQL esegue l'intero carico di lavoro del data warehouse all'interno di un database. I join tra database non sono consentiti. Il pool SQL prevede che tutte le tabelle utilizzate dal warehouse vengano archiviate all'interno di un database.

> [!NOTE]
> Il pool SQL non supporta query tra database di alcun tipo. Di conseguenza, le implementazioni di data warehouse che usano questo modello dovranno essere modificate.
> 
> 

## <a name="recommendations"></a>Consigli
Di seguito sono indicati alcuni suggerimenti per il consolidamento dei carichi di lavoro, della sicurezza, del dominio e dei limiti funzionali tramite schemi definiti dall'utente:What follows are recommendations for consolidating workloads, security, domain, and functional boundaries by using user-defined schemas:

- Usare un database del pool SQL per eseguire l'intero carico di lavoro del data warehouse.
- Consolidare l'ambiente del data warehouse esistente per utilizzare un unico database del pool SQL.
- Sfruttare gli **schemi definiti dall'utente** per fornire il limite implementato in precedenza tramite database.

Se gli schemi definiti dall'utente non sono stati utilizzati in precedenza, si dispone di una lavagna pulita. Utilizzare il nome del database precedente come base per gli schemi definiti dall'utente nel database del pool SQL.

Se gli schemi sono già stati utilizzati, sono disponibili alcune opzioni:If schemas have already been used, then you have a few options:

- Rimuovere i nomi degli schemi legacy e ricominciare da capo.
- Mantenere i nomi di schema legacy anteponendo il nome dello schema legacy al nome della tabella.
- Mantenere i nomi degli schemi legacy implementando viste sulla tabella in uno schema aggiuntivo per ricreare la struttura dello schema precedente.

> [!NOTE]
> A prima vista, l'opzione 3 può sembrare quella più interessante. È tuttavia importante osservare i dettagli. Le visualizzazioni sono di sola lettura nel pool SQL. Qualsiasi modifica di dati o tabelle dovrà essere eseguita sulla tabella di base. L'opzione 3 introduce anche un livello di viste nel sistema. È consigliabile valutare attentamente questo aspetto se si usano già viste nell'architettura.
> 
> 

### <a name="examples"></a>Esempi:
Implementare schemi definiti dall'utente in base ai nomi di database:Implement user-defined schemas based on database names:

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantenere i nomi degli schemi legacy pre-pendingndoli al nome della tabella. Usare gli schemi per il limite del carico di lavoro:Use schemas for the workload boundary:

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantenere i nomi degli schemi legacy utilizzando le visualizzazioni:Keep legacy schema names using views:

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Qualsiasi modifica nella strategia relativa agli schemi richiede una revisione del modello di sicurezza per il database. In molti casi, è possibile semplificare il modello di sicurezza assegnando autorizzazioni a livello di schema. Se sono necessarie autorizzazioni più granulari, è possibile utilizzare i ruoli del database.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).

