---
title: Assegna variabili
description: In questo articolo sono disponibili suggerimenti essenziali per l'assegnazione di variabili T-SQL nel pool SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0cbadc10fb817c70bd259397c840aae68abc2d54
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213330"
---
# <a name="assign-variables-in-synapse-sql-pool"></a>Assegnare variabili nel pool SQL sinapsi

In questo articolo sono disponibili suggerimenti essenziali per l'assegnazione di variabili T-SQL nel pool SQL.

## <a name="set-variables-with-declare"></a>Imposta variabili con dichiara

Le variabili nel pool SQL vengono impostate tramite l' `DECLARE` istruzione o l' `SET` istruzione. L'inizializzazione di variabili con DECLARE è uno dei modi più flessibili per impostare un valore di variabile nel pool SQL.

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

Non è possibile inizializzare e usare una variabile nella stessa istruzione DECLARE. Per illustrare il concetto, l'esempio seguente **non** è consentito, perché @p1 viene inizializzato e usato nella stessa istruzione DECLARE. Di conseguenza, l'esempio seguente genera un errore:

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Imposta valori con SET

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

Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).
