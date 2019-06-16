---
title: Restrizioni di funzionalità del Database SQL di Azure | Microsoft Docs
description: Restrizioni di funzionalità del Database SQL di Azure consente di migliorare la sicurezza del database da limitare le funzionalità del database che possono essere dagli utenti malintenzionati per accedere alle informazioni in essi contenuti.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: ac7a074e78def504a10b4daa07971f919f414a88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66259453"
---
# <a name="azure-sql-database-feature-restrictions"></a>Restrizioni di funzionalità del Database SQL di Azure

Una causa comune di attacchi di SQL Server è tramite le applicazioni web che accedono al database in cui vengono utilizzati varie forme di attacchi SQL injection per estrapolare informazioni relative al database.  In teoria, il codice dell'applicazione viene sviluppato in modo che non consente di attacchi SQL injection.  Tuttavia, nelle grandi-codebase che includono il codice legacy ed esterno, uno può mai essere assicurarsi che siano stati risolti tutti i casi, in modo che SQL injection siano della vita disponibili per la protezione da.  L'obiettivo di restrizioni di funzionalità è per evitare alcuni tipi di attacchi SQL injection dalla divulgazione di informazioni sul database, anche quando l'attacco SQL injection è riuscita.

## <a name="enabling-feature-restrictions"></a>Abilitazione delle restrizioni di funzionalità

Abilitazione delle restrizioni di funzionalità avviene tramite il `sp_add_feature_restriction` stored procedure come segue:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

È possibile limitare le funzionalità seguenti:

| Funzionalità          | Descrizione |
|------------------|-------------|
| N'ErrorMessages' | Quando la limitata, verranno applicata la maschera dati utente all'interno del messaggio di errore. Vedere [restrizione di funzionalità di messaggi di errore](#error-messages-feature-restriction) |
| N'Waitfor'       | Quando la limitata, il comando restituirà immediatamente senza alcun ritardo. Vedere [WAITFOR restrizione di funzionalità](#waitfor-feature-restriction) |

Il valore di `object_class` può essere rappresentata `N'User'` oppure `N'Role'` per indicare se `object_name` è un nome utente o un nome di ruolo nel database.

Nell'esempio seguente causa tutti i messaggi di errore per l'utente `MyUser` a essere mascherati:

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>La disabilitazione delle limitazioni delle funzionalità

La disabilitazione delle limitazioni delle caratteristiche viene eseguita usando il `sp_drop_feature_restriction` stored procedure come segue:

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

Nell'esempio seguente disabilita il mascheramento di messaggio di errore per l'utente `MyUser`:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>Limitazioni delle caratteristiche di visualizzazione

Il `sys.sql_feature_restrictions` Vista presenta tutte le restrizioni di funzionalità attualmente definiti nel database. Include le colonne seguenti:

| Nome colonna | Tipo di dati | Descrizione |
|-------------|-----------|-------------|
| class       | nvarchar(128) | Classe dell'oggetto a cui viene applicata la restrizione |
| object      | nvarchar(256) | Nome dell'oggetto a cui viene applicata la restrizione |
| Funzionalità     | nvarchar(128) | Funzionalità limitata |

## <a name="feature-restrictions"></a>Limitazioni delle funzionalità

### <a name="error-messages-feature-restriction"></a>Restrizione di funzionalità dei messaggi di errore

Un metodo attacco injection comune di SQL è inserire il codice che causa un errore.  Esaminando il messaggio di errore, un utente malintenzionato può per raccogliere informazioni relative al sistema, l'abilitazione di altri attacchi più mirati.  Questo attacco può essere particolarmente utile in cui l'applicazione non vengono visualizzati i risultati di una query, ma visualizzare messaggi di errore.

Si consideri un'applicazione web che dispone di una richiesta sotto forma di:

```html
http://www.contoso.com/employee.php?id=1
```

Che esegue la query di database seguenti:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Se il valore passato come il `id` parametro per la richiesta di applicazione web viene copiato per sostituire $EmpId nella query del database, un utente malintenzionato potrebbe eseguire la richiesta seguente:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

E verrebbe restituito l'errore seguente, che gli consente di conoscere il nome del database:

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

Dopo l'abilitazione dei messaggi di errore delle funzionalità restrizione per l'utente dell'applicazione nel database, il messaggio di errore restituito è nascosta, in modo che non va persa alcuna informazione interna sul database:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

Analogamente, l'autore dell'attacco è stato possibile eseguire la richiesta seguente:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

E verrebbe restituito l'errore seguente, che consente all'utente malintenzionato di informazioni sullo stipendio del dipendente:

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

Usa restrizioni di funzionalità dei messaggi di errore, il database restituisce:

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>Restrizione di funzionalità WAITFOR

Nascosta SQL Injection è quando un'applicazione non fornisce un utente malintenzionato con i risultati SQL inserito o con un messaggio di errore, ma l'autore dell'attacco può dedurre informazioni dal database mediante la costruzione di una query condizionale in cui i due rami condizionali richiedere una quantità di tempo di esecuzione diversa. Confrontando il tempo di risposta, l'autore dell'attacco puoi sapere quali branch è stato eseguito e in tal modo per raccogliere informazioni relative al sistema. Usa la variante di questo tipo di attacco più semplice la `WAITFOR` istruzione per introdurre il ritardo.

Si consideri un'applicazione web che dispone di una richiesta sotto forma di:

```html
http://www.contoso.com/employee.php?id=1
```

Che esegue la query di database seguenti:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Se il valore passato come parametro id per le richieste dell'applicazione web viene copiato per sostituire $EmpId nella query del database, un utente malintenzionato può eseguire la richiesta seguente:

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

E la query richiederebbe un 5 secondi aggiuntivi se il `sa` account è stato utilizzato. Se `WAITFOR` restrizione di funzionalità è disabilitata nel database, il `WAITFOR` istruzione verrà ignorata e perdita delle informazioni non utilizzando questo tipo di attacco.