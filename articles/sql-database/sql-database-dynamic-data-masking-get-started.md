---
title: Maschera dati dinamica
description: Il mascheramento dinamico dei dati limita l'esposizione dei dati sensibili mascherandoli agli utenti senza privilegi per il database SQL e Azure SynapseDynamic data masking limits sensitive data exposure by masking it to non-privileged users for SQL Database and Azure Synapse
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synpase
ms.openlocfilehash: e5b281d59245d8fbd32b18f4ac5fe577fc7ff309
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192915"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-azure-synapse-analytics"></a>Mascheramento dinamico dei dati per il database SQL di Azure e Azure Synapse AnalyticsDynamic data masking for Azure SQL Database and Azure Synapse Analytics

La funzione Maschera dati dinamica del database SQL limita l'esposizione dei dati sensibili, nascondendoli agli utenti senza privilegi. 

Il mascheramento dei dati dinamici impedisce l'accesso non autorizzato ai dati sensibili consentendo agli utenti di definire la quantità di dati sensibili da rivelare, con un impatto minimo sul livello dell'applicazione. Si tratta di una funzionalità di sicurezza basata su criteri che consente di nascondere i dati sensibili nel set di risultati di una query in campi di database designati, senza modificare i dati nel database.

Ad esempio, un addetto all'assistenza in un call center può identificare i chiamanti da alcune cifre del numero di carta di credito, ma tali elementi di dati non devono essere completamente visibili all'addetto all'assistenza. È possibile definire una regola di maschera che renda visibili solo le ultime quattro cifre del numero di carta di credito nel set di risultati di tutte le query. Oppure, è possibile definire una maschera dati appropriata per la protezione di informazioni personali identificabili (PII), in modo che uno sviluppatore possa eseguire una query negli ambienti di produzione a scopi di risoluzione dei problemi senza violare le normative di conformità.

## <a name="dynamic-data-masking-basics"></a>Nozioni di base sul mascheramento dinamico dei dati

Per impostare un criterio di maschera dati dinamica del database SQL nel portale di Azure, selezionare l'operazione Maschera dati dinamica nel pannello di configurazione o delle impostazioni del database SQL. Questa funzionalità non può essere impostata tramite il portale per Azure Synapse (usare Powershell o l'API REST)This feature cannot be set by using portal for Azure Synapse (Please use Powershell or REST API)

### <a name="dynamic-data-masking-permissions"></a>Autorizzazioni per il mascheramento dei dati dinamici

Il mascheramento dei dati dinamici può essere configurato dai ruoli Amministratore del database SQL di Azure, Amministratore del server o [Gestore Sicurezza SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager).

### <a name="dynamic-data-masking-policy"></a>Criteri di mascheramento dei dati dinamici

* **Utenti SQL esclusi dalla maschera**: set di utenti SQL o identità AAD che visualizzano dati senza maschera nei risultati delle query SQL. Gli utenti con privilegi di amministratore sono sempre esclusi dalla maschera e possono visualizzare i dati originali senza maschera.
* **Regole di maschera**: set di regole che definiscono i campi designati a cui applicare la maschera e la funzione maschera da usare. I campi designati possono essere definiti tramite uno schema, un nome di tabella e un nome di colonna del database.
* **Funzioni maschera** : set di metodi che consentono di controllare l'esposizione dei dati per scenari diversi.

| Funzione di mascheramento | Logica di mascheramento |
| --- | --- |
| **Predefinito** |**Mascheramento completo in base ai tipi di dati dei campi designati**<br/><br/>• Usare XXXX o un numero minore di X se la dimensione del campo è inferiore a 4 caratteri per i tipi di dati di stringa (nchar, ntext, nvarchar).<br/>• Usare un valore pari a zero per i tipi di dati numerici (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Usare 01-01-1900 per i tipi di dati data/ora (date, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Per sql_variant viene usato il valore predefinito del tipo corrente.<br/>Per XML viene \<utilizzato il documento mascherato/>.<br/>• Usare un valore vuoto per i tipi di dati speciali (timestamp table, hierarchyid, GUID, binary, image, varbinary spatial types). |
| **Carta di credito** |**Metodo di mascheramento, che espone le ultime quattro cifre dei campi designati** e aggiunge una stringa costante come prefisso sotto forma di carta di credito.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Posta elettronica** |**Metodo di mascheramento, che espone la prima lettera e sostituisce il dominio con XXX.com** utilizzando un prefisso di stringa costante sotto forma di indirizzo di posta elettronica.<br/><br/>aXX@XXXX.com |
| **Numero casuale** |**Metodo di mascheramento che genera un numero casuale** secondo i limiti selezionati e i tipi di dati effettivi. Se i limiti designati sono uguali, la funzione maschera è un numero costante.<br/><br/>![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Testo personalizzato** |**Metodo di mascheramento, che espone il primo e l'ultimo carattere** e aggiunge una stringa di spaziatura interna personalizzata al centro. Se la stringa originale è più corta del prefisso e del suffisso visibili, viene usata solo la stringa di riempimento. <br/>prefisso[riempimento]suffisso<br/><br/>![Riquadro di spostamento](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Campi consigliati a cui applicare la maschera

Il motore di raccomandazioni DDM evidenzia determinati campi del database come potenzialmente sensibili e quindi come ottimi candidati per l'applicazione della maschera. Nel pannello Maschera dati dinamica nel portale saranno visibili le colonne consigliate per il proprio database. È sufficiente fare clic su **Aggiungi maschera** per una o più colonne e quindi su **Salva** per applicare una maschera a questi campi.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configurare il mascheramento dinamico dei dati per il database utilizzando i cmdlet di PowerShellSet up dynamic data masking for your database using PowerShell cmdlets

Vedere [Cmdlet del database SQL di Azure](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configurare il mascheramento dei dati dinamici per il database usando l'API REST

Vedere [Operations for Azure SQL Database](https://docs.microsoft.com/rest/api/sql/) (Operazioni per i database SQL di Azure).
