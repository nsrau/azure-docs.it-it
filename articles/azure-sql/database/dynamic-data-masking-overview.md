---
title: Maschera dati dinamica
description: La maschera dati dinamica limita l'esposizione dei dati sensibili mediante la maschera a utenti senza privilegi per il database SQL di Azure, Azure SQL Istanza gestita e Azure sinapsi Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 08/04/2020
tags: azure-synpase
ms.openlocfilehash: 5442ddab5b4925e40250e63833a634006db7aead
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92781448"
---
# <a name="dynamic-data-masking"></a>Maschera dati dinamica 
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Il database SQL di Azure, Azure SQL Istanza gestita e Azure sinapsi Analytics supportano la maschera dati dinamica. La maschera dati dinamica limita l'esposizione dei dati sensibili nascondendoli agli utenti senza privilegi. 

Il mascheramento dei dati dinamici impedisce l'accesso non autorizzato ai dati sensibili consentendo agli utenti di definire la quantità di dati sensibili da rivelare, con un impatto minimo sul livello dell'applicazione. Si tratta di una funzionalità di sicurezza basata su criteri che consente di nascondere i dati sensibili nel set di risultati di una query in campi di database designati, senza modificare i dati nel database.

Ad esempio, un addetto all'assistenza in un call center può identificare i chiamanti da alcune cifre del numero di carta di credito, ma tali elementi di dati non devono essere completamente visibili all'addetto all'assistenza. È possibile definire una regola di maschera che renda visibili solo le ultime quattro cifre del numero di carta di credito nel set di risultati di tutte le query. Come altro esempio, è possibile definire una maschera dati appropriata per proteggere i dati personali, in modo che uno sviluppatore possa eseguire query negli ambienti di produzione per la risoluzione dei problemi senza violare le normative di conformità.

## <a name="dynamic-data-masking-basics"></a>Nozioni di base sulla maschera dati dinamica

È possibile configurare un criterio di maschera dati dinamica nel portale di Azure selezionando il pannello **Dynamic Data Masking** in **sicurezza** nel riquadro Configurazione del database SQL. Non è possibile impostare questa funzionalità usando il portale per le sinapsi di Azure (usare PowerShell o l'API REST) o SQL Istanza gestita. Per altre informazioni, vedere [Dynamic Data Masking](/sql/relational-databases/security/dynamic-data-masking).

### <a name="dynamic-data-masking-permissions"></a>Autorizzazioni per il mascheramento dei dati dinamici

Il mascheramento dei dati dinamici può essere configurato dai ruoli Amministratore del database SQL di Azure, Amministratore del server o [Gestore Sicurezza SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager).

### <a name="dynamic-data-masking-policy"></a>Criteri di mascheramento dei dati dinamici

* **Utenti SQL esclusi dalla maschera** : set di utenti SQL o identità Azure ad che ottengono dati senza maschera nei risultati della query SQL. Gli utenti con privilegi di amministratore sono sempre esclusi dalla maschera e possono visualizzare i dati originali senza maschera.
* **Regole di maschera** : set di regole che definiscono i campi designati a cui applicare la maschera e la funzione maschera da usare. I campi designati possono essere definiti tramite uno schema, un nome di tabella e un nome di colonna del database.
* **Funzioni maschera** : set di metodi che consentono di controllare l'esposizione dei dati per scenari diversi.

| Funzione maschera | Logica di mascheramento |
| --- | --- |
| **Default** |**Mascheramento completo in base ai tipi di dati dei campi designati**<br/><br/>• Usare XXXX o un numero minore di X se la dimensione del campo è inferiore a 4 caratteri per i tipi di dati di stringa (nchar, ntext, nvarchar).<br/>• Usare un valore pari a zero per i tipi di dati numerici (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Usare 01-01-1900 per i tipi di dati data/ora (date, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Per sql_variant viene usato il valore predefinito del tipo corrente.<br/>• Per XML viene usato il documento \<masked/>.<br/>• Usare un valore vuoto per i tipi di dati speciali (timestamp table, hierarchyid, GUID, binary, image, varbinary spatial types). |
| **Carta di credito** |**Metodo di mascheramento che espone le ultime quattro cifre dei campi designati** e aggiunge una stringa costante come prefisso sotto forma di carta di credito.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Indirizzo di posta elettronica** |**Metodo di mascheramento che espone la prima lettera e sostituisce il dominio con xxx.com** usando un prefisso di stringa costante sotto forma di indirizzo di posta elettronica.<br/><br/>aXX@XXXX.com |
| **Numero casuale** |**Metodo di mascheramento che genera un numero casuale** secondo i limiti selezionati e i tipi di dati effettivi. Se i limiti designati sono uguali, la funzione maschera è un numero costante.<br/><br/>![Screenshot che mostra il metodo di mascheramento per la generazione di un numero casuale.](./media/dynamic-data-masking-overview/1_DDM_Random_number.png) |
| **Testo personalizzato** |**Metodo di mascheramento che espone il primo e l'ultimo carattere** e aggiunge una stringa di riempimento personalizzata al centro. Se la stringa originale è più corta del prefisso e del suffisso visibili, viene usata solo la stringa di riempimento. <br/>prefisso[riempimento]suffisso<br/><br/>![Riquadro di spostamento](./media/dynamic-data-masking-overview/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Campi consigliati a cui applicare la maschera

Il motore di raccomandazioni DDM evidenzia determinati campi del database come potenzialmente sensibili e quindi come ottimi candidati per l'applicazione della maschera. Nel pannello Maschera dati dinamica nel portale saranno visibili le colonne consigliate per il proprio database. È sufficiente fare clic su **Aggiungi maschera** per una o più colonne e quindi su **Salva** per applicare una maschera a questi campi.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configurare la maschera dati dinamica per il database usando i cmdlet di PowerShell

### <a name="data-masking-policies"></a>Criteri di maschera dati

- [Get-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingPolicy)
- [Set-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingPolicy)

### <a name="data-masking-rules"></a>Regole per la maschera dati

- [Get-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingRule)
- [New-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/New-AzSqlDatabaseDataMaskingRule)
- [Remove-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Remove-AzSqlDatabaseDataMaskingRule)
- [Set-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingRule)

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-rest-api"></a>Configurare la maschera dati dinamica per il database usando l'API REST

È possibile usare l'API REST per gestire a livello di codice i criteri e le regole di maschera dati. L'API REST pubblicata supporta le operazioni seguenti:

### <a name="data-masking-policies"></a>Criteri di maschera dati

- [Crea o aggiorna](/rest/api/sql/datamaskingpolicies/createorupdate): crea o aggiorna un criterio di maschera dati del database.
- [Get](/rest/api/sql/datamaskingpolicies/get): ottiene un criterio di maschera dati del database. 

### <a name="data-masking-rules"></a>Regole per la maschera dati

- [Crea o aggiorna](/rest/api/sql/datamaskingrules/createorupdate): crea o aggiorna una regola di maschera dati del database.
- [List by database](/rest/api/sql/datamaskingrules/listbydatabase): ottiene un elenco di regole di maschera dati del database.