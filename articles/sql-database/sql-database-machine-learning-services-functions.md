---
title: Scrivere funzioni R avanzate
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Informazioni su come scrivere una funzione R per il calcolo statistico avanzato nel database SQL di Azure usando Machine Learning Services (anteprima).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 939798d5d9eb2843d7bbbbe74680342e4ce6ce95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60702453"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Scrivere le funzioni R avanzate nel Database SQL di Azure con Machine Learning Services (anteprima)

In questo articolo viene descritto come incorporare R funzioni matematiche e di utilità in una stored procedure SQL. Le funzioni statistiche avanzate complesse da implementare in T-SQL possono essere eseguite in R con una singola riga di codice.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/) prima di iniziare.

- Per eseguire il codice di esempio in questi esercizi, è necessario disporre di un database SQL di Azure con Machine Learning Services (con R) abilitato. Durante l'anteprima pubblica, Microsoft eseguirà l'onboarding e l'abilitazione dell'apprendimento automatico per il database nuovo o esistente. Seguire la procedura descritta in [Iscriversi per l'anteprima](sql-database-machine-learning-services-overview.md#signup).

- Assicurarsi di avere installato la versione più recente di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). È possibile eseguire gli script R tramite altri strumenti di gestione di database o di query, ma in questo Avvio rapido si userà SQL Server Management Studio.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Creare una stored procedure per generare numeri casuali

Per semplicità, usare il `stats` pacchetto R installato e caricato per impostazione predefinita con il database SQL di Azure usando Machine Learning Services (anteprima). Il pacchetto contiene centinaia di funzioni per `rnorm` attività statistiche comuni, tra cui la funzione. Questa funzione genera un numero specificato di numeri casuali utilizzando la distribuzione normale, data una deviazione standard e mezzi.

Ad esempio, il codice R seguente restituisce 100 numeri, in una media di 50, in base alla deviazione standard 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Per chiamare questa riga di R `sp_execute_external_script` da T-SQL, eseguire e aggiungere la funzione R nel parametro di script R, in questo modo:To call this line of R from T-SQL, run and add the R function in the R script parameter, like this:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Si vuole semplificare la generazione di un set diverso di numeri casuali?

Questo è facile quando combinato con SQL. Si definisce una stored procedure che ottiene gli argomenti dall'utente e quindi si passano tali argomenti nello script R come variabili.

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- La prima riga definisce ognuno dei parametri di input SQL necessari quando viene eseguita la stored procedure.

- La riga che inizia con `@params` definisce tutte le variabili usate dal codice R e i tipi di dati SQL corrispondenti.

- Le righe immediatamente successive eseguono il mapping dei nomi dei parametri SQL ai nomi delle variabili R corrispondenti.

Dopo aver eseguito il wrapping della funzione R in una stored procedure, è possibile chiamare facilmente la funzione e passare valori diversi, in questo modo:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Usare funzioni di utilità R per la risoluzione dei problemi

Il `utils` pacchetto, installato per impostazione predefinita, fornisce una varietà di funzioni di utilità per l'analisi dell'ambiente R corrente. Queste funzioni possono essere utili se si trovano discrepanze nel modo in cui il codice R esegue in SQL e in ambienti esterni. Ad esempio, è possibile usare la funzione R `memory.limit()` per ottenere memoria per l'ambiente R corrente.

Poiché il pacchetto `utils` viene installato, ma non viene caricato per impostazione predefinita, prima di tutto è necessario usare la funzione `library()` per caricarlo.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> Molti utenti amano utilizzare le funzioni di `system.time` temporizzazione del sistema in R, ad esempio e `proc.time`, per acquisire il tempo utilizzato dai processi R e analizzare i problemi di prestazioni.
