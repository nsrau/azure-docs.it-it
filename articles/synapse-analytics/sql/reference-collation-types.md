---
title: Regole di confronto
description: Tipi di regole di confronto supportati in Sql Synapse di AzureCollation types supported in Azure Synapse SQL
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 4270677f8f5f77e1ada0b1d9385163dad762bbda
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431540"
---
# <a name="database-collation-support-for-synapse-sql"></a>Database collation support for Synapse SQL

Le regole di confronto forniscono le regole relative alle impostazioni locali, alla tabella codici, all'ordinamento e alla sensibilità dei caratteri per i tipi di dati basati su caratteri. Una volta scelto, tutte le colonne e le espressioni che richiedono informazioni sulle regole di confronto ereditano le regole di confronto scelte dall'impostazione del database. L'ereditarietà predefinita può essere sottoposta a override indicando in modo esplicito regole di confronto diverse per un tipo di dati basato su caratteri.

È possibile modificare le regole di confronto predefinite del database dal portale di Azure quando si crea un nuovo database del pool SQL. Questa funzionalità semplifica ulteriormente la creazione di un nuovo database utilizzando una delle regole di confronto del database supportate da 3800.

È possibile specificare le regole di confronto predefinite del database Synapse SQL su richiesta al momento della creazione utilizzando l'istruzione CREATE DATABASE.

## <a name="changing-collation"></a>Modifica delle regole di confronto
Per modificare le regole di confronto predefinite per il database del pool SQL, è necessario eseguire un semplice aggiornamento al campo Regole di confronto nell'esperienza di provisioning. Ad esempio, se si desidera modificare le regole di confronto predefinite in maiuscole e minuscole, è sufficiente rinominare le regole di confronto da SQL_Latin1_General_CP1_CI_AS a SQL_Latin1_General_CP1_CS_AS. 

Per modificare le regole di confronto predefinite per il database SQL su richiesta, è possibile utilizzare l'istruzione ALTER DATABASE.

## <a name="list-of-unsupported-collation-types"></a>Elenco dei tipi di regole di confronto non supportati
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

Inoltre, il pool SQL non supporta i seguenti tipi di regole di confronto:

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS

## <a name="checking-the-current-collation"></a>Verifica delle regole di confronto correnti
Per controllare le regole di confronto correnti per il database, è possibile eseguire il frammento T-SQL seguente:To check the current collation for the database, you can run the following T-SQL snippet:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
Quando viene passato 'Collation' come parametro della proprietà, la funzione DatabasePropertyEx restituisce le regole di confronto correnti per il database specificato. Ulteriori informazioni sulla funzione DatabasePropertyEx sono informazioni su MSDN.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulle procedure consigliate per il pool SQL e SQL su richiesta sono disponibili negli articoli seguenti:Additional information on best practices for SQL pool and SQL on-demand can be found in the following articles:

- [Procedure consigliate per il pool SQLBest Practices for SQL pool](best-practices-sql-pool.md)
- [Procedure consigliate per SQL su richiestaBest practices for SQL on-demand](best-practices-sql-on-demand.md)


