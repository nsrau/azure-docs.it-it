---
title: Supporto delle regole di confronto
description: Tipi di regole di confronto supportati in SQL sinapsi di Azure
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 5e46cd744be609adff764edfe5a506b710e9d788
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91288070"
---
# <a name="database-collation-support-for-synapse-sql"></a>Supporto delle regole di confronto del database per sinapsi SQL

Le regole di confronto forniscono le impostazioni locali, la tabella codici, il tipo di ordinamento e la distinzione tra caratteri per i tipi di dati basati su caratteri. Una volta scelte, tutte le colonne e le espressioni che richiedono le informazioni sulle regole di confronto ereditano le regole di confronto selezionate dall'impostazione del database. È possibile eseguire l'override dell'ereditarietà predefinita specificando in modo esplicito una regola di confronto diversa per un tipo di dati basato su caratteri.

È possibile modificare le regole di confronto predefinite del database dal portale di Azure quando si crea un nuovo database del pool SQL. Questa funzionalità rende ancora più semplice la creazione di un nuovo database usando una delle regole di confronto del database supportate da 3800.

È possibile specificare le regole di confronto predefinite del database su richiesta di sinapsi SQL in fase di creazione usando l'istruzione CREATE DATABASE.

## <a name="change-collation"></a>Modificare le regole di confronto
Per modificare le regole di confronto predefinite per il database del pool SQL, aggiornare il campo regole di confronto nell'esperienza di provisioning. Se, ad esempio, si desidera modificare le regole di confronto predefinite in maiuscole/minuscole, è necessario rinominare le regole di confronto da SQL_Latin1_General_CP1_CI_AS a SQL_Latin1_General_CP1_CS_AS. 

Per modificare le regole di confronto predefinite per il database SQL su richiesta, è possibile utilizzare l'istruzione ALTER DATABASE.

## <a name="list-of-unsupported-collation-types"></a>Elenco di tipi di regole di confronto non supportate
*    Japanese_Bushu_Kakusu_140_BIN
*    Japanese_Bushu_Kakusu_140_BIN2
*    Japanese_Bushu_Kakusu_140_CI_AI_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI
*    Japanese_Bushu_Kakusu_140_CI_AI_WS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AI
*    Japanese_Bushu_Kakusu_140_CS_AI_WS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*    Japanese_XJIS_140_BIN
*    Japanese_XJIS_140_BIN2
*    Japanese_XJIS_140_CI_AI_VSS
*    Japanese_XJIS_140_CI_AI_WS_VSS
*    Japanese_XJIS_140_CI_AI_KS_VSS
*    Japanese_XJIS_140_CI_AI_KS_WS_VSS
*    Japanese_XJIS_140_CI_AS_VSS
*    Japanese_XJIS_140_CI_AS_WS_VSS
*    Japanese_XJIS_140_CI_AS_KS_VSS
*    Japanese_XJIS_140_CI_AS_KS_WS_VSS
*    Japanese_XJIS_140_CS_AI_VSS
*    Japanese_XJIS_140_CS_AI_WS_VSS
*    Japanese_XJIS_140_CS_AI_KS_VSS
*    Japanese_XJIS_140_CS_AI_KS_WS_VSS
*    Japanese_XJIS_140_CS_AS_VSS
*    Japanese_XJIS_140_CS_AS_WS_VSS
*    Japanese_XJIS_140_CS_AS_KS_VSS
*    Japanese_XJIS_140_CS_AS_KS_WS_VSS
*    Japanese_XJIS_140_CI_AI
*    Japanese_XJIS_140_CI_AI_WS
*    Japanese_XJIS_140_CI_AI_KS
*    Japanese_XJIS_140_CI_AI_KS_WS
*    Japanese_XJIS_140_CI_AS
*    Japanese_XJIS_140_CI_AS_WS
*    Japanese_XJIS_140_CI_AS_KS
*    Japanese_XJIS_140_CI_AS_KS_WS
*    Japanese_XJIS_140_CS_AI
*    Japanese_XJIS_140_CS_AI_WS
*    Japanese_XJIS_140_CS_AI_KS
*    Japanese_XJIS_140_CS_AI_KS_WS
*    Japanese_XJIS_140_CS_AS
*    Japanese_XJIS_140_CS_AS_WS
*    Japanese_XJIS_140_CS_AS_KS
*    Japanese_XJIS_140_CS_AS_KS_WS

Inoltre, il pool SQL non supporta i tipi di regole di confronto seguenti:

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS
*    UTF-8

## <a name="check-the-current-collation"></a>Controllare le regole di confronto correnti
Per verificare le regole di confronto correnti per il database, è possibile eseguire il seguente frammento T-SQL:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
Quando viene passato ' Collation ' come parametro Property, la funzione DatabasePropertyEx restituisce le regole di confronto correnti per il database specificato. Altre informazioni sulla funzione DatabasePropertyEx sono disponibili su MSDN.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle procedure consigliate per il pool SQL e SQL su richiesta, vedere gli articoli seguenti:

- [Procedure consigliate per il pool SQL](best-practices-sql-pool.md)
- [Procedure consigliate per SQL su richiesta](best-practices-sql-on-demand.md)


