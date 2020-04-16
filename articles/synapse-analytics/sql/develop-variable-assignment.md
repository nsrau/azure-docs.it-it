---
title: Assegnare variabili con Synapse SQLAssign variables with Synapse SQL
description: In questo articolo sono disponibili suggerimenti per l'assegnazione di variabili T-SQL con Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: b2a596b71ee7e5f58e01d5bc10b330f6f54a69d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428667"
---
# <a name="assigning-variables-with-synapse-sql"></a>Assegnazione di variabili con Synapse SQLAssigning variables with Synapse SQL

In questo articolo sono disponibili suggerimenti per l'assegnazione di variabili T-SQL con Synapse SQL.

## <a name="setting-variables-with-declare"></a>Impostazione delle variabili con DECLARE

Le variabili in Synapse `DECLARE` SQL vengono `SET` impostate utilizzando l'istruzione o l'istruzione . L'inizializzazione delle variabili con DECLARE è uno dei modi più flessibili per impostare un valore di variabile in Synapse SQL.

```sql
DECLARE @v  int = 0
;
```

È anche possibile usare DECLARE per impostare più di una variabile contemporaneamente. Non è possibile utilizzare SELECT o UPDATE per eseguire le operazioni seguenti:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Non è possibile inizializzare e utilizzare una variabile nella stessa istruzione DECLARE. Per illustrare, l'esempio seguente *@p1* non è consentito poiché è inizializzato e utilizzato nella stessa istruzione DECLARE. L'esempio seguente restituisce un errore.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Impostazione di valori con SET

SET è un metodo comune per l'impostazione di una singola variabile.

Le istruzioni seguenti sono tutte valide per impostare una variabile con SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

È possibile impostare una sola variabile alla volta con SET. Tuttavia, gli operatori composti sono consentiti.

## <a name="limitations"></a>Limitazioni

Non è possibile utilizzare UPDATE per l'assegnazione delle variabili.

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere l'articolo [Panoramica sullo sviluppo di Synapse SQL.](develop-overview.md)