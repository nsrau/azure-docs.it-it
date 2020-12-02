---
title: Utilizzo di schemi definiti dall'utente
description: Suggerimenti per l'uso di schemi T-SQL definiti dall'utente per sviluppare soluzioni per pool SQL dedicati in Azure sinapsi Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3204c77dd076d9aac6eb5a60b489280caefcbf4b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460442"
---
# <a name="user-defined-schemas-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Schemi definiti dall'utente per pool SQL dedicati in Azure sinapsi Analytics
Questo articolo è incentrato sulla fornitura di diversi suggerimenti per l'uso di schemi definiti dall'utente T-SQL per lo sviluppo di soluzioni in un pool SQL dedicato.

## <a name="schemas-for-application-boundaries"></a>Schemi per i limiti dell'applicazione

I data warehouse tradizionali spesso usano database separati per creare i limiti dell'applicazione in base a carico di lavoro, dominio o di sicurezza. 

Ad esempio, un SQL Server tradizionale data warehouse può includere un database di gestione temporanea, un database data warehouse e alcuni database data mart. In questa topologia ogni database funziona come un carico di lavoro e un limite di sicurezza nell'architettura.

Al contrario, un pool SQL dedicato esegue l'intero carico di lavoro data warehouse all'interno di un database. I join tra database non sono consentiti. Il pool SQL dedicato prevede l'archiviazione di tutte le tabelle utilizzate dal warehouse all'interno di un unico database.

> [!NOTE]
> Il pool SQL non supporta le query tra database di qualsiasi tipo. Di conseguenza, le implementazioni di data warehouse che usano questo modello dovranno essere modificate.
> 
> 

## <a name="recommendations"></a>Consigli
Di seguito sono riportati i consigli per consolidare i carichi di lavoro, la sicurezza, il dominio e i limiti funzionali usando schemi definiti dall'utente:

- Usare un database in un pool SQL dedicato per eseguire l'intero carico di lavoro data warehouse.
- Consolidare l'ambiente di data warehouse esistente per l'utilizzo di un database del pool SQL dedicato.
- Sfruttare gli **schemi definiti dall'utente** per fornire il limite implementato in precedenza tramite database.

Se gli schemi definiti dall'utente non sono stati usati in precedenza, si avrà uno Slate pulito. Utilizzare il nome del database precedente come base per gli schemi definiti dall'utente nel database del pool SQL dedicato.

Se gli schemi sono già stati usati, sono disponibili alcune opzioni:

- Rimuovere i nomi degli schemi legacy e iniziare da zero.
- Mantenere i nomi degli schemi legacy anteponendo il nome di schema precedente al nome della tabella.
- Mantenere i nomi degli schemi legacy implementando viste sulla tabella in uno schema aggiuntivo per ricreare la struttura dello schema precedente.

> [!NOTE]
> A prima vista, l'opzione 3 può sembrare quella più interessante. È tuttavia importante osservare i dettagli. Le visualizzazioni sono di sola lettura nel pool SQL dedicato. Qualsiasi modifica di dati o tabelle dovrà essere eseguita sulla tabella di base. L'opzione 3 introduce anche un livello di viste nel sistema. È consigliabile valutare attentamente questo aspetto se si usano già viste nell'architettura.
> 
> 

### <a name="examples"></a>Esempi:
Implementare schemi definiti dall'utente in base ai nomi di database:

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

Mantenendo i nomi degli schemi legacy in precedenza in base al nome della tabella. Utilizzare gli schemi per il limite del carico di lavoro:

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

Mantieni i nomi degli schemi legacy usando le visualizzazioni:

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
> Qualsiasi modifica nella strategia relativa agli schemi richiede una revisione del modello di sicurezza per il database. In molti casi, si potrebbe essere in grado di semplificare il modello di sicurezza assegnando autorizzazioni a livello di schema. Se sono necessarie autorizzazioni più granulari, è possibile usare i ruoli del database.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).

