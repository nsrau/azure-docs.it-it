---
title: Scrivere funzioni R avanzate
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Informazioni su come scrivere una funzione R per i calcoli statistici avanzati nel Database SQL di Azure con servizi di Machine Learning (anteprima).
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702453"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Scrivere le funzioni R avanzate nel Database SQL di Azure con servizi di Machine Learning (anteprima)

Questo articolo descrive come incorporare R matematica e stored procedure di funzioni di utilità in un database SQL. Funzioni statistiche avanzate che sono piuttosto complesse da implementare in T-SQL possono essere eseguite in R con una sola riga di codice.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/) prima di iniziare.

- Per eseguire il codice di esempio in questi esercizi, è necessario disporre di un database SQL di Azure con Machine Learning Services (con R) abilitato. Durante l'anteprima pubblica, Microsoft eseguirà l'onboarding e l'abilitazione dell'apprendimento automatico per il database nuovo o esistente. Seguire la procedura descritta in [Iscriversi per l'anteprima](sql-database-machine-learning-services-overview.md#signup).

- Assicurarsi di avere installato la versione più recente di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). È possibile eseguire gli script R tramite altri strumenti di gestione di database o di query, ma in questo Avvio rapido si userà SQL Server Management Studio.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Creare una stored procedure per generare numeri casuali

Per semplicità, si userà R `stats` pacchetto che ha installato e caricato per impostazione predefinita con il Database SQL di Azure usando servizi di Machine Learning (anteprima). Il pacchetto contiene centinaia di funzioni per le attività statistiche comuni, tra cui la `rnorm` (funzione). Questa funzione genera un numero specificato di numeri casuali usando la distribuzione normale, date una deviazione standard e mezzo.

Ad esempio, il seguente codice R restituisce 100 numeri in una media di 50, in base una deviazione standard pari a 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Per chiamare questa linea di R da T-SQL, eseguire `sp_execute_external_script` e aggiungere la funzione R nel parametro di script R, simile al seguente:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Cosa accade se si desidera rendono più semplice generare un set diverso di numeri casuali?

È facile in combinazione con SQL. Si definisce una stored procedure che ottiene gli argomenti da parte dell'utente, quindi passa tali argomenti nello script R come variabili.

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

- La prima riga definisce ognuno dei parametri di input SQL che sono necessari quando viene eseguita la stored procedure.

- La riga che inizia con `@params` definisce tutte le variabili usate dal codice R e i tipi di dati SQL corrispondenti.

- Le righe che seguono immediatamente mappare i nomi dei parametri SQL ai nomi delle variabili R corrispondenti.

Ora che è stato eseguito il wrapping di funzione R in una stored procedure, è facilmente possibile chiamare la funzione e passare valori diversi, simile al seguente:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Usare funzioni di utilità R per la risoluzione dei problemi

Il `utils` pacchetto, installato per impostazione predefinita, offre un'ampia gamma di funzioni di utilità per esaminare l'ambiente R corrente. Queste funzioni possono essere utile se si sta discrepanze nel modo che consente di eseguire il codice R in SQL e in ambienti esterni. Ad esempio, si potrebbe usare R `memory.limit()` funzione per ottenere memoria per l'ambiente R corrente.

Poiché il `utils` pacchetto viene installato ma non è stato caricato per impostazione predefinita, è necessario usare il `library()` funzione caricarlo prima di tutto.

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
> Numero di utenti come usare le funzioni di temporizzazione di sistema in R, ad esempio `system.time` e `proc.time`, per acquisire il tempo usato dai processi R e analizzare i problemi di prestazioni.
