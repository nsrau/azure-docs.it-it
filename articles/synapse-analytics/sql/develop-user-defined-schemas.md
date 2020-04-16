---
title: Schemi definiti dall'utente all'interno di Synapse SQLUser-defined schemas within Synapse SQL
description: Nelle sezioni seguenti sono disponibili vari suggerimenti per l'uso di schemi T-SQL definiti dall'utente per sviluppare soluzioni con la funzionalità SQL Synapse di Azure Synapse Analytics.In the sections below, you'll find various tips for using T-SQL user-defined schemas to develop solutions with the Synapse SQL capability of Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: ac4753da1405fe6b8cd209bb4899192e9f317aa1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428706"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Schemi definiti dall'utente all'interno di Synapse SQLUser-defined schemas within Synapse SQL

Nelle sezioni seguenti sono disponibili vari suggerimenti per l'utilizzo di schemi T-SQL definiti dall'utente per sviluppare soluzioni all'interno di Synapse SQL.

## <a name="schemas-for-application-boundaries"></a>Schemi per i limiti dell'applicazione

L'architettura di analisi tradizionale usa spesso database separati per creare limiti dell'applicazione in base al carico di lavoro, al dominio o alla sicurezza. Ad esempio, un'infrastruttura di analisi tradizionale di SQL ServerSQL Server potrebbe includere un database dell'area di gestione temporanea, un database di analisi e database di data mart. In questa topologia, ogni database opera come un carico di lavoro e limite di sicurezza nell'architettura.

Al contrario, Synapse SQL esegue l'intero carico di lavoro di analisi all'interno di un database. I join tra database non sono consentiti. Synapse SQL prevede che tutte le tabelle utilizzate dal warehouse vengano archiviate all'interno di un database.

> [!NOTE]
> I pool SQL non supportano query tra database di alcun tipo. Di conseguenza, le implementazioni di analisi che sfruttano questo modello dovranno essere riviste. SQL on-demand (anteprima) supporta le query tra database.

## <a name="user-defined-schema-recommendations"></a>Consigli per lo schema definito dall'utenteUser-defined schema recommendations

Sono inclusi suggerimenti per il consolidamento dei carichi di lavoro, della sicurezza, del dominio e dei limiti funzionali tramite schemi definiti dall'utente:Included recommendations for consolidating workloads, security, domain, and functional boundaries by using user-defined schemas:

- Usare un database per eseguire l'intero carico di lavoro di analisi.
- Consolidare l'ambiente di analisi esistente per utilizzare un database.
- Sfruttare gli **schemi definiti dall'utente** per fornire il limite implementato in precedenza tramite database.

Se gli schemi definiti dall'utente non sono stati utilizzati in precedenza, si dispone di una lavagna pulita. Utilizzare il nome del database precedente come base per gli schemi definiti dall'utente nel database SQL Synapse.

Se gli schemi sono già stati utilizzati, sono disponibili alcune opzioni:If schemas have already been used, then you have a few options:

- Rimuovere i nomi degli schemi legacy e ricominciare dall'inizio.
- Mantenere i nomi di schema legacy anteponendo il nome dello schema legacy al nome della tabella
- Mantenere i nomi degli schemi legacy implementando le viste sulla tabella in uno schema aggiuntivo, che ricrea la struttura dello schema precedente.

> [!NOTE]
> Alla prima ispezione, l'opzione 3 può sembrare la scelta più attraente. Le visualizzazioni sono di sola lettura in Synapse SQL. Qualsiasi modifica di dati o tabelle dovrà essere eseguita sulla tabella di base. L'opzione 3 introduce anche un livello di viste nel sistema. È possibile fornire un'ulteriore riflessione se si utilizzano già le visualizzazioni nell'architettura.
> 
> 

### <a name="examples"></a>Esempi

Implementare schemi definiti dall'utente in base ai nomi di database.

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantenere i nomi degli schemi legacy pre-pending loro il nome della tabella. Usare schemi per il limite del carico di lavoro.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantenere i nomi degli schemi legacy utilizzando le visualizzazioni.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
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
> Qualsiasi modifica nella strategia dello schema richiede una revisione del modello di sicurezza per il database. In molti casi, è possibile semplificare il modello di sicurezza assegnando autorizzazioni a livello di schema.

Se sono necessarie autorizzazioni più granulari, è possibile utilizzare i ruoli del database. Per altre informazioni sui ruoli del database, vedere l'articolo Gestire ruoli e utenti del [database.](../../analysis-services/analysis-services-database-users.md)

## <a name="next-steps"></a>Passaggi successivi

Per altre suggerimenti sullo sviluppo, vedere [Panoramica dello sviluppo sql di Synapse](develop-overview.md).For more development tips, see Synapse SQL development overview .
