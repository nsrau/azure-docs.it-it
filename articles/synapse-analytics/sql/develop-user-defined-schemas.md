---
title: Schemi definiti dall'utente all'interno di sinapsi SQL
description: Nelle sezioni seguenti sono disponibili vari suggerimenti per l'uso di schemi definiti dall'utente T-SQL per sviluppare soluzioni con la funzionalità sinapsi SQL di Azure sinapsi Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: ac4753da1405fe6b8cd209bb4899192e9f317aa1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428706"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Schemi definiti dall'utente all'interno di sinapsi SQL

Nelle sezioni seguenti sono disponibili vari suggerimenti per l'uso di schemi definiti dall'utente T-SQL per sviluppare soluzioni all'interno di sinapsi SQL.

## <a name="schemas-for-application-boundaries"></a>Schemi per i limiti dell'applicazione

L'architettura di analisi tradizionale usa spesso database distinti per creare i limiti dell'applicazione in base al carico di lavoro, al dominio o alla sicurezza. Ad esempio, un'infrastruttura di SQL Server Analytics tradizionale può includere un database di gestione temporanea, un database di analisi e database di data mart. In questa topologia ogni database funziona come un carico di lavoro e un limite di sicurezza nell'architettura.

Al contrario, sinapsi SQL esegue l'intero carico di lavoro di analisi all'interno di un database. I join tra database non sono consentiti. Per sinapsi SQL si prevede che tutte le tabelle utilizzate dal warehouse vengano archiviate all'interno di un unico database.

> [!NOTE]
> I pool SQL non supportano query tra database di qualsiasi tipo. Di conseguenza, le implementazioni di Analytics che sfruttano questo modello dovranno essere modificate. SQL su richiesta (anteprima) supporta le query tra database.

## <a name="user-defined-schema-recommendations"></a>Raccomandazioni dello schema definite dall'utente

Sono inclusi i suggerimenti per il consolidamento dei carichi di lavoro, la sicurezza, il dominio e i limiti funzionali usando schemi definiti dall'utente:

- Usare un database per eseguire l'intero carico di lavoro di analisi.
- Consolidare l'ambiente di analisi esistente per l'utilizzo di un database.
- Sfruttare gli **schemi definiti dall'utente** per fornire il limite implementato in precedenza tramite database.

Se gli schemi definiti dall'utente non sono stati usati in precedenza, si avrà uno Slate pulito. Usare il nome del database precedente come base per gli schemi definiti dall'utente nel database SQL sinapsi.

Se gli schemi sono già stati usati, sono disponibili alcune opzioni:

- Rimuovere i nomi degli schemi legacy e ricominciare dall'inizio.
- Mantieni i nomi degli schemi legacy anteponendo il nome precedente allo schema legacy al nome della tabella
- Mantenere i nomi degli schemi legacy implementando viste sulla tabella in uno schema aggiuntivo, che ricrea la struttura dello schema precedente.

> [!NOTE]
> Al primo controllo, l'opzione 3 potrebbe sembrare la scelta più interessante. Le visualizzazioni sono di sola lettura in sinapsi SQL. Qualsiasi modifica di dati o tabelle dovrà essere eseguita sulla tabella di base. L'opzione 3 introduce anche un livello di viste nel sistema. Se si usano già le viste nell'architettura, potrebbe essere necessario fornire questo aspetto.
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

Per i nomi degli schemi legacy, è necessario anteporli al nome della tabella. Usare schemi per il limite del carico di lavoro.

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

Mantenere i nomi di schemi legacy utilizzando le visualizzazioni.

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
> Qualsiasi modifica nella strategia dello schema richiede una revisione del modello di sicurezza per il database. In molti casi, si potrebbe essere in grado di semplificare il modello di sicurezza assegnando autorizzazioni a livello di schema.

Se sono necessarie autorizzazioni più granulari, è possibile utilizzare i ruoli del database. Per ulteriori informazioni sui ruoli del database, vedere l'articolo [gestire ruoli del database e utenti](../../analysis-services/analysis-services-database-users.md) .

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere la [Panoramica sullo sviluppo di sinapsi SQL](develop-overview.md).
