---
title: Maschera dati dinamica per il database SQL di Azure e data warehouse
description: La maschera dati dinamica limita l'esposizione dei dati sensibili mediante la maschera a utenti senza privilegi per il database SQL e data warehouse
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
ms.date: 03/04/2019
ms.openlocfilehash: dc0eab6aa45a943841d19a3aec2eddf4ab22d75d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690553"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-data-warehouse"></a>Maschera dati dinamica per il database SQL di Azure e data warehouse

La funzione Maschera dati dinamica del database SQL limita l'esposizione dei dati sensibili, nascondendoli agli utenti senza privilegi. 

Il mascheramento dei dati dinamici impedisce l'accesso non autorizzato ai dati sensibili consentendo agli utenti di definire la quantità di dati sensibili da rivelare, con un impatto minimo sul livello dell'applicazione. Si tratta di una funzionalità di sicurezza basata su criteri che consente di nascondere i dati sensibili nel set di risultati di una query in campi del database designati, senza alcuna modifica dei dati contenuti nel database.

Ad esempio, un addetto all'assistenza in un call center può identificare i chiamanti da alcune cifre del numero di carta di credito, ma tali elementi di dati non devono essere completamente visibili all'addetto all'assistenza. È possibile definire una regola di maschera che renda visibili solo le ultime quattro cifre del numero di carta di credito nel set di risultati di tutte le query. Oppure, è possibile definire una maschera dati appropriata per la protezione di informazioni personali identificabili (PII), in modo che uno sviluppatore possa eseguire una query negli ambienti di produzione a scopi di risoluzione dei problemi senza violare le normative di conformità.

## <a name="dynamic-data-masking-basics"></a>Nozioni di base sulla maschera dati dinamica

Per impostare un criterio di maschera dati dinamica del database SQL nel portale di Azure, selezionare l'operazione Maschera dati dinamica nel pannello di configurazione o delle impostazioni del database SQL.

### <a name="dynamic-data-masking-permissions"></a>Autorizzazioni per il mascheramento dei dati dinamici

Il mascheramento dei dati dinamici può essere configurato dai ruoli Amministratore del database SQL di Azure, Amministratore del server o [Gestore Sicurezza SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager).

### <a name="dynamic-data-masking-policy"></a>Criteri di mascheramento dei dati dinamici

* **Utenti SQL esclusi dalla maschera**: set di utenti SQL o identità AAD che visualizzano dati senza maschera nei risultati delle query SQL. Gli utenti con privilegi di amministratore sono sempre esclusi dalla maschera e possono visualizzare i dati originali senza maschera.
* **Regole di maschera**: set di regole che definiscono i campi designati a cui applicare la maschera e la funzione maschera da usare. I campi designati possono essere definiti tramite uno schema, un nome di tabella e un nome di colonna del database.
* **Funzioni maschera** : set di metodi che consentono di controllare l'esposizione dei dati per scenari diversi.

| Funzione di mascheramento | Logica di mascheramento |
| --- | --- |
| **Default** |**Mascheramento completo in base ai tipi di dati dei campi designati**<br/><br/>• Usare XXXX o un numero minore di X se la dimensione del campo è inferiore a 4 caratteri per i tipi di dati di stringa (nchar, ntext, nvarchar).<br/>• Usare un valore pari a zero per i tipi di dati numerici (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Usare 01-01-1900 per i tipi di dati data/ora (date, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Per sql_variant viene usato il valore predefinito del tipo corrente.<br/>• For XML viene usato il documento \<mascherato/>.<br/>• Usare un valore vuoto per i tipi di dati speciali (timestamp table, hierarchyid, GUID, binary, image, varbinary spatial types). |
| **Carta di credito** |**Metodo di mascheramento che espone le ultime quattro cifre dei campi designati** e aggiunge una stringa costante come prefisso sotto forma di carta di credito.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Indirizzo di posta elettronica** |**Metodo di mascheramento che rende visibile la prima lettera e sostituisce il dominio con XXX.com** usando un prefisso stringa costante sotto forma di indirizzo di posta elettronica.<br/><br/>aXX@XXXX.com |
| **Numero casuale** |**Metodo di mascheramento che genera un numero casuale** secondo i limiti selezionati e i tipi di dati effettivi. Se i limiti designati sono uguali, la funzione maschera è un numero costante.<br/><br/>![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Testo personalizzato** |**Metodo di mascheramento che rende visibile il primo e l'ultimo carattere** e aggiunge una stringa di riempimento personalizzata al centro. Se la stringa originale è più corta del prefisso e del suffisso visibili, viene usata solo la stringa di riempimento. <br/>prefisso[riempimento]suffisso<br/><br/>![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Campi consigliati a cui applicare la maschera

Il motore di raccomandazioni DDM evidenzia determinati campi del database come potenzialmente sensibili e quindi come ottimi candidati per l'applicazione della maschera. Nel pannello Maschera dati dinamica nel portale saranno visibili le colonne consigliate per il proprio database. È sufficiente fare clic su **Aggiungi maschera** per una o più colonne e quindi su **Salva** per applicare una maschera a questi campi.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configurare la maschera dati dinamica per il database usando i cmdlet di PowerShell

Vedere [Cmdlet del database SQL di Azure](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configurare il mascheramento dei dati dinamici per il database usando l'API REST

Vedere [Operations for Azure SQL Database](https://docs.microsoft.com/rest/api/sql/) (Operazioni per i database SQL di Azure).
