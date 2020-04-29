---
title: Assegnare variabili con sinapsi SQL
description: In questo articolo sono disponibili suggerimenti per l'assegnazione di variabili T-SQL con sinapsi SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: b2a596b71ee7e5f58e01d5bc10b330f6f54a69d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428667"
---
# <a name="assigning-variables-with-synapse-sql"></a>Assegnazione di variabili con sinapsi SQL

In questo articolo sono disponibili suggerimenti per l'assegnazione di variabili T-SQL con sinapsi SQL.

## <a name="setting-variables-with-declare"></a>Impostazione delle variabili con DECLARE

Le variabili in sinapsi SQL vengono impostate tramite `DECLARE` l'istruzione o `SET` l'istruzione. L'inizializzazione di variabili con DECLARE è uno dei modi più flessibili per impostare un valore di variabile in sinapsi SQL.

```sql
DECLARE @v  int = 0
;
```

È anche possibile usare DECLARE per impostare più di una variabile contemporaneamente. Non è possibile usare SELECT o UPDATE per eseguire le operazioni seguenti:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Non è possibile inizializzare e usare una variabile nella stessa istruzione DECLARE. Per illustrare, l'esempio seguente non è consentito *@p1* perché è inizializzato e usato nella stessa istruzione Declare. L'esempio seguente restituisce un errore.

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

Non è possibile usare l'aggiornamento per l'assegnazione di variabili.

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere l'articolo relativo alla [Panoramica sullo sviluppo di sinapsi SQL](develop-overview.md) .